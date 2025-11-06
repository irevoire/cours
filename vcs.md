# VCS cheatsheet

## C'est quoi

Qu'est-ce que c'est un "VCS".
Déjà, ça veut dire : Version Control System
C'est un programme qu'on utilise pour "versionner" quelque chose, souvent du
code ou au moins du texte, mais ça peut aussi stocker des images ou autre
format, c'est juste moins optimisé pour ces cas là.

> [!NOTE]
> Spécifiquement parce que la manière dont on stocke le code se fait en
> sauvegardant les différentes modifications qu'on a faites par **lignes**.
> Donc si le format de fichier stocké n'utilise pas de lignes, ça fonctionne
> mais c'est illisible pour un être humain et pas très optimal pour le vcs.
> En général on dit que lorsqu'on doit stocker des images, pdf, vidéo ou
> n'importe quel autre type de fichier "binaire" il ne faut les stocker qu'une
> fois puis presque jamais les modifier. Si on compte les modifier souvent alors
> il vaut mieux les stocker ailleurs.

Le but d'un VCS c'est donc de stocker plusieurs versions de ton code et de te
permettre de revenir à une vieille version. De voir quelle version avait
ajouté cette ligne de code qui te pose problème aujourd'hui et de fusionner
deux versions qui auraient divergé en même temps.
(on verra ce que diverger veut dire plus tard, pas de hehehe)

À l'usage ça veut dire que tu ne fais plus de `projet_v1.0_last_final_pour_du_vrai`
mais a la place tu vas toujours modifier ton projet au même endroit, ne jamais
le copier ailleurs ou quoi que ce soit et simplement déclarer des "versions" de
temps en temps quand tu as quelque chose que tu souhaites ne pas perdre.

## Comment ça fonctionne concrètement

Il y a de nombreux vcs, globalement les plus utilisé sont `git` et `mercurial`.
Les deux plus récent qui pourraient prendre de l'usage s'ils continuent de
grandir sont `pijul` et jujitsu (`jj`).

Ils partagent tous certains concept mais ici on va se concentrer sur la manière
dont `git` et `jj` fonctionnent puisque c'est les deux qui permettent de
collaborer sur tous les qui utilisent `git`.

