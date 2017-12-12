---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-2
title: "Partie 2 : Contrôleurs | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 2 couvre les contrôleurs."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/21/2011
ms.topic: article
ms.assetid: 998ce4e1-9d72-435b-8f1c-399a10ae4360
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-2
msc.type: authoredcontent
ms.openlocfilehash: bdafd751e996e759d516d0fa25b09eff21241ed7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-2-controllers"></a>Partie 2 : contrôleurs
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 2 couvre les contrôleurs.


Avec les infrastructures web classique, les URL entrantes sont généralement mappés à des fichiers sur disque. Par exemple : une demande pour une URL comme « / Products.aspx » ou « / Products.php » peut être traité par un fichier « Products.aspx » ou « Products.php ».

Infrastructures Web MVC mappent des URL au code serveur d’une manière légèrement différente. Au lieu de mapper des URL entrantes aux fichiers, ils mappent à la place des URL aux méthodes sur les classes. Ces classes sont appelées « Contrôleurs » et qu’ils sont chargés de traiter les requêtes HTTP entrantes, la gestion des entrées d’utilisateur, la récupération et l’enregistrement de données et déterminer la réponse à envoyer au client (afficher le code HTML, télécharger un fichier, rediriger vers un autre URL, etc.).

## <a name="adding-a-homecontroller"></a>Ajout d’un HomeController

Nous allons commencer notre application de magasin de musique MVC en ajoutant une classe de contrôleur qui gérera les URL pour la page d’accueil de notre site. Nous respectent les conventions d’affectation de noms par défaut d’ASP.NET MVC et appelez-le HomeController.

Cliquez sur le dossier « Contrôleurs » dans l’Explorateur de solutions et sélectionnez « Ajouter », puis le « contrôleur... » commande :

![](mvc-music-store-part-2/_static/image1.jpg)

La boîte de dialogue « Ajouter un contrôleur » s’affiche. Nommez le contrôleur « HomeController » et appuyez sur le bouton Ajouter.

![](mvc-music-store-part-2/_static/image1.png)

Cela va créer un nouveau fichier, HomeController.cs, avec le code suivant :

[!code-csharp[Main](mvc-music-store-part-2/samples/sample1.cs)]

Pour démarrer aussi simples que possible, nous allons remplacer la méthode de l’Index avec une méthode simple qui retourne simplement une chaîne. Nous allons effectuer deux modifications :

- Modifier la méthode pour retourner une chaîne au lieu d’une classe ActionResult
- Modifier l’instruction return pour retourner « Hello de Home »

La méthode doit maintenant ressembler à ceci :

[!code-csharp[Main](mvc-music-store-part-2/samples/sample2.cs)]

## <a name="running-the-application"></a>Exécution de l'application

Maintenant, exécutez le site. Nous pouvons démarrer notre serveur web et tester le site en utilisant l’une des opérations suivantes :

- Choisissez l’élément de menu Débogage ⇨ démarrer le débogage
- Cliquez sur le bouton de flèche verte dans la barre d’outils![](mvc-music-store-part-2/_static/image2.jpg)
- Utilisez le raccourci clavier, F5.

En utilisant l’une des étapes ci-dessus Compiler notre projet et forcer le serveur de développement ASP.NET qui est intégrée à Visual Web Developer pour démarrer. Une notification s’affiche dans la partie inférieure de l’écran pour indiquer que le serveur de développement ASP.NET a démarré et affiche le numéro de port qu’il s’exécute sous.

![](mvc-music-store-part-2/_static/image2.png)

Visual Web Developer s’ouvre ensuite automatiquement une fenêtre de navigateur dont l’URL pointe vers votre serveur web. Cela nous permettra de tester rapidement votre application web :

![](mvc-music-store-part-2/_static/image3.png)

OK, ce qui était assez rapide – nous avons créé un nouveau site Web, ajouté une fonction de trois lignes, et nous avons le texte dans un navigateur. Pas fusée science, mais il s’agit d’un point de départ.

*Remarque : Visual Web Developer inclut le serveur de développement ASP.NET, qui exécutera votre site Web sur un nombre aléatoire libre « port ». Dans la capture d’écran ci-dessus, le site est en cours d’exécution à `http://localhost:26641/`, afin qu’il utilise le port 26641. Votre numéro de port sera différent. Lorsque nous parlons /Store/Browse like de l’URL de ce didacticiel, qui passe une fois le numéro de port. En supposant qu’un numéro de port de 26641, accédant au magasin/Parcourir signifie accédant à `http://localhost:26641/Store/Browse`.*

## <a name="adding-a-storecontroller"></a>Ajout d’un StoreController

Nous avons ajouté un HomeController simple qui implémente la Page d’accueil de notre site. Vous allez maintenant ajouter un autre contrôleur que nous allons utiliser pour implémenter les fonctionnalités de navigation de notre magasin de musique. Notre contrôleur magasin prendra en charge trois scénarios :

