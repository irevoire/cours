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
```
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
```
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
```
git push origin kefir
```

Où `origin` peut être modifié par n'importe quelle remote et `kefir` par
n'importe quel nom de branche.

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
```
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
```
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
```
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
```
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

## jj
