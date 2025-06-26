Kod SQLite
==============

import sqlite3
import time
con=sqlite3.connect(":memory:")
cursor = con.cursor()


#LightSQL

# Tabela przechowująca dane pracowników
cursor.execute("""
CREATE TABLE Pracownik (
    numer_pracownika INTEGER PRIMARY KEY AUTOINCREMENT,
    imie            VARCHAR(50) NOT NULL,
    nazwisko        VARCHAR(50) NOT NULL
);
""")
# Tabela przechowująca dane klientów (sprzedawcy oraz nabywcy)
cursor.execute("""
CREATE TABLE Klient (
    numer_dowodu                       VARCHAR(20) PRIMARY KEY,
    imie                               VARCHAR(50) NOT NULL,
    nazwisko                           VARCHAR(50) NOT NULL,
    ilosc_wystawionych_przedmiotow      INTEGER DEFAULT 0,
    wymaga_kaucji                      BOOLEAN DEFAULT FALSE
)
""")
# Tabela rejestrująca transakcje aukcyjne
cursor.execute("""
CREATE TABLE LogAukcji (
    id_transakcji   INTEGER PRIMARY KEY AUTOINCREMENT,
    sprzedawca      VARCHAR(20) NOT NULL,  
    nabywca         VARCHAR(20),           
    data_transakcji DATE NOT NULL,
    cena_sprzedazy  NUMERIC(10,2) NOT NULL DEFAULT 0,
    numer_pracownika INTEGER NOT NULL,      
    FOREIGN KEY (sprzedawca) REFERENCES Klient(numer_dowodu),
    FOREIGN KEY (nabywca) REFERENCES Klient(numer_dowodu),
    FOREIGN KEY (numer_pracownika) REFERENCES Pracownik(numer_pracownika)
);
""")

cursor.execute("""
INSERT INTO Pracownik (imie, nazwisko) VALUES
('Anna', 'Kowalska'),
('Marek', 'Nowak'),
('Ewa', 'Zielińska'),
('Tomasz', 'Wiśniewski'),
('Karolina', 'Mazur');
""")


