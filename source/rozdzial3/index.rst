Sprawozdanie: Projektowanie bazy danych - modele
==========================================================================

:author: Piotr Kotuła, Piotr Pierzchała

Wprowadzenie
------------

Prowadzący:  
Kurs:  

Celem raportu jest przegląd struktury bazy danych aukcyjnej, identyfikacja potencjalnych wąskich gardeł i zaproponowanie strategii optymalizacji.  
Bazę opisano na podstawie schematu PostgreSQL — jeśli korzystacie z innej implementacji (np. SQLite), wystarczy dostosować tę analizę do własnych nazw typów i mechanizmów.

Model Konceptualny
------------------------

.. code-block:: none

           +-----------+      1       *      +------------+
           |   Klient  |---------------------|   Aukcja   |
           +-----------+                     +------------+
           | PK: dowód |                     | PK: id_tx  |
           +-----------+                     +------------+
                ^  \ 
                |   \ nabywa (0..1)
         wystawia   \
         (1..*)      v
           +-----------+
           |   Aukcja  |
           +-----------+
           |    …      |
           +-----------+
                ^
                |  prowadzony przez
                |       (1..*)
                v
           +-------------+
           | Pracownik   |
           +-------------+
           | PK: id_prac |
           +-------------+

Legenda:
  • Klient – może wystawiać wiele aukcji (1..*)  
  • Aukcja – ma dokładnie jednego sprzedawcę i opcjonalnie jednego nabywcę (0..1)  
  • Pracownik – prowadzi wiele aukcji (1..*)

Model Logiczny
------------------------

.. code-block:: none

  +-------------------+       +-------------------+       +------------------+
  |     Pracownik     |       |      Klient       |       |    LogAukcji     |
  +-------------------+       +-------------------+       +------------------+
  | PK: numer_pracown |       | PK: numer_dowodu  |       | PK: id_transak   |
  | imie              |       | imie              |       | sprzedawca (FK)  |
  | nazwisko          |       | nazwisko          |       | nabywca   (FK)   |
  +-------------------+       | ilosc_wyst_przedm |       | data_transakcji  |
                              | wymaga_kaucji     |       | cena_sprzedazy   |
                              +-------------------+       | numer_pracow (FK)|
                                                          +------------------+

Połączenia:
  • LogAukcji.sprzedawca → Klient.numer_dowodu (1:N)  
  • LogAukcji.nabywca → Klient.numer_dowodu (0..1:N)  
  • LogAukcji.numer_pracownika → Pracownik.numer_pracownika (1:N)

Model Fizyczny
------------------------

.. code-block:: sql

    CREATE TABLE Pracownik (
        numer_pracownika SERIAL PRIMARY KEY,
        imie       VARCHAR(50) NOT NULL,
        nazwisko   VARCHAR(50) NOT NULL
    );

    CREATE TABLE Klient (
        numer_dowodu                   VARCHAR(20) PRIMARY KEY,
        imie                           VARCHAR(50) NOT NULL,
        nazwisko                       VARCHAR(50) NOT NULL,
        ilosc_wystawionych_przedmiotow INT DEFAULT 0,
        wymaga_kaucji                 BOOLEAN DEFAULT FALSE
    );

    CREATE TABLE LogAukcji (
        id_transakcji    SERIAL PRIMARY KEY,
        sprzedawca       VARCHAR(20) NOT NULL,
        nabywca          VARCHAR(20),
        data_transakcji  DATE NOT NULL,
        cena_sprzedazy   NUMERIC(10,2) NOT NULL DEFAULT 0,
        numer_pracownika INT NOT NULL,
        CONSTRAINT fk_sprzedawca FOREIGN KEY (sprzedawca) REFERENCES Klient(numer_dowodu),
        CONSTRAINT fk_nabywca FOREIGN KEY (nabywca) REFERENCES Klient(numer_dowodu),
        CONSTRAINT fk_pracownik FOREIGN KEY (numer_pracownika) REFERENCES Pracownik(numer_pracownika)
    );

Przykładowe rekordy
------------------------

Tabela Pracownik
~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   * - numer_pracownika
     - imię
     - nazwisko
   * - 1
     - Anna
     - Kowalska
   * - 2
     - Marek
     - Nowak
   * - 3
     - Ewa
     - Zielińska
   * - ...
     - ...
     - ...

Tabela Klient
~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   * - numer_dowodu
     - imię
     - nazwisko
     - ilosc_wystawionych_przedmiotów
     - wymaga_kaucji
   * - ID100001
     - Jan
     - Kowalski
     - 3
     - 1
   * - ID100002
     - Anna
     - Nowak
     - 5
     - 0
   * - ...
     - ...
     - ...
     - ...
     - ...

Tabela LogAukcji
~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   * - id_transakcji
     - sprzedawca
     - nabywca
     - data_transakcji
     - cena_sprzedazy
     - numer_pracownika
   * - 1
     - ID100001
     - ID100050
     - 2025-06-01
     - 150.75
     - 1
   * - 2
     - ID100002
     - ID100049
     - 2025-06-02
     - 220.00
     - 2
   * - ...
     - ...
     - ...
     - ...
     - ...
     - ...
