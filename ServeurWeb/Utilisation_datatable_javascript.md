# UTILISATION DATATABLE JAVASCRIPT



## Installation

Telecharger les fichiers datatables ici : https://www.datatables.net/download/packages

Copier le repertoire media dans votre site.

Creer un fichier **tableau.js** dans lequel on va mettre notre code js.

Dans votre page HTML ajouter le code suivant dans la balise head:

```
<script type="text/javascript" src='media/js/jquery.js'></script>
<script type="text/javascript" src="media/js/jquery.dataTables.min.js"></script>
<script type="text/javascript" src="tableau.js"></script>
<link rel="stylesheet" type="text/css" href="media/css/jquery.dataTables.min.css">
```



Le tableau html doit contenir l'élément thead, qui définit les titres des colonnes, et l'élément tbody, comportant les données. Le code ci-après montre le corps du fichier HTML :

```
<table id='tab' class='display'>
        <caption>Fromages</caption>
        <thead>
            <tr><th>Nom</th><th>Lait</th><th>Prix</th></tr>
        </thead>
        <tbody>
            <tr><td>Roquefort</td><td>brebis</td><td>4</td></tr>
            <tr><td>Morbier</td><td>vache</td><td>1</td></tr>
            <tr><td>Raclette</td><td>vache</td><td>3</td></tr>
            <tr><td>St Nectaire</td><td>vache</td><td>2</td></tr>
        </tbody>
</table>

```



Dans le fichier JavaScript **tableau.js**, associez DataTables au tableau dont l'attribut id a la valeur tab :

```
// fichier tableau.js

$(document).ready(function () {
    $('#tab').DataTable();
});

```



## Elements de controle

L'option dom de DataTables permet de définir les éléments de contrôle du tableau qui seront affichés ainsi que leur ordre dans la page. Le tableau ci-après présente les différents contrôles disponibles pour cette option.

| **Contrôle** | **Description**                                              |
| ------------ | ------------------------------------------------------------ |
| l            | Affiche le *select* indiquant le nombre de lignes à afficher par page |
| f            | Affiche le champ permettant de filtrer les données du tableau |
| i            | Affiche des informations                                     |
| p            | Affiche le menu de pagination                                |
| t            | Indique l’emplacement du tableau par rapport aux contrôles   |
| r            | Affiche un message de traitement                             |



Par défaut la valeur de dom est lftrip (voir figure 4). Il est possible d'utiliser plusieurs fois la même lettre pour dupliquer un contrôle.

![img](https://connect.ed-diamond.com/var/diamond/storage/images/connect/gnu-linux-magazine/glmf-189/4894_datatables-article/datatables_figure_04/481955-1-fre-FR/datatables_figure_041.png)



Le code ci-après affiche la pagination au-dessus du tableau :

```

$(document).ready(function () {
    $('#tab').DataTable({
        dom: 'ptlf',
        language: {
            url: "DataTables/media/French.json"
        }
    });
});

```



## Pagination

Il est possible de personnaliser la pagination avec l'option pagingType. Quatre types de pagination sont disponibles : simple,simple_numbers, full, full_numbers.

Le menu qui définit le nombre de lignes affichées par page est configuré avec l'option lengthMenu. Par défaut, DataTables propose un menu comportant : **10, 25, 50, 100**. Le nombre de lignes à afficher au chargement du tableau est contrôlé par l'option pageLength.

Le code ci-après, affiche 3 lignes par page, et permet de basculer l'affichage à **5, 10, 15, 20 ou 25** lignes par page:

```
pagingType: "simple_numbers",
lengthMenu:[5,10,15,20,25],
pageLength: 3
```



## Tri

Par défaut, le tri est réalisé sur la première colonne, par ordre croissant. La propriété **order** indique les colonnes à utiliser pour le tri et l'ordre à leur appliquer : croissant (**asc**) ou décroissant (**desc**).
Dans le code ci-après, le tri est réalisé sur la deuxième colonne par ordre décroissant, puis sur la première colonne par ordre croissant .

```
order:[[1,'desc'], [0, 'asc']]
```

L'option **columns**, permet de définir le type de la colonne (alphabétique, numérique, … ). L'exemple ci-après, indique que les deux premières colonnes sont de type texte et que la troisième est de type numérique. Le type de la colonne sera pris en compte pour le tri.

```
columns: [ {type:"text"}, {type:"text"}, {type:"num"} ]
```



Une cellule peut contenir des éléments html comme dans l'exemple de code ci-après :

```
<td><em>buffle</em></td>
```



Alors, au moment du tri, les balises <em></em> seront considérées comme du texte, le tri sera différent de celui attendu par l'utilisateur. Dans la figure 8b, buffle est situé sur la dernière ligne du tableau, lorsque le tri est réalisé par ordre décroissant, car le caractère < a une valeur inférieure aux caractères de l'alphabet.

Pour ignorer les éléments HTML et obtenir le tri souhaité lors de la comparaison, il faut donner à la colonne le type html :

```
columns: [ {type:"text"}, {type:"html"}, {type:"num"} ]
```



Il est possible d'exclure une ou plusieurs colonnes du tri avec la propriété **orderable** :

```
columns: [ {type:"text"}, {type:"html"}, {orderable:false} ]
```



