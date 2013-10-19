---
layout: post
title: "Débuter avec Meteor.js"
date: 2013-10-19 15:16
comments: true
categories: [Javascript, Meteor.js]
---

### Introduction

[Meteor.js](http://www.meteor.com/) est un framework web de "nouvelle-génération" développé en Javascript et basé sur [Node.js](http://nodejs.org/), du côté serveur. Le projet remet totalement en question la manière de considérer les architectures [client-serveur](http://en.wikipedia.org/wiki/Client%E2%80%93server_model).

Plus qu'un framework, Meteor est un écosystème complet qui embarque un gestionnaire de paquets, un web serveur, etc. Il est même possible de déployer sont application sur les infrastructures de Meteor en une ligne commande.

Avec Meteor tout est entièrement dynamique et la base de données est accessible autant du côté serveur, que du côté client. La base données [MongoDB](http://www.mongodb.org/) est aussi livrée par défaut avec Meteor. Nous allons donc rapidement voir ce que ce concept implique et les possibilités qu'il nous offre.

### Démarrer en un éclair !

L'installation de Meteor est vraiment très simple, il suffit d'exécuter cette commande :

`curl https://install.meteor.com | /bin/sh`

Pour créer un nouveau projet, utilisez cette commande :

`meteor create myapp`

Puis vous pouvez démarrer le serveur local et naviguer à l'adresse *http://localhost:3000/* :

`cd myapp && meteor`

### MongoDB sur le client et le serveur ?

Tout d'abord, il faut bien comprendre que le code tourne par défaut sur le client et le serveur. Pour forcer l'exécution sur l'un des deux côté, nous pourrons tester dans quel environnement l'on se trouve, comme ceci:

```javascript myapp.js
// Code qui tourne sur le client et le serveur

if (Meteor.isClient) {
  // Code du client
}

if (Meteor.isServer) {
  // Code de serveur
}
```

Tout ce qui est défini est dehors de ces deux bloque sera exposé du côté client et serveur. Pour mieux comprendre le principe de "database everywhere", nous allons créer une simple [collection](http://docs.meteor.com/#collections), en haut du fichier *myapp.js* :

```javascript myapp.js
Posts = new Meteor.Collection("posts");
```

Ensuite il nous faut créer un "template", dans le bloque de code du client, pour afficher notre collection :

```javascript myapp.js
Template.post_list.posts = function () {
  return Posts.find({}, {sort: { title: 1}});
};
```

Meteor se charge de mettre à jour la page en temps réel. En d'autres termes, nous allons gagner énormément de temps, car nous n'aurons pas d'AJAX ou de manipulation du DOM a écrire.
Enfin, il nous suffit donc d'utiliser notre "template" dans le fichier *myapp.html* :

{% raw %}
```html myapp.html
...
<body>
  {{> post_list}}
</body>

<template name="post_list">
  <h1>Posts List!</h1>
  <ul>
  {{#each posts}}
    <li>{{title}}</li>
  {{/each}}
  </ul>
</template>
...
```
{% endraw %}

Et voilà, c'est fini. Nous pouvons commencer à ajouter des données et les regarder apparaitre dynamiquement. Pour ce faire, ouvrez votre navigateur et les outils de développement. Dans la section console, entrez ceci :

```javascript
Posts.insert({title: "hello world"});
```

Si vous ouvrez deux fenêtres de navigation vous pourrez constater que les changements se reflètent chez tous les clients. Meteor est magique, peut être un peu trop. Mais c'est tout de même plutôt cool de pouvoir développer une page si dynamique avec très peu de code.

J'espère que cet article vous aura donné envie de jeter au moins un coup d'oeil à ce projet. Mais attention, Meteor est encore en développement, la version 1.0 devrais arriver d'ici quelques mois, il n'est donc pas recommandé de l'utiliser en production pour le moment.

<!-- more -->
