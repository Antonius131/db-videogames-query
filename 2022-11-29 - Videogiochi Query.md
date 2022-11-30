## Repo
db-videogames-query

### Schema E-R
![[Pasted image 20221129162923.png]]

### Query
Dopo aver importato il dump del db contenente i dati ([[Videogiochi DB]]), eseguire le seguenti query:
#### SELECT
1. Selezionare tutte le software house americane (3) 
```sql
SELECT *
FROM software_houses
WHERE country = "united states"
```

2. Selezionare tutti i giocatori della cittÃ  di 'Rogahnland' (2)
```sql
SELECT *
FROM players
WHERE city = "rogahnland"
```

3. Selezionare tutti i giocatori il cui nome finisce per "a" (220)
```sql
SELECT *
FROM players
WHERE name like "%a"
```

4. Selezionare tutte le recensioni scritte dal giocatore con ID = 800 (11)
```sql
SELECT *
FROM reviews
WHERE player_id = 800
```

5. Contare quanti tornei ci sono stati nell'anno 2015 (9)
```sql
SELECT count(*)
FROM tournaments
WHERE year = 2015
```

6. Selezionare tutti i premi che contengono nella descrizione la parola 'facere' (2)
```sql
SELECT *
FROM awards
WHERE description LIKE "%facere%"
```

