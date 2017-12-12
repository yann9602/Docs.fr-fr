---
uid: mvc/overview/older-versions-1/nerddinner/use-controllers-and-views-to-implement-a-listingdetails-ui
title: "Utiliser des contrôleurs et vues pour implémenter une interface utilisateur/détails de l’annonce | Documents Microsoft"
author: microsoft
description: "Étape 4 montre comment ajouter un contrôleur à l’application qui tire parti de notre modèle pour fournir aux utilisateurs une expérience de navigation de référencement/données..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 64116e56-1c9a-4f07-8097-bb36cbb6e57f
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/use-controllers-and-views-to-implement-a-listingdetails-ui
msc.type: authoredcontent
ms.openlocfilehash: 2f9148a2d419863229e2c5a2a0c98984001fcee5
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="use-controllers-and-views-to-implement-a-listingdetails-ui"></a>Utiliser des contrôleurs et vues pour implémenter une interface utilisateur/détails de l’annonce
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 4 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 4 montre comment ajouter un contrôleur à l’application qui tire parti de notre modèle pour fournir aux utilisateurs une expérience de navigation/détails de l’annonce de données pour préparés sur notre site NerdDinner.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-4-controllers-and-views"></a>De NerdDinner étape 4 : Contrôleurs et les vues

Avec les infrastructures web classique (classic ASP, PHP, ASP.NET Web Forms, etc.), les URL entrantes sont généralement mappées aux fichiers sur le disque. Par exemple : une demande pour une URL comme « / Products.aspx » ou « / Products.php » peut être traité par un fichier « Products.aspx » ou « Products.php ».

Infrastructures Web MVC mappent des URL au code serveur d’une manière légèrement différente. Au lieu de mapper des URL entrantes aux fichiers, ils mappent à la place des URL aux méthodes sur les classes. Ces classes sont appelées « Contrôleurs » et qu’ils sont chargés de traiter les requêtes HTTP entrantes, la gestion des entrées d’utilisateur, la récupération et l’enregistrement de données et déterminer la réponse à envoyer au client (afficher le code HTML, télécharger un fichier, rediriger vers un autre URL, etc.).

Maintenant que nous avons créé un modèle de base pour notre application NerdDinner, notre étape suivante sera pour ajouter un contrôleur à l’application qui tire parti de celle-ci pour fournir aux utilisateurs une expérience de navigation/détails de l’annonce de données pour préparés sur notre site.

### <a name="adding-a-dinnerscontroller-controller"></a>Ajout d’un contrôleur DinnersController

Nous allons commencer en cliquant sur le dossier « Contrôleurs » dans notre projet web, puis sélectionnez le **Add -&gt;contrôleur** commande de menu (vous pouvez également exécuter cette commande en tapant Ctrl-M, Ctrl-C) :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image1.png)

La boîte de dialogue « Ajouter un contrôleur » s’affiche :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image2.png)

Nous allons nommer le nouveau contrôleur de « DinnersController » et cliquez sur le bouton « Ajouter ». Visual Studio ajoute ensuite un fichier DinnersController.cs sous le répertoire de notre \Controllers :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image3.png)

Il ouvre également la nouvelle classe DinnersController dans l’éditeur de code.

### <a name="adding-index-and-details-action-methods-to-the-dinnerscontroller-class"></a>Ajout de Index() et les méthodes d’Action Details() à la classe DinnersController

Vous souhaitez activer à l’aide de notre application pour parcourir une liste de préparés à venir et leur permettent de cliquer sur n’importe quel dîner dans la liste pour afficher les détails spécifiques concernant les visiteurs. Pour cela, nous allons les URL suivantes à partir de notre application de publication :

| **URL** | **Fonction** |
| --- | --- |
| */Dinners/* | Afficher une liste HTML de préparés à venir |
| */Dinners/détails / [id]* | Afficher des détails sur un dîner spécifique indiqué par un paramètre « id » incorporé dans l’URL qui correspond à la DinnerID du dîner dans la base de données. Par exemple : /Dinners/Details/2 affiche une page HTML avec des détails sur le dîner DinnerID dont la valeur est 2. |