# Klienci
cursor.execute("""
INSERT INTO Klient (numer_dowodu, imie, nazwisko, ilosc_wystawionych_przedmiotow, wymaga_kaucji) VALUES
('ID100001', 'Jan', 'Kowalski', 3, 1),
('ID100002', 'Anna', 'Nowak', 5, 0),
('ID100003', 'Piotr', 'Wiśniewski', 2, 1),
('ID100004', 'Katarzyna', 'Dąbrowska', 7, 0),
('ID100005', 'Tomasz', 'Lewandowski', 4, 1),
('ID100006', 'Magdalena', 'Szymańska', 6, 0),
('ID100007', 'Michał', 'Woźniak', 1, 1),
('ID100008', 'Agnieszka', 'Kozłowska', 8, 0),
('ID100009', 'Robert', 'Jankowski', 3, 1),
('ID100010', 'Ewa', 'Mazur', 5, 0),
('ID100011', 'Aleksandra', 'Krawczyk', 4, 1),
('ID100012', 'Kamil', 'Zając', 2, 0),
('ID100013', 'Barbara', 'Król', 7, 1),
('ID100014', 'Andrzej', 'Górski', 6, 0),
('ID100015', 'Natalia', 'Czarnecka', 1, 1),
('ID100016', 'Paweł', 'Kubiak', 9, 0),
('ID100017', 'Monika', 'Pawlak', 3, 1),
('ID100018', 'Łukasz', 'Zawadzki', 8, 0),
('ID100019', 'Renata', 'Piotrowska', 5, 1),
('ID100020', 'Grzegorz', 'Sikora', 4, 0),
('ID100021', 'Karolina', 'Wójcik', 2, 1),
('ID100022', 'Maciej', 'Adamski', 10, 0),
('ID100023', 'Beata', 'Czerwińska', 6, 1),
('ID100024', 'Damian', 'Włodarczyk', 3, 0),
('ID100025', 'Patryk', 'Lis', 7, 1),
('ID100026', 'Joanna', 'Matuszewska', 5, 0),
('ID100027', 'Sebastian', 'Zielinski', 1, 1),
('ID100028', 'Maria', 'Szczepańska', 8, 0),
('ID100029', 'Artur', 'Wilk', 4, 1),
('ID100030', 'Dominika', 'Wysocka', 6, 0),
('ID100031', 'Marcin', 'Kubiś', 2, 1),
('ID100032', 'Paulina', 'Gajewska', 9, 0),
('ID100033', 'Rafał', 'Michalski', 5, 1),
('ID100034', 'Sylwia', 'Kulesza', 4, 0),
('ID100035', 'Adrian', 'Jastrzębski', 3, 1),
('ID100036', 'Izabela', 'Sawicka', 7, 0),
('ID100037', 'Daniel', 'Kołodziej', 6, 1),
('ID100038', 'Martyna', 'Rutkowska', 1, 0),
('ID100039', 'Konrad', 'Dudek', 8, 1),
('ID100040', 'Weronika', 'Bednarek', 10, 0),
('ID100041', 'Dariusz', 'Majewski', 4, 1),
('ID100042', 'Aleksander', 'Borkowski', 2, 0),
('ID100043', 'Justyna', 'Markowska', 9, 1),
('ID100044', 'Szymon', 'Nowicki', 5, 0),
('ID100045', 'Elżbieta', 'Wojciechowska', 3, 1),
('ID100046', 'Krystian', 'Stankiewicz', 7, 0),
('ID100047', 'Zuzanna', 'Jędrzejczyk', 6, 1),
('ID100048', 'Mariusz', 'Olszewski', 1, 0),
('ID100049', 'Angelika', 'Bielawska', 8, 1),
('ID100050', 'Mateusz', 'Chmielewski', 5, 0),
('ID100051', 'Natalia', 'Borkowska', 4, 1),
('ID100052', 'Karol', 'Żak', 7, 0),
('ID100053', 'Oliwia', 'Szymańska', 3, 1),
('ID100054', 'Dawid', 'Czarnecki', 5, 0),
('ID100055', 'Marta', 'Kamińska', 2, 1),
('ID100056', 'Jakub', 'Kowalczyk', 8, 0),
('ID100057', 'Alicja', 'Kaczmarek', 6, 1),
('ID100058', 'Mateusz', 'Szczepański', 1, 0),
('ID100059', 'Kinga', 'Lis', 9, 1),
('ID100060', 'Dominik', 'Adamski', 4, 0),
('ID100061', 'Joanna', 'Górska', 10, 1),
('ID100062', 'Konrad', 'Michalski', 3, 0),
('ID100063', 'Paulina', 'Zielińska', 5, 1),
('ID100064', 'Rafał', 'Król', 7, 0),
('ID100065', 'Wiktoria', 'Nowakowska', 2, 1),
('ID100066', 'Sebastian', 'Włodarczyk', 6, 0),
('ID100067', 'Magdalena', 'Czerwińska', 1, 1),
('ID100068', 'Tomasz', 'Kołodziej', 8, 0),
('ID100069', 'Aleksandra', 'Markowska', 4, 1),
('ID100070', 'Maciej', 'Piotrowski', 9, 0),
('ID100071', 'Anna', 'Wilczyńska', 7, 1),
('ID100072', 'Adrian', 'Rutkowski', 3, 0),
('ID100073', 'Beata', 'Bednarek', 5, 1),
('ID100074', 'Grzegorz', 'Gajewski', 10, 0),
('ID100075', 'Natalia', 'Sawicka', 1, 1),
('ID100076', 'Marcin', 'Jastrzębski', 8, 0),
('ID100077', 'Martyna', 'Kubiak', 4, 1),
('ID100078', 'Dariusz', 'Pawlak', 9, 0),
('ID100079', 'Izabela', 'Dudek', 6, 1),
('ID100080', 'Damian', 'Wójcik', 2, 0),
('ID100081', 'Joanna', 'Stankiewicz', 7, 1),
('ID100082', 'Aleksander', 'Olszewski', 3, 0),
('ID100083', 'Weronika', 'Chmielewska', 5, 1),
('ID100084', 'Daniel', 'Lisowski', 10, 0),
('ID100085', 'Sylwia', 'Bielawska', 1, 1),
('ID100086', 'Mateusz', 'Kowalczyk', 8, 0),
('ID100087', 'Zuzanna', 'Wysocka', 4, 1),
('ID100088', 'Artur', 'Krupa', 9, 0),
('ID100089', 'Mariusz', 'Sikora', 6, 1),
('ID100090', 'Angelika', 'Górska', 2, 0),
('ID100091', 'Szymon', 'Majewski', 7, 1),
('ID100092', 'Karolina', 'Woźniak', 3, 0),
('ID100093', 'Paweł', 'Zawadzki', 5, 1),
('ID100094', 'Barbara', 'Lewandowska', 10, 0),
('ID100095', 'Michał', 'Pietrzak', 1, 1),
('ID100096', 'Dominika', 'Wilk', 8, 0),
('ID100097', 'Andrzej', 'Szymański', 4, 1),
('ID100098', 'Monika', 'Kubiak', 9, 0),
('ID100099', 'Robert', 'Adamski', 6, 1),
('ID100100', 'Katarzyna', 'Czarnecka', 2, 0),
('ID100101', 'Aleksandra', 'Zielińska', 7, 0),
('ID100102', 'Tomasz', 'Kowalczyk', 5, 1),
('ID100103', 'Marta', 'Wilczyńska', 3, 0),
('ID100104', 'Jakub', 'Adamski', 9, 1),
('ID100105', 'Natalia', 'Borkowska', 4, 0),
('ID100106', 'Karol', 'Żak', 6, 1),
('ID100107', 'Oliwia', 'Szymańska', 1, 0),
('ID100108', 'Dawid', 'Czarnecki', 8, 1),
('ID100109', 'Katarzyna', 'Kamińska', 10, 0),
('ID100110', 'Mateusz', 'Lis', 2, 1),
('ID100111', 'Alicja', 'Kaczmarek', 5, 0),
('ID100112', 'Sebastian', 'Szczepański', 7, 1),
('ID100113', 'Paulina', 'Górska', 4, 0),
('ID100114', 'Rafał', 'Król', 3, 1),
('ID100115', 'Wiktoria', 'Nowakowska', 9, 0),
('ID100116', 'Maciej', 'Włodarczyk', 6, 1),
('ID100117', 'Grzegorz', 'Czerwiński', 1, 0),
('ID100118', 'Damian', 'Kołodziej', 8, 1),
('ID100119', 'Izabela', 'Markowska', 10, 0),
('ID100120', 'Dariusz', 'Piotrowski', 2, 1),
('ID100121', 'Anna', 'Wilczyńska', 5, 0),
('ID100122', 'Adrian', 'Rutkowski', 7, 1),
('ID100123', 'Beata', 'Bednarek', 4, 0),
('ID100124', 'Karolina', 'Gajewska', 3, 1),
('ID100125', 'Patryk', 'Pawlak', 9, 0),
('ID100126', 'Monika', 'Dudek', 6, 1),
('ID100127', 'Robert', 'Wójcik', 1, 0),
('ID100128', 'Angelika', 'Stankiewicz', 8, 1),
('ID100129', 'Szymon', 'Olszewski', 10, 0),
('ID100130', 'Mariusz', 'Chmielewski', 2, 1),
('ID100131', 'Joanna', 'Szymańska', 5, 0),
('ID100132', 'Aleksander', 'Zawadzki', 7, 1),
('ID100133', 'Weronika', 'Krupa', 4, 0),
('ID100134', 'Martyna', 'Sikora', 3, 1),
('ID100135', 'Paweł', 'Majewski', 9, 0),
('ID100136', 'Barbara', 'Lewandowska', 6, 1),
('ID100137', 'Michał', 'Pietrzak', 1, 0),
('ID100138', 'Dominika', 'Wilk', 8, 1),
('ID100139', 'Andrzej', 'Szymański', 10, 0),
('ID100140', 'Renata', 'Kubiak', 2, 1),
('ID100141', 'Grzegorz', 'Adamski', 5, 0),
('ID100142', 'Karolina', 'Czarnecka', 7, 1),
('ID100143', 'Patryk', 'Michalski', 4, 0),
('ID100144', 'Monika', 'Lis', 3, 1),
('ID100145', 'Robert', 'Zieliński', 9, 0),
('ID100146', 'Angelika', 'Włodarczyk', 6, 1),
('ID100147', 'Szymon', 'Czerwiński', 1, 0),
('ID100148', 'Mariusz', 'Kołodziej', 8, 1),
('ID100149', 'Joanna', 'Markowska', 10, 0),
('ID100150', 'Aleksander', 'Piotrowski', 2, 1),
('ID100151', 'Karolina', 'Jankowska', 7, 0),
('ID100152', 'Mateusz', 'Szymański', 5, 1),
('ID100153', 'Natalia', 'Nowicka', 3, 0),
('ID100154', 'Dawid', 'Adamski', 9, 1),
('ID100155', 'Aleksandra', 'Borkowska', 4, 0),
('ID100156', 'Sebastian', 'Żak', 6, 1),
('ID100157', 'Monika', 'Kubiak', 1, 0),
('ID100158', 'Patryk', 'Czarnecki', 8, 1),
('ID100159', 'Joanna', 'Kamińska', 10, 0),
('ID100160', 'Rafał', 'Lis', 2, 1),
('ID100161', 'Magdalena', 'Kaczmarek', 5, 0),
('ID100162', 'Grzegorz', 'Szczepański', 7, 1),
('ID100163', 'Katarzyna', 'Górska', 4, 0),
('ID100164', 'Damian', 'Król', 3, 1),
('ID100165', 'Beata', 'Nowakowska', 9, 0),
('ID100166', 'Maciej', 'Włodarczyk', 6, 1),
('ID100167', 'Izabela', 'Czerwińska', 1, 0),
('ID100168', 'Robert', 'Kołodziej', 8, 1),
('ID100169', 'Szymon', 'Markowski', 10, 0),
('ID100170', 'Paulina', 'Piotrowska', 2, 1),
('ID100171', 'Tomasz', 'Wilczyński', 5, 0),
('ID100172', 'Karol', 'Rutkowski', 7, 1),
('ID100173', 'Angelika', 'Bednarek', 4, 0),
('ID100174', 'Aleksander', 'Gajewski', 3, 1),
('ID100175', 'Wiktoria', 'Sawicka', 9, 0),
('ID100176', 'Daniel', 'Wójcik', 6, 1),
('ID100177', 'Joanna', 'Stankiewicz', 1, 0),
('ID100178', 'Mariusz', 'Olszewski', 8, 1),
('ID100179', 'Martyna', 'Chmielewska', 10, 0),
('ID100180', 'Dariusz', 'Lisowski', 2, 1),
('ID100181', 'Barbara', 'Bielawska', 5, 0),
('ID100182', 'Patryk', 'Zieliński', 7, 1),
('ID100183', 'Dominika', 'Krupa', 4, 0),
('ID100184', 'Paweł', 'Sikora', 3, 1),
('ID100185', 'Karolina', 'Majewska', 9, 0),
('ID100186', 'Michał', 'Lewandowski', 6, 1),
('ID100187', 'Andrzej', 'Pietrzak', 1, 0),
('ID100188', 'Sylwia', 'Wilk', 8, 1),
('ID100189', 'Grzegorz', 'Szymański', 10, 0),
('ID100190', 'Natalia', 'Kubiak', 2, 1),
('ID100191', 'Tomasz', 'Adamski', 5, 0),
('ID100192', 'Magdalena', 'Czarnecka', 7, 1),
('ID100193', 'Rafał', 'Michalski', 4, 0),
('ID100194', 'Monika', 'Lis', 3, 1),
('ID100195', 'Robert', 'Zieliński', 9, 0),
('ID100196', 'Angelika', 'Włodarczyk', 6, 1),
('ID100197', 'Szymon', 'Czerwiński', 1, 0),
('ID100198', 'Mariusz', 'Kołodziej', 8, 1),
('ID100199', 'Joanna', 'Markowska', 10, 0),
('ID100200', 'Aleksander', 'Piotrowski', 2, 1);
""")

