# Notes de cours MVA

Ce dépôt rassemble des notes de cours au format [Obsidian](https://obsidian.md/). Il est pensé pour être lu et navigué dans Obsidian : les liens entre les notes, les encadrés et les formules y seront plus agréables que dans un navigateur web.

Ce guide explique tout pas à pas. Il ne suppose aucune connaissance de Git, GitHub ou de la ligne de commande.

## Ce dont vous avez besoin

- un ordinateur sous Windows ou macOS ;
- une connexion Internet ;
- environ cinq minutes pour installer deux applications gratuites : GitHub Desktop et Obsidian.

GitHub Desktop sert à récupérer une copie du dépôt sur l'ordinateur et à la mettre à jour plus tard. Obsidian sert à lire les notes.

> [!tip] Pas besoin de programmer
> Les étapes ci-dessous ne demandent pas d'écrire de commandes dans un terminal. Suivez simplement les boutons indiqués.

## 1. Installer GitHub Desktop et cloner le dépôt

### Sur Mac

1. Ouvrez la page officielle de [GitHub Desktop](https://desktop.github.com/download/).
2. Téléchargez la version correspondant à votre Mac : **Apple silicon** pour la plupart des Mac récents, ou **Intel** pour les anciens modèles.
3. Ouvrez le fichier téléchargé, puis faites glisser l'icône GitHub Desktop vers le dossier **Applications** si macOS le propose.
4. Lancez GitHub Desktop depuis Applications. Connectez-vous à GitHub si l'application vous le demande ; créer un compte gratuit est possible si vous n'en avez pas.

### Sur Windows

1. Ouvrez la page officielle de [GitHub Desktop](https://desktop.github.com/download/).
2. Cliquez sur **Download for Windows**.
3. Ouvrez le fichier téléchargé et suivez les étapes de l'installation.
4. Lancez GitHub Desktop depuis le menu Démarrer. Connectez-vous à GitHub si l'application vous le demande ; créer un compte gratuit est possible si vous n'en avez pas.

### Télécharger une copie du dépôt

Les étapes suivantes sont les mêmes sur Mac et Windows.

1. Dans GitHub Desktop, ouvrez le menu **File**, puis cliquez sur **Clone repository...**.
2. Choisissez l'onglet **URL**.
3. Copiez-collez cette adresse dans le champ **Repository URL** :

```text
https://github.com/comarquet/mva.git
```

4. Dans **Local path**, choisissez un emplacement facile à retrouver, par exemple le dossier `Documents`.
5. Cliquez sur **Clone**.

GitHub Desktop crée alors un dossier appelé `mva`. C'est votre copie locale du dépôt : ne déplacez pas les fichiers un par un ; déplacez le dossier `mva` entier si vous devez le ranger ailleurs.

> [!info] Mettre les notes à jour plus tard
> Ouvrez GitHub Desktop, sélectionnez ce dépôt, puis cliquez sur **Fetch origin**. Si le bouton devient **Pull origin**, cliquez aussi dessus : les nouvelles notes seront alors téléchargées sur votre ordinateur.

## 2. Installer Obsidian

### Sur Mac

1. Ouvrez la page officielle de [téléchargement d'Obsidian](https://obsidian.md/download).
2. Dans la section **Mac**, téléchargez la version **Universal**.
3. Ouvrez le fichier téléchargé et placez Obsidian dans le dossier **Applications** si macOS le propose.
4. Lancez Obsidian depuis Applications.

### Sur Windows

1. Ouvrez la page officielle de [téléchargement d'Obsidian](https://obsidian.md/download).
2. Dans la section **Windows**, téléchargez la version **Universal**.
3. Ouvrez le fichier téléchargé et suivez les étapes de l'installation.
4. Lancez Obsidian depuis le menu Démarrer.

> [!tip] Obsidian est gratuit pour cet usage
> Vous pouvez utiliser l'application sans créer de compte et sans activer les offres payantes. Un compte n'est utile que pour certains services optionnels, comme la synchronisation proposée par Obsidian.

## 3. Ouvrir les notes dans Obsidian

1. Au premier démarrage, choisissez **Open folder as vault**. Si Obsidian est déjà ouvert, cliquez sur l'icône de coffre-fort en bas à gauche, puis sur **Open another vault** et **Open folder as vault**.
2. Sélectionnez le dossier `mva` créé par GitHub Desktop. Si vous l'avez cloné dans Documents, ce sera normalement `Documents/mva`.
3. Cliquez sur **Open**.

Les notes apparaissent dans la colonne de gauche. Commencez par le dossier `prerentree`, puis choisissez une matière et son dossier `notes`.

> [!warning] Choisir le bon dossier
> Sélectionnez le dossier entier `mva`, pas seulement un fichier Markdown et pas le dossier caché `.obsidian`. Le dossier `.obsidian` contient les réglages ; il ne faut pas l'ouvrir seul ni le supprimer.

## Utiliser les notes

- Cliquez sur un lien bleu ou violet pour ouvrir une autre note.
- Utilisez la recherche, en haut à gauche, pour retrouver un mot ou une notion.
- Les fichiers qui se terminent par `.md` sont les notes. Ils restent de simples fichiers texte : vous pouvez les lire et les modifier dans Obsidian.
- Si vous modifiez des notes et souhaitez conserver vos changements sur GitHub, demandez de l'aide à une personne habituée à GitHub Desktop avant de cliquer sur **Push origin**. Cela évite d'écraser par erreur le travail d'autres personnes.

## Traduire les cours avec un coding agent

Les notes sont principalement en français, mais elles peuvent être traduites facilement avec un **coding agent** : il peut parcourir les fichiers Markdown, traduire le texte et conserver la structure des titres, les liens Obsidian et les formules mathématiques.

Si vous réalisez ou souhaitez réaliser une traduction, contactez Corentin à **cormarquet@gmail.com**. Il pourra alors ajouter ou coordonner l'ajout des cours en anglais dans le dépôt.

> [!tip] Bon réflexe pour une traduction
> Demandez explicitement à l'agent de préserver les liens `[[...]]`, les blocs de code, les formules et la structure des fichiers. Ainsi, la version traduite restera navigable dans Obsidian.
