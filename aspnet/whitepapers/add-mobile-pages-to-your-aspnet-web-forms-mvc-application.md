---
uid: whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application
title: "Comment : Ajouter des Pages mobiles à ASP.NET Web Forms / MVC Application | Documents Microsoft"
author: rick-anderson
description: "Cette rubrique décrit différentes méthodes pour servir les pages optimisés pour les appareils mobiles à partir de votre ASP.NET Web Forms / application MVC et suggère architecturaux et en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2011
ms.topic: article
ms.assetid: 3124f28e-cc32-418a-afe3-519fa56f4c36
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application
msc.type: content
ms.openlocfilehash: c7d893fb9633aaa8628f2f46a8db7f2c09f81830
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="how-to-add-mobile-pages-to-your-aspnet-web-forms--mvc-application"></a>Comment : Ajouter des Pages mobiles à ASP.NET Web Forms / Application MVC
====================
> **S’applique à**
> 
> - Version de Web Forms ASP.NET 4.0
> - ASP.NET MVC version 3.0
> 
> **Résumé**
> 
> Cette rubrique décrit différentes méthodes pour servir les pages optimisés pour les appareils mobiles à partir de votre ASP.NET Web Forms / application MVC et suggère architecture et les problèmes à prendre en compte lorsque vous ciblez une large gamme de périphériques de conception. Ce document explique également pourquoi les contrôles d’ASP.NET 2.0 Mobile ASP.NET 3.5 sont désormais obsolètes et présente certaines solutions modernes.


## <a name="contents"></a>Sommaire

- Vue d'ensemble
- Options d’architecture
- Détection de navigateurs et de périphériques
- Comment les applications Web Forms ASP.NET peuvent présenter des pages mobiles spécifiques
- Comment les applications ASP.NET MVC peuvent présenter des pages mobiles spécifiques
- Ressources supplémentaires