# Logi Aukcji
cursor.execute("""
INSERT INTO LogAukcji (sprzedawca, nabywca, data_transakcji, cena_sprzedazy, numer_pracownika) VALUES
('ID100001', 'ID100050', '2025-06-01', 150.75, 1),
('ID100002', 'ID100049', '2025-06-02', 220.00, 2),
('ID100003', NULL, '2025-06-03', 180.50, 3),
('ID100004', 'ID100048', '2025-06-04', 95.99, 4),
('ID100005', 'ID100047', '2025-06-05', 130.25, 5),
('ID100006', NULL, '2025-06-06', 175.00, 6),
('ID100007', 'ID100046', '2025-06-07', 200.00, 7),
('ID100008', 'ID100045', '2025-06-08', 89.99, 8),
('ID100009', NULL, '2025-06-09', 160.75, 9),
('ID100010', 'ID100044', '2025-06-10', 250.00, 10),
('ID100011', 'ID100043', '2025-06-11', 190.25, 11),
('ID100012', NULL, '2025-06-12', 175.50, 12),
('ID100013', 'ID100042', '2025-06-13', 210.75, 13),
('ID100014', NULL, '2025-06-14', 145.99, 14),
('ID100015', 'ID100041', '2025-06-15', 225.00, 15),
('ID100016', 'ID100040', '2025-06-16', 199.50, 16),
('ID100017', NULL, '2025-06-17', 185.25, 17),
('ID100018', 'ID100039', '2025-06-18', 110.75, 18),
('ID100019', 'ID100038', '2025-06-19', 95.00, 19),
('ID100020', 'ID100037', '2025-06-20', 140.99, 20),
('ID100021', NULL, '2025-06-21', 175.50, 21),
('ID100022', 'ID100036', '2025-06-22', 230.00, 22),
('ID100023', 'ID100035', '2025-06-23', 125.25, 23),
('ID100024', NULL, '2025-06-24', 155.00, 24),
('ID100025', 'ID100034', '2025-06-25', 190.75, 25),
('ID100026', 'ID100033', '2025-06-26', 205.00, 26),
('ID100027', 'ID100032', '2025-06-27', 98.99, 27),
('ID100028', NULL, '2025-06-28', 170.00, 28),
('ID100029', 'ID100031', '2025-06-29', 240.50, 29),
('ID100030', 'ID100030', '2025-06-30', 135.00, 30),
('ID100031', 'ID100029', '2025-07-01', 185.75, 31),
('ID100032', NULL, '2025-07-02', 160.50, 32),
('ID100033', 'ID100028', '2025-07-03', 220.99, 33),
('ID100034', 'ID100027', '2025-07-04', 115.25, 34),
('ID100035', 'ID100026', '2025-07-05', 130.00, 35),
('ID100036', NULL, '2025-07-06', 195.75, 36),
('ID100037', 'ID100025', '2025-07-07', 175.50, 37),
('ID100038', 'ID100024', '2025-07-08', 260.00, 38),
('ID100039', NULL, '2025-07-09', 145.99, 39),
('ID100040', 'ID100023', '2025-07-10', 215.00, 40),
('ID100041', 'ID100022', '2025-07-11', 180.25, 41),
('ID100042', NULL, '2025-07-12', 155.00, 42),
('ID100043', 'ID100021', '2025-07-13', 195.75, 43),
('ID100044', 'ID100020', '2025-07-14', 135.50, 44),
('ID100045', NULL, '2025-07-15', 250.00, 45),
('ID100046', 'ID100019', '2025-07-16', 160.75, 46),
('ID100047', 'ID100018', '2025-07-17', 140.99, 47),
('ID100048', 'ID100017', '2025-07-18', 185.50, 48),
('ID100049', NULL, '2025-07-19', 175.00, 49),
('ID100050', 'ID100016', '2025-07-20', 220.75, 50),
('ID100051', 'ID100100', '2025-07-21', 185.50, 1),
('ID100052', NULL, '2025-07-22', 210.75, 2),
('ID100053', 'ID100099', '2025-07-23', 150.99, 3),
('ID100054', 'ID100098', '2025-07-24', 230.00, 4),
('ID100055', NULL, '2025-07-25', 125.50, 5),
('ID100056', 'ID100097', '2025-07-26', 195.99, 6),
('ID100057', 'ID100096', '2025-07-27', 215.00, 7),
('ID100058', NULL, '2025-07-28', 140.75, 8),
('ID100059', 'ID100095', '2025-07-29', 175.50, 9),
('ID100060', 'ID100094', '2025-07-30', 260.99, 10),
('ID100061', NULL, '2025-07-31', 135.50, 11),
('ID100062', 'ID100093', '2025-08-01', 180.75, 12),
('ID100063', 'ID100092', '2025-08-02', 95.99, 13),
('ID100064', NULL, '2025-08-03', 145.00, 14),
('ID100065', 'ID100091', '2025-08-04', 175.99, 15),
('ID100066', 'ID100090', '2025-08-05', 250.50, 16),
('ID100067', NULL, '2025-08-06', 190.25, 17),
('ID100068', 'ID100089', '2025-08-07', 225.75, 18),
('ID100069', 'ID100088', '2025-08-08', 120.00, 19),
('ID100070', NULL, '2025-08-09', 170.50, 20),
('ID100071', 'ID100087', '2025-08-10', 200.99, 21),
('ID100072', 'ID100086', '2025-08-11', 145.25, 22),
('ID100073', NULL, '2025-08-12', 185.50, 23),
('ID100074', 'ID100085', '2025-08-13', 260.00, 24),
('ID100075', 'ID100084', '2025-08-14', 130.50, 25),
('ID100076', NULL, '2025-08-15', 155.75, 26),
('ID100077', 'ID100083', '2025-08-16', 195.99, 27),
('ID100078', 'ID100082', '2025-08-17', 215.50, 28),
('ID100079', NULL, '2025-08-18', 140.75, 29),
('ID100080', 'ID100081', '2025-08-19', 250.50, 30),
('ID100081', 'ID100080', '2025-08-20', 180.75, 31),
('ID100082', NULL, '2025-08-21', 155.99, 32),
('ID100083', 'ID100079', '2025-08-22', 220.00, 33),
('ID100084', 'ID100078', '2025-08-23', 115.50, 34),
('ID100085', NULL, '2025-08-24', 130.75, 35),
('ID100086', 'ID100077', '2025-08-25', 170.99, 36),
('ID100087', 'ID100076', '2025-08-26', 250.50, 37),
('ID100088', NULL, '2025-08-27', 190.75, 38),
('ID100089', 'ID100075', '2025-08-28', 145.99, 39),
('ID100090', 'ID100074', '2025-08-29', 225.00, 40),
('ID100091', NULL, '2025-08-30', 175.99, 41),
('ID100092', 'ID100073', '2025-08-31', 195.50, 42),
('ID100093', 'ID100072', '2025-09-01', 130.99, 43),
('ID100094', NULL, '2025-09-02', 250.50, 44),
('ID100095', 'ID100071', '2025-09-03', 160.75, 45),
('ID100096', 'ID100070', '2025-09-04', 135.99, 46),
('ID100097', NULL, '2025-09-05', 185.50, 47),
('ID100098', 'ID100069', '2025-09-06', 175.25, 48),
('ID100099', 'ID100068', '2025-09-07', 250.99, 49),
('ID100100', NULL, '2025-09-08', 210.50, 50),
('ID100101', 'ID100150', '2025-09-09', 195.50, 1),
('ID100102', NULL, '2025-09-10', 180.75, 2),
('ID100103', 'ID100149', '2025-09-11', 205.99, 3),
('ID100104', 'ID100148', '2025-09-12', 250.50, 4),
('ID100105', NULL, '2025-09-13', 125.99, 5),
('ID100106', 'ID100147', '2025-09-14', 220.00, 6),
('ID100107', 'ID100146', '2025-09-15', 165.50, 7),
('ID100108', NULL, '2025-09-16', 150.75, 8),
('ID100109', 'ID100145', '2025-09-17', 185.99, 9),
('ID100110', 'ID100144', '2025-09-18', 275.00, 10),
('ID100111', NULL, '2025-09-19', 135.75, 11),
('ID100112', 'ID100143', '2025-09-20', 195.99, 12),
('ID100113', 'ID100142', '2025-09-21', 100.50, 13),
('ID100114', NULL, '2025-09-22', 145.99, 14),
('ID100115', 'ID100141', '2025-09-23', 160.75, 15),
('ID100116', 'ID100140', '2025-09-24', 235.50, 16),
('ID100117', NULL, '2025-09-25', 200.75, 17),
('ID100118', 'ID100139', '2025-09-26', 275.99, 18),
('ID100119', 'ID100138', '2025-09-27', 190.00, 19),
('ID100120', NULL, '2025-09-28', 210.50, 20),
('ID100121', 'ID100137', '2025-09-29', 250.99, 21),
('ID100122', 'ID100136', '2025-09-30', 140.50, 22),
('ID100123', NULL, '2025-10-01', 185.75, 23),
('ID100124', 'ID100135', '2025-10-02', 265.50, 24),
('ID100125', 'ID100134', '2025-10-03', 125.99, 25),
('ID100126', NULL, '2025-10-04', 145.75, 26),
('ID100127', 'ID100133', '2025-10-05', 215.99, 27),
('ID100128', 'ID100132', '2025-10-06', 240.75, 28),
('ID100129', NULL, '2025-10-07', 155.50, 29),
('ID100130', 'ID100131', '2025-10-08', 280.00, 30),
('ID100131', 'ID100130', '2025-10-09', 195.75, 31),
('ID100132', NULL, '2025-10-10', 160.99, 32),
('ID100133', 'ID100129', '2025-10-11', 230.50, 33),
('ID100134', 'ID100128', '2025-10-12', 115.99, 34),
('ID100135', NULL, '2025-10-13', 125.75, 35),
('ID100136', 'ID100127', '2025-10-14', 200.99, 36),
('ID100137', 'ID100126', '2025-10-15', 275.50, 37),
('ID100138', NULL, '2025-10-16', 190.75, 38),
('ID100139', 'ID100125', '2025-10-17', 135.99, 39),
('ID100140', 'ID100124', '2025-10-18', 225.00, 40),
('ID100141', NULL, '2025-10-19', 170.99, 41),
('ID100142', 'ID100123', '2025-10-20', 195.50, 42),
('ID100143', 'ID100122', '2025-10-21', 140.99, 43),
('ID100144', NULL, '2025-10-22', 250.99, 44),
('ID100145', 'ID100121', '2025-10-23', 165.75, 45),
('ID100146', 'ID100120', '2025-10-24', 125.99, 46),
('ID100147', NULL, '2025-10-25', 185.50, 47),
('ID100148', 'ID100119', '2025-10-26', 200.75, 48),
('ID100149', 'ID100118', '2025-10-27', 250.99, 49),
('ID100150', NULL, '2025-10-28', 210.50, 50),
('ID100151', 'ID100200', '2025-10-29', 195.50, 1),
('ID100152', NULL, '2025-10-30', 180.75, 2),
('ID100153', 'ID100199', '2025-10-31', 205.99, 3),
('ID100154', 'ID100198', '2025-11-01', 250.50, 4),
('ID100155', NULL, '2025-11-02', 125.99, 5),
('ID100156', 'ID100197', '2025-11-03', 220.00, 6),
('ID100157', 'ID100196', '2025-11-04', 165.50, 7),
('ID100158', NULL, '2025-11-05', 150.75, 8),
('ID100159', 'ID100195', '2025-11-06', 185.99, 9),
('ID100160', 'ID100194', '2025-11-07', 275.00, 10),
('ID100161', NULL, '2025-11-08', 135.75, 11),
('ID100162', 'ID100193', '2025-11-09', 195.99, 12),
('ID100163', 'ID100192', '2025-11-10', 100.50, 13),
('ID100164', NULL, '2025-11-11', 145.99, 14),
('ID100165', 'ID100191', '2025-11-12', 160.75, 15),
('ID100166', 'ID100190', '2025-11-13', 235.50, 16),
('ID100167', NULL, '2025-11-14', 200.75, 17),
('ID100168', 'ID100189', '2025-11-15', 275.99, 18),
('ID100169', 'ID100188', '2025-11-16', 190.00, 19),
('ID100170', NULL, '2025-11-17', 210.50, 20),
('ID100171', 'ID100187', '2025-11-18', 250.99, 21),
('ID100172', 'ID100186', '2025-11-19', 140.50, 22),
('ID100173', NULL, '2025-11-20', 185.75, 23),
('ID100174', 'ID100185', '2025-11-21', 265.50, 24),
('ID100175', 'ID100184', '2025-11-22', 125.99, 25),
('ID100176', NULL, '2025-11-23', 145.75, 26),
('ID100177', 'ID100183', '2025-11-24', 215.99, 27),
('ID100178', 'ID100182', '2025-11-25', 240.75, 28),
('ID100179', NULL, '2025-11-26', 155.50, 29),
('ID100180', 'ID100181', '2025-11-27', 280.00, 30),
('ID100181', 'ID100180', '2025-11-28', 195.75, 31),
('ID100182', NULL, '2025-11-29', 160.99, 32),
('ID100183', 'ID100179', '2025-11-30', 230.50, 33),
('ID100184', 'ID100178', '2025-12-01', 115.99, 34),
('ID100185', NULL, '2025-12-02', 125.75, 35),
('ID100186', 'ID100177', '2025-12-03', 200.99, 36),
('ID100187', 'ID100176', '2025-12-04', 275.50, 37),
('ID100188', NULL, '2025-12-05', 190.75, 38),
('ID100189', 'ID100175', '2025-12-06', 135.99, 39),
('ID100190', 'ID100174', '2025-12-07', 225.00, 40),
('ID100191', NULL, '2025-12-08', 170.99, 41),
('ID100192', 'ID100173', '2025-12-09', 195.50, 42),
('ID100193', 'ID100172', '2025-12-10', 140.99, 43),
('ID100194', NULL, '2025-12-11', 250.99, 44),
('ID100195', 'ID100171', '2025-12-12', 165.75, 45),
('ID100196', 'ID100170', '2025-12-13', 125.99, 46),
('ID100197', NULL, '2025-12-14', 185.50, 47),
('ID100198', 'ID100169', '2025-12-15', 200.75, 48),
('ID100199', 'ID100168', '2025-12-16', 250.99, 49),
('ID100200', NULL, '2025-12-17', 210.50, 50);
""")




