Analiza Bazy danych i optymalizacja zapytań
=================================================

Analiza normalizacji
--------------------

Schemat jest zgodny z 3NF:
- Każda tabela ma klucz główny.
- Wszystkie atrybuty są bezpośrednio zależne od klucza.
- Brak redundancji poza LogAukcji (wskazania na ID klientów), co sprzyja JOIN-om.

Ewentualna denormalizacja — np. dodanie imienia i nazwiska do LogAukcji — wymaga uzasadnienia korzyści wydajnościowych.

Potencjalne problemy wydajnościowe
----------------------------------

- Brak indeksów poza kluczami głównymi
- JOIN-y na sprzedawca/nabywca oraz filtrowanie po data_transakcji mogą wymagać pełnych skanów
- LogAukcji rośnie — przy dużej liczbie rekordów wydajność spada
- Złożone zapytania agregujące (np. raporty miesięczne) będą kosztowne bez wsparcia

Strategie optymalizacji
-----------------------

**Indeksy:**

.. code-block:: sql

    CREATE INDEX idx_log_sprzedawca ON LogAukcji(sprzedawca);
    CREATE INDEX idx_log_nabywca ON LogAukcji(nabywca);
    CREATE INDEX idx_log_data ON LogAukcji(data_transakcji);
    CREATE INDEX idx_log_cena_data ON LogAukcji(data_transakcji, cena_sprzedazy);

**Partycjonowanie:**
- Partycjonuj LogAukcji po data_transakcji (np. miesiąc lub rok)
- Ułatwia archiwizację i przyspiesza zapytania zakresowe

**Widoki materializowane:**

.. code-block:: sql

    CREATE MATERIALIZED VIEW mv_miesieczna_sprzedaz AS
    SELECT date_trunc('month', data_transakcji) AS m,
           COUNT(*) AS cnt,
           SUM(cena_sprzedazy) AS suma
    FROM LogAukcji
    GROUP BY 1;

- Odświeżaj np. codziennie

**Optymalizacja zapytań:**

- Korzystaj z `EXPLAIN (ANALYZE, BUFFERS)` do analizy kosztów
- Unikaj `SELECT *`
- Jeśli masz wiele schematów: używaj w pełni kwalifikowanych nazw

Przykład optymalizacji konkretnego zapytania
--------------------------------------------

**Przed optymalizacją:**

.. code-block:: sql

    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC;

**Po optymalizacji:**
-- indeks na (sprzedawca, data_transakcji) już utworzony
.. code-block:: sql

    EXPLAIN ANALYZE
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    LIMIT 10;  -- jeśli potrzebujemy tylko top 10

Wykorzystanie EXPLAIN w PostgreSQL do analizy wydajności zapytań
================================================================

EXPLAIN to podstawowe narzędzie w PostgreSQL do poznania, jak silnik bazodanowy realizuje zapytanie.  
W wersji z ``ANALYZE`` i ``BUFFERS`` pozwala zmierzyć realne czasy i zużycie I/O.

Formy EXPLAIN
-------------

- ``EXPLAIN <zapytanie>``  
  Pokaże szacowany plan (bez wykonania).

- ``EXPLAIN ANALYZE <zapytanie>``  
  Wykona zapytanie, zwracając rzeczywiste czasy (actual).

- Dodatek ``BUFFERS``  
  Pokazuje liczbę odczytanych/zapisanych bloków (shared hit/read/write, temp).

- Dodatki ``VERBOSE``, ``FORMAT JSON`` lub ``FORMAT YAML``  
  Pełniejsze opisy node’ów i struktury planu (łatwiejsze parsowanie skryptami).

Przykład:

.. code-block:: sql

    EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
    SELECT ...;

Kluczowe metryki w EXPLAIN ANALYZE
----------------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 40 35

   * - Metryka
     - Co mówi
     - Punkt kontroli
   * - startup cost
     - czas uruchomienia node’a
     - wczesne agregacje / wyszukiwanie
   * - total cost
     - szacowany koszt wykonania
     - najdroższy etap w planie
   * - actual time (first)
     - czas pierwszego wiersza
     - opóźnienia dostępu do danych
   * - actual time (last)
     - czas przetwarzania wierszy
     - wolne sortowania / łączenia
   * - rows vs loops
     - liczba wierszy vs liczba wywołań
     - niska selektywność filtrów/joinów
   * - buffers read/hit
     - odczyty z dysku vs cache
     - niewystarczający cache
   * - temp read/write
     - użycie plików tymczasowych
     - duże sorty / HashJoin bez pamięci
   * - planning time
     - czas budowy planu
     - skomplikowane zapytania/statystyki
   * - execution time
     - czas wykonania zapytania
     - kryterium SLA