Nous publierons implémentations initiales de ces URL en ajoutant deux public « méthodes d’action » à la classe DinnersController comme ci-dessous :

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample1.cs)]

Nous allons ensuite exécuter l’application de NerdDinner et notre navigateur permet d’appeler. Tapant dans le *« Préparés / »* URL entraîne notre *Index()* méthode à exécuter et il sera renvoyer la réponse suivante :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image4.png)

Tapant dans le *« / préparés/détails/2 »* URL entraîne notre *Details()* méthode à exécuter et renvoie la réponse suivante :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image5.png)

Vous vous demandez peut-être - comment ASP.NET MVC connaissiez pour créer notre classe DinnersController et appeler ces méthodes ? De comprendre que nous allons prendre un coup de œil le fonctionnement du routage.

### <a name="understanding-aspnet-mvc-routing"></a>Routage de présentation ASP.NET MVC

ASP.NET MVC inclut un puissant moteur de routage d’URL qui fournit une grande souplesse pour contrôler la façon dont les URL sont mappés aux classes de contrôleur. Il nous permet de personnaliser entièrement la façon dont ASP.NET MVC choisit la classe de contrôleur à créer, la méthode à appeler sur celle-ci, ainsi que configurer les différents moyens variables peuvent être automatiquement analysés à partir de la chaîne de requête/URL et transmis à la méthode en tant que paramètre arguments. Il offre la flexibilité nécessaire pour totalement optimiser un site pour SEO (optimisation de moteur de recherche) ainsi que toute structure URL que nous souhaitons à partir d’une application de publication.

Par défaut, les nouveaux projets ASP.NET MVC sont fournis avec un ensemble préconfiguré de règles de routage d’URL déjà enregistrée. Cela permet de facilement commencer sur une application sans avoir à configurer explicitement quoi que ce soit. Les inscriptions de règle de routage par défaut se trouve dans la classe « Application » de nos projets : nous pouvons ouvrir en double-cliquant sur le fichier « Global.asax » à la racine du projet :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image6.png)

Les règles de routage ASP.NET MVC par défaut sont enregistrés dans la méthode « RegisterRoutes » de cette classe :

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample2.cs)]

Les itinéraires ». MapRoute() » appel de méthode ci-dessus enregistre une règle de routage par défaut qui mappe les URL entrantes aux classes de contrôleur à l’aide du format d’URL : » / {controller} / {action} / {id} », où « contrôleur » est le nom de la classe de contrôleur à instancier, « action » est le nom d’un une méthode publique à appeler sur et « id » est un paramètre facultatif est incorporé dans l’URL qui peut être passé en tant qu’argument à la méthode. Le troisième paramètre passé à l’appel de méthode « MapRoute() » est un ensemble de valeurs par défaut à utiliser pour les valeurs de contrôleur / / id d’action dans le cas où elles ne sont pas présentes dans l’URL (contrôleur = « Home », Action = « Index », Id = « »).

Voici une table qui montre comment une variété d’URL sont mappées à l’aide de la valeur par défaut «*/ {contrôleurs} / {action} / {id} »*règle de routage :

