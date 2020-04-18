# Creaza tabelele

```sql
create table Amici
(
    idPersoana1 int null,
    idPersoana2 int null
);

create table Persoane
(
    idPersoana int auto_increment
        primary key,
    Numele     varchar(255) null,
    Virsta     tinyint      null
);

create table Rude
(
    idPersoana1 int null,
    idPersoana2 int null
);
```
## Populeaza tabelele cu date

Table Amici
```sql
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (1, 2);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (1, 3);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (2, 4);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (2, 6);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (3, 9);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (4, 9);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (7, 5);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (5, 8);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (6, 10);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (13, 6);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (7, 6);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (8, 7);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (9, 11);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (12, 9);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (10, 15);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (12, 11);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (12, 15);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (13, 16);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (15, 13);
INSERT INTO mysql_playground.Amici (idPersoana1, idPersoana2) VALUES (16, 14);
```

Table Persoane
```sql
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (1, 'Elvi', 19);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (2, 'Farouk', 19);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (3, 'Sam', 19);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (4, 'Tiany', 19);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (5, 'Nadia', 14);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (6, 'Chris', 12);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (7, 'Kris', 10);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (8, 'Bethany', 16);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (9, 'Louis', 17);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (10, 'Austin', 22);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (11, 'Gabriel', 21);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (12, 'Jessica', 20);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (13, 'John', 16);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (14, 'Alfred', 19);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (15, 'Samantha', 17);
INSERT INTO mysql_playground.Persoane (idPersoana, Numele, Virsta) VALUES (16, 'Craig', 17);
```

Table Rude
```sql
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (4, 6);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (2, 4);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (9, 7);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (7, 8);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (11, 9);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (13, 10);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (14, 5);
INSERT INTO mysql_playground.Rude (idPersoana1, idPersoana2) VALUES (12, 13);
```

Ex1.
```sql
DROP PROCEDURE IF EXISTS amicii_crescator; 
create
    procedure amicii_crescator()
begin
    UPDATE Amici as t1
        LEFT JOIN Persoane as t2 on t1.idPersoana1 = t2.idPersoana
        LEFT JOIN Persoane as t3 on t1.idPersoana2 = t3.idPersoana
        LEFT JOIN Amici as t4 on t1.idPersoana1 = t4.idPersoana1
    SET t1.idPersoana1 = t4.idPersoana2,
        t1.idPersoana2 = t4.idPersoana1
    WHERE t2.Virsta > t3.Virsta;
end;
```
Exemplu de utilizare:
```sql
CALL amicii_crescator();
```

Ex2

```sql
drop function if exists CEL_MAI_PUTINE_RUDE;
create
    function CEL_MAI_PUTINE_RUDE() returns text deterministic
BEGIN
    DROP temporary table if exists temp_table;
    CREATE TEMPORARY TABLE temp_table (Numele varchar(255), TotalRude int);

    INSERT INTO temp_table (SELECT Numele, TotalRude FROM (SELECT idPersoana1 as idPersoana, COUNT(*) as TotalRude
                                                    FROM (SELECT idPersoana1 FROM Rude UNION ALL SELECT idPersoana2 FROM Rude) as t1
                                                GROUP BY idPersoana) as t1 LEFT JOIN Persoane as t2 on t1.idPersoana=t2.idPersoana);

    SELECT MIN(TotalRude) into @min FROM temp_table;

    SELECT GROUP_CONCAT(Numele) into @result FROM temp_table where TotalRude = @min;

    DROP table temp_table;
    return @result;
END;
```
Exemplu de utilizare:
```sql
SELECT CEL_MAI_PUTINE_RUDE();
```
![Screen Shot 2020-04-18 at 11.25.49.png](https://photos.google.com/search/_tra_/photo/AF1QipPuLR-AJEA48rXEozQL0pzF4iEpIVRS7HFCcwLr)

Ex.3
```sql
drop function if exists COMPARA_VIRSTA;
create
    function COMPARA_VIRSTA(persoana1 varchar(255), persoana2 varchar(255))
    returns text deterministic
BEGIN
    SELECT Virsta into @virsta1 FROM Persoane where Numele = persoana1;
    SELECT Virsta into @virsta2 FROM Persoane where Numele = persoana2;
    IF @virsta1 > @virsta2 THEN
        return CONCAT(persoana1, ' este mai mare decit ', persoana2);
    elseif @virsta1 < @virsta2 then
        return CONCAT(persoana2, ' este mai mare decit ', persoana1);
    else
        return CONCAT(persoana1,' si ', persoana2, ' au aceeasi virsta');
    END IF;
END;
```
Exemplu de utilizare:
```sql
SELECT COMPARA_VIRSTA('Elvi', 'Sam');
```
![Screen Shot 2020-04-18 at 11.21.06.png](https://photos.google.com/search/_tra_/photo/AF1QipMvea-bdpID1wcpi4EwyfRA-JxWO_Cpcc3gXAEE)