Pour obtenir des exemples de code téléchargeable illustrant les techniques de ce livre blanc pour ASP.NET Web Forms et MVC, consultez [applications mobiles et des Sites avec ASP.NET](https://docs.microsoft.com/aspnet/mobile/overview).

## <a name="overview"></a>Vue d'ensemble

Les appareils mobiles – smartphones, fonctionnalité des téléphones et tablettes – continuent à croître en popularité comme un moyen d’accéder à Internet. Pour de nombreux développeurs web et les entreprises d’orientée web, cela signifie qu’il est plus important de fournir une excellente expérience de navigation pour les visiteurs qui utilisent ces appareils.

### <a name="how-earlier-versions-of-aspnet-supported-mobile-browsers"></a>Versions antérieures d’ASP.NET pris en charge les navigateurs mobiles

Les versions d’ASP.NET 2.0 à 3.5 inclus *contrôles mobiles*: un ensemble de contrôles de serveur pour les appareils mobiles dans le *System.Web.Mobile.dll* assembly et le  *System.Web.UI.MobileControls* espace de noms. L’assembly est toujours inclus dans ASP.NET 4, mais elle est déconseillée. Les développeurs sont invités à migrer vers des approches plus modernes, tels que ceux décrits dans ce document.

Pourquoi les contrôles mobiles ont été marquées comme obsolètes parce que leur conception est orientée sur les téléphones portables qui étaient courantes autour de 2005 et antérieures. Les contrôles sont principalement conçus pour restituer le balisage WML ou cHTML (au lieu de régulière HTML) pour les navigateurs WAP de l’époque. Mais WAP, WML et cHTML ne sont plus pertinentes pour la plupart des projets, car HTML est devenu le langage de balisage omniprésent pour les navigateurs de bureau et mobiles similaires.

### <a name="the-challenges-of-supporting-mobile-devices-today"></a>Les défis que représentent aujourd'hui de prise en charge des appareils mobiles

Bien que les navigateurs mobiles prennent désormais en charge presque universellement les HTML, toujours pose de nombreux défis visant à créer des expériences de navigation mobiles exceptionnelles :

- ***Taille de l’écran*** : les périphériques mobiles varient considérablement dans le formulaire et des écrans sont souvent beaucoup plus petite que les moniteurs de bureau. Par conséquent, vous devrez peut-être concevoir des dispositions de page totalement différente pour eux.
- ***Entrée des méthodes*** – certains périphériques ont des claviers, certains ont des pointes de lecture, d’autres utilisent des fonctions tactiles. Vous devrez peut-être prendre en compte la navigation de plusieurs méthodes d’entrée de données et des mécanismes.
- ***La conformité aux normes*** – nombreux navigateurs mobiles ne prennent pas en charge les dernières normes HTML, CSS et JavaScript.
- ***La bande passante*** : performances du réseau des données cellulaires varient fortement et certains utilisateurs finaux se trouvent sur les tarifs facturent en mégaoctets.

Il n’existe pas de solution universelle ; votre application devra ressemblent et se comportent différemment en fonction de l’appareil d’y accéder. Selon le niveau de prise en charge mobile que vous voulez, cela peut être un défi pour les développeurs web plus grand que le bureau « entre les navigateurs » a été jamais.

Les développeurs proche de prise en charge du navigateur mobile pour la première fois souvent initialement pense qu’il est uniquement important prendre en charge des plus récente et plus sophistiquées smartphones (par exemple, Windows Phone 7, iPhone ou Android), peut-être parce que les développeurs possèdent souvent personnellement par appareils. Toutefois, plus économique téléphones sont toujours extrêmement populaires et leurs propriétaires les utilisez pour parcourir le web, en particulier dans les pays dans lesquels les téléphones mobiles sont plus obtenir une connexion haut débit. Votre entreprise devrez décider quelle plage de périphériques pour prendre en charge en tenant compte de ses clients susceptibles de. Si vous créez une brochure en ligne pour spa luxe d’intégrité, vous pouvez apporter une décision économique uniquement pour cibler des smartphones avancées, tandis que si vous créez un système de réservation de ticket pour cinéma, vous devez probablement pour prendre en compte pour les visiteurs avec fonctionnalité moins puissante téléphones.

## <a name="architectural-options"></a>Options d’architecture

Avant des détails techniques spécifiques des Web Forms ASP.NET MVC, notez que les développeurs web ont en général trois options principales pour prendre en charge les navigateurs mobiles :

1. ***Ne rien faire –*** vous pouvez simplement créer une application web standard, orienté sur le bureau et s’appuient sur les navigateurs mobiles soit acceptable. 

    - **Avantage**: c’est l’option la moins coûteux à implémenter et gérer : ne supplémentaire de travail
    - **Inconvénient**: offre l’expérience utilisateur final pire : 

        - Les dernière smartphones peut rendre votre code HTML aussi bien que d’un navigateur de bureau, mais les utilisateurs sont obligés toujours effectuer un zoom et faire défiler horizontalement et verticalement pour consommer votre contenu sur un petit écran. Ceci est loin d’être optimales.
        - Anciens appareils et téléphones de fonctionnalité peuvent échouer restituer le balisage d’une manière satisfaisante.
        - Sur les tablettes dernière (dont écrans peuvent être aussi volumineux que les écrans d’ordinateur portable), interaction différentes règles s’appliquent. Entrée basée sur les fonctions tactiles fonctionne mieux avec les boutons plus volumineux et des liens de propagation plus éloignée, et il n’existe aucun moyen pour pointer un curseur de la souris sur un menu contextuel.
2. ***Résoudre le problème sur le client* –** avec usage modéré de CSS et [amélioration progressive](http://en.wikipedia.org/wiki/Progressive_enhancement) vous pouvez créer le balisage, les styles et les scripts qui s’adaptent aux tout navigateur les exécute. Par exemple, avec [les requêtes de média CSS 3](http://www.w3.org/TR/2010/CR-css3-mediaqueries-20100727/), vous pouvez créer une disposition qui transforme une disposition de colonne unique sur les appareils dont les écrans sont plus étroits qu’un seuil défini. 

    - **Avantage**: optimise le rendu du périphérique en cours d’utilisation, même pour les appareils de futures inconnus en fonction des caractéristiques d’entrée et toute écran qu’ils ont
    - **Avantage**: facilement vous permet de partager une logique côté serveur sur tous les types d’appareil – minimale duplication de code ou l’effort
    - **Inconvénient**: les appareils mobiles sont donc différents à partir d’appareils de bureau peut réellement souhaité vos pages mobiles soit complètement différent de pages de votre bureau, affichant des informations différentes. Ces variations peuvent s’avérer inefficaces ou impossible à atteindre efficacement via CSS seul, surtout si l'on considère la façon incohérente anciens appareils interprètent les règles CSS. Cela est particulièrement vrai des attributs de CSS 3.
    - **Inconvénient**: ne fournit aucune prise en charge pour les variable logique côté serveur et de flux de travail pour différents appareils. Vous ne peut pas, par exemple, implémenter un workflow d’extraction panier d’achat simplifiée pour les utilisateurs mobiles au moyen de CSS seul.
    - **Inconvénient**: utilisation inefficace de la bande passante. Votre serveur peut avoir transmettre le balisage et les styles qui s’appliquent à tous les périphériques possible, même si le périphérique cible utilise uniquement un sous-ensemble de ces informations.
3. ***Résoudre le problème sur le serveur* –** si votre serveur sait quel appareil accède à elle, ou au moins les caractéristiques de ces périphériques, tels que sa taille de l’écran et la méthode d’entrée, et s’il s’agit d’un appareil mobile, il peut exécuter une logique différente et balisage HTML différent sortie. 

    - **Avantage :** une grande souplesse maximale. Il n’existe aucune limite à la quelle vous pouvez varier votre logique côté serveur pour les mobiles ou optimiser votre balisage pour la disposition souhaitée, spécifique au périphérique.
    - **Avantage :** utilisation efficace de la bande passante. Il vous suffit de vous transmettre le balisage et les informations de style qui va utiliser l’appareil cible.
    - **Inconvénient :** parfois force la répétition du travail ou du code (par exemple, en faisant vous créer des copies de vos pages Web Forms ou les vues MVC semblables, mais légèrement différentes). Où possible que vous serez factoriser logique commune dans une couche sous-jacente ou service, mais néanmoins, certaines parties de votre code d’interface utilisateur ou d’un balisage peut-être être dupliqué, puis conservées, en parallèle.
    - **Inconvénient :** détection de l’appareil n’est pas simple. Il requiert une liste ou une base de données de types d’appareils connus et leurs caractéristiques (qui ne peuvent toujours être parfaitement à jour) et faire correspondre chaque demande entrante n’est pas garanti. Ce document décrit certaines options et leurs pièges ultérieurement.

Pour obtenir de meilleurs résultats, la plupart des développeurs trouver qu'ils ont besoin de combiner des options (2) et (3). Des différences mineures stylistiques sont mieux prises en charge sur le client à l’aide de CSS ou même JavaScript, tandis que les principales différences dans les données, de flux de travail ou de balisage sont implémentées plus efficacement dans le code côté serveur.

### <a name="this-paper-focuses-on-server-side-techniques"></a>Ce document se concentre sur les techniques de côté serveur

Étant donné que ASP.NET Web Forms et MVC sont les deux technologies principalement côté serveur, ce livre blanc se concentrera sur les techniques de côté serveur qui vous permettent de générer un balisage différent et logique pour les navigateurs mobiles. Bien entendu, vous pouvez également combiner cela avec n’importe quelle technique côté client (par exemple, CSS 3 requêtes de média, amélioration progressive JavaScript), mais qui est plus une question de conception web de développement ASP.NET.

## <a name="browser-and-device-detection"></a>Détection de navigateurs et de périphériques

Le composant de clé requis pour toutes les techniques de côté serveur pour prendre en charge des appareils mobiles consiste à déterminer le périphérique à l’aide de votre visiteur. En fait, même de mieux connaître le nombre de fabricant et le modèle de cet appareil est de savoir le *caractéristiques* de l’appareil. Caractéristiques peuvent inclure :

- Il est un appareil mobile ?
- Méthode d’entrée (de la souris et le clavier, tactile, clavier, manette,...)
- Taille de l’écran (physiquement et en pixels)
- Prise en charge multimédia et formats de données
- etc.

Il est préférable de prendre des décisions basées sur les caractéristiques que le numéro de modèle, car, puis vous serez mieux équipées pour gérer les appareils.

### <a name="using-aspnets-built-in-browser-detection-support"></a>À l’aide de ASP. Prise en charge de la détection de navigateur intégré de NET

Les développeurs ASP.NET Web Forms et MVC peuvent découvrir immédiatement des caractéristiques importantes d’un navigateur visite en examinant les propriétés de la *Request.Browser* objet. Par exemple, consultez

- Request.Browser.IsMobileDevice
- Request.Browser.MobileDeviceManufacturer, Request.Browser.MobileDeviceModel
- Request.Browser.ScreenPixelsWidth
- Request.Browser.SupportsXmlHttp
- .. .et beaucoup d’autres

En arrière-plan, la plateforme ASP.NET correspond à entrant *User-Agent* en-tête HTTP de (l’agent utilisateur) par rapport aux expressions régulières dans un ensemble de fichiers XML de définition de navigateur. Par défaut la plateforme inclut les définitions pour de nombreux périphériques mobiles courantes, et vous pouvez ajouter des fichiers de définition de navigateur personnalisés pour d’autres que vous voulez reconnaître. Pour plus d’informations, consultez la page MSDN [des contrôles serveur Web ASP.NET et les fonctionnalités du navigateur](https://msdn.microsoft.com/en-us/library/x3k2ssx2.aspx).

### <a name="using-the-wurfl-device-database-via-51degreesmobi-foundation"></a>À l’aide de la base de données de périphérique WURFL via 51Degrees.mobi Foundation

Alors que ASP. Prise en charge de la détection de navigateur intégré de NET sera suffisant pour de nombreuses applications, il existe deux cas principales lorsqu’il ne peut pas être suffisamment :

- ***Vous souhaitez identifier les périphériques les plus récents***(sans création manuelle des fichiers de définition de navigateur pour eux). Notez que les fichiers de définition de navigateur du .NET 4 ne sont pas assez récentes pour reconnaître le Windows Phone 7, les téléphones Android, navigateurs du navigateur Opera Mobile ou iPad d’Apple.
- ***Vous avez besoin des informations plus détaillées sur les fonctionnalités de l’appareil***. Vous devez connaître sur la méthode de saisie d’un appareil (par exemple, tactile vs du pavé numérique) ou quel audio met en forme le navigateur prend en charge. Ces informations n’est pas disponibles dans les fichiers de définition de navigateur standards.

Le [ *fichier de ressources sans fil universel* projet (WURFL)](http://wurfl.sourceforge.net/) conserve beaucoup plus à jour et plues d’informations sur les appareils mobiles en cours d’utilisation aujourd'hui.

Pour les développeurs .NET est que ASP. Fonctionnalité de détection du navigateur de NET est extensible, il est donc possible d’améliorer afin de résoudre ces problèmes. Par exemple, vous pouvez ajouter l’open source [ *51Degrees.mobi Foundation* ](https://github.com/51Degrees/dotNET-Device-Detection) bibliothèque à votre projet. Il est un IHttpModule d’ASP.NET ou un navigateur fonctionnalités fournisseur (utilisable sur les applications Web Forms et MVC), qui lit les données WURFL et le raccorde dans ASP directement. Mécanisme de détection du navigateur intégré de NET. Une fois que vous avez installé le module, *Request.Browser* soudainement contient des informations plus précises et détaillées : il correctement reconnaît la plupart des appareils mentionnés plus haut et répertorier leurs fonctions (y compris fonctionnalités supplémentaires telles que de la méthode d’entrée). Consultez la documentation du projet pour plus de détails.

## <a name="how-web-forms-applications-can-present-mobile-specific-pages"></a>Comment les applications Web Forms peuvent présenter des pages mobiles spécifiques

Par défaut, voici comment une toute nouvelle application de Web Forms affiche sur les appareils mobiles courants :

![](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/_static/image1.png)

Clairement, ni mise en forme soit très mobile-friendly : cette page a été conçue pour une analyse de grande taille, en mode paysage, pas pour un petit écran orienté en mode portrait. Ce que peut faire sur celui-ci ?

Comme indiqué plus haut dans ce document, il existe de nombreuses façons de personnaliser vos pages pour les appareils mobiles. Certaines techniques sont basée sur le serveur, d’autres s’exécuter sur le client.

### <a name="creating-a-mobile-specific-master-page"></a>Création d’une page maître spécifique mobile

Selon vos besoins, vous pourrez peut-être utiliser le même Web Forms pour tous les visiteurs, mais deux séparer les pages maîtres : une pour les visiteurs de bureau, un autre pour les visiteurs mobiles. Cela vous donne la souplesse de la modification de la feuille de style CSS ou votre balisage HTML de niveau supérieur pour satisfaire des appareils mobiles, sans vous obliger à dupliquer une logique de la page.

Il est facile à suivre. Par exemple, vous pouvez ajouter un gestionnaire de PreInit telle que la suivante à un formulaire Web :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample1.cs)]

À présent, créez une page maître appelée Mobile.Master dans le dossier de niveau supérieur de votre application, et il sera utilisé lors de la détection d’un appareil mobile. Votre page maître peut faire référence à une feuille de style CSS mobiles spécifiques si nécessaire. Bureau visiteurs pourront toujours voir votre page maître par défaut, pas celui mobile.

### <a name="creating-independent-mobile-specific-web-forms"></a>Créer des formulaires Web mobiles spécifiques indépendant

Pour une flexibilité maximale, vous pouvez aller plus loin que le fait d’avoir des pages maîtres distinctes pour différents types d’appareils. Vous pouvez implémenter deux *totalement séparer les ensembles de pages Web Forms* – défini pour les navigateurs de bureau, un autre ensemble pour mobiles. Cela fonctionne mieux si vous souhaitez présenter des informations très différentes ou des flux de travail pour les visiteurs mobiles. Le reste de cette section décrit cette approche en détail.

En supposant que vous disposez déjà d’une application Web Forms conçue pour les navigateurs de bureau, le moyen le plus simple pour continuer est pour créer un sous-dossier appelé « Mobile » au sein de votre projet et générer vos pages mobiles. Vous pouvez construire un sous-site entier, avec ses propres pages maîtres, les feuilles de style et les pages, en utilisant les mêmes techniques que vous utiliseriez pour toute autre application Web Forms. Vous n’avez pas nécessairement besoin produire un équivalent mobile pour *chaque* page de votre site de bureau ; vous pouvez choisir le sous-ensemble de fonctionnalités de sens pour les visiteurs mobiles.

Vos pages mobiles peuvent partager des ressources communes statiques (comme des images, JavaScript ou CSS fichiers) avec vos pages régulières si vous le souhaitez. Étant donné que le dossier « Mobile » sera *pas* être marquée comme une application séparée lorsqu’il est hébergé dans IIS (il est simplement un sous-dossier simple des pages Web Forms), il sera également partager les mêmes configuration, les données de Session et toute autre infrastructure en tant que votre pages de bureau.

> [!NOTE]
> Étant donné que cette approche implique généralement une duplication de code (pages mobiles sont susceptibles de partager des similarités avec des pages de bureau), il est important de facteur de n’importe quel entreprise logique ou les données accès le code commun dans une couche sous-jacente partagé ou un service. Sinon, vous allez double l’effort de création et de gestion de votre application.


#### <a name="redirecting-mobile-visitors-to-your-mobile-pages"></a>Redirection des visiteurs mobiles pour vos pages mobiles

Il est souvent utile de rediriger les visiteurs mobiles vers les pages mobiles uniquement sur le *premier* une requête dans leur session de navigation (et non à chaque requête dans leur session), car :

- Vous pouvez autoriser puis facilement de visiteurs mobiles accéder aux pages de votre bureau s’ils veulent – simplement placer un lien sur votre page maître qui accède à la « Version de bureau ». Le visiteur ne sera pas être redirigé vers une page mobile, car il n’est plus la première demande dans leur session.
- Il permet d’éviter le risque d’interférence avec les demandes de ressources dynamiques partagées entre les parties de bureau et mobiles de votre site (par exemple, si vous disposez d’un formulaire Web courantes qui affichent des parties de bureau et mobiles de votre site dans un IFRAME, ou certains gestionnaires d’Ajax)

Pour ce faire, vous pouvez placer votre logique de redirection dans un **Session\_Démarrer** (méthode). Par exemple, ajoutez la méthode suivante à votre fichier Global.asax.cs :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample2.cs)]

#### <a name="configuring-forms-authentication-to-respect-your-mobile-pages"></a>Configuration de l’authentification par formulaire afin de respecter vos pages mobiles

Notez que l’authentification par formulaire effectue certaines hypothèses concernant où il peut rediriger les visiteurs pendant et après le processus d’authentification :

- Lorsqu’un utilisateur doit être authentifié, l’authentification par formulaire les redirige vers votre page de connexion du bureau, qu’ils soient d’un utilisateur de bureau ou portable (car il a seulement un concept de *un* URL de connexion). En supposant que vous souhaitez appliquer un style votre page de connexion mobiles différemment, vous devez améliorer votre page de connexion de bureau afin qu’il redirige les utilisateurs mobiles vers une page de connexion mobile distinct. Par exemple, ajoutez le code suivant à votre **bureau** login page code-behind : 

    [!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample3.cs)]