#zacznij liczyć czas
start_time = time.time()

#przykładowe zapytanie do bazy danych w celu zmierzenia czasu wyonania i pobrania wyniku
cursor.execute("""
SELECT k.imie, k.nazwisko, COUNT(*) AS cnt
FROM LogAukcji l
JOIN Klient k ON l.sprzedawca = k.numer_dowodu
WHERE l.data_transakcji BETWEEN '2025-06-01' AND '2025-12-31'
GROUP BY k.imie, k.nazwisko
ORDER BY cnt DESC
""")
#pobranie wyniku
results = cursor.fetchall()
#skończ liczyć czas
end_time = time.time()
#policz różnice czasów od początku do końca
elapsed = end_time - start_time
#wypisz czas wykonaia
print(f"Czas wykonania zapytania: {elapsed:.4f} sekundy")


start_time = time.time()

#przykładowe zapytanie do bazy danych z podstawową optymalizacją zapytaniń poprzez dodanie Limit 10
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
elapsed = end_time - start_time

print(f"Czas wykonania zapytania: {elapsed:.4f} sekundy")


#doadanie ineksów do tabeli w celu zwiększenia prętkości wykonania zapytania
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
elapsed = end_time - start_time

print(f"Czas wykonania zapytania: {elapsed:.4f} sekundy")


cursor.execute("""
Drop Table LogAukcji
""")

cursor.execute("""
Drop Table Klient
""")

cursor.execute("""
Drop Table Pracownik
""")

connection.commit()
cursor.close()
connection.close()