- Une page de liste des genres de musique dans notre magasin de musique
- Une page de navigation qui répertorie tous les albums de musique dans un genre particulier
- Une page de détails qui affiche des informations sur un album de musique spécifique

Nous allons commencer en ajoutant une nouvelle classe StoreController... Si vous n’avez pas encore, arrêter l’exécution de l’application soit en fermant le navigateur ou en sélectionnant l’élément de menu Débogage ⇨ arrêter le débogage.

Maintenant, ajoutez un nouveau StoreController. Comme nous l’avons fait HomeController, nous ferez en cliquant sur le dossier « Contrôleurs » dans l’Explorateur de solutions et en choisissant Ajouter -&gt;élément de menu de contrôleur

![](mvc-music-store-part-2/_static/image4.png)

Notre nouvelle StoreController a déjà une méthode de « Index ». Nous allons utiliser cette méthode de « Index » à implémenter notre page relative à la liste qui répertorie tous les genres dans notre magasin de musique. Nous allons également ajouter deux autres méthodes pour implémenter les deux autres scénarios nous voulons que notre StoreController à gérer : Parcourir et détails.

Ces méthodes (Index, parcourir et détails) dans notre contrôleur sont appelées « Contrôleur Actions », et que vous avez déjà vu avec la méthode d’action HomeController.Index (), leur travail est pour répondre aux demandes d’URL et déterminer (en règle générale) de contenu doit être envoyé vers le navigateur ou l’utilisateur qui a appelé l’URL.

Nous allons commencer notre implémentation StoreController en modifiant la méthode theIndex() pour retourner la chaîne « Hello de Store.Index() » et nous allons ajouter des méthodes semblables pour Browse() et Details() :

[!code-csharp[Main](mvc-music-store-part-2/samples/sample3.cs)]

Exécuter à nouveau le projet et parcourir les URL suivantes :

- / Store
- / Magasin/Parcourir
- / / Détails du magasin

L’accès à ces URL pour appeler les méthodes d’action dans notre contrôleur et renvoie des réponses de la chaîne :

![](mvc-music-store-part-2/_static/image5.png)

Qui est élevée, mais ce sont des chaînes constantes uniquement. Nous allons les rendre dynamique, afin de prendre des informations à partir de l’URL et afficher dans la sortie de page.

Tout d’abord, nous allons modifier la méthode d’action Parcourir pour récupérer une valeur de chaîne de requête à partir de l’URL. Pour cela, nous pouvons ajouter un paramètre de « genre » à la méthode d’action. Lorsque cela, nous ASP.NET MVC passe automatiquement les paramètres de publication de chaîne de requête ou un formulaire nommés « genre » à la méthode d’action lorsqu’il est appelé.

[!code-csharp[Main](mvc-music-store-part-2/samples/sample4.cs)]

*Remarque : Nous utilisons la méthode utilitaire HttpUtility.HtmlEncode expurgation de l’entrée d’utilisateur. Cela empêche les utilisateurs à partir de Javascript injectant notre vue avec un lien comme /Store/Browse ? Genre =&lt;script&gt;window.location= 'http://hackersite.com'&lt;/script&gt;.*

Maintenant nous allons accédez au magasin/Parcourir ? Genre = Disco

![](mvc-music-store-part-2/_static/image6.png)

Nous allons ensuite de modifier l’action de détails pour lire et afficher un paramètre d’entrée nommé ID. Contrairement à la méthode précédente, nous ne sera pas incorporer la valeur d’ID comme paramètre de chaîne de requête. Nous allons à la place l’incorporer directement dans l’URL proprement dite. Par exemple : /Store/Details/5.

ASP.NET MVC permet de facilement effectuer cette opération sans avoir à configurer quoi que ce soit. Convention de routage par défaut de ASP.NET MVC est à traiter le segment d’URL après le nom de la méthode d’action comme un paramètre nommé « ID ». Si votre méthode d’action possède un paramètre nommé ID puis ASP.NET MVC sera automatiquement passer le segment d’URL pour vous en tant que paramètre.

[!code-csharp[Main](mvc-music-store-part-2/samples/sample5.cs)]

Exécutez l’application et accédez à /Store/Details/5 :

![](mvc-music-store-part-2/_static/image7.png)

Récapitulons ce que nous avons faites jusqu'à présent :

- Nous avons créé un nouveau projet ASP.NET MVC dans Visual Web Developer
- Nous avons décrit la structure de dossiers de base d’une application ASP.NET MVC
- Nous avons appris comment exécuter notre site Web à l’aide du serveur de développement ASP.NET
- Nous avons créé deux classes de contrôleur : un HomeController et un StoreController
- Nous avons ajouté des méthodes d’Action à nos contrôleurs qui répondent aux demandes d’URL et retournent du texte dans le navigateur


>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-1.md)
[Suivant](mvc-music-store-part-3.md)