- Une fois un utilisateur se connecte, l’authentification par formulaire sera par défaut les redirige vers votre page d’accueil de bureau (car il a seulement un concept de *un* page par défaut). Vous devez améliorer votre page de connexion mobile afin qu’il redirige vers la page d’accueil mobile après une journal réussie. Par exemple, ajoutez le code suivant à votre **mobile** login page code-behind : 

    [!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample4.cs)]
  
 Ce code suppose que votre page possède un contrôle de serveur de connexion appelé LoginUser, comme dans le modèle de projet par défaut.

### <a name="working-with-output-caching"></a>Utilisation de la mise en cache de sortie

Si vous utilisez la mise en cache de sortie, veillez à ce que par défaut, qu'il est possible pour un utilisateur du Bureau à visiter une certaine URL (à l’origine de sa sortie doit être mis en cache), suivi d’un utilisateur mobile puis reçoit la sortie de postes de travail mis en cache. Cet avertissement s’applique que vous soyez simplement varying votre page maître par type de périphérique ou implémenter totalement distincts de Web Forms par type de périphérique.

Pour éviter ce problème, vous pouvez demander à ASP.NET pour faire varier l’entrée du cache selon que le visiteur utilise un appareil mobile. Ajoutez un paramètre VaryByCustom à la déclaration de OutputCache de votre page comme suit :

