# TP 03 : Shell Bash 

## Exercice 1 : Paramètres


Pour cela, on utilise les variables suivantes :
- `$#` : le nombre de paramètres, c'est comme si tu demandais combien de trucs l'utilisateur a passé en argument au script.
- `$0` : le nom du script, c'est toujours le premier argument automatique, c'est utile pour savoir quel fichier est en train d'être exécuté.
- `$3` : le troisième paramètre (si disponible), si tu as passé au moins trois arguments.
- `$@` : la liste de tous les paramètres, c'est pour récupérer tous les arguments en une seule fois.

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

# Affiche le nombre de paramètres passés au script
echo "Bonjour, vous avez rentré $# paramètres"

# Affiche le nom du script
echo "Le nom du script est $0"

# Affiche la valeur du 3e paramètre (s'il y en a au moins trois)
echo "Le 3ème paramètre est $3"

# Affiche tous les paramètres passés au script
echo "Voici la liste des paramètres : $@"
```

**Explication :** 
Ce script permet de visualiser les informations principales d'un script bash, notamment les arguments qu'on lui passe. C'est super utile pour comprendre ce que l'utilisateur a entré. Par exemple, si tu exécutes le script avec `./mon_script.sh arg1 arg2 arg3`, tu verras les infos comme le nombre de paramètres (3), le nom du script, etc.

Resultat :

```bash
root@serveur1:~# ./analyse.sh arg1 arg2 arg3
Bonjour, vous avez rentré 3 paramètres
Le nom du script est ./analyse.sh
Le 3ème paramètre est arg3
Voici la liste des paramètres : arg1 arg2 arg3
```

## Exercice 2 : Vérification du nombre de paramètres

**Objectif :** Créer un script qui vérifie que l'utilisateur a bien passé deux arguments. Si c'est le cas, le script les concatène et affiche le résultat, sinon il affiche un message d'erreur.

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

# Vérifie que le nombre de paramètres est égal à 2
if [ $# -eq 2 ]
then
  CONCAT="$1$2"
  echo $CONCAT
else
  echo "Erreur, vous devez rentrer 2 paramètres."
fi
```