| **URL** | **Classe de contrôleur** | **Méthode d’action** | **Paramètres passés** |
| --- | --- | --- | --- |
| */ Préparés/détails/2* | DinnersController | Details(ID) | ID = 2 |
| */ Préparés/modifier/5* | DinnersController | Edit(ID) | ID = 5 |
| */ Préparés/créer* | DinnersController | Create() | N/A |
| */ Préparés* | DinnersController | Index() | N/A |
| */ Home* | HomeController | Index() | N/A |
| */* | HomeController | Index() | N/A |

Les trois dernières lignes affichent les valeurs par défaut (contrôleur = Home, Action = Index, Id = « ») qui est utilisé. Étant donné que la méthode de « Index » est enregistrée en tant que le nom d’action par défaut s’il n’est pas spécifié, le « / préparés » et « /Home » URL cause la méthode d’action Index() à appeler sur leurs classes de contrôleur. Étant donné que le contrôleur « Home » est inscrit en tant que le contrôleur par défaut s’il n’est pas spécifié, l’URL « / » provoque le HomeController doit être créé et la méthode d’action Index() dessus à appeler.

Si vous n’aimez pas ces règles de routage d’URL par défaut, la bonne nouvelle est qu’ils sont faciles à modifier, de les modifier dans la méthode RegisterRoutes ci-dessus. Pour notre application NerdDinner, cependant, nous n’allons pas modifier les règles de routage d’URL par défaut, nous allons utiliser plutôt en tant que-est.

### <a name="using-the-dinnerrepository-from-our-dinnerscontroller"></a>À l’aide de la DinnerRepository à partir de notre DinnersController

Remplaçons maintenant notre implémentation actuelle de la DinnersController méthodes d’action Index() et Details() avec les implémentations qui utilisent notre modèle.

Nous allons utiliser la classe DinnerRepository que nous avons créé précédemment pour implémenter le comportement. Nous allons commencer en ajoutant une instruction « using » qui fait référence à l’espace de noms « NerdDinner.Models » et déclare une instance de notre DinnerRepository en tant que champ sur notre classe DinnerController.

Plus loin dans ce chapitre nous présentent le concept de « Injection de dépendance » et afficher une autre méthode pour obtenir une référence à un DinnerRepository qui permet une meilleure unité notre contrôleurs test – mais droit maintenant nous allons créer simplement une instance de notre DinnerRepository inline, comme ci-dessous.

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample3.cs)]

Nous sommes maintenant prêts à générer une réponse HTML à l’aide de ses objets de modèle de données récupérées.

### <a name="using-views-with-our-controller"></a>Utilisation de vues avec notre contrôleur

Bien qu’il soit possible d’écrire du code dans nos méthodes d’action pour assembler HTML, puis utiliser le *Response.Write ()* méthode d’assistance pour l’envoyer au client, qu’approche devient assez complexe rapidement. Une meilleure approche est pour nous uniquement effectuer l’application et la logique des données à l’intérieur de nos méthodes d’action DinnersController et transmettez les données requises pour restituer une réponse HTML à un modèle distinct « vue » qui est responsable de la sortie de la représentation sous forme de HTML de celui-ci. Comme nous le verrons dans un moment, un modèle de « afficher » est un fichier texte qui contient généralement une combinaison de balisage HTML et le code de rendu incorporé.

Séparation notre logique du contrôleur à partir de notre affichage offre plusieurs avantages. En particulier il permet d’appliquer une « séparation nette » entre le code d’application et le code de mise en forme/rendu de l’interface utilisateur. Cela rend beaucoup plus facile à la logique d’application de test unitaire de manière isolée à partir de la logique de rendu de l’interface utilisateur. Elle facilite la modifier ultérieurement les modèles de rendu de l’interface utilisateur sans avoir à apporter des modifications de code d’application. Et elle peut rendre plus facile pour les développeurs et concepteurs de collaborer sur des projets.

Nous pouvons mettre à jour notre classe DinnersController pour indiquer que vous voulez utiliser un modèle d’affichage pour renvoyer une réponse de l’interface utilisateur HTML en modifiant les signatures de méthode nos deux de méthodes d’action de l’utilisation d’un type de retour de « void » à la place ayant un type de retour de « ActionResult ». Nous pouvons appeler ensuite la *View()* méthode d’assistance sur la classe de base de contrôleur pour retourner un objet « ViewResult » comme ci-dessous :

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample4.cs)]

La signature de la *View()* méthode d’assistance que nous utilisons ci-dessus se présente comme ci-dessous :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image7.png)

Le premier paramètre de la *View()* méthode d’assistance est le nom du fichier de modèle d’affichage à utiliser pour restituer la réponse HTML. Le deuxième paramètre est un objet de modèle qui contient les données dont le modèle d’affichage a besoin pour afficher la réponse HTML.

Dans notre méthode d’action Index() que nous appelons le *View()* méthode d’assistance et en indiquant que vous voulez afficher une liste HTML de préparés à l’aide d’un modèle d’affichage de « Index ». Nous passons une séquence d’objets dîner pour générer la liste à partir du modèle d’affichage :

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample5.cs)]

Dans notre méthode d’action Details(), nous tentons de récupérer un objet dîner à l’aide de l’id fourni dans l’URL. Si un dîner valid est trouvé, nous appelons le *View()* méthode d’assistance, qui indique que nous souhaitons utiliser un modèle d’affichage « Details » pour restituer l’objet Dinner récupéré. Si un dîner non valide est demandée, nous restituer un message d’erreur utile qui indique que le dîner n’existe pas à l’aide d’un modèle d’affichage « NotFound » (et une version surchargée de la *View()* méthode d’assistance qui simplement prend le nom du modèle ):

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample6.cs)]

Nous allons maintenant implémenter les modèles d’affichage « NotFound », « Détails » et « Index ».

### <a name="implementing-the-notfound-view-template"></a>Implémentation du modèle d’affichage « NotFound »

Nous allons commencer en implémentant le modèle d’affichage « NotFound », qui affiche un message d’erreur convivial indiquant que le dîner demandé est introuvable.

Nous allons créer un nouveau modèle d’affichage en plaçant votre curseur de texte au sein d’une méthode d’action de contrôleur et ensuite avec le bouton droit, choisissez la commande de menu « Ajouter une vue » (nous pouvons également exécuter cette commande en tapant Ctrl-M, Ctrl + V) :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image8.png)

Cela affiche une boîte de dialogue « Ajouter une vue », comme ci-dessous. Par défaut, que la boîte de dialogue préremplir le nom de la vue à créer pour correspondre au nom de la méthode d’action le curseur lors de dans la boîte de dialogue a été lancée (dans ce cas, « Détails »). Étant donné que nous souhaitons tout d’abord implémenter le modèle « NotFound », nous remplacer ce nom de la vue et définir à la place comme « NotFound » :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image9.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio va créer un nouveau modèle de vue « NotFound.aspx » pour nous dans le répertoire « \Views\Dinners » (qui sera également créé si le répertoire n’existe pas déjà) :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image10.png)

Il ouvre également notre nouveau modèle d’affichage de « NotFound.aspx » dans l’éditeur de code :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image11.png)

Afficher les modèles par défaut ont deux « contenu » où nous pouvons ajouter le contenu et code. Le premier permet de personnaliser la « title » de la page HTML renvoyé. La deuxième permet de personnaliser le « contenu principal » de la page HTML renvoyé.

Pour mettre en œuvre de notre modèle de vue « NotFound », nous allons ajouter du contenu de base :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample7.aspx)]

Nous pouvons ensuite essayer dans le navigateur. Pour cela, nous allons demande le *« / préparés/détails/9999 »* URL. Cela fait référence à un dîner qui n’existe pas actuellement dans la base de données et de notre méthode d’action DinnersController.Details() à restituer de notre modèle de vue « NotFound » :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image12.png)

Vous remarquerez que dans la capture d’écran ci-dessus est que notre modèle de vue de base a hérité d’un ensemble de code HTML qui entoure le contenu principal à l’écran. Il s’agit, car notre modèle d’affichage utilise un modèle « page maître » qui permet d’appliquer une disposition cohérente à toutes les vues sur le site. Nous verrons comment des pages maîtres plus dans une partie ultérieure de ce didacticiel.

### <a name="implementing-the-details-view-template"></a>Implémentation du modèle d’affichage « Détails »

Nous allons maintenant implémenter le modèle d’affichage « Détails » – qui générera du code HTML pour un seul modèle dîner.

Nous allons cela en plaçant votre curseur de texte au sein de la méthode d’action plus d’informations, puis avec le bouton droit sur et choisissez la commande de menu « Ajouter une vue » (ou appuyez sur Ctrl-M, Ctrl + V) :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image13.png)

La boîte de dialogue « Ajouter une vue » s’affiche. Nous allons conserver le nom de la vue par défaut (« détails »). Nous allons également sélectionner la case à cocher « Créer une vue fortement typée » dans la boîte de dialogue et sélectionnez (à l’aide de la liste déroulante de combobox) le nom du type de modèle que nous passons à partir du contrôleur à la vue. Pour cette vue, nous passons un objet Dinner (le nom complet de ce type est : « NerdDinner.Models.Dinner ») :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image14.png)

Contrairement au modèle précédent, où nous avons choisi de créer une « vue vide », cette fois, à que nous allons choisir automatiquement « structurez » la vue à l’aide d’un modèle « Details ». Nous pouvons indiquer cela en modifiant la liste déroulante « Afficher le contenu » dans la boîte de dialogue ci-dessus.

« Génération de modèles automatique » génère une implémentation initiale de notre modèle de vue Détails basé sur l’objet Dinner que nous sommes en lui passant. Cela fournit un moyen simple pour que nous puissions commencer rapidement sur notre implémentation de modèle de vue.

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio va créer un nouveau fichier de modèle de vue « Details.aspx » pour nous dans notre répertoire « \Views\Dinners » :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image15.png)

Il ouvre également notre nouveau modèle d’affichage de « Details.aspx » dans l’éditeur de code. Il contient une implémentation d’une vue de structure initiale d’une vue Détails basée sur un modèle Dinner. Le moteur de génération de modèles automatique utilise la réflexion .NET pour examiner les propriétés publiques exposées sur la classe transmises et ajoute le contenu approprié en fonction de chaque type qu’elle trouve :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample8.aspx)]

Nous pouvons demander le *« / préparés/détails/1 »* URL pour afficher un aperçu de cette implémentation scaffold « details » dans le navigateur. À l’aide de cette URL affiche l’un des préparés, que nous avons ajouté manuellement à notre base de données lorsque nous avons tout d’abord créé :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image16.png)

Nous avons rapidement opérationnel, il nous fournit une implémentation initiale de notre fichier details.aspx. Nous pouvons ensuite accéder et le modifier pour personnaliser l’interface utilisateur à notre satisfaction.

Lorsque nous examinons plus en détail le modèle Details.aspx, nous trouverez qu’il contient du code HTML statique ainsi que rendu code intégré. &lt;%%&gt; pépites de code d’exécuter du code lorsque le modèle d’affichage est rendu, et &lt;% = %&gt; pépites de code exécuter le code qu’ils contiennent, puis pour restituer le résultat dans le flux de sortie du modèle.

Nous pouvons écrire le code au sein de notre vue qui accède à l’objet de modèle « Dîner » qui a été passé à partir de notre contrôleur à l’aide d’une propriété fortement typée de « Modèle ». Visual Studio fournit nous avec code-intellisense complète lors de l’accès à cette propriété « Modèle » dans l’éditeur :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image17.png)

Apportons quelques ajustements afin que la source de notre modèle de vue Détails final se présente comme ci-dessous :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample9.aspx)]

Lorsque nous l’accès le *« / préparés/détails/1 »* URL à nouveau ce qu’il va maintenant rendu comme ci-dessous :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image18.png)

### <a name="implementing-the-index-view-template"></a>Implémentation du modèle d’affichage « Index »

Nous allons maintenant implémenter le modèle d’affichage « Index » – qui génère une liste de préparés à venir. Action, nous allons positionner notre curseur de texte au sein de la méthode d’action Index et ensuite avec le bouton droit cliquez sur et choisissez la commande de menu « Ajouter une vue » (ou appuyez sur Ctrl-M, Ctrl + V).

Dans la boîte de dialogue « Ajouter une vue » nous conserver le modèle d’affichage nommé « Index » et sélectionnez la case à cocher « Créer une vue fortement typée ». Cette fois, nous allons choisir automatiquement générer un modèle d’affichage « Liste » et sélectionnez « NerdDinner.Models.Dinner » comme type de modèle passées à la vue (qui, car nous avons indiqué, nous allons créer une vue entraîne la boîte de dialogue Ajouter une vue à supposer que nous sont « liste » en passant une séquence d’objets de dîner à partir de notre contrôleur à la vue) :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image19.png)

Lorsque vous cliquez sur le bouton « Ajouter », Visual Studio créera un nouveau fichier de modèle de vue « Index.aspx » pour nous dans notre répertoire « \Views\Dinners ». Il sera de « structurez » une implémentation initiale dans celui-ci qui fournit une liste de table HTML des préparés que vous passez à la vue.

Quand nous exécutons l’accès aux applications et le *« Préparés / »* URL, il doit rendre notre liste de préparés comme suit :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image20.png)

La solution de table ci-dessus nous offre une mise en page de grille de nos données Dinner : qui ne fait pas tout à fait ce que nous souhaitons pour notre consommateur faisant face à la liste Dinner. Nous pouvons mettre à jour le modèle d’affichage Index.aspx et modifiez-le pour répertorier les moins de colonnes de données et utiliser un &lt;ul&gt; élément afin de les restituer au lieu d’une table en utilisant le code ci-dessous :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample10.aspx)]

Nous utilisons le mot-clé « var » dans l’instruction foreach ci-dessus comme nous une boucle sur chaque dîner dans notre modèle. Celles êtes pas familiarisé avec c# 3.0 peuvent considérer qu’à l’aide de « var » signifie que l’objet dîner est à liaison tardive. Cela signifie plutôt que le compilateur utilise l’inférence de type par rapport à la propriété « Modèle » fortement typée (qui est de type « IEnumerable&lt;Dinner&gt;») et la compilation de la variable locale « dîner » comme type de dîner – ce qui signifie que nous obtenons complètes IntelliSense et la vérification pour celui-ci dans les blocs de code lors de la compilation :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image21.png)

Lorsque nous avons atteint l’actualisation à la */Dinners* URL dans le navigateur notre vue mise à jour se présente comme ci-dessous :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image22.png)