[!code-aspx[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample5.aspx)]

Ensuite, définissez *isMobileDevice* comme un cache personnalisé substituer des paramètre en ajoutant la méthode suivante à votre fichier Global.asax.cs :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample6.cs)]

Cela permettra que mobiles visiteurs de la page ne recevez pas de sortie précédemment placée dans le cache par un visiteur de bureau.

### <a name="a-working-example"></a>Un exemple fonctionnel

Pour afficher ces techniques en action, téléchargez [exemples de code de ce livre blanc](https://docs.microsoft.com/aspnet/mobile/overview). L’exemple d’application Web Forms redirige automatiquement les utilisateurs mobiles à un ensemble de pages mobiles spécifiques dans un sous-dossier appelé Mobile. Le balisage et le style de ces pages est optimisée pour les navigateurs mobiles, comme vous pouvez le voir dans les captures d’écran suivants :

![](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/_static/image2.png)

Pour plus d’informations sur l’optimisation de votre balisage et le CSS pour les navigateurs mobiles, consultez la section « Conception de styles pour les pages mobiles pour les navigateurs mobiles » plus loin dans ce document.

## <a name="how-aspnet-mvc-applications-can-present-mobile-specific-pages"></a>Comment les applications ASP.NET MVC peuvent présenter des pages mobiles spécifiques

Étant donné que le modèle Model-View-Controller dissocie la logique d’application (dans les contrôleurs) de la logique de présentation (dans les affichages), vous pouvez choisir à partir d’une des approches suivantes pour gérer la prise en charge mobile dans le code côté serveur :

1. ***Utiliser les mêmes contrôleurs et vues pour les navigateurs de bureau et mobiles, mais les vues avec différentes dispositions Razor selon le type de périphérique de rendu*.** Cette option fonctionne mieux si vous affichez des données identiques sur tous les appareils, mais simplement fournir différentes feuilles de style CSS ou de modifier quelques éléments HTML de niveau supérieur pour les mobiles.
2. ***Utiliser les contrôleurs de mêmes pour les navigateurs de bureau et mobiles, mais effectuer le rendu des vues différentes selon le type d’appareil***. Cette option fonctionne mieux si vous êtes affichage à peu près les mêmes données et en fournissant le même flux de travail pour les utilisateurs finaux, mais souhaitez restituer un balisage HTML très différent en fonction de l’appareil utilisé.
3. ***Créer des zones séparées pour les navigateurs de bureau et mobiles, implémentation des contrôleurs indépendants et les vues pour chaque*.** Cette option fonctionne mieux si vous êtes affichant les écrans très différentes, qui contient des informations différentes et début de l’utilisateur à travers différents flux de travail optimisé pour les types de périphériques. Cela peut signifier une répétition de code, mais vous pouvez réduire qui en factorisant logique commune dans une couche ou un service sous-jacent.

Si vous voulez prendre le **premier** option et modifier uniquement la mise en page Razor par type d’appareil, il est très facile. Il suffit de modifier votre \_ViewStart.cshtml de fichiers comme suit :

[!code-cshtml[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample7.cshtml)]

Vous pouvez désormais créer une disposition spécifique mobile appelée \_LayoutMobile.cshtml avec une structure de page et CSS règles optimisées pour les appareils mobiles.

Si vous voulez prendre le **deuxième** option totalement différentes vues de rendu en fonction du type de périphérique du visiteur, consultez [billet de blog de Scott Hanselman](http://www.hanselman.com/blog/ABetterASPNETMVCMobileDeviceCapabilitiesViewEngine.aspx).

Le reste de ce document se concentre sur la **troisième** option : création de contrôleurs distincts *et* vues pour les appareils mobiles : afin de contrôler exactement le sous-ensemble de fonctionnalités est proposé pour les visiteurs mobiles.

### <a name="setting-up-a-mobile-area-within-your-aspnet-mvc-application"></a>Configuration d’une zone Mobile au sein de votre application ASP.NET MVC

Vous pouvez ajouter une zone appelée « Mobile » pour une application ASP.NET MVC existante de façon normale : avec le bouton droit sur le nom de votre projet dans l’Explorateur de solutions, puis cliquez sur Ajouter à zone. Vous pouvez ensuite ajouter des contrôleurs et vues comme vous le feriez pour n’importe quel autre zone dans une application ASP.NET MVC. Par exemple, ajoutez à votre zone Mobile un nouveau contrôleur appelé HomeController d’agir comme une page d’accueil pour les visiteurs mobiles.

### <a name="ensuring-the-url-mobile-reaches-the-mobile-homepage"></a>Assurer l’URL /Mobile atteint la page d’accueil mobile

Si vous souhaitez que l’URL /Mobile pour atteindre l’action de l’Index sur HomeController à l’intérieur de votre zone Mobile, vous devez effectuer deux modifications mineures à votre configuration de routage. Tout d’abord, mettez à jour votre classe MobileAreaRegistration afin que HomeController est le contrôleur de valeur par défaut de votre zone Mobile, comme indiqué dans le code suivant :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample8.cs)]