Jak interpretować plan?
-----------------------

1. Przejdź drzewo node’ów od góry (root) do liści:

   a. Zidentyfikuj najwyższy ``actual total cost`` vs ``estimated cost`` – to może być wąskie gardło.  
   b. Porównaj ``rows`` vs ``actual rows`` – duża rozbieżność może oznaczać nieaktualne statystyki.

2. Zwróć uwagę na typ node’a:

   - ``Seq Scan`` → możliwy dodatek indeksu  
   - ``Index Scan`` → indeks działa  
   - ``Nested Loop``, ``Hash Join``, ``Merge Join`` → dobierz strategię joinów do danych  

3. Sprawdź zużycie buforów:

   - duża liczba ``shared read`` → praca na dysku  
   - ``temp read/write`` → może wymagać większego ``work_mem``

Systematyczny proces analizy planu
----------------------------------

- **Wyznacz kluczowe zapytania**  
  Raporty, dashboardy, API endpointy.

- **Przeanalizuj bez indeksów**  
  ``EXPLAIN (ANALYZE, BUFFERS)`` i zapisz czasy oraz ilości odczytanych bloków.

- **Wdroż indeksy lub refaktoryzuj zapytania**  
  Porównaj metryki „przed–po”.

- **Dokumentuj**  
  - Fragmenty ``JSON``/``YAML`` do analizy skryptami  
  - Wybrane node’y z najwyższym kosztem

- **Ustal punkty alarmowe**  
  Dla execution_time, temp space, zużycia I/O

- **Automatyzuj**  
  - Włącz ``auto_explain`` w pliku ``postgresql.conf``  
  - Loguj plany przekraczające próg czasu  
  - Analizuj dane historyczne z ``pg_stat_statements`` lub narzędzi jak ``pgBadger``, ``pganalyze``

Wnioski
-------

- ``EXPLAIN ANALYZE`` to nie tylko plan, ale też **realne metryki**: czas wykonania, I/O, cache.
- Systematyczne porównywanie metryk **przed i po** wdrożeniu indeksów/zmian daje wymierne wyniki.
- Kluczowe punkty kontroli:
  - ``execution_time``
  - ``buffers``
  - rozbieżność między ``rows`` a ``actual rows``
- Automatyzacja (np. ``auto_explain`` i ``pg_stat_statements``) pozwala wykrywać problemy zanim pojawią się skargi od użytkowników.


Analiza i optymalizacja na danych SQLite (SQLite in-memory)
============================================================

Optymalizacja przykładowych zapytań
-----------------------------------

Przykładowe zapytanie:

.. code-block:: sql

    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC;

Dzięki indeksom na ``(data_transakcji, sprzedawca)`` oraz ``Klient(numer_dowodu)``, SQLite wykonuje index-seek, a następnie szybki sort/LIMIT zamiast pełnego skanu tabeli.

Indeksy w SQLite
----------------

Dodajemy indeksy:

.. code-block:: sql

    CREATE INDEX IF NOT EXISTS idx_la_sprzedawca    ON LogAukcji(sprzedawca);
    CREATE INDEX IF NOT EXISTS idx_la_nabywca       ON LogAukcji(nabywca);
    CREATE INDEX IF NOT EXISTS idx_la_data          ON LogAukcji(data_transakcji);
    CREATE INDEX IF NOT EXISTS idx_la_data_spr      ON LogAukcji(data_transakcji, sprzedawca);

Przykłady pomiaru czasu wykonania
---------------------------------

**1. Pomiar przed optymalizacją**

.. code-block:: python

    start_time = time.time()
    cursor.execute("""
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    """)
    results = cursor.fetchall()
    end_time = time.time()
    print(f"Czas wykonania zapytania: {end_time - start_time:.4f} sekundy")

*Wynik:* ``0.0019 sekundy``

**2. Pomiar z optymalizacją LIMIT**

.. code-block:: python

    start_time = time.time()
    cursor.execute("""
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    LIMIT 10;
    """)
    results = cursor.fetchall()
    end_time = time.time()
    print(f"Czas wykonania zapytania: {end_time - start_time:.4f} sekundy")

*Wynik:* ``0.0013 sekundy``

**3. Pomiar po dodaniu indeksów**

