```sql
SELECT 
	a.actor_id
	,a.first_name
	,a.last_name
	,group_concat(DISTINCT (c.name || ': ' || ( 
	    SELECT group_concat(f.title) AS group_concat
	    FROM film f
	      JOIN film_category fc_1 ON f.film_id = fc_1.film_id
	      JOIN film_actor fa_1 ON f.film_id = fa_1.film_id
	    WHERE fc_1.category_id = c.category_id AND fa_1.actor_id = a.actor_id
	    GROUP BY fa_1.actor_id))
   ) AS film_info
FROM actor a
  LEFT JOIN film_actor fa ON a.actor_id = fa.actor_id
  LEFT JOIN film_category fc ON fa.film_id = fc.film_id
  LEFT JOIN category c ON fc.category_id = c.category_id
GROUP BY a.actor_id, a.first_name, a.last_name
```
```text
---top 5 rows output---
1	"Penelope"	"Guiness"	"Animation: Anaconda Confessions, Children: Language Cowboy, Classics: Color Philadelphia, Westward Seabiscuit, Comedy: Vertigo Northwest, Documentary: Academy Dinosaur, Family: King Evolution, Splash Gump, Foreign: Mulholland Beast, Games: Bulworth Commandments, Human Graffiti, Horror: Elephant Trojan, Lady Stage, Rules Human, Music: Wizard Coldblooded, New: Angels Life, Oklahoma Jumanji, Sci-Fi: Cheaper Clyde, Sports: Gleaming Jawbreaker"
2	"Nick"	   	"Wahlberg"	"Action: Bull Shawshank, Animation: Fight Jawbreaker, Children: Jersey Sassy, Classics: Dracula Crystal, Gilbert Pelican, Comedy: Mallrats United, Rushmore Mermaid, Documentary: Adaptation Holes, Drama: Wardrobe Phantom, Family: Apache Divine, Chisum Behavior, Indian Love, Maguire Apache, Foreign: Baby Hall, Happiness United, Games: Roof Champion, Music: Lucky Flying, New: Destiny Saturday, Flash Wars, Jekyll Frogmen, Mask Peach, Sci-Fi: Chainsaw Uptown, Goodfellas Salute, Travel: Liaisons Sweet, Smile Earring"
3	"Ed"	    	"Chase" 	"Action: Caddyshack Jedi, Forrest Sons, Classics: Frost Head, Jeepers Wedding, Documentary: Army Flintstones, French Holiday, Halloween Nuts, Hunter Alter, Wedding Apollo, Young Language, Drama: Luck Opus, Necklace Outbreak, Spice Sorority, Foreign: Cowboy Doom, Whale Bikini, Music: Alone Trip, New: Eve Resurrection, Platoon Instinct, Sci-Fi: Weekend Personal, Sports: Artist Coldblooded, Image Princess, Travel: Boondock Ballroom"
4	"Jennifer"  	"Davis"		"Action: Barefoot Manchurian, Animation: Anaconda Confessions, Ghostbusters Elf, Comedy: Submarine Bed, Documentary: Bed Highball, National Story, Raiders Antitrust, Drama: Blade Polish, Greedy Roots, Family: Splash Gump, Horror: Treasure Command, Music: Hanover Galaxy, Reds Pocus, New: Angels Life, Jumanji Blade, Oklahoma Jumanji, Sci-Fi: Random Go, Silverado Goldfinger, Unforgiven Zoolander, Sports: Instinct Airport, Poseidon Forever, Travel: Boondock Ballroom"
5	"Johnny"    	"Lollobrigida"	"Action: Amadeus Holy, Grail Frankenstein, Rings Heartbreakers, Animation: Sunrise League, Children: Hall Cassidy, Comedy: Daddy Pittsburgh, Documentary: Bonnie Holocaust, Metal Armageddon, Pacific Amistad, Pocus Pulp, Drama: Chitty Lock, Coneheads Smoochy, Games: Fire Wolves, Horror: Commandments Express, Love Suicides, Patton Interview, Music: Banger Pinocchio, Heavenly Gun, New: Frontier Cabin, Ridgemont Submarine, Sci-Fi: Daisy Menagerie, Goodfellas Salute, Soldiers Evolution, Sports: Groove Fiction, Kramer Chocolate, Star Operation, Travel: Enough Raging, Escape Metropolis, Smile Earring"