Cela signifie que la page d’accueil mobile maintenant se trouve à /Mobile, plutôt que/Mobile/Home, car « Home » est désormais l’implicitement les nom de contrôleur par défaut pour les pages mobiles.

Ensuite, notez qu’en ajoutant un deuxième HomeController à votre application (par exemple, mobile celui, en plus d’un bureau existant), vous allez rompu votre page d’accueil de bureau standard. Elle échoue avec l’erreur «*plusieurs types correspondant au contrôleur nommé « Accueil » ont été trouvés*». Pour résoudre ce problème, vous devez mettre à jour la configuration de routage de niveau supérieur (dans Global.asax.cs) pour spécifier que votre bureau HomeController doit être prioritaires lorsqu’il existe une ambiguïté :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample9.cs)]

À présent l’erreur passe et l’URL http://*yoursite*/ atteint la page d’accueil de bureau et http://*yoursite*/mobile/ va atteindre la page d’accueil mobile.

### <a name="redirecting-mobile-visitors-to-your-mobile-area"></a>Redirection des visiteurs mobiles à votre zone mobile

Il existe plusieurs points d’extensibilité de différents dans ASP.NET MVC, donc de nombreuses méthodes possibles pour injecter la logique de redirection. Il est une option intéressante pour créer un attribut de filtre, tel que [RedirectMobileDevicesToMobileArea], qui effectue une redirection si les conditions suivantes sont remplies :