.. code-block:: python

    cursor.execute("CREATE INDEX IF NOT EXISTS idx_log_sprzedawca ON LogAukcji(sprzedawca);")
    cursor.execute("CREATE INDEX IF NOT EXISTS idx_log_nabywca ON LogAukcji(nabywca);")
    cursor.execute("CREATE INDEX IF NOT EXISTS idx_log_data ON LogAukcji(data_transakcji);")
    cursor.execute("CREATE INDEX IF NOT EXISTS idx_log_data_sprzed ON LogAukcji(data_transakcji, sprzedawca);")

    start_time = time.time()
    cursor.execute("""
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    LIMIT 10;
    """)
    results = cursor.fetchall()
    end_time = time.time()
    print(f"Czas wykonania zapytania: {end_time - start_time:.4f} sekundy")

*Wynik:* ``0.0005 sekundy``

Wnioski:
~~~~~~~~

Wprowadzenie indeksów oraz LIMIT istotnie zmniejsza czas wykonania zapytania w SQLite nawet na niewielkiej próbce. Przy większych zbiorach danych efekty te są jeszcze bardziej wyraźne.

Indeksowanie i pomiar wydajności zapytań w PostgreSQL
======================================================

Tworzenie indeksów
------------------

Po załadowaniu danych, w celu przyspieszenia zapytań zawierających filtry i JOIN-y, należy dodać indeksy:

.. code-block:: sql

    -- Przyspieszenie filtrów i JOIN-ów na LogAukcji
    CREATE INDEX idx_log_sprzedawca  ON LogAukcji(sprzedawca);
    CREATE INDEX idx_log_nabywca     ON LogAukcji(nabywca);
    CREATE INDEX idx_log_data        ON LogAukcji(data_transakcji);

    -- Kompozytowy indeks dla częstych filtrów po dacie i sprzedawcy
    CREATE INDEX idx_log_data_sprzed ON LogAukcji(data_transakcji, sprzedawca);

Pomiar wydajności na przykładowym zapytaniu
-------------------------------------------

Przykład zapytania:

.. code-block:: sql

    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC;

PostgreSQL optymalizacja zapytań
=================================

**1. Pomiar czasu bez LIMIT**

.. code-block:: python

    explain_limit = """
    EXPLAIN (ANALYZE, BUFFERS)
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    """
    with engine.begin() as conn:
        result = conn.execute(text(explain_limit))
        for row in result:
            print(row[0])

**Wynik:**

.. code-block:: none

    Planning:
      Buffers: shared hit=7
    Planning Time: 0.099 ms
    Execution Time: 0.266 ms

**2. Pomiar z LIMIT 10**

.. code-block:: python

    explain_limit = """
    EXPLAIN (ANALYZE, BUFFERS)
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    LIMIT 10;
    """
    with engine.begin() as conn:
        result = conn.execute(text(explain_limit))
        for row in result:
            print(row[0])

**Wynik:**

.. code-block:: none

    Planning:
      Buffers: shared hit=3
    Planning Time: 0.169 ms
    Execution Time: 0.478 ms

**3. Pomiar z dodanymi indeksami**

.. code-block:: python

    create_indexes = """
    CREATE INDEX IF NOT EXISTS idx_log_sprzedawca  ON LogAukcji(sprzedawca);
    CREATE INDEX IF NOT EXISTS idx_log_nabywca     ON LogAukcji(nabywca);
    CREATE INDEX IF NOT EXISTS idx_log_data        ON LogAukcji(data_transakcji);
    CREATE INDEX IF NOT EXISTS idx_log_data_sprzed ON LogAukcji(data_transakcji, sprzedawca);
    """
    with engine.begin() as conn:
        conn.execute(text(create_indexes))

    explain_limit = """
    EXPLAIN (ANALYZE, BUFFERS)
    SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
    FROM LogAukcji l
    JOIN Klient k ON l.sprzedawca = k.numer_dowodu
    WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
    GROUP BY k.imie, k.nazwisko
    ORDER BY cnt DESC
    LIMIT 10;
    """
    with engine.begin() as conn:
        result = conn.execute(text(explain_limit))
        for row in result:
            print(row[0])

**Wynik:**

.. code-block:: none

    Planning:
      Buffers: shared hit=3
    Planning Time: 0.099 ms
    Execution Time: 0.828 ms

Wniosek:
~~~~~~~~

Zastosowanie indeksów i `LIMIT` pozwala lepiej kontrolować koszt zapytania — choć Execution Time może wzrosnąć w przypadku dodatkowej pracy np. na sortowaniu wyników, plan wykonania staje się bardziej przewidywalny. W przypadku większych danych różnice w buforach i czasie wykonania stają się znacznie bardziej zauważalne.