Cette recherche mieux – mais entièrement n’existait pas encore. Notre dernière étape consiste à activer les utilisateurs finaux de cliquer sur préparés individuels dans la liste et de voir les détails les concernant. Nous allons implémenter cela en les affichant les éléments de lien hypertexte HTML qui sont liées à la méthode d’action de détails sur notre DinnersController.

Nous pouvons générer ces liens hypertexte dans notre vue d’Index de deux manières. La première consiste à créer manuellement un fichier HTML &lt;un&gt; éléments tels que ci-dessous, où nous incorporer &lt;%%&gt; bloque dans la &lt;un&gt; élément HTML :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image23.png)

Une autre approche que nous pouvons utiliser consiste à tirer parti de la méthode d’assistance « Html.ActionLink() » intégrée dans ASP.NET MVC qui prend en charge la création d’un élément HTML par programmation &lt;un&gt; élément qui établit un lien vers une autre méthode d’action sur un Contrôleur :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample11.aspx)]

Le premier paramètre à la méthode d’assistance Html.ActionLink() est le texte du lien pour afficher (dans ce cas, le titre de la dinner), le deuxième paramètre est le nom d’action de contrôleur que nous voulons générer le lien pour (dans ce cas, la méthode de détails), et le troisième paramètre est un ensemble de paramètres à envoyer à l’action (implémentée comme un type anonyme avec le nom de propriété/valeur). Dans ce cas, nous spécifions le paramètre « id » de la dîner à lier à et nous étant donné que le routage d’URL par défaut de règle dans ASP.NET MVC est « {Controller} / {Action} / {id} » la méthode d’assistance Html.ActionLink() génère la sortie suivante :