1. Elle est la première requête dans la session utilisateur (par exemple, Session.IsNewSession est égal à true)
2. La demande provient d’un navigateur mobile (par exemple, Request.Browser.IsMobileDevice est égal à true)
3. L’utilisateur n’est pas déjà demandant une ressource dans la zone de mobile (par exemple, le *chemin d’accès* partie de l’URL ne commence pas par /Mobile)

L’exemple téléchargeable inclus dans ce livre blanc inclut une implémentation de cette logique. Il est implémenté comme un filtre d’autorisation, dérivé d’AuthorizeAttribute, ce qui signifie qu’il puisse fonctionner correctement même si vous utilisez la mise en cache de sortie (dans le cas contraire, si un accès de première visiteur bureau certaine une URL, la sortie de bureau peuvent être mis en cache et pris en charge pour ultérieures visiteurs mobiles).

Comme il s’agit d’un filtre, vous pouvez choisir pour l’appliquer aux contrôleurs spécifiques et les actions, par exemple,

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample10.cs)]

… ou vous pouvez l’appliquer à tous les contrôleurs et les actions comme un MVC 3 *filtre global* dans votre fichier Global.asax.cs :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample11.cs)]

L’exemple téléchargeable montre également comment vous pouvez créer des sous-classes de cet attribut qui redirigent vers des emplacements spécifiques au sein de votre zone mobile. Cela signifie, par exemple, que vous pouvez :

