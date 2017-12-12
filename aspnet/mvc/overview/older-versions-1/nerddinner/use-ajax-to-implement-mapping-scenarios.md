---
uid: mvc/overview/older-versions-1/nerddinner/use-ajax-to-implement-mapping-scenarios
title: "Utiliser AJAX pour implémenter des scénarios de mappage | Documents Microsoft"
author: microsoft
description: "Étape 11 montre comment intégrer la prise en charge du mappage AJAX dans notre application NerdDinner, permettant aux utilisateurs qui créent, modification ou affichage préparés pour voir le l..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: f731990a-0a81-4d62-81df-87d676cdedd6
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/use-ajax-to-implement-mapping-scenarios
msc.type: authoredcontent
ms.openlocfilehash: cc55560ce691826b6d52971b16d0515ed73d72a6
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="use-ajax-to-implement-mapping-scenarios"></a>Utiliser AJAX pour implémenter des scénarios de mappage
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 11 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 11 montre comment intégrer la prise en charge du mappage AJAX dans notre application NerdDinner, permettant aux utilisateurs qui créent, modification ou affichage préparés pour afficher l’emplacement de la dinner graphiquement.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-11-integrating-an-ajax-map"></a>NerdDinner étape 11 : Intégrer un mappage AJAX

Nous allons maintenant effectuer notre application un peu plus visuellement attrayante en intégrant la prise en charge du mappage AJAX. Cela permettra aux utilisateurs qui créent, modification ou affichage préparés pour afficher l’emplacement de la dinner graphiquement.

### <a name="creating-a-map-partial-view"></a>Création d’une vue partielle de carte

Nous allons utiliser la fonctionnalité de mappage à plusieurs endroits au sein de notre application. Pour conserver notre code sec nous allons encapsulent les fonctionnalités courantes de la carte dans un seul modèle partielle que nous pouvons réutiliser entre plusieurs actions de contrôleur et les vues. Nous nommez cette vue partielle « map.ascx » et le créer dans le répertoire \Views\Dinners.

Nous pouvons créer la map.ascx partielle en cliquant sur le répertoire \Views\Dinners et en choisissant Ajouter -&gt;afficher la commande de menu. Nous allons nommer la vue « Map.ascx », il recherche une vue partielle et indiquer que nous allons passer une classe de modèle fortement typé « dîner » :

![](use-ajax-to-implement-mapping-scenarios/_static/image1.png)

Lorsque vous cliquez sur le bouton « Ajouter » partielle de notre modèle sera créée. Nous allons ensuite mettre à jour le fichier Map.ascx pour que le contenu suivant :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample1.aspx)]

La première &lt;script&gt; points de référence pour la bibliothèque de mappage Microsoft Virtual Earth 6.2. La seconde &lt;script&gt; référence pointe vers un fichier map.js que nous allons créer peu de temps qui doit encapsuler notre logique de mappage Javascript commune. Le &lt;div id = « theMap »&gt; élément est le conteneur HTML Virtual Earth utilisera pour héberger la carte.

Nous avons ensuite incorporé &lt;script&gt; bloc qui contient deux fonctions JavaScript spécifiques à cette vue. La première fonction utilise jQuery à câble d’une fonction qui s’exécute lorsque la page est prête à exécuter un script côté client. Il appelle une fonction d’assistance de LoadMap() que nous allons définir dans le fichier de script Map.js pour charger le contrôle de carte virtual earth. La deuxième fonction est un gestionnaire d’événements de rappel qui ajoute un code confidentiel à la carte qui identifie un emplacement.

Notez la façon dont nous utilisons un côté serveur &lt;% = %&gt; bloc dans le bloc de script côté client pour incorporer la latitude et la longitude du dîner nous voulons mapper dans le code JavaScript. Cette technique est utile pour générer des valeurs dynamiques qui peuvent être utilisés par un script côté client (sans nécessiter un AJAX appel séparé sur le serveur pour récupérer les valeurs – ce qui le rend plus rapide). Le &lt;% = %&gt; blocs ne seront exécute lorsque la vue est rendu sur le serveur, et par conséquent, la sortie du code HTML simplement se retrouvent avec des valeurs JavaScript incorporés (par exemple : var latitude = 47.64312 ;).

### <a name="creating-a-mapjs-utility-library"></a>Création d’une bibliothèque d’utilitaires Map.js

Nous allons maintenant créer le fichier Map.js que nous pouvons utiliser pour encapsuler les fonctionnalités de JavaScript pour notre carte (et implémenter les méthodes LoadMap et LoadPin ci-dessus). Nous pouvons faire cela en cliquant sur le répertoire \Scripts dans notre projet, puis choisissez le « Add -&gt;un nouvel élément « commande de menu, sélectionnez l’élément JScript et nommez-le « Map.js ».

