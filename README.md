# Séance 5 de Design : modélisation sur KiCad 7.0 du circuit imprimé d'un keyfinder de chez Action

## Igor Robin - ZZ2 - F1

### Contenu du dépôt
- ```diagramme_originel.png``` et ```diagramme_modifie.pdf``` - Diagramme donné à titre d'exemple en cours et diagramme complété avec d'autres fonctionnalités
- Les fichiers KiCad 7.0 correspondant au shématique, au circuit imprimé, ainsi qu'au projet lui-même
- La sheet du Development Kit (PCA10040) de Nordic Semiconductors (PCA10040_Schematic_And_PCB.pdf)
- Une photo du schématique
- Un photo du circuit imprimé
- Une vue 3D
- Ce ```README.md```

###
*Utilisation : télécharger le dépôt complet, extraire l'archive .zip, mettre tous les fichiers qu'elle contient dans un dossier choisi, mettre au même niveau que le ficher projet (.kicad_pro) le dépôt complet qui se trouve à cette adresse :*
https://github.com/NordicPlayground/nrf5-eagle-reference-design
*Ouvrir le projet dans KiCad et faire en sorte que le package nRF52832 soit prit en compte*

## Introduction
Le but de cette séance de Design était de modéliser sur KiCad 7.0 le circuit imprimé contenu dans le keyfinder de chez Action. Au préalable, il fallait élaborer un diagramme inspiré de celui montré en cours présentant les fonctionnalités du circuit imprimé et notamment quels sont les composants présents et par quels pins ils sont reliés au SoC. Par la suite, il fallait associer les composants avec leurs empreintes et trouver des composants répondant aux besoins. Enfin, il fallait prendre soin de router correctement les composants sur KiCad afin d'assurer leur fonctionnement.

## Mise en œuvre
Afin de trouver les composants requis, la méthode utilisée a été celle-ci : chercher une empreinte sur KiCad pour chaque composant (un epreinte qui mentionne une référence complète et un fabricant), aller voir grâce au moteur de recherche de composants Octopart si le composant répondait aux besoins et s'il était disponible sur des sites courants (DigiKey, Farnell, Mouser), et si c'était le cas, associer l'empreinte au composant.

De plus, les critères de sélection étaient les suivants :
- Le composant est-il disponible sur les principaux sites web ?
- Le composant est-il des dimensions acceptables ? (taille raisonnable, au maximum 12mm sur sa plus grande dimensions, sauf pour la pile)
- A t-il les bonnes caractéristiques techniques ? (*Travail réalisé par un débutant et donc susceptible de contenir des erreurs, des confusions de termes et des points oubliés, notamment à ce niveau là*)

## LED
La LED choisie est une IN-PI554FCH de chez Inolux. Comme nous pouvons le voir, sur le schématique, elle est reliée à la masse d'un côté et a une resistance de l'autre, la résistance étant reliée au pin P0.12 du SoC. Le SoC alimente donc la LED directement. Au niveau des caractéristiques techniques, la datasheet de la LED indique qu'elle fonctionne entre 1.8V et 3.3V, or le SoC fonctionne également à ce niveau de tension, et donc cette LED a été retenue.

## Boutons
Les boutons choisis sont des EVQPUJ02K de chez Panasonic. Le raisonnement a été le suivant : le bouton 1 (voir schématique), lorsqu'il est pressé, doit envoyer un signal au SoC, et le SoC doit décider ensuite d'allumer la LED et de déclencher le buzzer. Le bouton 1 est donc branché d'un côté à l'alimentation provenant de la pile, et de l'autre côté à un pin du SoC (ici le pin P0.15).

## Reset et JTAG
Grâce notamment à l'aide d'un ami lors d'un évènement IsiLabs (club d'informatique de l'ISIMA), le système suivant a été mit en place : un connecteur de programmation de type JTAG a été utilisé. Sur celui-ci, on relie le VCC à l'alimentation provenant de la pile, le GND à la masse, on laisse le SWO flottant, on relie le SWDIO au SWDIO du SoC (pin SWDIO @26), le SWCLK au pin SWCLK@25 du SoC et le RESET au pin P0.21/RESET du SoC. Le fil reliant les deux RESET est aussi connecté à l'alimentation en passant par une résistance et à un bouton qui, lorsqu'il est actionné, fait qu'un 0 logique est envoyé au niveau du RESET, ce qui le déclenche. Les sources d'inspiration pour ce mécanisme ont été les feuilles de dessin du Development Kit (PCA10040), disponibles en .pdf dans le dépôt (voir Interface MCU Programming Connector sur la Sheet 4 et Reset switch sur la Sheet 2). Il est a noté que dans la Sheet 4 le connecteur utilisé est un TC2050-IDC, or celui présent sur le schématique est un TC2030-IDC, mais étant donné que le principe de fonctionnement est le même, le choix de celui-ci a été fait. De plus, le TC2050-IDC n'était pas disponible de base dans KiCad.

## Buzzer
Le buzzer choisi est un AST1109MLTRQ de chez Mallory, celui-ci est relié d'un côté au pin P0.03 du SoC et de l'autre côté à la masse. Le SoC aliment donc directement le buzzer quand il le décide (en général après qu'on ait appuyé sur le bouton 1).

## Pile
La pile choisie est une CR2032, pour rester fidèle au keyfinder. Sur KiCad, il s'agit de la modélisation du battery holder qui est effectuée, qui est un 1057 de chez Keystone. Un grand trou est ainsi visible sur le circuit imprimé ou ce holder est censé s'incruster et accueillir la pile.

## Antenne
Il a été choisi de rajouter une antenne PRO-OB-440 de chez Abracon connectée directement au pin P0.27 du SoC qui est donc alimentée dès lors que le SoC le décide et qui est notamment Bluetooth.

## Résistances
Les résistances choisies sont des WSKW06123L000FEA de chez Vishay. Il est a noté que la résistance utilisée au niveau du RESET n'a pas une valeur correcte puisque selon la sheet elle doit être de 18k Ohms.

Les références exactes des composants sont marquées sur le schématique.

## Concepts à éclaircir et questions
- Le branchement du JTAG est-il correct ?
- Le SoC peut-il alimenter à la fois le buzzer et la LED ?
- Le routage est-il correct ? Par exemple, le SoC est-il alimenté correctement puisque les VDD ont l'air d'être reliés à SWDCLK ?
- A quel point faut-il aller loin dans l'analyse dans caractéristiques techniques et le réalisme concernant les composants ? (ex : faut-il regarder les tensions de fonctionnement, la valeur des résistances, etc ?)
- Comment marche réellement le JTAG, une machine vient se connecter sur le connecteur et programme le SoC ?
- N'y a-t-il pas d'autres composants a rajouter ? Inductances, capacités, résistances qui permettent à ceux rajouter de fonctionner ? Notamment entre l'antenne et le Soc, ou autres ?
- Peut-on directement envoyer un signal au SoC avec le bouton 1 en reliant directement la pile au pin du SoC lorsque le bouton est appuyé ? Y a-t-il un risque de tension trop élevée/faible ?
- Mettre un battery holder sur KiCad est-il correct ? Le grand trou dans le circuit imprimé est-il problématique ?
- (Question de débutant) : différence entre une MCU, un SoC, un CPU, un processeur et un micro-contrôleur ?
