# Sanitisation en PHP


## Sanitiser ?! À quoi ça sert ?

En français, on pourrait traduire cela par "désinfecter". Quand les utilisateurs peuvent entrer des données comme dans un formulaire de contact, il est important de s'assurer qu'il ne s'agit pas d'une tentative d'attaque (comme par exemple, les injection sql) voire une tentative de piratage. C'est pour cela que nous allons désinfecter TOUTES les données entrées par l'utilisateur AVANT de les manipuler dans notre script. 


## Une méthode pratique
Il arrive parfois qu'on ait besoin de traiter un nombre important d'entrées dans un formulaire. Dans ce cas, il est préférable de réaliser la sanitization via la fonction `filter_input_array()`. Cette fonction est utile pour récupérer plusieurs valeurs sans avoir à appeler plusieurs fois la fonction `filter_input()`.

![Schéma Sanitization](https://github.com/LudovicPatho/Sanitization-en-PHP/blob/master/Sanitisation.png)


https://www.figma.com/file/EiwrtimqcaqxDZj2Lf7orn5U/Sanitisation

## Description  

```php
    mixed filter_input_array ( int $type [, mixed $definition [, bool $add_empty = true ]] )
```

### §type :
Une constante parmi `INPUT_GET`, `INPUT_POST`, `INPUT_COOKIE`, `INPUT_SERVER` ou `INPUT_ENV`. Elle permet de récupérer dans ce cas-ci les inputs envoyés par la méthode POST.

### filter :
L'ID du filtre à appliquer. La page du manuel PHP liste les filtres disponibles. Si le filtre n'est pas spécifié, aucun filtre n'est appliqué (en effet, par défaut `FILTER_DEFAULT` sera utilisé, ce qui est équivalent à `FILTER_UNSAFE_RAW`, ce qui  revient à n'avoir aucun filtre en place par défaut).  Les filtres existants : http://php.net/manual/fr/filter.filters.sanitize.php

### Valeurs de retour :

Valeur de la variable demandée en cas de succès, `FALSE` si le filtre échoue, ou `NULL` si la variable n'est pas définie. Si le drapeau `FILTER_NULL_ON_FAILURE` est utilisé, la fonction retournera `FALSE` si la variable n'est pas définie et `NULL` si le filtre échoue.
    
## Exemple :


Imaginons un formulaire avec les inputs suivants :


```html
    <form>
		<input type="text" name="first_name" >
		<input type="text" name="last_name" >
		<input type="email" name="mail">
		<input type="tel" name="phone">
		<input type="url" name="url">
		<input type="text" name="subject">
		<input type="text" name="message">
    </form>
```
Dans un premier temps, nous allons créer un tableau qui contient les filtres dont nous avons besoin. Vous trouverez tous les filtres ici : http://php.net/manual/fr/filter.filters.sanitize.php. Pour les clés du tableau, il est important de conserver les noms de vos inputs. 

```php
    $options = array(
    'first_name' 	=> FILTER_SANITIZE_STRING,
    'last_Name' 	=> FILTER_SANITIZE_STRING,
    'mail' 		=> FILTER_VALIDATE_EMAIL,
    'phone' 		=> FILTER_SANITIZE_NUMBER_INT,
    'url' 		=> FILTER_SANITIZE_URL,
    'subject' 		=> FILTER_SANITIZE_STRING,
    'message' 		=> FILTER_SANITIZE_STRING);
```

Ensuite on crée une variable `$result` avec la fonction `filter_input_array`. Comme son nom l'indique, la fonction va également retourner un Array() qui sera associatif. Cette fonction doit recevoir au moins deux arguments: ici `INPUT_POST` pour récupérer les valeurs encodées dans le champs et la variable `$option` pour appliquer les filtres.

```php  
  $result = filter_input_array(INPUT_POST, $options);  
```
    
Et voilà, tous vos champs ont été sanitisés ! En cas de problème, la fonction retourne un `NULL` si la variable est vide et un `FALSE` s'il y a eu une erreur.

```php
if ($result != null AND $result != FALSE) {

	echo "Tous les champs ont été nettoyés !";

} else {

	echo "Un champ est vide ou n'est pas correct!";

}
```
     

Pour afficher les résultats, on fait une boucle `foreach` avec la variable `$result` :  

```php     
     foreach($options as $key => $value) 
     {
        $result[$key]=trim($result[$key]);
     }
```
Puisque ``` $result``` est un tableau, on peut afficher les résultats de manière habituelle.

```php
	echo $result['first_name'];
	echo $result['last_name'];
	echo $result['email'];
	echo $result['phone'];
	//Etc...
```

## Sources

* Vous trouverez tous les filtres disponibles ici : http://php.net/manual/fr/filter.filters.sanitize.php

* La documentation sur la fonction filter_input() : http://php.net/manual/fr/function.filter-input.php

* La documentation sur la fonction filter_input_array() : http://php.net/manual/fr/function.filter-input-array.php