Voici le code JavaScript, nous allons ajouter au fichier Map.js qui interagit avec Virtual Earth à afficher notre plan et ajoutez-y des codes confidentiels emplacements pour notre préparés :

[!code-javascript[Main](use-ajax-to-implement-mapping-scenarios/samples/sample2.js)]

### <a name="integrating-the-map-with-create-and-edit-forms"></a>Intégration de la carte avec la création et modification de formulaires

Nous allons à présent intégrer la prise en charge de la carte avec nos scénarios existants de créer et modifier. La bonne nouvelle est que cela est assez facile action et ne nécessite pas pour modifier un de nos code du contrôleur. Notre créer et modifier les vues partagent une vue partielle « DinnerForm » commune pour implémenter l’interface utilisateur du formulaire dîner, nous pouvons ajouter la carte dans un seul emplacement et ont nos scénarios créer et modifier à l’utiliser.

Il suffit d’action consiste à ouvrir la vue partielle \Views\Dinners\DinnerForm.ascx et mettre à jour pour inclure de notre nouveau mappage partielle. Voici à quoi ressemblent le DinnerForm mis à jour une fois que la carte est ajoutée (Remarque : les éléments de formulaire HTML sont omis de l’extrait de code ci-dessous par souci de concision) :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample3.aspx)]

Le DinnerForm partielle ci-dessus prend un objet de type « DinnerFormViewModel » comme type de modèle (car il a besoin d’un objet dîner, ainsi qu’un SelectList pour remplir l’objet dropdownlist de pays). Notre carte partielle doit juste un objet de type « Dîner » comme type de modèle, et par conséquent, lorsque nous restituer la carte partielle nous sommes simplement la dîner sous-propriété du DinnerFormViewModel en lui passant :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample4.aspx)]

La fonction JavaScript que nous avons ajouté à la jQuery partielle utilise pour attacher un événement de « flou » à la zone de texte « Address » HTML. Vous n’avez probablement entendu onglets ou les événements « focus » qui se déclenchent lorsqu’un utilisateur clique dans une zone de texte. L’inverse est un événement de « flou » qui se déclenche lorsqu’un utilisateur quitte une zone de texte. Le Gestionnaire d’événements ci-dessus efface les valeurs de zone de texte de latitude et longitude lorsque cela se produit et représente le nouvel emplacement de l’adresse sur notre carte. Un gestionnaire d’événements de rappel que nous avons défini dans le fichier map.js sera puis mettre à jour les zones de texte de longitude et de latitude sur notre formulaire à l’aide des valeurs retournées par virtual earth basé sur l’adresse que nous lui avez attribué.

Et maintenant lorsque nous réexécuter votre application et cliquez sur l’onglet « Hôte Dinner » nous verrons une valeur par défaut de mapper affiche ainsi que nos éléments de formulaire Dinner standards :

![](use-ajax-to-implement-mapping-scenarios/_static/image2.png)

Tapez une adresse, et puis onglet absent, le mappage met à jour dynamiquement pour afficher l’emplacement et notre gestionnaire d’événements remplit les zones de texte latitude/longitude avec les valeurs d’emplacement :

![](use-ajax-to-implement-mapping-scenarios/_static/image3.png)

Si nous enregistrer le nouveau dîner et ouvrez de nouveau pour la modification, nous vous trouverez que l’emplacement de la carte s’affiche lorsque la page charge :

![](use-ajax-to-implement-mapping-scenarios/_static/image4.png)

Chaque fois que le champ adresse est modifié, le mappage et les coordonnées de latitude/longitude met à jour.

Maintenant que la carte affiche l’emplacement du dîner, nous pouvons également modifier les champs de formulaire de Latitude et Longitude ne soient pas visibles zones de texte pour être à la place les éléments masqués (étant donné que la carte est automatiquement les mettre à jour chaque fois qu’une adresse est entrée). Action cela nous allons basculer à partir de l’aide du programme d’assistance HTML de Html.TextBox() à l’aide de la méthode d’assistance Html.Hidden() :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample5.aspx)]

Et maintenant notre forms sont un peu plus conviviales et éviter d’afficher la latitude/longitude brute (tout en stockant toujours avec chaque dîner dans la base de données) :

![](use-ajax-to-implement-mapping-scenarios/_static/image5.png)

### <a name="integrating-the-map-with-the-details-view"></a>Intégration de la carte avec la vue Détails

Maintenant que nous avons intégré avec nos scénarios créer et modifier le mappage de, nous allons également intégrer à notre scénario de détails. Il suffit d’action consiste à appeler &lt;: Html.RenderPartial("map") ; %&gt; dans l’affichage des détails.

Voici à quoi ressemble le code source à la vue Détails complet (avec l’intégration de la carte) :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample6.aspx)]

