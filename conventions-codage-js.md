% Conventions de codage Javascript

Syntaxe générale
---------------------

## Objects

  - Utiliser la syntaxe litérale pour la création d'objets.

    ```javascript
    // bad
    var item = new Object();

    // good
    var item = {};
    ```

  - Eviter d' utiliser de [mots réservés](http://es5.github.io/#x7.6.1) comme clé d'objet.

    ```javascript
    // bad
    var superman = {
      default: { clark: 'kent' },
      private: true
    };

    // good
    var superman = {
      defaults: { clark: 'kent' },
      hidden: true
    };
    ```
## Arrays

  - Utiliser la syntaxe litérale pour la création de tableaux.

    ```javascript
    // bad
    var items = new Array();

    // good
    var items = [];
    ```

  - Utiliser la méthode `Array#push` au lieu d'une affectation direct d'un élément du tableau.

    ```javascript
    var someStack = [];


    // bad
    someStack[someStack.length] = 'abracadabra';

    // good
    someStack.push('abracadabra');
    ```

  - Utiliser `Array#slice` pour copier un tableau. [jsPerf](http://jsperf.com/converting-arguments-to-an-array/7)

    ```javascript
    var len = items.length;
    var itemsCopy = [];
    var i;

    // bad
    for (i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // good
    itemsCopy = items.slice();
    ```

  - Utiliser `Array#slice` pour convertir un simili-tableau vers un tableau.

    ```javascript
    function trigger() {
      var args = Array.prototype.slice.call(arguments);
      ...
    }
    ```
## Strings

  - Utiliser les simples quotes `'...'` pour les chaines de caractères.

    ```javascript
    // bad
    var name = "Bob Parr";

    // good
    var name = 'Bob Parr';

    // bad
    var fullName = "Bob " + this.lastName;

    // good
    var fullName = 'Bob ' + this.lastName;
    ```

  - Utiliser `Array#join` pour concatener des chaines de manière programatique. [jsPerf](http://jsperf.com/string-vs-array-concat/2).

    ```javascript
    var items;
    var messages;
    var length;
    var i;

    messages = [{
      state: 'success',
      message: 'This one worked.'
    }, {
      state: 'success',
      message: 'This one worked as well.'
    }, {
      state: 'error',
      message: 'This one did not work.'
    }];

    length = messages.length;

    // bad
    function inbox(messages) {
      items = '<ul>';

      for (i = 0; i < length; i++) {
        items += '<li>' + messages[i].message + '</li>';
      }

      return items + '</ul>';
    }

    // good
    function inbox(messages) {
      items = [];

      for (i = 0; i < length; i++) {
        // use direct assignment in this case because 
        // we're micro-optimizing.
        items[i] = '<li>' + messages[i].message + '</li>';
      }

      return '<ul>' + items.join('') + '</ul>';
    }
    ```

## Fonctions

  - Ne jamais déclarer une fonction dans un block (`if`, `while`, etc).

    ```javascript
    // bad
    if (currentUser) {
      function test() {
        console.log('Nope.');
      }
    }

    // good
    var test;
    if (currentUser) {
      test = function test() {
        console.log('Yup.');
      };
    }
    ```

  - Ne jamais nommer un paramètre `arguments`. Cela masquerait l'objet `arguments` qui est fourni dans le scope de toutes les fonctions.

    ```javascript
    // bad
    function nope(name, options, arguments) {
      // ...stuff...
    }

    // good
    function yup(name, options, args) {
      // ...stuff...
    }
    ```

## Properties

  - Utiliser le `.` pour accéder aux propriétés d'un objet.

    ```javascript
    var luke = {
      jedi: true,
      age: 28
    };

    // bad
    var isJedi = luke['jedi'];

    // good
    var isJedi = luke.jedi;
    ```

  - Utiliser la notation `[]` pour accéder à une propriété avec une variable.

    ```javascript
    var luke = {
      jedi: true,
      age: 28
    };

    function getProp(prop) {
      return luke[prop];
    }

    var isJedi = getProp('jedi');
    ```

## Variables

  - Toujours utiliser le mot clé `var` pour déclarer une variable. Son omission pollue le scope global.

    ```javascript
    // bad
    superPower = new SuperPower();

    // good
    var superPower = new SuperPower();
    ```
  - Assigner les variable au début de leur scope. Cela permet d'éviter les problèmes de déclaration et de hissage (hoisting) de variable.

    ```javascript
    // bad
    function() {
      test();
      console.log('doing stuff..');

      //..other stuff..

      var name = getName();

      if (name === 'test') {
        return false;
      }

      return name;
    }

    // good
    function() {
      var name = getName();

      test();
      console.log('doing stuff..');

      //..other stuff..

      if (name === 'test') {
        return false;
      }

      return name;
    }

    // bad - unnessary function call
    function() {
      var name = getName();

      if (!arguments.length) {
        return false;
      }

      this.setFirstName(name);

      return true;
    }

    // good
    function() {
      var name;

      if (!arguments.length) {
        return false;
      }

      name = getName();
      this.setFirstName(name);

      return true;
    }
    ```
## Règles de hissage (Hoisting)

Voici un rappel des règles de hissage des symboles dans le scope en javascript (hoisting).

  - Les déclarations de variables sont hissées en haut de leur scope, mais pas leur affectation.

    ```javascript
    // we know this wouldn't work (assuming there
    // is no notDefined global variable)
    function example() {
      console.log(notDefined); // => throws a ReferenceError
    }

    // creating a variable declaration after you
    // reference the variable will work due to
    // variable hoisting. Note: the assignment
    // value of `true` is not hoisted.
    function example() {
      console.log(declaredButNotAssigned); // => undefined
      var declaredButNotAssigned = true;
    }

    // The interpreter is hoisting the variable
    // declaration to the top of the scope,
    // which means our example could be rewritten as:
    function example() {
      var declaredButNotAssigned;
      console.log(declaredButNotAssigned); // => undefined
      declaredButNotAssigned = true;
    }
    ```

  - Les expressions de fonction annonymes hissent leur nom de variable, mais pas l'affectation de la fonction.

    ```javascript
    function example() {
      console.log(anonymous); // => undefined

      anonymous(); // => TypeError anonymous is not a function

      var anonymous = function() {
        console.log('anonymous function expression');
      };
    }
    ```

  - Les expression de fonction nommées hissent leurs variable, mais pas le nom ou le corps de la fonction.

    ```javascript
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      superPower(); // => ReferenceError superPower is not defined

      var named = function superPower() {
        console.log('Flying');
      };
    }

    // the same is true when the function name
    // is the same as the variable name.
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      var named = function named() {
        console.log('named');
      }
    }
    ```

  - Les déclaration de fonction hissent leur nom et le corps de la fonction.

    ```javascript
    function example() {
      superPower(); // => Flying

      function superPower() {
        console.log('Flying');
      }
    }
    ```

  - Pour plus d'information [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting) par [Ben Cherry](http://www.adequatelygood.com/).

## Operateurs de comparaison & Egalité

  - Utiliser `===` et `!==` plutôt que `==` et `!=`.

  - Les clause conditionnelles comme `if` statement évaluent leurs expression en utilisant la coercition de type avec la méthode abstraite `ToBoolean` qui suit toujours ces règles simples:

    + **Objects** sont évalués à **true**
    + **Undefined** est évalué à **false**
    + **Null** est évalué à **false**
    + **Booleans** sont évalués à **la valeur du booléen**
    + **Numbers** sont évalués à **false** si **+0, -0, ou NaN**, sinon **true**
    + **Strings** sont évalués à **false** si la chaine est vide `''`, sinon **true**

    ```javascript
    if ([0]) {
      // true
      // An array is an object, objects evaluate to true
    }
    ```

  - Utiliser les formes abrégées.

    ```javascript
    // bad
    if (name !== '') {
      // ...stuff...
    }

    // good
    if (name) {
      // ...stuff...
    }
    ```

  - Pour plus d'information [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) par Angus Croll.


## Indentation

 * Indentation avec **4 espaces**, pas de tabulations.

 * retours à la ligne après **140 charactères** par ligne max.

### Accolades

  - Ouverture en fin de ligne

    ```javascript
    if (val) {
        // ...
    }
    ```

  > Noter les espaces de part et d'autre de la condition.

  - Pour les constructions `if/else` :

    ```javascript
    if (val) {
        // ...
    } else {
        // ...
    }
    ```

  - Les blocs contenant une seule instruction doivent avoir des accolades. Cela permet d'éviter les erreurs et rend le code plus facile à maintenir.

    ```javascript
    // bad
    if (val)
        doSomething();

    // good
    if (val) {
        doSomething();
    }
    ```

  - Laisser un espace entre le mot clé `function` et les parenthèses. Cela permet de signifier que la function est anonyme.

    ```javascript
    function (val) {
        // fonction anonyme
    }
    ```

  - Laisser un espace entre 1 espace avant l'accolade ouvrante.

    ```javascript
    // bad
    function test(){
      console.log('test');
    }

    // good
    function test() {
      console.log('test');
    }

    // bad
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });

    // good
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });
    ```

  - Laisser un space avant la parenthèse ouvrante d'une clause de contrôle (`if`, `while` etc.). Ne pas laisser d'espace avant la liste d'argument pour les déclarations et appels de fonctions.

    ```javascript
    // bad
    if(isJedi) {
      fight ();
    }

    // good
    if (isJedi) {
      fight();
    }

    // bad
    function fight () {
      console.log ('Swooosh!');
    }

    // good
    function fight() {
      console.log('Swooosh!');
    }
    ```

  - Séparer les opérateurs avec des espaces.

    ```javascript
    // bad
    var x=y+5;

    // good
    var x = y + 5;
    ```

## Virgules

  - Pas de virgules au début :

    ```javascript
    // bad
    var story = [
        once
      , upon
      , aTime
    ];

    // good
    var story = [
      once,
      upon,
      aTime
    ];

    // bad
    var hero = {
        firstName: 'Bob'
      , lastName: 'Parr'
      , heroName: 'Mr. Incredible'
      , superPower: 'strength'
    };

    // good
    var hero = {
      firstName: 'Bob',
      lastName: 'Parr',
      heroName: 'Mr. Incredible',
      superPower: 'strength'
    };
    ```
  - Pas de virgule supplémentaire à la fin: Cela peut poser problème sur IE9 en quirksmode. 

    ```javascript
    // bad
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn',
    };

    var heroes = [
      'Batman',
      'Superman',
    ];

    // good
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn'
    };

    var heroes = [
      'Batman',
      'Superman'
    ];
    ```

## Point virgules

  - Toujours.

    ```javascript
    // bad
    (function() {
      var name = 'Skywalker'
      return name
    })()

    // good
    (function() {
      var name = 'Skywalker';
      return name;
    })();

    // good (guards against the function becoming
    // an argument when two files with IIFEs are concatenated)
    ;(function() {
      var name = 'Skywalker';
      return name;
    })();
    ```

## Trans-typage & coercition de type

  - Effectuer la coercition de type coercion en début d'expression.
  - Strings:

    ```javascript
    //  => this.reviewScore = 9;

    // bad
    var totalScore = this.reviewScore + '';

    // good
    var totalScore = '' + this.reviewScore;

    // bad
    var totalScore = '' + this.reviewScore + ' total score';

    // good
    var totalScore = this.reviewScore + ' total score';
    ```
  - Utiliser `parseInt` pour les nombres Numbers et toujours préciser la base (10).

    ```javascript
    var inputValue = '4';

    // bad
    var val = new Number(inputValue);

    // bad
    var val = +inputValue;

    // bad
    var val = inputValue >> 0;

    // bad
    var val = parseInt(inputValue);

    // good
    var val = Number(inputValue);

    // good
    var val = parseInt(inputValue, 10);
    ```
  - Booléens:

    ```javascript
    var age = 0;

    // bad
    var hasAge = new Boolean(age);

    // good
    var hasAge = Boolean(age);

    // good
    var hasAge = !!age;
    ```

## Conventions de nommage

### déclarations de functions / variables

  - Utiliser le [lower camel-case](http://fr.wikipedia.org/wiki/CamelCase#Variations%20et%20synonymes) pour les nom de fonctions, méthodes et variables.

    ```javascript
    function maFonction(maVariable) {
        // ...
    }
    ```

  - Utiliser le [upper camel-case](http://fr.wikipedia.org/wiki/CamelCase#Variations%20et%20synonymes) pour les objets.

    ```javascript
    function User(val) {
        // constructeur
    }
    ```

  - Déclarer les constantes en majuscules.

    ```javascript
    var SECOND = 1 * 1000;

    function File() {
    }
    File.FULL_PERMISSIONS = 0777;
    ```

## Accesseurs

  - L'accès aus propriétés via des fonctions accesseurs n'est pas nécessaire.
  - Si toutefois vous créez des fonctions accesseurs utiliser la convention `getVal()` and `setVal('hello')` (~ JavaBeans).

    ```javascript
    // bad
    dragon.age();

    // good
    dragon.getAge();

    // bad
    dragon.age(25);

    // good
    dragon.setAge(25);
    ```

  - Si la propriété est un booléen, utiliser la convention `isVal()` or `hasVal()`.

    ```javascript
    // bad
    if (!dragon.age()) {
      return false;
    }

    // good
    if (!dragon.hasAge()) {
      return false;
    }
    ```

## Constructeurs

  - Assigner les methods a l'objet prototype, au lieu d'écraser le prototype avec un nouvel objet. L'écrasement du prototype rend l'héritage impossible: en écrasant le prototype vous perdez le protoype de base !

    ```javascript
    function Jedi() {
      console.log('new jedi');
    }

    // bad
    Jedi.prototype = {
      fight: function fight() {
        console.log('fighting');
      },

      block: function block() {
        console.log('blocking');
      }
    };

    // good
    Jedi.prototype.fight = function fight() {
      console.log('fighting');
    };

    Jedi.prototype.block = function block() {
      console.log('blocking');
    };
    ```

  - Les méthodes peuvent retourner `this` pour permettre le chainage de méthodes.

    ```javascript
    // bad
    Jedi.prototype.jump = function() {
      this.jumping = true;
      return true;
    };

    Jedi.prototype.setHeight = function(height) {
      this.height = height;
    };

    var luke = new Jedi();
    luke.jump(); // => true
    luke.setHeight(20); // => undefined

    // good
    Jedi.prototype.jump = function() {
      this.jumping = true;
      return this;
    };

    Jedi.prototype.setHeight = function(height) {
      this.height = height;
      return this;
    };

    var luke = new Jedi();

    luke.jump()
      .setHeight(20);
    ```

## Structures du langage

### With

  - Ne pas utiliser le with. cf [Douglas Crockford](http://yuiblog.com/blog/2006/04/11/with-statement-considered-harmful/)

### Switch

  - Limiter l'utilisation du switch. cf [Todd Motto](deprecating-the-switch-statement-for-object-literals)

    La plus part du temps la construction peut être remplacée par un objet litéral et permet d'obtenir du code plus simple et plus facilement testable.

### for..in

  - Eviter d'utiliser la construction `for..in`. cf [Douglas Crockford](http://yuiblog.com/blog/2006/09/26/for-in-intrigue/). 
    Cette construction peut-être remplacée par la méthode `Object.keys`.

    ```javascript
    var obj = {one: 1, two: 2};
    Object.keys(obj).forEach(console.log);
    // one 0 [ 'one', 'two' ]
    // two 1 [ 'one', 'two' ]

    ```

### utilisation du ternaire

  - Préférer l'utilisation de l'opérateur `||` pour gérer les valeurs par défaut lorque cela est possible.

    ```javascript
    return val ? val : 'default';
    ```
    peut s'écrire simplement ainsi :
    ```javascript
    return val || 'default';
    ```

## Règles de codage

### Ne pas étendre les prototypes de base

La modification des prototypes de base du langage (Object, Array, Date, Function, ...)  
est [compliquée, risquée](https://github.com/felixge/node-style-guide#do-not-extend-built-in-prototypes)
 et déroutant. 

### Garder des fonctions petites

  - Les fonctions ne doivent pas faire plus de **25 lignes**.

Une fonction doit faire une seule chose et doit être facilement compréhensible.

### Garder des fichiers petits

  - Les fichiers ne doivent pas faire plus de **750 lignes**.

Un fichier de source trop gros indique qu'il faut découper et modulariser le code.

### Retourner rapidement des fonctions

  - Eviter l'imbrication trop complexe de condition en retournant dès que possible des fonctions.

    ```javascript
    function isPercentage(val) {
      if (val < 0) {
        return false;
      }
      if (val > 100) {
        return false;
      }
      return true;
    }
    ```

### Utiliser des conditions descriptives

  - Assigner les expression booléénnes à des variable pour expliciter la condition.

    ```javascript
    // bad
    if (password.length >= 4  && /^(?=.*\d).{4,}$/.test(password)) {
      console.log('winning');
    }
    
    // good
    var isValidPassword = password.length >= 4 
			        && /^(?=.*\d).{4,}$/.test(password);

    if (isValidPassword) {
      console.log('winning');
    }
    ```

### Nommer les fonctions

  - Trop de fonctions anonymes rendent la pile d'exécution incompréhensible (stack trace).

    ```javascript
    req.on('end', function onEnd() {
      console.log('winning');
    });
    ```

## Spécificités AngularJS

### Appels de services 

  - Ne pas faire les appels http directement dans les controllers.

    Il vaut mieux utiliser un service pour encapsuler l'appel http.

  - Ne pas retourner directement le résultat de l'appel d'un service http

    Il ne faut pas retourner directement l'objet résultat de l'appel http en sortie de méthode. 
    A minima, il faut retourner la propriété `data`. Cela permet de faire abstraction de la couche http rapidement.

    ```javascript
    this.search = function (text) {
      return $http.get('resources?q=' + text).then(function (res) {
        return res.data;
      });
    };
    ```

  - Ne pas faire de `watch` dans les services.

    Le  `watch` sert à mettre à jour le dom lorsque les données sont modifiées, il faut donc le gérer dans les contrôleurs.
    Les services peuvent utiliser les événements javascript pour notifier d'autres composants.

## Spécificités Node.JS

### Appels asynchrones

  - éviter d'imbriquer plus de 2 appels de méthode asynchrones. cf [Callback Hell](http://callbackhell.com/)

    L'imbrication de nombreuses méthodes asynchrones rends le flux d'exécution très difficilement compréhensible.
    Extraires les méthode et le nommer. Dans le cas d'appels nombreux utiliser la 
    librairie [async](https://www.npmjs.org/package/async).
    
    ```javascript
    // bad
    var results = [];
    fs.stat('file1', function (err1, res1) {
        results.push(res1);
        fs.stat('file2', function (err2, res2) {
            results.push(res2);
            fs.stat('file3', function (err3, res3) {
                results.push(res3);
                console.log(results);
            });
        });
    });
    
    // good
    async.map(['file1','file2','file3'], fs.stat, 
       function(err, results){
        // results is now an array of stats for each file
        console.log(results);
    });
    ```

  - Garder les appels asynchrones

    Lorsqu'une méthode est asynchrone tous les chemins d'exécution doivent être asynchrone, sinon vous risquez 
    de [libérer Zalgo](http://blog.izs.me/post/59142742143/designing-apis-for-asynchrony).

    ```javascript
    // bad
    function _listStocks(context, cb) {
        if (context.confirmedAt) {
            return cb(null); //called synchronously
        }
        return callSomeAsyncFunc(codes, function (err) {
            return cb(err); //called asynchronously
        });
    };

    // good
    function _listStocks(context, cb) {
        if (context.confirmedAt) {
            setTimeout(cb); //called asynchronously
            return; 
        }
        return callSomeAsyncFunc(codes, function (err) {
            return cb(err); //called asynchronously
        });
    };
    ```

## Sources :

 * https://github.com/airbnb/javascript/tree/master/es5

### AngularJS :

 * http://toddmotto.com/opinionated-angular-js-styleguide-for-teams/

### Node :

 * http://blog.izs.me/post/59142742143/designing-apis-for-asynchrony
 * http://blog.ometer.com/2011/07/24/callbacks-synchronous-and-asynchronous/