> [!NOTE]
> According to the [2022 Stack Overflow developer survey](https://survey.stackoverflow.co/2022/#version-control-version-control-system-prof),
> 96% of professional developers use Git. 

----

Une fois que notre projet atteint un stade satisfaisant que l'on souhaite
sauvegarder on va créer ce qu'on appelle un **commit**.
C'est un fichier que `git` va stocker en interne et qui contient plusieurs
informations :
- Un identifiant qui permet de retrouver le commit
- Le ou les auteurs et le ou leurs e-mails
- Une signature cryptographique optionnelle qui permet de garantir que le
	commit a bien été créé par l'auteur indiqué et pas par quelqu'un qui aurait
	eu accès a leurs compte.
- Une description des changements contenu dans le commit, typiquement c'est
	une phrase courte d'une 50ène de caractère suivie d'un paragraphe explicatif
	deux lignes plus bas si besoin.
- La liste des changements

Un "changements" pour `git` c'est une description des lignes modifiée: leurs
position, ce qu'elle contenait, ce qu'elle contienne maintenant.

Par exemple si luna créé un fichier `truc` avec le contenu suivant:
```
Okami est le plus beau des chiens
```

Puis décide de faire un commit avec le message suivant : "init commit" voilà
le contenu stocké par `git` (obtenu en faisant la commande `git show`) :
```
commit 29398084b262da111345c05028555e3e94f9c5ab
Author: Luna <luna@okami.dog>
Date:   Tue Nov 4 00:51:20 2025 +0100

    init commit

diff --git a/truc b/truc
new file mode 100644
index 0000000..9998555
--- /dev/null
+++ b/truc
@@ -0,0 +1 @@
+Okami est le plus beau des chiens
```

1. La première ligne nous donne l'identifiant du commit, ici
	`29398084b262da111345c05028555e3e94f9c5ab`
2. La seconde ligne donne l'auteur
3. Ensuite la date
4. Pui le message
5. Et finalement on voit ce qu'on appelle le **diff**

Après avoir stocké quelques données interne sur les fichier stocké on retrouve
ces lignes:
```diff
+++ b/truc
@@ -0,0 +1 @@
+Okami est le plus beau des chiens
```

1. `+++ b/truc` signifie qu'un nouveau fichier `truc` a été créé
2. `@@ -0,0 +1 @@` nous indique a quelle ligne il a été créé et les lignes
	qu'il a remplacé. Ici il a remplacé `0` lignes a partir de la ligne `0`
	(d'où le `-0,0`). Et il rajoute une ligne `+1`.
3. Finalement les lignes modifiée : 
	`+Okami est le plus beau des chiens`

Cette partie c'est ce qu'on manipule et regarde le plus souvent en tant qu'être
humain avec les descriptions de commit. Il faut s'habituer a ce format.
Les lignes précédée d'un `-` indiquent qu'elles ont été supprimée, et celles
précédée d'un `+` sont rajoutée au fichier.

Imaginons que tamo télécharge ce projet et décide de modifier la ligne pour
y ajouter le kef. Voilà a quoi son commit pourrait ressembler :
```diff
commit 4e900683e7c60813dd177f976c0a2f67f1b44aa7
Author: Tamo <irevoire@protonmail.ch>
Date:   Tue Nov 4 01:06:01 2025 +0100

    Add the kef

diff --git a/truc b/truc
index 9998555..8200600 100644
--- a/truc
+++ b/truc
@@ -1 +1 @@
-Okami est le plus beau des chiens
+Okami et kefir sont les plus beau chiens du monde
```

Ici on voit bien que la ligne originale a été marqué d'un `-` puis la nouvelle
ligne a été ajoutée.
Ça signifie que lorsque `git` voudra regénérer le projet il devra prendre tous
les commits dans l'ordre et appliquer les changements l'un après l'autre.
D'abord on rajoute la ligne de luna, puis on la supprime et on rajoute celle de
tamo.

On remarque aussi que le numéro du commit est différent. Tous les commits ont
des identifiants différent.

### Quelle garantie nous donne ce système

Grace a ce système `git` a plusieurs avantages :
- Il est rapide (plus que les autres a son époque)
- En stockant uniquement la différence d'une ligne a l'autre les projets sont
	petit, ce qui veut dire que tout le monde peut stocker l'entièreté d'un projet
	pour pas cher. (Le kernel linux contient 1.4 millions de commits pour 1.7GiB
	de code et son repo `git` ne pèse que 5.5GiB)
- Il garantie qu'un projet n'as pas été altéré

Le dernier point peut sembler évident mais avant `git` ce n'était pas une garantie
des autres vcs. Si une corruption du disque venait a modifier une ligne d'un diff
cela pouvait passer complètement inaperçu.
Mais grace a la manière dont `git` génère ses identifiants de commit une
modification de commit ne peut pas arriver.
L'identifiant d'un commit est la somme de toutes les données contenue dans un
commit (auteur, email, signature, description, date, diff) ET de l'identifiant
de ses parents.
Les commits forment une chaine de changement qui doivent être appliqué dans
l'ordre et si jamais l'un de ces commit venait a avoir un problème et être
modifié alors il est très simple de recalculer son identifiant et de se rendre
compte qu'il y a un problème (on peut le faire avec la commande `git fsck`).

C'est cette astuce qui fait que `git` résiste aux corruption de donnée, mais
également aux attaquant malveillant qui voudrait fabriquer un commit contenant
un "hack". Leurs commit ne pourra pas s'insérer discrêtement dans l'historique
puisque changer un commit a un endroit nécessite de modifier l'identifiant de
tous ses enfants.

## Qu'est-ce qu'il se passe si deux versions entrent en conflit

La dernière chose a voir avant de parler du fonctionnement de `git` et de `jj`
c'est la gestion des conflits.
Comme nous avons vu plus haut, `git` gère les `diff` avec la granularité d'une
ligne. Cela veut dire que si deux personnes modifies une version `A` en créant
un commit `B0` et `B1` qui modifient tous les deux la même ligne alors `git` ne
peut pas savoir comment appliquer les changements spécifié.

Reprenont le cas de tout a l'heure. Luna a créé son fichier `truc` avec le
contenu suivant :
```
Okami est le plus beau des chiens
```

Elle a ensuite mis ça dans un commit avec l'identifiant `29398084b262da111345c05028555e3e94f9c5ab`.
Finalement elle a partagé son projet avec tamo et sam.
Les deux ont modifié le projet a leurs manière, tamo a modifié le projet en y ajoutant kefir :

```
Okami et kefir sont les plus beau chiens du monde
```

Il a ensuite créé le commit : `4e900683e7c60813dd177f976c0a2f67f1b44aa7` qui
descend directement du commit de luna **2939...**.

Sans concertation et sans savoir que tamo avait fait ça, sam a également
modifié le projet en partant de la version de luna :
```
Okami et echo sont les plus belle chiennes du monde
```

Il a créé le commit `05a6bbfb69e484738ef55c2f3c7dbbdac519fd1a` également
descendant du **2939...** de luna.

Grace a la commande `git log` on peut visualiser un peu mieux qui est un parent de qui :
```
* 05a6bbf add echo
| * 4e90068 Add the kef
|/  
* 2939808 (HEAD -> main) init commit
```

Ici, `HEAD -> main` indique le point de vu de luna.
On peut voir que les commits **05a6** et **4e90** de tamo et sam partent
directement du commit de luna. Jusque là il n'y a pas de problème.
Admettons que tamo donne son commit en premier a luna et qu'elle décide de
l'intégrer a son projet via la commande `git merge 4e90` par exemple :
```
Updating 2939808..4e90068
Fast-forward
 truc | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

`git` nous indique qu'il a réussi a intégrer (merger) les changements de tamo
sur la version qu'on avait. Si on effectue a nouveau un `git log` on voit que :

```
* 05a6bbf add echo
| * 4e90068 (HEAD -> main) Add the kef
|/  
* 2939808 init commit
```

`git` a simplement "déplacé" la `HEAD` sur le commit de tamo. C'est ce que
le mot "fast-forward" indiquait dans le retour de commande précédent.

### Maintenant comment est-ce qu'on peut intégrer le changement de sam dans notre
projet ?

Il y a deux manières en réalité :

#### On merge son changement a nouveau

```
% git merge 05a6
Auto-merging truc
CONFLICT (content): Merge conflict in truc
Automatic merge failed; fix conflicts and then commit the result.
```

`git` nous indique immédiatement que le merge n'as pas pu avoir lieu. Il y a eu
un ou plusieurs conflits. Pour voir ou on en est on peut faire la commande
`git status` :
```
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
	both modified:   truc

no changes added to commit (use "git add" and/or "git commit -a")
```

`git` nous montre que le fichier `truc` a été modifié par "both". Ici both
signifie par les deux "versions" ou les deux commits que l'on essaie de merger.

Voilà le contenu du fichier `truc` après le conflit :
```
<<<<<<< HEAD
Okami et kefir sont les plus beau chiens du monde
||||||| 2939808
Okami est le plus beau des chiens
=======
Okami et echo sont les plus belle chiennes du monde
>>>>>>> 05a6
```

Ou peut voir la version contenu dans `HEAD` en premier, la "notre" et celle du
commit de tamo **4e90**.
Ensuite git nous montre a nouveau la version originale a partir de la quelle
les deux commits ont divergé, ici c'est la **2939**, le commit de luna, suivi
du contenu du commit de sam.

Pour régler le conflit ou peut choisir l'une des trois versions ou simplement
modifier le texte comme ça nous arrange. Ici je vais modifier le texte pour
arriver a :
```
Okami, echo et kefir sont les plus beau chiens du monde
```

Ensuite je dois indiquer a `git` que j'ai résolu le conflit a l'aide de la
commande `git add truc`.
Puis je fais a nouveau un commit en indiquant que j'ai mergé les changements :
```
*   aa7960f (HEAD -> main) Merge commit '05a6'
|\  
| * 05a6bbf add echo
* | 4e90068 Add the kef
|/  
* 2939808 init commit
```

On peut voir que les commit `4e90` et `05a6` n'ont pas bougé et ont maintenu
leurs identifiant comme prévu mais un nouveau commit a du être rajouté pour
expliquere a `git` comment les fusionner là où il ne pouvait pas se débrouiller
seul.

Ici on a vu spécifiquement un cas qui ne fonctionne pas, mais si les deux
changements avaient impacté des lignes différentes alors `git` aurait pu
effectuer le merge lui même sans interaction de l'utilisateur.

#### On extrait son changement pour l'intégrer au projet

On se remet dans cette situation ou `HEAD` correspond a **4e90** :
```
*   aa7960f Merge commit '05a6'
|\  
| * 05a6bbf add echo
* | 4e90068 (HEAD -> main) Add the kef
|/  
* 2939808 init commit
```

Mais au lieu d'aller vers le merge commit **aa79** on va plutôt extraire le
contenu du commit de sam et le mettre par dessus le notre avec la commande
`git cherry-pick 05a6` :
```
git cherry-pick 05a6
Auto-merging truc
CONFLICT (content): Merge conflict in truc
error: could not apply 05a6bbf... add echo
```

On se retrouve a nouveau avec le conflit, je le résoud et je `git add truc`
puis voilà le résultat de `git status` :
```
On branch main
You are currently cherry-picking commit 05a6bbf.
  (all conflicts fixed: run "git cherry-pick --continue")
  (use "git cherry-pick --skip" to skip this patch)
  (use "git cherry-pick --abort" to cancel the cherry-pick operation)

Changes to be committed:
	modified:   truc
```

Au lieu de faire un commit comme précédemment, `git` me demande à la place
d'écrire `git cherry-pick --continue`.
Une fois fait si je lance `git log` a nouveau je me retrouve avec :
```
* 9cddd29 (HEAD -> main) add echo
| * aa7960f Merge commit '05a6'
|/| 
| * 05a6bbf add echo
* | 4e90068 Add the kef
|/  
* 2939808 init commit
```

Ou, en ignorant tous les commits du merge précédent qui ne nous concernent plus :
```
* 9cddd29 (HEAD -> main) add echo
* 4e90068 Add the kef
* 2939808 init commit
```

Simplement trois commits les uns derrière les autres. Il y a plusieurs choses
à noter ici :
- L'identifiant du commit de sam a changer, ce n'est plus **05a6** mais **9cdd**
	C'est obligatoire puisqu'il n'as plus le même père. Au lieu d'avoir **2939**
	il a maintenant **4e90** comme père. En le modifiant `git` a également rajouté
	luna comme co-autrice du commit, et la date du commit a également été modifiée
- Il n'y a plus de "merge commit" un peu complexe, l'historique est devenu
	beaucoup plus simple a lire.
- En revanche on a perdu l'information du contexte dans lequel sam avait fait
	son commit. Si dans la réécriture de son commit luna a fait une erreur on
	ne pourra plus jamais savoir l'intention qu'avait sam au moment ou il a fait
	son commit.

### Les informations importante a retenir de tout ça

- `git` fonctionne avec un système d'historique en "arbre", les commits se
	succèdent les uns après les autres et modifier un nœud aura des impacts sur
	tous les commits qui le succèdent
- Plusieurs personnes peuvent travailler en même temps sur le même projet
- On visualise les changements de chacun via des `diff`s
- Il y a deux manières d'intégrer des changements à son projet, soit via des
	"merges", soit via des "rebases" (c'est l'action de prendre des commits et
	de changer leurs base, littéralement). Les deux existent. Souvant une
	entreprise va choisir un système que tout le monde devra suivre. Le plus
	commun est de fonctionner avec un système de rebase. Celui conseillé par
	`git` est au contraire de fonctionner par merge.

## Comment travailler avec d'autre personnes

Une fois qu'on a vu tout ça il manque toujours l'information de comment
partager du code avec d'autres personnes.

### À l'ancienne

Comment tamo pourrait faire pour m'envoyer son code par exemple ? Une des
manières (celle qui est actuellement utilisée sur le kernel linux) consiste
a appeler la commande `git show 4e900` sur son ordinateur et d'envoyer le
résultat de la commande a luna sur discord ou par mail par exemple.
Luna récupère les changements dans un fichier puis peut ensuite les appliquer
sur son projet via la commande `git apply path/to/file`.

### Avec github, gitlab, gogs ou autre

Mais aujourd'hui la plupart du temps ce qu'il est plus commun de faire c'est
d'utiliser un site qui va héberger notre projet `git` chez eux et qui permet
ensuite de soumettre des changements sous forme de pull ou de merge request
(PR ou MR, ce sont des synonymes).

Pour proposer un changement sam devra maintenant aller sur ce site, demander
une copie du projet sur son compte (un fork), la modifier en faisant son
commit, "pousser" ses changements en ligne (via la commande `git push`), puis
faire une pull request sur le projet de luna. De son coté elle aura accès
directement a un `diff` en ligne et un espace de discussion où laisser des
commentaires.

----

Dans les deux cas, comme il est fréquent de vouloir merger plus qu'un seul
commits et que le travail peut prendre du temps on va grouper plusieurs commits
derrière une "branche" ou un "tag".
Ces deux concepts son très similaire et permettent simplement de mettre un nom
compréhensible par un humain sur un commit plutôt que d'utiliser son
identifiant partout.
La différence entre une branche et un tag c'est que lorsque je demande a `git`
d'aller sur une branche puis que je fais un commit, la branche va se déplacer
automatiquement pour suivre ce commit tandis qu'un tag restera là où il a été
mis.

# git

Git dépends énormément de ce concept de branche. Lorsque l'on souhaite
travailler sur une nouvelle fonctionnalité en général la première chose qu'on
va faire avant de créer son commit c'est de créer une branche sur laquelle on
fera ensuite un commit. On veut généralement éviter de déplacer la branche
principale d'un projet parce que si ce n'est pas notre prochain changement qui
est mergé on risque de créer inutilement des conflits.

## Créer son projet

Pour créer son projet on va se déplacer a la racine du dossier qui nous
intéresse puis exécuter la commande `git init`.
Cette commande va créer un dossier caché nommé `.git` qu'on peut observer
en faisant `ls -a`. C'est ce dossier qui contient tous les commits. **Il ne faut
jamais le modifier soit même**.

## Travailler avec un projet distant (github, gitlab, etc)

Si le projet est relié a un repository `github` ou autre il faudra lui ajouter
une "remote".
On peut observer les remote de son projet en faisant la commande `git remote`.
Par exemple sur un de mes projet la commande renvoie "origin" et "upstream".
Ce sont des termes assez commun :
- **upstream** représente **mon** projet, c'est celui sur lequel je peux créer
	des branches et faire des `git push`
- **origin** représente le projet original, c'est celui sur lequel je vais
	faire des pull request et du quel je vais récupérer les dernières mises à
	jours

Avec la commande `git remote show origin` on peut récupérer les détails d'une
remote :
```
* remote origin
  Fetch URL: git@github.com:sharkdp/numbat.git
  Push  URL: git@github.com:sharkdp/numbat.git
  HEAD branch: master
  Remote branches:
...
```

### Partir d'un projet vide sur github

Une fois qu'on a créé un projet sur github et qu'on a créé un projet en local,
la manière de relier les deux et de pouvoir pousser des changements va donc
être de mettre la remote correctement. Pour le cas de numbat par exemple
j'aurais pu écrire :
```
git remote add origin git@github.com:sharkdp/numbat.git
```

La remote doit est généralement donnée sur github quand on créé un projet sous
le gros bouton vert `code`.

> [!CAUTION]
> Il y a toujours deux urls pour les projets sur github, une url qui commence
> par `git@...` et une qui commence par `http...`. L'url qui commence par
> `http` ne permet QUE de faire des lectures. Il sera donc impossible de
> pousser des changements avec une remote configurée comme ça.

### Partir d'un projet qui existe déjà sur github

Si l'on part d'un projet qui existe déjà alors on peut simplement "cloner" le
projet plutôt que de le configurer soit même.
On peut donc lancer la commande `git clone git@github.com:sharkdp/numbat.git`.

### Récupérer les dernières mises à jour d'un projet localement

Quand on travaille avec d'autres personnes forcément le projet va évouler sans
nous. Des nouvelles branches vont être créée et des nouveaux commits vont être
ajouté a des branches déjà existante.
Pour télécharger tous ces changements on utilise la commande `git fetch`.
Si la branche `main` a été modifiée par exemple, après avoir fait `git fetch`
ou aura une branche `origin@main` de disponible qui contient la dernière
version du code.
Pour se simplifier la vie en général on préfère travailler directement sur
notre branche `main` que sur `origin@main` donc on va suivre la commande
`fetch` d'un `git merge main@origin` pour rappatrier les changements qui ont
été fait en ligne sur notre version locale de cette branche.

Pour aller plus vite et effectuer cette action automatiquement sur toutes les
branches en même temps on peut directement exécuter la commande `git pull` qui
fera les deux actions en une.

### Mettre ses changements en ligne

Pour mettre une branche en ligne il suffit d'écrire `git push`. Si l'on as
pas encore défini de remote par défaut ou que la branche n'existe pas encore
en ligne `git` risque de demander des précisions. On devra alors écrire la
commande complète :
```bash
git push -u origin kefir
```

Où `origin` peut être modifié par n'importe quelle remote et `kefir` par
n'importe quel nom de branche.

## Créer des commits

L'action qu'on va le plus utiliser avec `git` c'est le fait de créer des
commits.
Il y a quelques étapes a suivre pour créer un commit ;

### Décider du diff qu'on va mettre dans notre commit

La chose la plus importante d'un commit c'est évidemment le diff qu'il
contient.
Par défaut le diff est vide et c'est a nous de lui ajouter du contenu.
La commande `git add path/to/file` permet de rajouter un fichier au diff.
- Si le fichier n'existait pas il sera créé
- Si le fichier existe déjà c'est simplement les changements entre l'ancienne
	version et la nouvelle qui sera ajouté

> [!NOTE]
> Si un fichier a été supprimé il faut quand même l'ajouter au diff avec la
	même commande.

On peut également rajouter des dossiers, ou même le dossier courant avec
`git add .`.

Pour rajouter des parties de fichier la commande `git add -p` existe également,
`git` va proposer différent changement en mode interactif et nous laisser
l'option de les ajouter ou non au commit qu'on va créer ensuite.

Si l'on a ajouté un fichier par erreur on peut annuler son ajout avec la
commande `git reset path/to/file`.

> [!IMPORTANT]
> Les fichiers qui ont été ajouté un jour dans `git` via la commande `add`
> sont considéré comme "tracké".

Pour éviter de tracker des fichiers par erreur il est considéré de rajouter un
fichier `.gitignore` a la racine du projet.
Par exemple le fichier `.DS_Store` généré par macOS ne sert a rien et devrait
être ignoré.

### Créer le commit

Et finalement la dernière étape, créer le commit.
Si le message est simple on peut créer le commit avec la commande ;
`git commit -m "La description du commit"`

Si le message est long il est aussi possible de juste écrire ;
`git commit`
Ensuite `git` va ouvrir un éditeur de texte et te laisser écrire ton message.

Une dernière manière plus rapide encore consiste a faire le `git add` et le
`git commit` en une étape avec la commande `git commit -a` (ou
`git commit -am "description"`).
Puisqu'on a pas choisi nous même les changements a rajouter, `git` va utiliser
tous les changements effectué sur les fichiers tracké automatiquement.

> [!TIP]
> Ces changements correspondent a la commande `git diff`.

## Manipuler les branches

Comme dit plus tôt, avec `git` tout le travail est stocké sur des branches.
Il y a donc beaucoup d'opération qui permettent de manipuler les branches du
projet.

### Créer une branche

Avant de faire le moindre changement il est conseillé de créer une branche
qui contiendra ces changements. On peut le faire via la commande ;
`git checkout -b kefir` où `kefir` représente le nom de la branche.

> [!NOTE]
> La branche est créée là ou se trouvait `HEAD`. Le prochain commit partira de
	là.

### Se "déplacer" sur une autre branche

Que ce soit pour travailler dessus ou juste en visualiser le contenu il arrive
qu'on veuille se déplacer sur une autre branche.
Dans `git` notre `position` actuelle dans l'historique s'appelle `HEAD`.
Pour déplacer HEAD sur une autre branche on peut utiliser la commande
```bash
git checkout kefir
```

### Déplacer une branche sans perte de travail

Une autre action qui peut être utile est celle de déplacer une branche
directement. On a vu plus tôt qu'une branche ce n'était qu'un nom plus simple
sur un identifiant de commit. Il n'y a rien qui nous empêche de déplacer cette
branche ailleurs.
On effectue cette action avec la commande `git reset identifiant-de-commit`.
Cela va déplacer la tête de la branche et HEAD sur l'identifiant du commit
spécifié.
Le travail qui avait été fait localement reste en place en revanche.

Si par exemple j'ai fais les commits suivant :
```
* ca20263 (HEAD -> main) wip
* 5e694e3 wip
* ef330de wip
* 9cddd29 add echo
* 4e90068 Add the kef
* 2939808 init commit
```

Et que je souhaite supprimer tous les commits `wip` pour les remplacer par
un seul commit "j'aime les frites" alors je peux écrire simplement :
```bash
git reset 9cdd
git add bidule
git commit -m "j'aime les frites"
```

Et ensuite voilà mon historique :
```
* 605fbde (HEAD -> main) j'aime les frites
* 9cddd29 add echo
* 4e90068 Add the kef
* 2939808 init commit
```

Le commit `605f` contient la somme des trois commit `wip` que j'avais créé
précedemment.

### Déplacer une branche avec perte de travail

Un autre usage du reset c'est le fait de vouloir se débarasser de travail.
Si après mes 3 wip je décide d'abandonner mon travail et de repartir d'une
base saine alors je peux également écrire `git reset --hard 9cdd`.

> [!CAUTION]
> Attention cependant, il peut être assez compliqué de récupérer son travail
	après ça et `git` le supprimera automatiquement au bout d'un moment.

### Merger deux branches

Comme on a vu tout a l'heure, pour merger deux branches il faut se mettre sur
la branche qui va **recevoir** les changements et ensuite appeler la commande
`git merge kefir` où "kefir" est le nom de l'autre branche.

Si je souhaite intégrer les changements de `kefir` dans `main` je fais :
```bash
git checkout main # on commence par se déplacer au bon endroit
git merge kefir
# Ici, si le commit n'est pas fast-forward il faudra régler les conflits et
# créer un merge commit

git push # pas obligatoire
```

### Rebaser une branches sur une autre

L'autre action que nous avons vu plus tôt et qui est souvent employée dans les
entreprise est l'action de rebaser une branche sur une autre.
Précedemment nous avons vu la commande `cherry-pick` qui permet de sélectionner
un unique commit, d'en extraire le contenu et de l'appliquer a HEAD.
Mais les branches sont rarement constituée d'un unique commit c'est pourquoi en
général on utilise la commande `rebase` qui effectue la même action mais pour
tous les commits d'une branches.

Imaginons que je sois dans cette situation et que je veuille rebase les changements
de la branche `kef` sur la branche `echo` :
```bash
# Déjà, HEAD n'est pas au bon endroit donc on commence par se déplacer sur kef
git checkout kef

git rebase echo
# Ici on risque de devoir régler les conflits

git push # pas obligatoire
```

### Git FAQ

#### Je n'arrive pas a `git push`

La raison la plus commune c'est qu'il y a des choses en ligne que tu n'as pas
localement sur ton ordinateur.
Le message d'erreur typique pour ce cas là devrait être suivie de cet "indice" :
```
error: failed to push some refs to 'https://github.com/irevoire/cours.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Dans ce cas là il y a deux solutions, si tu viens de créer le projet et que le
contenu en ligne ne t'importe pas tu peux rajouter l'option `--force` au
`push`, ça donnerait par exemple `git push -u origin main --force`.

> [!CAUTION]
> En faisant ça le code en ligne sera réellement supprimé. Si tu travaillais
> avec quelqu'un d'autre sur le projet ça peut avoir de réelle conséquences.
> En général tu ne veux faire ça presque que lorsque tu viens de créer un repo
> et que sans faire exprès tu as demandé a github de créer un README ou de
> mettre un fichier de licence.

La majorité du temps ça tu rencontreras cette erreur parce que ;
- Une autre personne a travaillé en ton absence et rajouté du code sur cette
  branche.
- Tu as deux dossiers qui pointent vers le même repository et l'un d'eux a été
	mis à jour mais pas le second.
- Tu as changé d'ordinateur et oublié de `pull` les nouveaux changement avant
	de recommencer a travailler.

Dans tous ces cas là ce que tu veux c'est de récupérer le code en ligne puis
reprendre ton travail par dessus.
La commande pour faire ça est `git pull --rebase`.
Git va prendre tous les commits que tu as fait localement sur ta branche et qui
ne sont pas en ligne. Les mettres de coté, ensuite faire un `git pull` pour
mettre ta branche a jour avec ce qui existe en ligne et finalement recréer tes
commits par dessus la branche mises à jours.
À ce moment là il est possible que tu aies des conflits a résoudre.

# Jujitsu (`jj`)

Jusqu'à présent j'ai vaguement mentionné `jj` mais je n'ai jamais expliqué ce
que c'était ni ses différence avec `git`.

`jj` est un vcs complètement différent de `git` mais qui sait utiliser le
système de commit et de branche comme base pour stocker ses changements.
Ce qui le rends complètement compatible avec `git` et donc github ou gitlab.
Ça permet aussi de l'utiliser dans ton coin sans que tes collègues sachent
que tu l'utilises. Tu peux choisir ce que tu préfères.

Mon avis c'est que `jj` est plus simple a utiliser mais également plus
puissant. Et comme `jj` est beaucoup plus moderne le nom des commandes est
beaucoup plus simple a retenir et elles sont plus simple a utiliser.

> [!NOTE]
> `git` est sorti en 2005 ! Le développement de `jj` à commencé fin 2020 en
	comparaison.

> [!TIP]
> Les commandes de `jj` sont composées de "sous commandes", par exemple la
> commande `jj git` permet d'exécuter beaucoup d'opération liée au `.git`.
> Pour obtenir l'aide sur cette commande on peut entrer simplement la commande
> `jj git` dans le terminal qui va automatiquement afficher l'aide. Ou de
> manière générale on peut ajouter `--help` à n'importe quelle commande.

## Les points commun et différences entre `jj` et `git`

### Un nouveau concept, les changes

Avec `jj` même si les commits de `git` existent toujours puisque c'est ce qu'on
va mettre en ligne, on ne les manipules pas en général. À la place on va
manipuler des "changes" et il est important de faire la distinction entre les
deux puisqu'il ont des identifiants différent.
Comme un commit, un change contient un ou des auteurs, un identifiant, une
date, une description et possiblement une signature cryptographique.
Mais contrairement au commit, l'identifiant du change n'es pas basé sur ses
"parents".
Ce qui veut dire que l'on peut déplacer ou modifier un change sans avoir a le
recréer. Son identifiant restera le même et `jj` s'occupera de recréer les
"vrai" commits de `git` nécessaire pour nous automatiquement sans qu'on s'en
soucie.

### Tous les fichiers sont tracké

Dans `git` par défaut aucun fichier n'est tracké, il faut les ajouter soit même
avec la commande `git add`.
Dans `jj` par défaut tous les fichiers sont toujours tracké et pour en ignorer
certains il faut les ajouter au fichier `.gitignore`.

### Tout le travail fait toujours parti d'un change

Contrairement a `git` où le travail courant qu'on est entrain d'effectuer n'est
pas versionné dans un commit, dans `jj` on est toujours sur un change, il peut
ne pas avoir de nom mais il aura toujours un identifiant et ne sera jamais
perdu.

### L'état du projet est versionné

Avec `git`, comme on a vu plus tôt on versionne des fichiers, du code, du texte
ou n'importe quoi qu'on mette dans le dossier et sur lequel on fait la commande
`git add`.
`jj` va plus loin et versionne également l'état du projet. Ce qui veut dire que
si j'effectue une action et que je me trompe, il sera possible de remettre le
projet dans l'état où il se trouvait avant notre erreur.
Il devient alors presque impossible de perdre du travail contrairement a `git`.
Dans le pire des cas ou pourra toujours remettre le projet dans l'état où il se
trouvait avant notre erreur.

Il devient alors agréable de se tromper puisque dans le pire des cas on aura
perdu quelques minutes et dans le meilleur des cas on aura appris quelque
chose.

### Les conflits ne bloquent pas le projet

`jj` est capable de gérer les conflits comme des "changes" presque normaux. Ils
sont marqué comme contenant un conflit mais il n'y a pas besoin de le résoudre
dans l'instant.
Contrairement a `git` on peut continuer son travail ailleur, aller voir
d'autres changes, faire des rebases etc sans être bloqué parce qu'un conflit
est en cours.

### Les branches n'existent pas

On a vu que `git` ajoutait toujours les commits sur des branches, dès qu'on
essaie de travailler sans branches on reçoit des warnings sur chaque commande
et il n'est pas possible de partager son code en ligne.
Ensuite on se retrouve a devoir manipuler ces branches pour les faires
représenter ce que l'on souhaite partager.

`jj` à la place met plutôt l'accent sur les changes individuel. Il n'y a plus
de branche, on manipule l'arbre de change directement pour obtenir ce qu'on
souhaite et lorsque l'on souhaite partager du code on utilise des "bookmarks".

Un "bookmarks" est représenté dans `git` comme une branche, mais contrairement
a une branche, il représente l'état du projet a un instant T et ne se déplace
pas automatiquement à chaque fois qu'on rajoute un change.
C'est bien plus proche d'un `tag` dans `git` que d'une branche finalement mais
ça reste une branche pour faciliter le partage de travail avec les autres.

La manière de travailler va donc légèrement différer de ce qu'on aurait fait
avec `git`.
Au lieu de ;
1. Se déplacer sur la branche que l'on souhaite avoir comme base avec un
	`git checkout base-branch`
2. Puis de créer une branche qui va représenter le travail qu'on est entrain
	d'effectuer avec `git checkout -b nouvelle-branche`
3. De rajouter ses changements sous forme de commit
4. Et finalement de mettre en ligne son code avec un `git push`

On va plutôt fonctionner dans l'autre sens ;
1. On créé un nouveau changement qui prends la bookmark comme parent avec
	`jj new base-bookmark`
2. On rajoute nos changes
3. On créé la bookmark que l'on souhaite mettre en ligne avec
	`jj bookmark create nouvelle-bookmark`
4. On met en ligne la bookmark avec `jj git push`

## Créer son projet

Pour créer son projet c'est comme dans `git`, on va lancer la commande
`jj git init`.
Il faut préciser `git` parce que `jj` n'est pas obligé d'utiliser `git`.
Cette commande va créer un dossier `.git` et `.jj` qu'il ne faut surtout
pas modifier soit même.

## Travailler avec un autre projet distant

La gestion des bookmarks se passe exactement comme avec `git`.
Pour accéder aux remotes on va utiliser la sous commande `jj git remote`.
On peut ensuite y ajouter :
- `list` pour voir toutes les remotes.
- `add` pour rajouter une remote.
- `remove` pour supprimer une remote.

## Si le projet existe déjà

On peut simplement le cloner avec `jj git clone`

## Mettre en ligne ses changements

Pareil que pour `git` sauf qu'on précède la commande de `jj` :
```bash
jj git push
# ou
jj git push -u origin kefir
```

## Télécharger les changements en ligne

Contrairement a `git`, on ne peut pas faire de `git pull` puisqu'il n'y a pas
de branches a mettre à jour. À la place on va faire un `jj git fetch` et on
pourra travailler directement avec les branches `origin/main`.

## Manipuler les bookmarks

Comme dit précédemment, les `jj` ne fonctionne pas avec des branches mais avec
des "bookmarks". Elle seront convertie en branches `git` en interne et quand on
met le code en ligne pour ceux qui utilisent juste `git` sans `jj`.

Il n'y a pas de question "d'aller" sur une branche, de faire avancer une
branche, de la rebase ou quoi que ce soit. On fera toutes ces opérations
directement sur des changes.

### Créer une nouvelle bookmark

```bash
jj bookmark create kefir -r <change-id>
```

Si on ne précise pas le `-r`, la bookmark sera créé a l'endroit ou on se trouve
actuellement.

Il est assez fréquent de vouloir créer la bookmark juste avant notre position
actuelle, on peut donc écrire :
```bash
jj bookmark create kefir -r @-
```

### Déplacer une bookmark déjà existante

Puisque les bookmarks ne se déplacent pas automatiquement en suivant le dernier
commit on va souvent devoir les déplacer juste avant de pousser les changements
en ligne.

On peut le faire avec la commande `jj bookmark move`. Le reste de la syntaxe
est identique. Attention cependant, pour déplacer la bookmark **avant** notre
position actuelle (comme on ferait avec un `@-`) il faut rajouter le flag
`--allow-backwards` (ou `-B` pour la version courte).

La commande complète donnerait donc :
```bash
jj bookmark move kefir -r @- --allow-backwards
# Ou en abbrégé
jj b m kefir -r @- -B
```

> [!TIP]
> Il y a également la commande `jj bookmark set` qui existe pour déplacer
> **ou** créer une nouvelle bookmark si elle n'existe pas déjà

## Manipuler des changes

La majorité des opérations qu'on va effectuer avec `jj` sont donc des
manipulations de l'arbre qui stocke les changes. (ou de l'historique pour `git`)

### Supprimer un change

La commande pour supprimer des changes s'appelle `abandon`.
Il faut préciser l'identifiant du change derrière.
La commande complète ressemblera donc à ;
```bash
jj abandon <identifiant-de-change>
```

Pour supprimer le travail courant par exemple, l'équivalent de
`git reset --hard HEAD`, on fait `jj abandon @`.

> [!TIP]
> Si on omet de préciser l'identifiant du change c'est par défaut `@` qui sera
> précisé. On peut donc écrire simplement `jj abandon`.

### Créer un change

Comme nous avons plus tôt, dans `jj` on est **toujours** entrain de modifier un
change. Ce qui veut dire que le change contenant notre travail courant existe
déjà.

#### Nommer le change courant

Avant d'en créer un nouveau on veut en général décrire le change courant via la
commande `jj describe`.
Comme dans `git` on peut écrire `-m` pour mettre un message rapide sous ouvrir
d'éditeur de texte.

#### Créer un change

Pour créer un nouveau change c'est la commande `jj new` qu'il faut exécuter.
Un nouveau change vide sera généré et notre position ira automatiquement dessus.

#### Créer et nommer un change en une seule commande

Pour nommer et créer un nouveau change vide en une seule commande on peut
utiliser `jj commit` (qui supporte également le `-m`) :
```bash
jj describe -m "hello"
jj new
# est strictement équivalent à
jj commit -m "hello"
```

Mais la commande `commit` permet de faire bien plus que ça, elle permet
également de choisir quel changement on souhaite intégrer au commit.
Par exemple en écrivant ce document je me suis souvenu de détails que je
voulais ajouter a la partie sur `git` a de nombreuses reprise alors que je
n'avais pas encore commit la partie sur `jj`. Au lieu de créer un commit qui
rajoute tout d'un coup je peux décider quelle lignes ajouter a mon commit avec
l'argument `--interactive` (ou `-i`).

> [!TIP]
> Il est également possible de rajouter un fichier entier sans utiliser le mode
> interactif en ajoutant simplement le chemin du ou des fichiers derrière la
> commande `commit` comme ça ; `jj commit -m "hello" path/to/a path/to/b`

### Éclater un change en deux

Une autre opération pratique consiste a séparer un change en deux quand on se
rends compte qu'on a mis trop de contenu dans un change.
On effectue cette opération avec la commande `jj split`. On peut spécifier
n'importe quel identifiant de change derrière. Si on ne précise rien alors
c'est `@` qui sera splitté.

> [!TIP]
> Cela veut dire que la commande `jj commit -i` est équivalente à la commande
> `jj split` puisque les deux commandes reviennent a split le changement
> courant

## Merger plusieurs changes

Évidemment on ne parle pas de branche ici puisque que `jj` ne travaille pas
avec des branches mais simplement de changes.
Pour faire l'équivalent d'un `git merge` en fast forward dans `jj` on va
juste déplacer la bookmark, il n'y a pas besoin de faire un `merge`.
Mais dans les cas où l'historique a divergé, on va créer un `merge` change qui
va fusionner plusieurs changes.

Dans `jj` il n'y a pas d'opération particulière pour exprimer un "merge", on
va plutôt parler de change qui aurait plusieurs parents.
Par exemple imaginons cette historique :
```
B   C
 \ /
  A
```

Pour créer un change de merge entre `B` et `C` on lance la commande ;
```bash
jj new B C
```

et on obtient l'historique suivant :

```
  D
 / \
B   C
 \ /
  A
```

> [!NOTE]
> Contrairement a `git`, `jj` peut merger plus que deux changes en une fois
> tout simplement en précisant plus que deux changes : `jj new A B C D`

## Rebaser des changements

Comme avec `git` il arrive qu'on veuille changer la base d'un change.

Pour déplacer un seul change on va devoir spécifier :
- Le commit "source", celui que l'on veut déplacer
- Et le commit "destination" celui que l'on veut utiliser comme nouvelle base

Par exemple voilà le commande a exécuter pour transformer l'historique
ci-dessous :
```bash
jj rebase --source M --destination O
```

```
O           N
|           |
| N         M
| |         |
| M         O
| |    =>   |
| | L       | L
| |/        | |
| K         | K
|/          |/
J           J
```

## Copier un unique changement

Il n'y a pas de commande `cherry-pick` dans `jj` mais il y a une commande
`duplicate` qui permet, comme son nom l'indique, de dupliquer des changes.

Par défaut `jj` va dupliquer le change courant `@` et nous demande de spécifier
la `destination`.

L'équivalent de la commande `git cherry-pick abcdef` serait donc
```bash
jj duplicate abcdef -d @
```

## Annuler des erreur et remettre le projet dans son état avant une commande

L'une des forces de `jj` c'est qu'il versionne également l'historique des
opérations que vous avez fait avec.

La majorité des opérations se passent derrière la sous commande `jj operation`
(ou `jj op` pour la version courte).

Avec la commande `jj op list` vous pouvez lister toutes les commandes `jj` que
vous avez fait dans ce projet.
Chaque opération possède un identifiant que vous pouvez ensuite utiliser pour
restaurer a un état précédent avec `jj op restore <identifiant>`.

Pour annuler rapidement la dernière commande que vous venez de faire il existe
également la commande `jj undo`.

## Visualiser l'état du projet

Il y a plusieurs manière de voir dans quel état on se trouve.

### Voir l'historique

Avec la commande `jj log` on visualise l'historique.
`jj` va surtout se concentrer sur le bout des branches de l'arbre. Pour voir
tous les changes sans exception vous pouvez préciser les révisions que vous
souhaitez voir avec en sélectionnant des changements avec l'argument `-r`.
```bash
jj log -r ::
```

Affiche tous les changes sans exception.

Un changement ressemble a ça :
```
@  znonkozz irevoire@protonmail.ch 2025-11-06 15:05:36 8566ed46
│  (no description set)
○  zpxqrvnq irevoire@protonmail.ch 2025-11-06 01:06:47 main* git_head() 48ebe8c5
│  Configure the code block to be bash
```

Le `@` indique que c'est notre position actuelle dans l'historique.
À la place du `@` on peut également voir le symbole `○` qui indique que le
change n'as pas encore été mis en ligne.
Et finalement le dernier symbole possible est le `◆`. Il indique que ce
changement a été mis en ligne. Lorsqu'on essaie de modifier un de ces
changement `jj` va nous avertir qu'on risque de modifier un changement que
quelqu'un d'autre a déjà téléchargé localement sur son ordinateur.

L'identifiant directement après, `znonkozz` ici, indique l'identifiant du
change.
Ensuite on peut voir mon mail puis la date et l'heure de création du change.
Directement après il peut y avoir la liste des bookmarks qui pointent sur ce
change. Sur mon second change on voit `main` par exemple. Derrière `jj` nous
montre aussi où se trouve la `HEAD` de `git` avec `git_head()`.

Et finalement l'identifiant du commit du point de vu de `git`.

> [!CAUTION]
> Il est possible d'utiliser l'identifiant du commit dans les commandes de `jj`
> mais je n'ai pas bien compris ce que ça fait. Ça cause beaucoup d'erreurs en
> tout cas donc je conseille de ne jamais l'utiliser et d'uniquement utiliser
> les identifiants de **changes**

### Voir l'état du change courant

Avec `jj status` (ou `jj st`) on peut voir l'état courant du projet :
```
Working copy changes:
M vcs.md
Working copy  (@) : znonkozz b94a4837 (no description set)
Parent commit (@-): zpxqrvnq 48ebe8c5 main* | Configure the code block to be bash
```

D'abord `jj` nous montre tous les fichiers qui ont été **M**odifié, ici
uniquement `vcs.md`.
Ensuite la `working copy` représente le changement courant que nous somme
entrain de modifier et de créer.
Et le change/commit précédent.

### Voir le contenu d'un changement

Pour voir le changement courant, `@`. On dispose des commandes ;
- `jj show`, équivalent de `git show`, il peut être suivi de l'identifiant d'un
	change., il peut être suivi de l'identifiant d'un change si on ne veut pas
	voir le courant.
- `jj diff`, équivalent de `git diff` qui peut être suivi du chemin d'un
	fichier. On peut également préciser un identifiant avec `-r <identifiant>`

## Refaisons l'exemple du début avec `jj`

Avant de terminer on va refaire l'exemple de luna mais avec `jj` cete fois ci
et en détaillant toutes les commandes.

### D'abord, la création du projet

Après avoir créé un dossier et s'être déplacé dedans, on va créer le `.git` et
le `.jj` avec la commande `jj git init`.

### Notre premier change

Ensuite luna va créer son premier fichier, `truc`, avec le texte :
```
Okami est le plus beau des chiens
```

Il n'y a pas besoin de le `git add` puisque `jj` tracke automatiquement tous
les fichiers du projet. On peut le voir en faisant `jj st` :
```
Working copy changes:
A truc
Working copy  (@) : nnlpvvol 694718d2 (no description set)
Parent commit (@-): zzzzzzzz 00000000 (empty) (no description set)
```

Le fichier `truc` est marqué avec un `A` pour "addition".

Maintenant on créé le changement avec `jj commit -m "init commit"`

### Visualiser notre change

Contrairement a `git`, comme `jj commit` a créé un nouveau change si on lance
juste la commande `jj show` c'est le changement courant qui sera affiché :
```
Commit ID: 7968cd1bf3dac34871741e3e08a2945d63f6157a
Change ID: qloozloswpxoxpqlyxnuyypzuqymnxvn
Author   : Tamo <irevoire@protonmail.ch> (2025-11-06 15:47:50)
Committer: Tamo <irevoire@protonmail.ch> (2025-11-06 15:47:50)

    (no description set)
```

Le changement courant vient d'être créé et est vide.
On peut le voir avec `jj log` :
```
@  qloozlos luna@okami.dog 2025-11-06 15:47:50 7968cd1b
│  (empty) (no description set)
○  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 git_head() a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

Le changement qu'on voulait voir était celui avec l'identifiant `nnlpv` ou
également `@-` :
```
% jj show @-
Commit ID: a2bc9be5c5b6c436210f89254a84bea7860ac1e8
Change ID: nnlpvvolynpwrzrnwqzxswxppznssmyo
Author   : Luna <luna@okami.dog> (2025-11-06 15:45:10)
Committer: Luna <luna@okami.dog> (2025-11-06 15:47:50)

    init commit

Added regular file truc:
        1: Okami est le plus beau des chiens
```

### Luna créé sa bookmark et la mets en ligne

Elle fait la bookmark avec `jj b s main -r @-` (ou
`jj bookmarks set main -r @-` en version complète)

Puis elle mets son projet en ligne avec `jj git push`.

### Tamo et Sam font leurs changements

Tamo, après avoir cloné le projet de son coté avec la commande `jj git clone`
change le contenu du fichier `truc` et créé un commit avec
`jj commit -m "Add the kef"` et sam créé le siens avec
`jj commit -m "add echo"`.

### Luna souhaite récupérer leurs changements avec un merge

Voilà l'état du projet avec `git log` ;
```
○  vzrnssnl sam@echo.dog 2025-11-06 16:04:39 383618fd
│  add echo
│ ○  qloozlos irevoire@protonmail.ch 2025-11-06 15:58:11 6547aa54
├─╯  Add the kef
@  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 main a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

Contrairement a `git` il n'y a pas besoin de faire un premier `git merge` avec
le commit de tamo. Si on souhaite déplacer la bookmark `main` dessus on peut
simplement lancer la commande `jj b s main -r qlooz`.
Dans notre cas ce que l'on souhaite c'est de créer un change de merge entre
les changes de sam et tamo, on va donc créer un nouveau change avec leurs
changes comme parents :
```bash
jj new vzrn qloo
```

`jj` nous indique maintenant qu'il y a un conflit, on peut le voir en faisant
`jj log` :
```
@    tmopwmyn luna@okami.dog 2025-11-06 16:09:04 01b16556 conflict
├─╮  (empty) (no description set)
│ ○  qloozlos irevoire@protonmail.ch 2025-11-06 15:58:11 6547aa54
│ │  Add the kef
○ │  vzrnssnl sam@echo.dog 2025-11-06 16:04:39 git_head() 383618fd
├─╯  add echo
○  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 main a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

`jj` propose la commande `jj resolve` pour résoudre le conflit, mais
puisqu'elle n'as qu'une granularité au niveau de la ligne c'est plus
simple d'ouvrir le fichier manuellement et régler le conflit :
```
<<<<<<< Conflict 1 of 1
+++++++ Contents of side #1
Okami et echo sont les plus belle chiennes du monde
%%%%%%% Changes from base to side #2
-Okami est le plus beau des chiens
+Okami et kefir sont les plus beau chiens du monde
>>>>>>> Conflict 1 of 1 ends
```

Le format est un peu différent de celui de `git`.
Après avoir résolu le conflit `jj` le détecte automatiquement et on peut
faire un commit contenant la résolution du conflit avec
`jj commit -m "merge echo and kefir"`.

Elle peut ensuite déplacer la bookmark `main` sur le commit de merge et la
mettre en ligne avec la commande `jj b s main -r @- -B` puis `jj git push`.

Voilà l'historique après avoir déplacé la bookmark :
```
@  wtpnnsws luna@okami.dog 2025-11-06 16:14:02 00db1545
│  (empty) (no description set)
○    tmopwmyn luna@okami.dog 2025-11-06 16:14:02 main git_head() d1ef12fe
├─╮  merge echo and kefir
│ ○  qloozlos irevoire@protonmail.ch 2025-11-06 15:58:11 6547aa54
│ │  Add the kef
○ │  vzrnssnl sam@echo.dog 2025-11-06 16:04:39 383618fd
├─╯  add echo
○  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

### Luna souhaite récupérer leurs changements avec un rebase

> [!TIP]
> Avant de faire quoi que ce soit je remet le projet dans son état avant le
> merge.
> Je commence par retrouver l'identifiant de la dernière commande avant le
> `jj new vzrn qloo`, en l'occurence c'était `105e289c6b66` chez moi. Puis je
> lance la commande `jj operation restore 105e289c6b66`.

Après un `jj log` voilà l'état du projet :
```
○  vzrnssnl sam@echo.dog 2025-11-06 16:04:39 383618fd
│  add echo
│ ○  qloozlos irevoire@protonmail.ch 2025-11-06 15:58:11 6547aa54
├─╯  Add the kef
@  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 main a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

Cette fois, au lieu de créer un change de merge on va plutôt changer la base
du change de sam (`vzrn`) avec la commande :
```bash
jj rebase --source vzrn --destination qlooz
```

Voilà l'historique obtenu en résultat :
```
×  vzrnssnl sam@echo.dog 2025-11-06 16:40:29 691b022b conflict
│  add echo
○  qloozlos irevoire@protonmail.ch 2025-11-06 15:58:11 6547aa54
│  Add the kef
@  nnlpvvol luna@okami.dog 2025-11-06 15:47:50 main a2bc9be5
│  init commit
◆  zzzzzzzz root() 00000000
```

Après avoir réglé le conflit on peut a nouveau déplacer la bookmark et mettre
les changements en ligne avec `jj b s main -r vzrn` puis `jj git push`.

> [!NOTE]
> Contrairement a `git` tu peux voir que cette fois ci nous avons déplacé un
> change mais son identifiant n'as pas changé, il s'appelle toujours `vzrn...`,
> l'identifiant du commit a droite a changé en revanche.

## jj FAQ

### Quand je fais une commande le résultat est illisible

Par défaut `jj` essaie d'utiliser le "pager" par défaut qui ne supporte pas
les couleurs. On peut corriger ce problème en utilisant le "pager" proposé
par `jj` directement avec cette commande :

```bash
jj config set --user ui.pager :builtin
```

### Je reçois l'erreur `Commit xxx is immutable`

Cela signifie que tu essaies de modifier un commit qui a déjà été mis en ligne.
Si tu le modifie alors qu'une autre personne a téléchargé ce commit localement
et rajouté du travail par dessus elle risque d'avoir des conflits juste en
faisant un `jj git fetch`.
Si tu es sûr de ce que tu fais tu peux rajouter l'argument `--ignore-immutable`
pour ignorer la protection et avoir le droit de modifier le commit comme tu le
souhaite.

# FAQ générale pour `git` et `jj`

## Je n'arrive pas a utiliser l'éditeur de texte ouvert par défaut

`git` et `jj` utilisent la variable d'environnement `$EDITOR` pour décider de
quel éditeur ouvrir.
Par exemple pour utiliser `vim` comme éditeur de texte vous pouvez écrire la
commande `export EDITOR=vim`.
