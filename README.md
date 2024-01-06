# PetoiBittle-RobotDog
Programmer le robot chien Petoi Bittle

# Objectif du projet
Petoi est une entreprise proposant des animaux robotisés, tels qu'un chien, qu'il est possible de programmer entièrement. Dans cette série de vidéos, nous allons découvrir son fonctionnement en le programmant via l’IDE Arduino, et nous tenterons de le rendre encore plus complexe en lui ajoutant des modules supplémentaires comme des capteurs, du Wi-Fi, ou une Raspberry Pi.

# I- Fonctionnement du robot

Vidéo du projet : <https://www.tiktok.com/@__hakii__/video/7297934570622651681>

### a) Carte Nyboard

La carte utilisée sur le robot est très similaire à une carte Arduino classique car elle est basée sur le même microcontrôleur, l'ATmega328P.

![Screenshot 2023-11-03 171207](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/977fce0c-7340-46a2-a4d7-2b560d18deb5)

- Elle dispose de plusieurs pins digitaux et analogiques où l’on peut connecter différents capteurs.
- Des pins ground et 5V.
- Des pins SDA et SCL pour la communication via le protocole I2C.
- Une interface série Rx Tx, utilisable par exemple pour envoyer des informations de l'ordinateur vers la carte.
- Un capteur infrarouge.
- Un accéléromètre et un gyroscope pour détecter les mouvements du robot dans l'espace.

![Screenshot 2023-11-03 171223](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/9460054a-9a98-40be-9d6a-334043104bb2)

- Un buzzer pour produire des sons.
- Des pins dédiés au contrôle des servomoteurs.
- La batterie.
  
Le commutateur I2C permet de changer le maître des appareils I2C (gyroscope/accéléromètre, servomoteur, EEPROM externe). Sur « Arduino » par défaut, le NyBoard utilise l'ATmega328P intégré comme puce principale ; sur « RPi », il utilise des puces externes connectées via les ports I2C (SDA, SCL) comme puce principale.


### b) Le robot et ses modules
Comme vu sur la carte, il y a de nombreux servomoteurs qu’il est possible de contrôler individuellement. Il y a également un emplacement pour la batterie.

![Picture2](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/9611ee35-094b-482e-a38e-018274ea092c)

On peut lui connecter n’importe quel type de capteur disponible sur le site, mais nous utiliserons principalement des capteurs classiques d'Arduino, ainsi que le connecter en Bluetooth ou par Wi-Fi.

![Screenshot 2023-11-03 171755](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/56435239-1001-43df-a17d-d44eccc26646)


On verra que certains modules ont déja des fonctions qui existent pour les faire fonctionner  comme pour la photoresistance, le capteur de distance ultrasonic, la caméra….

# II - Mouvements du robot par sérial

Vidéo du projet : <https://www.tiktok.com/@__hakii__/video/7307951749493574944>

Nous allons maintenant voir comment contrôler le robot à travers Arduino, d'abord par communication série.

### Uploader un programme Arduino

Pour commencer à interagir avec notre robot, nous devons télécharger OpenCat, un dossier très complet avec de nombreux programmes pour faire fonctionner le robot. On télécharge le fichier ZIP Opencat :
<https://github.com/PetoiCamp/OpenCat>

Le fichier principal est Opencat.ino, où tout est configuré et où toutes les fonctions créées pour le robot sont appelées. De nombreux fichiers header situés dans le dossier src sont inclus dans Opencat.ino. Ils peuvent être modifiés avec un éditeur comme VScode pour ajuster certaines fonctionnalités.

Dans le fichier OpenCat.ino :

- Pour calibrer le robot, mettre en commentaire `#define MAIN_SKETCH`.
- Pour l'utiliser avec l'application Petoi ou envoyer des commandes sérielles, décommenter `#define MAIN_SKETCH`.
- Pour implémenter du code personnalisé, décommenter `#define OtherModules`, permettant à la fonction `void otherModule()` d'être appelée dans la boucle.

Dans les trois cas, nous devons également indiquer la version de la carte et le type de robot :  define NyBoard_V1_1 et define BITTLE 

On connecte le robot au PC via un convertisseur micro USB-C et on sélectionne la carte Arduino UNO pour le téléversement.

![branchement](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/f89c1ad4-b6db-4d01-8798-02190fda9836)

![Untitled](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/8caab7fb-a8a9-4cc3-a079-30afddb3f45d)

Interface de téléchargement NyBoard : utilisée pour se connecter à NyBoard, télécharger le firmware sur le robot et communiquer via le port série.

Interface de débogage du module de communication : pour connecter le module Bluetooth ou Wi-Fi, mettre à jour le programme du module et déboguer les paramètres.

Remarque : 

- Le Nyboard V1 utilisé par le robot utilise le contrôleur Atmel ATMEGA328P, qui ne prend en charge qu'un seul port série.
- Le débit en bauds série par défaut est **de 115 200 bps**.

### Envoyer des instructions sérial

Une fois le robot connecté au PC, il est possible de lui envoyer directement des instructions depuis le moniteur série.
  
Tous les token commencent par un seul caractère codé en ASCII pour spécifier le type d’action à réaliser. Ils sont sensibles à la casse et sont généralement en minuscules.

![Askii](https://github.com/Haki-i/PetoiBittle-RobotDog/assets/137703849/276b85b7-48f8-460a-a70c-5c92846339e9)


Les caractères qui vont nous interessés sont 'k', 'm' et 'i'.

- 'k' permet d’exécuter des actions ou compétences prédéfinies. Par exemple, 'ksit' pour asseoir le robot, 'kpee' pour le faire uriner.
- 'm' contrôle un ou plusieurs servomoteurs en indiquant leur numéro et l’angle souhaité. Exemple : m 0 -70 0 70 8 -30 : bouge le servo 0 à -70 degrés puis le servo 0 à 70 et enfin le servo 8 à -30 degrés
- 'i' fait bouger plusieurs servos simultanément de la même manière. Exemple : i 0 -45 8 -30 12 -60 : bouge simultanément servo 0, 8 et 12

Ces instructions fonctionnent également via le module Bluetooth. On peut télécharger une application pour se connecter au module et pour pouvoir lui envoyer des informations. On peut écrire les mêmes types de commandes comme m 0 90 et le module va les transmettre par serial à la carte.

  