[!code-html[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample12.html)]

Pour notre vue Index.aspx nous allons utiliser l’approche de méthode d’assistance Html.ActionLink() et ont des chaque dîner dans le lien de la liste à l’URL de détails appropriés :

[!code-aspx[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample13.aspx)]

Et maintenant, lorsque nous avons atteint la */Dinners* URL notre liste dinner se présente comme ci-dessous :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image24.png)

Lorsque vous cliquez sur un des préparés dans la liste nous allons naviguer pour afficher les détails concernant :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image25.png)

### <a name="convention-based-naming-and-the-views-directory-structure"></a>Basée sur une convention d’affectation de noms et la structure de répertoires \Views

Les applications ASP.NET MVC par défaut utilisent un répertoire basée sur une convention d’affectation de noms structure lors de la résolution des modèles d’affichage. Cela permet aux développeurs d’éviter de devoir qualifiez entièrement un chemin d’accès de l’emplacement lors du référencement des vues à partir d’une classe de contrôleur. Par défaut, ASP.NET MVC recherchera le fichier de modèle de vue dans le * \Views\[ControllerName]\* répertoire en dessous de l’application.

Par exemple, nous avons travaillé sur la classe DinnersController – qui fait explicitement référence à trois modèles d’affichage : « Index », « Détails » et « NotFound ». ASP.NET MVC par défaut recherchera ces vues dans le *\Views\Dinners* répertoire sous le répertoire racine de notre application :

