Thérémin Lumineux 🔆🎵
Projet Arduino n°6 de ma série de projets électronique, réalisée en parallèle de ma L2 EEEA.
Description
Un thérémin est un instrument de musique qu'on joue sans le toucher : le son change selon la position des mains par rapport à l'instrument. Le vrai thérémin fonctionne avec des antennes qui détectent des variations de capacité électrique.
Pour ce projet, j'ai remplacé la détection capacitive par un phototransistor qui mesure l'intensité lumineuse. En approchant ou en éloignant la main du capteur, on fait varier la quantité de lumière qui l'atteint, et ça change la fréquence du son émis par un petit buzzer piézoélectrique.
Je ne contrôle ici que la fréquence du son, pas le volume (contrairement à un vrai thérémin, qui contrôle les deux).
Matériel utilisé
	•	1x Arduino Uno
	•	1x Phototransistor
	•	1x Résistance 10 kΩ
	•	1x Buzzer piézoélectrique (piezo)
	•	1x Breadboard
	•	Câbles de connexion
Schéma de montage
	•	Phototransistor : la patte longue (anode) va au 5V, l'autre patte va à la broche analogique A0 et à la masse (GND) via la résistance de 10 kΩ (pont diviseur de tension).
	•	Piezo : une patte sur la broche numérique 8, l'autre à la masse (GND).

  Comment ça marche
Le code se déroule en deux étapes.
1. Calibration (les 5 premières secondes)
Au démarrage, la LED intégrée (broche 13) s'allume pour indiquer que la calibration est en cours. Pendant 5 secondes (avec millis()), le programme lit en boucle la valeur du capteur et garde en mémoire :
	•	la valeur max atteinte (sensorHigh)
	•	la valeur min atteinte (sensorLow)
Il suffit de bouger la main au-dessus du phototransistor pendant ces 5 secondes pour que le capteur soit calibré à la luminosité de la pièce et à l'amplitude de mouvement que je veux utiliser.
Une fois les 5 secondes passées, la LED s'éteint.
2. Boucle principale
Dans loop(), je lis en continu la valeur du capteur et je la convertis en fréquence avec map(), qui reprojette la plage réelle mesurée (sensorLow → sensorHigh) vers une plage de fréquences audibles (50 Hz → 4000 Hz). La fréquence obtenue est ensuite jouée sur le piezo avec tone().

Ce que j'ai appris avec ce projet
	•	tone() : je pensais au début que ça marchait comme analogWrite(), mais en fait c'est différent. analogWrite() fait varier le rapport cyclique à fréquence fixe (PWM), alors que tone() garde toujours un rapport cyclique de 50 % et fait varier la fréquence des impulsions. C'est ce changement de fréquence qui permet de jouer des notes différentes sur un haut-parleur ou un piezo.
	•	Pourquoi calibrer un capteur analogique : avec le montage en pont diviseur, le phototransistor ne couvre jamais toute la plage 0-1023 (la résistance fixe limite le bas, la luminosité ambiante limite le haut). Calibrer au début du programme permet d'exploiter la vraie plage disponible, peu importe la pièce dans laquelle je suis.
	•	map() pour reprojeter une plage de valeurs vers une autre.
	•	while() + millis() pour faire tourner une boucle bloquante pendant une durée donnée, sans passer par delay().
