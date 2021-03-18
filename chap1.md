# Trad 1 - Beyond the basic stuff with python 

S'il-vous-plaît, n'anthropomorphisez par les ordinateurs, ils trouvent ça très irritant. Quand un ordinateur affiche une message d'erreur, ce n'est pas parce que vous l'avez offensé. Les ordinateurs sont les outils les plus sophistiqués avec lesquels le commun des mortels interagit, mais ils restent des outils.

Mais c'est facile de rejeter la faute sur ces outils. Comme une grande partie de l'apprentissage de la programmation se fait en autodidacte, on se sent souvent nul·le quand on est forcé·e de consulter internet plusieurs fois dans la journée pour résoudre ses problèmes, même quand ça fait des mois qu'on apprend à programmer en Python. Mais même les ingénieur·es logiciel professionnel·les font des recherches sur internet ou consultent la documentation pour trouver des réponses à leurs questions.

A moins d'avoir les ressources financières ou sociales suffisantes pour payer un tuteur personnel qui répondra à toutes vos questions, vous ne pouvez que compter sur votre ordinateur, les moteurs de recherche et votre persévérance. Heureusement, quelqu'un a sûrement déjà posé votre question. Pour un·e programmeur·euse, être capable de trouver des réponses à ses questions soi-même est beaucoup plus important que n'importe quel algorithme ou structure de données. Ce chapitre vous montrera comment développer cette compétence essentielle.

# Comprendre les messages d'erreur de Python
Quand un·e développeur·euse est confronté·e au mur de jargon technique d'un message d'erreur, le premier instinct est de l'ignorer complètement. Mais dans ce message d'erreur ce trouve la raison pour laquelle votre programme ne marche pas. Trouver cette raison requiert deux étapes : examiner la trace d'appels et rechercher le message d'erreur sur un moteur de recherche.

## Examiner les traces d'appels
Les programmes en Python plantent quand le code lève une exception qui n'est pas gérée par une instruction `except`. Quand cela arrive, Python affiche le message lié  l'exception et une *trace d'appels* (*traceback* en anglais). On l'appelle aussi *trace de la pile d'exécution*, et son rôle est de montrer où dans le programme l'exception a été levée, et la chaîne d'appels de fonction qui a mené à cette exception.

Pour s'entraîner à lire des traces d'appels, saisis le programme suivant et enregistre le sous le nom *abcTraceback.py*. Les numéros de ligne servent de référence et ne font pas partie du programme.

``` 
   1. def a():
   2.     print('début de a()')
1  3.     b()  # Appelle b().
   4.
   5. def b():
   6.     print('Début de b()')
2  7.     c()  # Appelle c().
   8.
   9. def c():
  10.     print('Début de c()')
3 11.     42 / 0  # Ceci causera une erreur de division par zéro.
  12.
  13. a()  # Call a().
```
Dans ce programme, la fonction `a()` appelle `b()` (1), qui appelle `c()` (2). A l'intérieur de `c()`, l'expression `42/0` cause une erreur de division par zéro. Quand tu exécutes ce programme, l'output devrait ressembler à ça :

```
Début de a()
Début de b()
Début de c()
Traceback (most recent call last):
  File "abcTraceback.py", line 13, in <module>
    a()  # Appelle a().
  File "abcTraceback.py", line 3, in a
    b()  # Appelle b().
  File "abcTraceback.py", line 7, in b
    c()  # Appelle c().
  File "abcTraceback.py", line 11, in c
    42 / 0  # Ceci causera une erreur de division par zéro.
ZeroDivisionError: division by zero
```
Examinons cette trace d'appels ligne par ligne, en commençant par 
```
Traceback (most recent call last):
```
Ce message t'indique que ce qui suit est une trace d'appels. Le texte `most recent call last` signifie que que chaque appel de fonction est listé dans l'ordre, en commençant par le premier appel et en finissant avec l'appel le plus récent.

La ligne suivante montre le premier appel de fonction de la trace d'appels:
```
File "abcTraceback.py", line 13, in <module>
    a()  # Appelle a().
```
Ces deux lignes sont un résumé de *frame* (*frame summary*), et montrent l'information à l'intérieur d'un objet *frame*. Quand une fonction est appelée, les données contenues dans les variables locales ainsi que la position où retourner après la fin de l'appel de la fonction sont stockés dans un objet *frame*. Les objets *frame* contiennent les variables locales et d'autres données associées à un appel de fonction. Les objets *frame* sont créés quand la fonction est appelée, et détruits à la fin de la fonction. La trace d'appels montre le résumé de chaque *frame* jusqu'au plantage. On peut y lire que cet appel de fonction a lieu à la ligne 13 de *abcTraceback.py*, et que le texte `<module>` nous indique que cette ligne a une portée globale. La ligne 13 est affichée après deux espaces d'indentation.