![](use-controllers-and-views-to-implement-a-listingdetails-ui/_static/image26.png)

Notez ci-dessus comment il existe actuellement trois classes de contrôleur dans le projet (DinnersController, HomeController et AccountController – les deux dernières ont été ajoutés par défaut, lorsque nous avons créé le projet), et qu’il existe trois sous-répertoires (un pour chaque contrôleur) dans le répertoire \Views.

Vues référencées à partir des contrôleurs accueil et comptes de résoudre automatiquement leurs modèles d’affichage de respectifs *\Views\Home* et *\Views\Account* répertoires. Le *\Views\Shared* sous-répertoire fournit un moyen de stocker les modèles d’affichage qui sont réutilisées par plusieurs contrôleurs de l’application. Lorsque ASP.NET MVC tente de résoudre un modèle d’affichage, il vérifie d’abord dans le *\Views\[contrôleur]* répertoire spécifique, et si elle ne peut pas trouver le modèle de vue il il ressemblera au sein de la *\Views\ Partagé* active.

Lorsqu’il s’agit de modèles de vue individuelle d’affectation de noms, la recommandation est d’avoir au modèle d’affichage de partager le même nom que la méthode d’action qui a provoqué son rendu. Par exemple, au-dessus de notre « Index » méthode d’action est à l’aide de la vue « Index » pour afficher le résultat de la vue, et la méthode d’action « Détails » est à l’aide de la vue « Détails » pour afficher ses résultats. Cela facilite la visualiser rapidement le modèle est associé à chaque action.