- Inscrire un filtre global comme indiqué ci-dessus qui envoie les visiteurs mobiles à la page d’accueil mobile par défaut.
- Également appliquer un filtre [RedirectMobileDevicesToMobileProductPage] spécial à une action « product vue » qui accepte les visiteurs mobiles à la version mobile de toute page du produit qu’ils avaient demandée.
- S’appliquent également autres sous-classes spéciale du filtre à des actions spécifiques, redirigeant les visiteurs mobiles à la page mobile équivalente

### <a name="configuring-forms-authentication-to-respect-your-mobile-pages"></a>Configuration de l’authentification par formulaire afin de respecter vos pages mobiles

Si vous utilisez l’authentification par formulaire, vous devez noter que lorsqu’un utilisateur doit se connecter, il redirige automatiquement l’utilisateur vers une seule « session » URL spécifique, qui est par défaut **/Account / d’ouverture de session**. Cela signifie que les utilisateurs mobiles peuvent être redirigés vers votre action bureau « connexion ».

Pour éviter ce problème, ajouter une logique à votre bureau action « session » afin que les utilisateurs mobiles effectue une redirection pour une action « session » mobile. Si vous utilisez la valeur par défaut du modèle d’application ASP.NET MVC, action mettre à jour de AccountController d’ouverture de session comme suit :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample12.cs)]

… puis implémentez une action de « session » approprié mobiles spécifiques sur un contrôleur appelé AccountController dans votre région Mobile.

### <a name="working-with-output-caching"></a>Utilisation de la mise en cache de sortie

Si vous utilisez le filtre [OutputCache], vous devez forcer l’entrée de cache à varient selon le type de périphérique. Par exemple, écrire :

[!code-javascript[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample13.js)]

Ensuite, ajoutez la méthode suivante à votre fichier Global.asax.cs :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample14.cs)]

Cela permettra que mobiles visiteurs de la page ne recevez pas de sortie précédemment placée dans le cache par un visiteur de bureau.

### <a name="a-working-example"></a>Un exemple fonctionnel

Pour afficher ces techniques en action, téléchargez [code de ce livre blanc associés exemples](https://docs.microsoft.com/aspnet/mobile/overview). L’exemple inclut une application ASP.NET MVC 3 (Release Candidate) améliorée pour prendre en charge des appareils mobiles à l’aide des méthodes décrites ci-dessus.

## <a name="further-guidance-and-suggestions"></a>Autres conseils et des suggestions

La discussion suivante s’applique à la fois aux Web Forms et MVC développeurs qui utilisent les techniques décrites dans ce document.

### <a name="enhancing-your-redirection-logic-using-51degreesmobi-foundation"></a>Amélioration de votre logique de redirection à l’aide de 51Degrees.mobi Foundation

La logique de redirection indiquée dans ce document peut être parfaitement suffire pour votre application, mais il ne fonctionne pas si vous avez besoin désactiver des sessions, ou avec les navigateurs mobiles rejeter les cookies (il ne peut pas avoir de sessions), car il ne sait pas si une requête donnée est la première condition à partir de ce visiteur.

Vous est déjà appris comment la 51Degrees.mobi open source Foundation peut améliorer la précision de ASP. Détection du navigateur du réseau. Il a également une capacité intégrée pour rediriger les visiteurs mobiles à des emplacements spécifiques configurés dans le fichier Web.config. Il est en mesure de travailler sans selon les Sessions ASP.NET (et par conséquent, les cookies) en stockant un journal temporaire des hachages d’en-têtes HTTP et les adresses IP des visiteurs, par conséquent, il sait ou non de chaque demande est le premier à partir d’un vistor donné.

L’élément suivant est ajouté à la section fiftyOne du fichier web.config redirige la première demande à partir de la détection d’un périphérique mobile vers la page ~ / Mobile/Default.aspx. Toutes les demandes vers les pages, dans le dossier Mobile seront *pas* redirigé, quel que soit le type de périphérique. Si le périphérique mobile a été inactif pendant une période de 20 minutes ou plus de l’appareil sera oublié et les demandes suivantes seront traités en tant que nouvelles dans le cadre de la redirection.

[!code-xml[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample15.xml)]

Pour plus d’informations, consultez [51degrees.mobi documentation Foundation](https://github.com/51Degrees/dotNET-Device-Detection).

> [!NOTE]
> Vous *pouvez* fonctionnalité de redirection de la Fondation utilisation 51Degrees.mobi sur les applications ASP.NET MVC, mais vous devez définir votre configuration de la redirection en termes d’URL bruts, pas en termes de paramètres de routage ou en plaçant des filtres MVC sur les actions. Il s’agit, car (au moment de l’écriture) 51Degrees.mobi Foundation ne reconnaît pas les filtres ou routage.


### <a name="disabling-transcoders-and-proxy-servers"></a>La désactivation de transcodeurs et serveurs Proxy

Les opérateurs de réseau mobile ont deux objectifs large dans leur approche internet mobile :

1. Fournir en tant que contenu applique autant que possible
2. Maximiser le nombre de clients qui peuvent partager la bande passante limitée de cases d’option

Étant donné que la plupart des pages web sont conçus pour les grands écrans de taille de bureau et les connexions haut débit fixe d’accélérée en ligne, de nombreux opérateurs utilisent *transcodeurs* ou *serveurs proxy* qui modifier dynamiquement le contenu web. Ils peuvent modifier votre balisage HTML ou les CSS pour satisfaire des écrans plus petits (en particulier pour « fonctionnalité téléphones » qui ne disposent pas de la puissance de traitement pour gérer des dispositions complexes), et ils peuvent recompresser vos images (considérablement réduire leur qualité) pour améliorer la vitesse de remise de page.

Mais si vous avez pris l’effort à produire une version optimisée mobile de votre site, vous ne souhaitez probablement l’opérateur de réseau à interférer avec lui les autres. Vous pouvez ajouter la ligne suivante à la Page\_événement de chargement dans un Web Form ASP.NET :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample16.cs)]

