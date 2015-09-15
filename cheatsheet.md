## Arrays 

### *Applatir* un tableau d'objets

Utile pour aggréger des tableaux imbriqués dans une grappe d'objet.
Par exemple, avec le tableau suivant :

```js
var data : [
 {
    tickets : [
        {payments :[ {amount: 1}, {amount: 3}]}, {payments :[/*...*/]}, {payments :[/*...*/]}]}
    ],
 {
    tickets : [
        {payments :[/*...*/]}
    ]
 }
];
```

On souhaite faire la somme de tous les montants (*amount*).
Avec la méthode suivante :

```js
function flatten(arr, keys) {
    var elems = arr;
    keys.split('.').forEach(function(key) {
        if(elems.map) {
            elems = elems.map(function(e){
                return e[key];
            });
        }
        elems = Array.prototype.concat.apply([], elems);
    });
    return elems;
}
```

Cela devient simplement :

```js
    var total = flatten(data, 'tickets.payments.amount').reduce(function(pv, cv) { return pv + cv; }, 0);
```