Les développeurs n’avez pas besoin de spécifier explicitement le nom de modèle d’affichage lorsque le modèle d’affichage a le même nom que la méthode d’action invoquée sur le contrôleur. Nous pouvons à la place simplement transmettre l’objet de modèle à la méthode d’assistance « View() » (sans spécifier le nom de la vue) et ASP.NET MVC déduira automatiquement que vous voulez utiliser le *\Views\[ControllerName]\[ActionName]* modèle d’affichage sur le disque pour la rendre.

Cela permet de nettoyer un peu de notre code de contrôleur et éviter de dupliquer le nom à deux reprises dans notre code :

[!code-csharp[Main](use-controllers-and-views-to-implement-a-listingdetails-ui/samples/sample14.cs)]

Le code ci-dessus est tout ce qui est nécessaire pour implémenter une jolie Dinner/détails annonce expérience pour le site.

#### <a name="next-step"></a>Étape suivante

Nous disposons désormais d’une jolie Dinner construit l’expérience de navigation.

Nous allons à présent activer édition prise en charge du formulaire de données CRUD (création, lecture, mise à jour, suppression).

>[!div class="step-by-step"]
[Précédent](build-a-model-with-business-rule-validations.md)
[Suivant](provide-crud-create-read-update-delete-data-form-entry-support.md)