Ou, pour un contrôleur ASP.NET MVC, vous pouvez ajouter la substitution de méthode suivante afin qu’elle s’applique à toutes les actions sur ce contrôleur :

[!code-csharp[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample17.cs)]

Le message HTTP résultant informe les transcodeurs conformes W3C et les proxys ne pas à modifier le contenu. Bien entendu, il n’existe aucune garantie que les opérateurs de réseau mobile respecte ce message.

### <a name="styling-mobile-pages-for-mobile-browsers"></a>Conception de styles pour les pages mobiles pour les navigateurs mobiles

Il s’agit dépasse le cadre de ce document pour décrire en détail les types de travail de balisage HTML correctement ou les techniques de conception web optimiser la facilité d’utilisation de périphériques particuliers. Il a à vous permet de rechercher une mise en page suffisamment simple, optimisé pour un écran de taille mobile, sans utiliser de non fiable HTML ou des astuces de positionnement CSS. Une technique importante intéressant de mentionner, toutefois, est la *balise meta viewport*.

Certains navigateurs mobiles modernes, dans un effort affichage des pages web destiné à des moniteurs de bureau, afficher la page dans une zone virtuelle, également appelée « fenêtre d’affichage » (par exemple, la fenêtre d’affichage virtuel est 980 pixels de larges sur iPhone et 850 pixels de larges sur Opera Mobile par défaut), puis sur les pixels physiques de l’écran à l’échelle le résultat vers le bas. L’utilisateur peut ensuite faire un zoom et panoramique de cette fenêtre d’affichage. Cela présente l’avantage qu’il permet le navigateur pour afficher la page dans sa disposition prévue, mais il est également présente un inconvénient : qu’il force le zoom et défilement à l’aide, qui n’est pas pratique pour l’utilisateur. Si vous créez pour mobile, il est préférable de conception pour un écran étroit afin qu’aucun effet de zoom ou de défilement horizontal n’est nécessaire.

Indiquer le navigateur mobile à la largeur la fenêtre d’affichage doit être est via le non standard *la fenêtre d’affichage* balise meta. Par exemple, si vous ajoutez le code suivant à la section d’en-tête de votre page,

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample18.html)]

… prise en charge des navigateurs de smartphone puis disposer de la page sur une zone de dessin virtuel 480 pixels. Cela signifie que si vos éléments HTML définissent leurs largeurs en pourcentage, les pourcentages seront interprétés par rapport à cette largeur de 480 pixels, pas la largeur de la fenêtre d’affichage par défaut. Par conséquent, l’utilisateur est moins susceptible d’avoir à effectuer un zoom et faire défiler horizontalement – considérablement améliorer l’expérience de navigation mobile.

Si vous souhaitez que la largeur de la fenêtre d’affichage pour faire correspondre les pixels physiques du périphérique, vous pouvez spécifier les éléments suivants :

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample19.html)]

Pour que cela fonctionne correctement, vous devez forcer pas explicitement éléments dépasse cette largeur (par exemple, à l’aide un *largeur* attribut ou propriété CSS), due à utiliser une fenêtre d’affichage plus grande quel que soit le navigateur. Voir aussi : [plus de détails sur l’étiquette de la fenêtre d’affichage non standard](https://developer.apple.com/library/safari/#documentation/AppleApplications/Reference/SafariHTMLRef/Articles/MetaTags.html).

Prend en charge des smartphones les plus récents *double orientation*: ils peuvent être contenues dans le mode portrait ou paysage. Par conséquent, il est important de ne pas faire d’hypothèses concernant la largeur d’écran en pixels. Ne supposez même que la largeur d’écran est fixe, car l’utilisateur peut réorienter leur appareil pendant qu’elles sont sur votre page.

Les périphériques Windows Mobile et Blackberry anciens peuvent également accepter les balises meta suivantes dans l’en-tête de page pour indiquer leur contenu a été optimisé pour mobile et ne doit donc pas être transformée.

[!code-html[Main](add-mobile-pages-to-your-aspnet-web-forms-mvc-application/samples/sample20.html)]

## <a name="additional-resources"></a>Ressources supplémentaires

Pour obtenir la liste des émulateurs de périphérique mobile et simulateurs, vous pouvez utiliser pour tester votre application de web mobile ASP.NET, consultez la page [simuler des appareils mobiles courants pour le test](../mobile/device-simulators.md).

## <a name="credits"></a>Crédits

- Auteur principal : Steven Sanderson
- Réviseurs / supplémentaires enregistreurs de contenu : James Rosewell, Mikael Söderström, Scott Hanselman, Scott Hunter
