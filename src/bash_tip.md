# Bash

## recherche

- `grep -rn "ref" .` : 
	cherche toute les reference de "ref" dans le dossier "." 
	en affichant la ligne et le ficheir 
	très pratique qd on cherche dans un gros projet la definition/l'initialisation d'une
	d'une variable. Mieux que bootlin car local et rapide.

- `find . -name "giga*" 2> /dev/null`:
	cherche avec le nom du fichiers.
	Ici je vais avoir la liste de tout les fichiers qui commence par "giga"
	ps: je me sert que de ca, en generale je le trouve inutile

- `nautilus .`:
	Ouvre l'exploirateur de fichier graphique au dossier ".".
	ps: `Dolphin` `Thunar`  `Nemo` `Caja` `PCManFM` depend de l'environnement bureau.



## Raccourcis clavier

- `Ctr + a` : 
	permet de revenir au debut de la ligne de commande 
	pratique quand on veut changer la commande après un long cd.

- `Ctrl + L` :
	remonte la ligne de commande en haut de fenetre
	quand on fait une compilation ou qu'on tape trop de commande 
	on veut parfois re-ecrire en haut de page pour un peu recommencer 
	un peu comme un clear mais sans effacer.
	Il met bcp d'espace par contre


## miscealinous

- `cd -` : 
	permet d'aller dans le denier repertoire avant le dernier cd.
	c'est pratique dans le cas ou le compilateur et l'excutable sont dans des dossiers
	différent.
- `sync`:
	synchronise le buffer memoires et le disque 
	cas d'usage: ejecter une clé, debugger un filesystem pour xdd dessus.