Les quatre lignes suivantes sont les deux autres résumés de *frame* :
```
File "abcTraceback.py", line 3, in a
    b()  # Appelle b().
  File "abcTraceback.py", line 7, in b
    c()  # Appelle c().
```
`line 3, in a` signifie que `b()` a été appelé à la ligne 3, à l'intérieur de la fonction `a()`, ce qui a ensuite entraîné un appel de `c()` à la ligne 7, à l'intérieur de `b()`. Remarque bien que l'appel à `print()` aux lignes 2,6 et 10 n'apparaissent pas dans la trace d'appels, alors qu'ils ont été exécutés avant les appels de fonction. Seules les lignes contenant des appels de fonction conduisant à l'exception sont affichées dans la trace d'appels.

Le dernier résumé de *frame* montre la ligne qui a causé l'exception non gérée, suivie par le nom de l'exception et son message :
```
File "abcTraceback.py", line 11, in c
    42 / 0  # Ceci causera une erreur de division par zéro.
ZeroDivisionError: division by zero
```
Remarque que le numéro de ligne donné par la trace d'appels est la ligne où Python a détecté l'erreur. La vraie source du bug pourrait être quelque part avant cette ligne.

Les messages d'erreurs sont connus pour être courts et impénétrables : les trois mots `division by zero` ne signifient rien si tu ne sais pas que diviser un nombre par zéro est mathématiquement impossible et que c'est un bug courant. Dans ce programme, le bug n'est pas trop dur à trouver. En regardant la ligne de code dans le résumé de *frame*, il est facile de voir où l'erreur de division par zéro a lieu dans le code `42 / 0`.

Mais regardons un exemple plus ardu. Saisie le code suivant dans un éditeur de texte et enregistre le sous le nom *zeroDivideTraceback.py* :
```
def spam(nombre1, nombre2):
    return nombre1 / (nombre2 - 42)

spam(101, 42)
```
Quand tu exécutes ce programme, l'output devrait ressembler à ça :
```
Traceback (most recent call last):
  File "zeroDivideTraceback.py", line 4, in <module>
    spam(101, 42)
  File "zeroDivideTraceback.py", line 2, in spam
    return nombre1 / (nombre2 - 42)
ZeroDivisionError: division by zero
```
Le message d'erreur est le même, mais la division par zéro dans `return nombre1 / (nombre2 - 42)` n'est pas aussi évidente. Tu peux déduire qu'il y a une division en voyant l'opérateur `/` et que l'expression `(nombre1 - 42)` doit dont être évaluée à `0`. Cela te conduira à conclure que la fonction `spam()` plante dès que le paramètre `nombre2` vaut `42`.

Parfois la trace d'appels peut indiquer qu'une erreur est à la ligne d'après la vraie cause du bug. Par exemple, dans le programme suivant, il manque une parenthèse fermante à la première ligne :
```
print('Bonjour.'
print('Comment ça va?')
```
Mais le message d'erreur pour ce programme indique que le problème vient de la deuxième ligne :
```
  File "example.py", line 2
    print('How are you?')
        ^
SyntaxError: invalid syntax
```
C'est parce que l'interpréteur Python a remarqué l'erreur de syntaxe quand il a lu la deuxième ligne. La trace d'appels peut indiquer là où le problème a eu lieu, mais ce n'est pas toujours là où se trouve la cause du bug. Si le résumé de *frame* ne te donne pas assez d'information pour trouver le bug, ou si la vraie cause du bug n'est pas affichée par la trace d'appels, il faudra que tu ailles retracer toi-même ton programmer avec un débogueur ou avec des messages de suivi pour trouver la cause. Cela peut prendre longtemps. Une recherche sur internet avec le message d'erreur peut te donner des indices cruciaux pour trouver une solution beaucoup plus rapidement.

