---
layout: post
title: "Lire les vidéos de Vimeo sur iOS"
date: 2013-10-16 23:44
comments: true
categories: [CocoaPods, iOS, Objective-C]
---

### Le contexte

Il y a un peu plus d'un an, deux proches amis ont débuté un voyage participatif autour du monde appelé [You Make My Trip](http://www.youmakemytrip.com). Le principe est simple, environ une fois par mois, ils proposent aux internautes de voter pour l'un des quatre défis proposés. Une fois le challenge terminé, ils publient une vidéo, sous forme de mini documentaire. En tant qu'amis et supporteur, j'ai souhaité les aider en développant une application mobile.

Jusqu'à maintenant, la seule solution pour lire des vidéos hébergées chez Vimeo.com était de charger leur [Universal Player](http://developer.vimeo.com/player/embedding) dans un "widget" [UIWebView](https://developer.apple.com/library/ios/documentation/uikit/reference/UIWebView_Class/Reference/Reference.html). Le problème avec cette solution, est que le lecteur retourne souvent une erreur indiquant que la vidéo demandée n'est pas disponible sur mobile. Bien sûr, c'est faux, toutes les vidéos sont encodées en trois versions, dès lors qu'elles sont "uploadées".

J'ai donc développé une tout autre solution, inspirée par [PlayVimeo](https://github.com/tannauit/PlayVimeo), qui exploite les redirections (faille?) du lecteur de Vimeo.

### Juste deux classes

YTVimeoExtractor va vous permettre de récupérer l'adresse des fichiers mp4 lisibles dans le lecteur natif de iOS. Il est même possible de choisir la qualité de la vidéo. Trois options sont disponibles, mobile, standard et haute définition. YTVimeoExtractor est compatible avec iOS 4.0 et plus, car il utilise [ARC](http://en.wikipedia.org/wiki/Automatic_Reference_Counting).

Mais le plus cool dans tout ça, c'est que YTVimeoExtractor n'utilise aucun "widget" UIWebView, ce qui le rend léger et rapide.

## L'installation

Je recommande d'utiliser CocoaPods pour l'installation car c'est de loin la solution la plus rapide. Ajoutez cette ligne à votre Podfile :

```ruby
pod 'YTVimeoExtractor'
```

Puis, exécutez la commande: `pod install`

Autrement, vous pouvez aussi copier le dossier [YTVimeoExtractor](https://github.com/lilfaf/YTVimeoExtractor/tree/master) dans votre projet.

Dans tous les cas, il vous faudra aussi importer la classe dans votre controlleur :

```objc
#import "YTVimeoExtractor.h"
```

## Comment l'utiliser?

YTVimeoExtractor peut être utilisé de deux façons différentes. Je vous recommande d'utiliser les [Blocks](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html) car la syntaxe est plus déclarative et expressive :

```objc
[YTVimeoExtractor fetchVideoURLFromURL:@"http://vimeo.com/58600663"
                               quality:YTVimeoVideoQualityMedium
                               success:^(NSURL *videoURL) {
    // Initializer le lecteur
}
failure:^(NSError *error) {
    // Gérer les erreurs ici!
}];
```

Vous pouvez aussi, simplement créer une instance de YTVimeoExtractor et définir le "[delegate](https://developer.apple.com/library/ios/documentation/general/conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html)" :

```objc
self.extractor = [[YTVimeoExtractor alloc] initWithURL:@"http://vimeo.com/58600663"
                                               quality:YTVimeoVideoQualityHigh];
self.extractor.delegate = self;
[self.extractor start];
```

Puis implémentez ces deux "callbacks" pour répondre aux évènements de YTVimeoExtractor :

```objc
- (void)vimeoExtractor:(YTVimeoExtractor *)extractor didSuccessfullyExtractVimeoURL:(NSURL *)videoURL
{
    // On initialize le lecteur directement avec l'url retournée
    self.playerViewController = [[MPMoviePlayerViewController alloc] initWithContentURL:videoURL];
    [self.playerViewController.moviePlayer prepareToPlay];
    [self presentViewController:self.playerViewController animated:YES completion:nil];
}

- (void)vimeoExtractor:(YTVimeoExtractor *)extractor failedExtractingVimeoURLWithError:(NSError *)error;
{
    // Gérer les erreurs ici!
}
```

Si nécessaire, vous trouverez plus de détails sur l'implémentation dans l'application [sample](https://github.com/lilfaf/YTVimeoExtractor/tree/master/Sample).

### TODO ?

Je pensais éventuellement ajouter l'option "YTVimeoVideoQualityAuto" qui pourrait suivre ces règles :

- Wifi : Haute définition
- 4G : Haute définition ou standard
- 3G : Standard ou mobile

Le choix de la qualité pourrait aussi être dictée par la version de l'iphone, retina ou pas ? Votre avis ?

<!-- more -->