**Explication :** 
Le script commence par vérifier combien d'arguments ont été passés avec `$#`. Si c'est égal à 2, il va juste concaténer les deux arguments (c'est-à-dire les coller ensemble) et les afficher. Si tu passes, par exemple, `./mon_script.sh bon jour`, il affichera `bonjour`. Si tu passes moins ou plus de deux arguments, il te dira qu'il faut exactement 2 arguments.

Resultat :

```bash
root@serveur1:~# ./concat.sh bon jour
bonjour
```

## Exercice 3 : Type de fichier et droits d'accès

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

# Vérifie qu'un argument a été passé
if [ $# -eq 1 ]
then
  if [[ -d $1 ]]
  then
    path_file="répertoire"
  elif [[ -f $1 ]]
  then
    path_file="fichier ordinaire"
    if [ -s $1 ]
    then
      path_file="$path_file qui n'est pas vide"
    else
      path_file="$path_file vide"
    fi
  else
    echo "$1 n'est pas valide"
    exit 1
  fi

  echo "Le fichier $1 est un $path_file"

  # Vérifie les permissions
  permission=""
  if [ -r $1 ]
  then
    permission="lecture"
  fi
  if [ -w $1 ]
  then
    permission="$permission écriture"
  fi
  if [ -x $1 ]
  then
    permission="$permission exécution"
  fi

  echo "$1 est accessible par $USER en $permission"
else
  echo "Erreur, vous devez rentrer 1 paramètre."
fi
```

**Explication :** 
Le script commence par vérifier qu'il y a bien un argument. Ensuite, il regarde si l'argument est un répertoire ou un fichier.
- Si c'est un répertoire, il dit juste "répertoire".
- Si c'est un fichier, il précise s'il est vide ou non.
Puis il vérifie les droits d'accès (lecture, écriture, exécution) et les affiche à la fin. Par exemple, si tu passes un fichier auquel tu as tous les droits, il affichera quelque chose comme "lecture écriture exécution".

## Exercice 4 : Afficher le contenu d'un répertoire



### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

if [ $# -eq 1 ]
then
  if [[ -d $1 ]]
  then
    echo "####### Fichiers dans $1"
    for file in "$1"/*
    do
      if [[ -f "$file" ]]
      then
        echo $file
      fi
    done

    echo "####### Répertoires dans $1"
    for file in "$1"/*
    do
      if [[ -d "$file" ]]
      then
        echo $file
      fi
    done
  else
    echo "Erreur, vous devez rentrer un répertoire et non un fichier ordinaire."
  fi
else
  echo "Erreur, vous devez rentrer 1 paramètre."
fi
```

**Explication :** 
Le script commence par vérifier que l'utilisateur a passé un seul argument, qui doit être un répertoire. Ensuite, il sépare ce qu'il trouve à l'intérieur :
- Il liste d'abord les fichiers simples.
- Ensuite, il liste les sous-répertoires.
Par exemple, si tu exécutes `./listdir.sh /mon/repertoire`, il affichera d'abord les fichiers puis les répertoires.

Resultat : 

```bash
####### fichier dans /mon/repertoire/
/mon/repertoire/config-3.16.0-4-amd64
/mon/repertoire/initrd.img-3.16.0-4-amd64
/mon/repertoire/System.map-3.16.0-4-amd64
/mon/repertoire/vmlinuz-3.16.0-4-amd64
####### repertoires dans /mon/repertoire/
/mon/repertoire/grub
```

## Exercice 5 : Lister les utilisateurs


### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

if [ $# -eq 0 ]
then
  awk -F':' '$3>99 {print $1 " UID: "$3}' /etc/passwd | column -t | grep -v nobody
else
  echo "Erreur, vous ne devez pas entrer de paramètres"
fi
```

**Explication :** 
Le script utilise `awk` pour parcourir le fichier `/etc/passwd`. Il va chercher les utilisateurs ayant un UID (User ID) supérieur à 100, ce qui correspond souvent aux utilisateurs normaux (les UID inférieurs à 100 sont souvent des utilisateurs système). Ensuite, il affiche leur nom et leur UID.
Par exemple, si tu exécutes le script sans argument (`./listlog.sh`), il affichera tous les utilisateurs qui ont un UID supérieur à 100.

## Exercice 6 : Mon utilisateur existe-t-il ?

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

# Vérifie qu'il y a bien un seul argument
if [ $# -eq 1 ]
then
  dir="/etc/passwd"
  # Utilisation de awk pour vérifier si le paramètre correspond au login ou à l'UID
  awk -v var=$1 -F ':' '{
    if (var == $1 || var == $3)
      print "Cet utilisateur nommé " $1 " existe avec comme UID : " $3
  }' $dir
else
  echo "Erreur, vous devez rentrer un UID valide ou un nom d'utilisateur."
fi
```

**Explication :** 
Le script prend un argument qui peut être un login ou un UID et parcourt le fichier `/etc/passwd` avec `awk`. Si l'argument correspond soit au nom d'utilisateur (`$1`), soit à l'UID (`$3`), il affiche que l'utilisateur existe avec son UID. Par exemple, si tu passes `./searchuser.sh root`, il affichera comme résultat "Cet utilisateur nommé root existe avec comme UID : 0".

## Exercice 7 : Création d'utilisateur

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

ROOT_UID=0
SUCCESS=0
E_USEREXISTS=70

# Vérifie que le script est lancé en tant que root
if [ "$UID" -ne "$ROOT_UID" ]
then
  echo "Must be root to run this script."
  exit 1
fi  

# Vérifie qu'il y a bien deux arguments (nom d'utilisateur et mot de passe)
if [ $# -eq 2 ]
then
  username=$1
  pass=$2

  # Vérifie si l'utilisateur existe déjà
grep -q "$username" /etc/passwd
  if [ $? -eq $SUCCESS ]
  then  
    echo "User $username does already exist."
    echo "Please choose another username."
    exit $E_USEREXISTS
  fi  

  # Création de l'utilisateur
  useradd -p $(openssl passwd -1 "$pass") -d /home/"$username" -m -g users -s /bin/bash "$username"

  echo "The account is setup."
else
  echo "This program needs 2 arguments, you have given $#."
  echo "You have to call the script $0 username and password."
fi

exit 0
```

**Explication :** 
Le script commence par vérifier que tu es bien root avec la variable `$UID` (car seul root peut créer des utilisateurs). Ensuite, il vérifie si deux arguments ont été passés (le nom d'utilisateur et le mot de passe). Si l'utilisateur existe déjà, il te demande de choisir un autre nom. Sinon, il crée l'utilisateur avec `useradd`. Note que pour le mot de passe, on utilise `openssl` pour le chiffrer avant de l'ajouter.

## Exercice 8 : Lecture au clavier


### Explication :
La commande `read` permet de lire une entrée utilisateur et de l'affecter à une variable. Voici un exemple simple :
```bash
echo -n "Entrer votre nom: "
read nom
echo "Votre nom est $nom"
```

- La commande `file` permet d'afficher des informations sur le contenu d'un fichier.
- La commande `more` permet d'afficher des fichiers texte page par page. La commande `less` est similaire mais un peu plus sophistiquée (parce que "less is more").

**Questions :**
1. Comment quitter `more` ? Tape `q`.
2. Comment avancer d’une ligne ? Tape `Entrée`.
3. Comment avancer d’une page ? Tape `Espace`.
4. Comment remonter d’une page ? Utilise `b`.
5. Comment chercher une chaîne de caractères ? Tape `/` suivi de la chaîne recherchée.
6. Comment passer à l’occurrence suivante ? Tape `n`.

**Objectif supplémentaire :** Écrire un script qui propose à l'utilisateur de visualiser page par page chaque fichier texte d'un répertoire spécifié en argument. Le script affichera pour chaque fichier texte la question "Voulez-vous visualiser le fichier machintruc ?". En cas de réponse positive, il lancera `more`, avant de passer à l'examen du fichier suivant.

### Code :
```bash
#!/bin/bash
# Auteur : Ibrahima Sory Diallo

if [ $# -eq 1 ]
then
  if [[ -d $1 ]]
  then
    for file in "$1"/*
    do
      if [[ -f "$file" ]] && file "$file" | grep -q "text"
      then
        echo -n "Voulez-vous visualiser le fichier $file ? (o/n) : "
        read reponse
        if [ "$reponse" == "o" ]
        then
          more "$file"
        fi
      fi
    done
  else
    echo "Erreur, vous devez spécifier un répertoire."
  fi
else
  echo "Erreur, vous devez rentrer 1 paramètre."
fi
```

**Explication :**
Le script vérifie que l'argument est un répertoire. Ensuite, il parcourt chaque fichier du répertoire et utilise la commande `file` pour vérifier s'il s'agit d'un fichier texte. Pour chaque fichier texte, il demande à l'utilisateur s'il veut le visualiser. Si la réponse est "o" (oui), il lance la commande `more` pour afficher le contenu du fichier.

## Exercice 9 : Appréciation


### Code :
```bash
#!/bin/bash 
# Auteur : Ibrahima Sory Diallo

note=0 
i=0 

until [ "$note" -lt 0 ]; do 
  echo "Entrez votre note (q pour quitter) :"
  read -r note 
  if [ "$note" = "q" ]; then 
    note=-1 
    echo "au revoir !"
  elif [ "$note" -ge 16 ]; then 
    echo "très bien"
  elif [ "$note" -ge 14 ]; then 
    echo "bien"
  elif [ "$note" -ge 12 ]; then 
    echo "assez bien"
  elif [ "$note" -ge 10 ]; then 
    echo "moyen"
  elif [ "$note" -ge 0 ]; then 
    echo "insuffisant"
  else 
    echo "au revoir !"
  fi 
  let i=$i+1 
done 

if [ "$i" -le 0 ]; then 
  let i=1 
fi 
```

**Explication :** 
Le script demande à l'utilisateur d'entrer une note, puis il donne une appréciation en fonction de la note. Si tu veux quitter, tu tapes `q`. Par exemple, si tu entres 15, le script affiche "bien". Le programme continue tant que tu n'as pas saisi `q`.