7. Selezionare tutti i videogame che hanno la categoria 2 (FPS) o 6 (RPG), mostrandoli una sola volta (del videogioco vogliamo solo l'ID) (287)
```sql
SELECT DISTINCT videogame_id
FROM category_videogame
WHERE category_id = 2 OR category_id = 6
```

8. Selezionare tutte le recensioni con voto compreso tra 2 e 4 (2947)
```sql
SELECT *
FROM reviews
WHERE rating >= 2 
	AND rating <= 4
```

9. Selezionare tutti i dati dei videogiochi rilasciati nell'anno 2020 (46)
```sql
SELECT *
FROM videogames
WHERE YEAR(release_date) > 2019
	AND YEAR(release_date) < 2021
```

10. Selezionare gli id dei videogame che hanno ricevuto almeno una recensione da stelle, mostrandoli una sola volta (443)
```sql
SELECT DISTINCT videogame_id
FROM reviews
WHERE rating = 5
```

##### **BONUS**
11. Selezionare il numero e la media delle recensioni per il videogioco con ID = 412 (review number = 12, avg_rating = 3.16 circa)
```sql
SELECT *
FROM reviews
WHERE videogame_id = 412

SELECT AVG(rating)
FROM reviews
WHERE videogame_id = 412
```

12. Selezionare il numero di videogame che la software house con ID = 1 ha rilasciato nel 2018 (13)
```sql
SELECT *
FROM videogames
WHERE software_house_id = 1
	AND YEAR(release_date) = 2018
```

---

#### GROUP BY
1. Contare quante software house ci sono per ogni paese (3)
```sql
SELECT country, count(*)
FROM software_houses
GROUP BY country 
```

2. Contare quante recensioni ha ricevuto ogni videogioco (del videogioco vogliamo solo l'ID) (500)
```sql
SELECT videogame_id, count(*)
FROM reviews
GROUP BY videogame_id
```

3. Contare quanti videogiochi hanno ciascuna classificazione PEGI (della classificazione PEGI vogliamo solo l'ID) (13)
```sql
SELECT pegi_label_id, count(*)
FROM pegi_label_videogame
GROUP BY pegi_label_id
```

4. Mostrare il numero di videogiochi rilasciati ogni anno (11)
```sql
SELECT release_date, count(*)
FROM videogames
GROUP BY YEAR(release_date)
```

5. Contare quanti videogiochi sono disponbiili per ciascun device (del device vogliamo solo l'ID) (7)
```sql
SELECT device_id, count(*)
FROM device_videogame
GROUP BY device_id
```

6. Ordinare i videogame in base alla media delle recensioni (del videogioco vogliamo solo l'ID) (500)
```sql
SELECT videogame_id, AVG(rating)
FROM reviews
GROUP BY videogame_id
ORDER BY AVG(rating) ASC 
```


---

#### JOIN
1. Selezionare i dati di tutti giocatori che hanno scritto almeno una recensione, mostrandoli una sola volta (996)
```sql
SELECT DISTINCT players.*
FROM players
JOIN reviews
	ON reviews.player_id = players.id
```

2. Sezionare tutti i videogame dei tornei tenuti nel 2016, mostrandoli una sola volta (226)
```sql
SELECT DISTINCT videogames.name
FROM videogames
JOIN tournament_videogame
    ON tournament_videogame.videogame_id = videogames.id
JOIN tournaments
	ON tournaments.id = tournament_videogame.tournament_id
WHERE tournaments.year = 2016 
```

3. Mostrare le categorie di ogni videogioco (1718)
```sql
SELECT videogames.name, categories.name
FROM categories
JOIN category_videogame
	ON category_videogame.category_id = categories.id
JOIN videogames
	ON videogames.id = category_videogame.videogame_id
```

4. Selezionare i dati di tutte le software house che hanno rilasciato almeno un gioco dopo il 2020, mostrandoli una sola volta (6)
```sql
SELECT DISTINCT software_houses.*
FROM software_houses
JOIN videogames
	ON videogames.software_house_id = software_houses.id
WHERE YEAR(release_date) > 2019 
```

5. Selezionare i premi ricevuti da ogni software house per i videogiochi che ha prodotto (55)
```sql
SELECT awards.name, software_houses.name
FROM awards
JOIN award_videogame
	ON award_videogame.award_id = awards.id
JOIN videogames
	ON videogames.id = award_videogame.videogame_id
JOIN software_houses
	ON software_houses.id = videogames.software_house_id
```

6. Selezionare categorie e classificazioni PEGI dei videogiochi che hanno ricevuto recensioni da 4 e 5 stelle, mostrandole una sola volta (3363)
```sql
SELECT DISTINCT categories.name, pegi_labels.name
FROM categories
JOIN category_videogame
    ON category_videogame.category_id = categories.id
JOIN videogames
    ON videogames.id = category_videogame.videogame_id
JOIN reviews
    ON reviews.videogame_id = videogames.id
JOIN pegi_label_videogame
    ON pegi_label_videogame.videogame_id = videogames.id
JOIN pegi_labels
	ON pegi_labels.id = pegi_label_videogame.pegi_label_id
WHERE reviews.rating = 4 OR reviews.rating = 5
```

7. Selezionare quali giochi erano presenti nei tornei nei quali hanno partecipato i giocatori il cui nome inizia per 'S' (474)
```sql
SELECT DISTINCT videogames.id
FROM videogames
JOIN tournament_videogame
    ON tournament_videogame.videogame_id = videogames.id
JOIN tournaments
    ON tournaments.id = tournament_videogame.tournament_id
JOIN player_tournament
    ON player_tournament.tournament_id = tournaments.id
JOIN players
    ON players.id = player_tournament.player_id
WHERE players.name LIKE "S%" 
```

8. Selezionare le cittÃ  in cui Ã¨ stato giocato il gioco dell'anno del 2018 (36)
```sql
SELECT tournaments.city
FROM tournaments
JOIN tournament_videogame
    ON tournament_videogame.tournament_id = tournaments.id
JOIN videogames
    ON videogames.id = tournament_videogame.videogame_id
JOIN award_videogame
	ON award_videogame.videogame_id = videogames.id
JOIN awards
	ON awards.id = award_videogame.award_id
WHERE awards.name LIKE "gioco dell'anno"
	AND award_videogame.year = 2018
```

9. Selezionare i giocatori che hanno giocato al gioco piÃ¹ atteso del 2018 in un torneo del 2019 (3306)
```sql
SELECT players.*
FROM players
JOIN player_tournament
    ON player_tournament.player_id = players.id
JOIN tournaments
    ON tournaments.id = player_tournament.tournament_id
JOIN tournament_videogame
    ON tournament_videogame.tournament_id = tournaments.id
JOIN videogames
    ON videogames.id = tournament_videogame.videogame_id
JOIN award_videogame
    ON award_videogame.videogame_id = videogames.id
JOIN awards
    ON awards.id = award_videogame.award_id
WHERE awards.name LIKE "gioco più atteso"
    AND award_videogame.year = 2018
	AND tournaments.year = 2019 
```


##### **BONUS**
10. Selezionare i dati della prima software house che ha rilasciato un gioco, assieme ai dati del gioco stesso (software house id : 5)
```sql
SELECT videogames.*, software_houses.*
FROM software_houses
JOIN videogames
	ON videogames.software_house_id = software_houses.id
WHERE videogames.release_date = 
	(SELECT MIN(videogames.release_date) FROM videogames)
```

11. Selezionare i dati del videogame (id, name, release_date, totale recensioni) con piÃ¹ recensioni (videogame id : potrebbe uscire 449 o 398, sono entrambi a 20)


12. Selezionare la software house che ha vinto piÃ¹ premi tra il 2015 e il 2016 (software house id : potrebbe uscire 3 o 1, sono entrambi a 3)
13. Selezionare le categorie dei videogame i quali hanno una media recensioni inferiore a 1.5 (10)