Et maintenant lorsqu’un utilisateur accède à une URL de /Dinners/détails / [id] s’affichent plus d’informations sur le dîner, l’emplacement de la dinner sur la carte (avec une punaise qui une fois dessus affiche le titre de la dinner et l’adresse de celle-ci), et possèdent un lien AJAX pour RSVP fo r il :

![](use-ajax-to-implement-mapping-scenarios/_static/image6.png)

### <a name="implementing-location-search-in-our-database-and-repository"></a>L’implémentation de la recherche de l’emplacement dans notre base de données et le référentiel

Pour terminer notre implémentation AJAX, vous allez ajouter un mappage à la page d’accueil de l’application qui permet aux utilisateurs de rechercher graphiquement préparés près d’eux.

![](use-ajax-to-implement-mapping-scenarios/_static/image7.png)

Nous allons commencer en mettant en œuvre la prise en charge dans notre couche référentiel de base de données et les données pour effectuer efficacement une recherche basée sur l’emplacement de radius pour préparés. Nous pouvons utiliser la nouvelle [fonctions géospatiales de SQL 2008](https://www.microsoft.com/sqlserver/2008/en/us/spatial-data.aspx) à implémenter, ou vous pouvez également utiliser une approche de la fonction SQL qui Gary Dryden abordé dans l’article ici : [http://www.codeproject.com/KB/cs/ distancebetweenlocations.aspx](http://www.codeproject.com/KB/cs/distancebetweenlocations.aspx) et Rob Conery vécu ici à l’aide de LINQ to SQL : [http://blog.wekeroad.com/2007/08/30/linq-and-geocoding/](http://blog.wekeroad.com/2007/08/30/linq-and-geocoding/)

Pour implémenter cette technique, nous sera ouvrir l’Explorateur de serveurs » » dans Visual Studio, sélectionnez la base de données de NerdDinner et puis avec le bouton droit sur le sous-nœud de « fonctions » dans cette section et choisir de créer une nouveau « fonction scalaire » :

![](use-ajax-to-implement-mapping-scenarios/_static/image8.png)

Nous allons coller ensuite dans la fonction DistanceBetween suivante :

[!code-sql[Main](use-ajax-to-implement-mapping-scenarios/samples/sample7.sql)]

Nous allons ensuite créer une nouvelle fonction table incluse dans SQL Server, que nous appellerons « NearestDinners » :

![](use-ajax-to-implement-mapping-scenarios/_static/image9.png)

Cette fonction de la table « NearestDinners » utilise la fonction d’assistance DistanceBetween pour renvoyer tous les préparés dans 100 miles de latitude et longitude que nous fournir :

[!code-sql[Main](use-ajax-to-implement-mapping-scenarios/samples/sample8.sql)]

Pour appeler cette fonction, nous allons tout d’abord ouvrir le concepteur LINQ to SQL en double-cliquant sur le fichier NerdDinner.dbml dans notre répertoire \Models :

![](use-ajax-to-implement-mapping-scenarios/_static/image10.png)

Nous allons puis faites glisser les fonctions NearestDinners et DistanceBetween sur LINQ pour concepteur SQL, ce qui entraîne à ajouter en tant que méthodes sur notre LINQ à SQL NerdDinnerDataContext classe :

![](use-ajax-to-implement-mapping-scenarios/_static/image11.png)

Nous pouvons, exposer une méthode de requête « FindByLocation » sur notre classe DinnerRepository qui utilise la fonction NearestDinner pour retourner à venir préparés qui se trouvent dans 100 miles de l’emplacement spécifié :

[!code-csharp[Main](use-ajax-to-implement-mapping-scenarios/samples/sample9.cs)]

### <a name="implementing-a-json-based-ajax-search-action-method"></a>Implémentation d’une méthode d’Action de recherche JSON en fonction de AJAX

Nous allons maintenant implémenter une méthode d’action de contrôleur qui tire parti de la nouvelle méthode de référentiel FindByLocation() pour retourner une liste de données dîner qui peuvent être utilisées pour remplir un mappage. Nous aurons cette méthode d’action retournent les données dîner dans un format JSON (JavaScript Object Notation) afin qu’il peut être facilement manipulé à l’aide de JavaScript sur le client.

Pour l’implémenter, nous allons créer une nouvelle classe « SearchController » en cliquant sur le répertoire \Controllers et en choisissant Ajouter -&gt;commande de menu de contrôleur. Nous allons ensuite implémenter une méthode d’action « SearchByLocation » dans la nouvelle classe SearchController comme ci-dessous :

[!code-csharp[Main](use-ajax-to-implement-mapping-scenarios/samples/sample10.cs)]

Méthode d’action de la SearchController SearchByLocation appelle en interne la méthode FindByLocation sur DinnerRespository pour obtenir une liste de préparés à proximité. Plutôt que de retourner les objets Dinner directement au client, cependant, il retourne à la place des objets de JsonDinner. La classe JsonDinner expose un sous-ensemble des propriétés de dîner (par exemple : pour des raisons de sécurité qu’il ne divulguer les noms des personnes qui ont auxquels elle répondu pour un dîner). Il inclut également une propriété RSVPCount qui n’existe pas sur Dinner – et qui est calculé de façon dynamique en comptant le nombre d’objets RSVP associé à un dîner particulier.

Nous utilisons ensuite la méthode d’assistance Json() sur la classe de base du contrôleur pour retourner la séquence de préparés à l’aide d’un format de transmission de basée sur JSON. JSON est un format de texte standard pour représenter des structures de données simples. Voici un exemple d’une liste au format JSON de deux objets JsonDinner aspect lorsque renvoyé à partir de la méthode d’action :

[!code-json[Main](use-ajax-to-implement-mapping-scenarios/samples/sample11.json)]

### <a name="calling-the-json-based-ajax-method-using-jquery"></a>Appel de la méthode JSON en fonction de AJAX utilisant jQuery

Nous sommes maintenant prêts à mettre à jour de la page d’accueil de l’application de NerdDinner à utiliser la méthode d’action de la SearchController SearchByLocation. Des tâches à effectuer cette opération, nous ouvrir le modèle d’affichage /Views/Home/Index.aspx et mettre à jour pour qu’il dispose d’une zone de texte, bouton Rechercher, notre table et un &lt;div&gt; élément nommé dinnerList :

[!code-aspx[Main](use-ajax-to-implement-mapping-scenarios/samples/sample12.aspx)]

Nous pouvons ensuite ajouter deux fonctions JavaScript à la page :

[!code-html[Main](use-ajax-to-implement-mapping-scenarios/samples/sample13.html)]

La première fonction JavaScript charge la carte lors du premier charge de la page. Les deuxième fils de fonction JavaScript d’un code JavaScript cliquez sur Gestionnaire d’événements sur le bouton de recherche. Lorsque le bouton est activé, il appelle la fonction FindDinnersGivenLocation() JavaScript qui nous allons ajouter à notre fichier Map.js :

[!code-javascript[Main](use-ajax-to-implement-mapping-scenarios/samples/sample14.js)]

Cette fonction FindDinnersGivenLocation() appelle la carte. Find() sur le contrôle de terre virtuel pour centrer sur l’emplacement spécifié. Lorsque le service de carte virtual earth est retournée, la carte. Find() méthode appelle la méthode de rappel callbackUpdateMapDinners que nous le passé comme argument final.

La méthode callbackUpdateMapDinners() est où le travail est terminé. Il utilise la méthode d’assistance de $.post() de jQuery pour effectuer un appel AJAX de méthode d’action de notre SearchController SearchByLocation() – en lui passant la latitude et la longitude de la carte qui vient d’être centrée. Il définit une fonction inline qui sera appelée lorsque la méthode d’assistance $.post() se termine, et les résultats au format JSON de dîner retourné à partir de la méthode d’action est passée à l’aide d’une variable appelée « préparés » de SearchByLocation(). Il effectue d’une instruction foreach via chaque dinner retourné et que vous utilise les latitude du dîner et de longitude et d’autres propriétés pour ajouter un nouveau code confidentiel de la carte. Il ajoute également une entrée dîner à la liste HTML de préparés à droite de la carte. Il puis fils à distance un événement de pointage pour les clics-infos et la liste HTML afin que les détails concernant le dîner s’affichent lorsqu’un utilisateur pointe dessus :

[!code-html[Main](use-ajax-to-implement-mapping-scenarios/samples/sample15.html)]

Et maintenant lorsque nous, exécutez l’application et visitez la page d’accueil que nous s’affiche avec une carte. Lorsque nous Entrez le nom d’une ville la carte affiche les prochaines préparés à proximité :

![](use-ajax-to-implement-mapping-scenarios/_static/image12.png)

Vous pointez sur un dîner affichera les détails à ce sujet.

En cliquant sur le titre de Dinner dans la bulle ou sur le côté droit de la liste HTML accéderez nous dîner – ce qui nous pouvons RSVP puis si vous le souhaitez pour :

![](use-ajax-to-implement-mapping-scenarios/_static/image13.png)

### <a name="next-step"></a>Étape suivante

Nous avons maintenant implémenté toutes les fonctionnalités d’application de notre application NerdDinner. Nous allons maintenant examiner comment nous pouvons activer automatisée unité test de celui-ci.

>[!div class="step-by-step"]
[Précédent](use-ajax-to-deliver-dynamic-updates.md)
[Suivant](enable-automated-unit-testing.md)