## Rechercher les messages d'erreur
Souvent les messages d'erreur sont si courts qu'ils ne sont même pas des phrases complètes. Comme les développeur·euses les rencontrent souvent, ce sont plus des rappels que des explications complètes. Si tu rencontres un message d'erreur pour la première fois, faire un copier-coller dans un moteur de recherche permet souvent de trouver une explication détaillée de l'erreur et de ses causes probables. La _Figure 1-1_ montre les résultats de la requête **python “ZeroDivisionError: division by zero”**. Inclure des guillemets autour du message d'erreur permet de trouver exactement cette expression, et l'ajout du mot *python* permet aussi de préciser la recherche.
![clipboard.png](T5XieT5Tv-clipboard.png)
Rechercher des messages d'erreur n'est pas tricher. Personne ne s'attend à ce que tu mémorises tous les messages d'erreur possible d'un langage de programmation. Les ingénieur·es logiciel professionnel·les font tous les jours des recherches sur internet pour trouver des réponses à leurs questions de programmation.

Il ne faut mieux pas inclure dans la recherche les parties du message d'erreur spécifiques à ton code. Par exemple, dans le message d'erreur suivant :
```
  >>> print(employDossier)
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
1 NameError: name 'employDossier' is not defined
  >>> 42 - 'bonjour'
  Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
2 TypeError: unsupported operand type(s) for -: 'int' and 'str'
```
Cet exemple a une coquille dans la variable `employDossier`, ce qui cause l'erreur 1. Comme l'identifiant `employDossier` dans `NameError: name 'employDossier' is not defined` est spécifique à ton code, il faut plutôt chercher **python “NameError: name” “is not defined”**. Dans la dernière ligne, les mots  `'int'` et `'str'` dans le deuxième message d'erreur semble faire référence aux valeurs `42` et `'bonjour'`, donc en tronquant la requête à **python “TypeError: unsupported operand type(s) for”** éviterait les parties spécifiques à ton code. Si ces recherches ne renvoient pas de résultats utiles, essaye avec le message d'erreur complet.

# Eviter les erreurs avec les linters
La meilleure manière de réparer ses erreurs est de ne pas les faire en premier lieu. Les logiciels Lint, ou *linters*, sont des applications qui analysent ton code source pour te prévenir d'erreurs potentielles. Leur nom fait référence aux petites peluches et débris qui s'amassent dans le filtre d'un sèche-linge. Même si un linter ne peut pas détecter toutes les erreurs, l'*analyse statique* (examiner du code source sans l'exécuter) peut identifier des erreurs fréquences causées par des fautes de frappe. (Le chapitre 11 explore l'utilisation d'indications de type pour l'analyse statique.) Beaucoup d'éditeurs de textes et d'environnements de développement (*IDE*) incorporent un linter qui tournent en tâche de fond et peut signaler les problèmes en temps réel, comme dans la _Figure 1-2_ :
![clipboard.png](IeW6faQ9U-clipboard.png)
![clipboard.png](-I4No3E4P-clipboard.png)
![clipboard.png](iHre5uG2U-clipboard.png)
_Figure 1-2_ : Un linter signale une variable non définie dans Mu, PyCharm et Sublime Text.

Les notifications presque instantanées fournies par un linter améliorent grandement la productivité. Sans linter il faudrait faire tourner le programme, le voir planter, lire la trace d'appel, et trouver la ligne dans le code source pour corriger une coquille. Et s'il y a plusieurs coquilles, ce cycle ne permettrait que d'en trouver une à la fois. Les linters peuvent signaler plusieurs erreurs en même temps, directement dans l'éditeur de texte, ce qui permet de voir la ligne où elles se trouvent.

Ton éditeur ou IDE n'a peut-être pas de linter, mais si tu peux installer des plug-ins, tu pourras sûrement trouver un linter. Souvent ces plug-ins utilisent un module nommé Pyflakes, ou un autre module, pour faire leur analyse. Tu peux installer Pyflakes sur https://pypi.org/project/pyflakes/ ou en exécutant `pip install --user pyflakes`. ça vaut largement le coup.

---

**NOTE**
Sous Windows, tu peux exécuter les commandes *python* et *pip*. Mais sur macOS et Linux, ces commandes sont pour Python 2 seulement, donc à la place tu devras faire *python3* et *pip3*. Souviens-toi en à chaque fois que tu vois *python* ou *pip* dans ce livre.

---

# Demander de l'aide en programmation
Quand les recherches et les linters n'ont pas pu t'aider à régler ton problème, tu peux demander de l'aide sur internet. 
