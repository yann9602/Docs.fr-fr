---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-web-services
title: "Présentation des Services Web ASP.NET AJAX | Documents Microsoft"
author: scottcate
description: "Services Web font partie intégrante du .NET framework qui fournissent une solution multiplateforme pour échanger des données entre des systèmes distribués. Bien que Web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/28/2008
ms.topic: article
ms.assetid: 3332d6e7-e2e1-4144-b805-e71d51e7e415
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-asp-net-ajax-web-services
msc.type: authoredcontent
ms.openlocfilehash: 8eb3486c9b3f4ddb6a8bc2c1cdcac774a6852574
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-aspnet-ajax-web-services"></a>Présentation des Services Web ASP.NET AJAX
====================
par [ble pour indiquer Scott](https://github.com/scottcate)

[Télécharger le PDF](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial05_Web_Services_with_MS_Ajax_cs.pdf)

> Services Web font partie intégrante du .NET framework qui fournissent une solution multiplateforme pour échanger des données entre des systèmes distribués. Bien que les Services Web sont généralement utilisés pour autoriser différents systèmes d’exploitation, les modèles objet et les langages de programmation pour envoyer et recevoir des données, ils peuvent également servir à dynamiquement injecter des données dans une page ASP.NET AJAX ou envoyer des données à partir d’une page à un système back-end. Tout ceci peut être effectuée sans avoir recours pour les opérations de publication (postback).


## <a name="calling-web-services-with-aspnet-ajax"></a>Appel de Services Web avec ASP.NET AJAX

Dan Wahlin

Services Web font partie intégrante du .NET framework qui fournissent une solution multiplateforme pour échanger des données entre des systèmes distribués. Bien que les Services Web sont généralement utilisés pour autoriser différents systèmes d’exploitation, les modèles objet et les langages de programmation pour envoyer et recevoir des données, ils peuvent également servir à dynamiquement injecter des données dans une page ASP.NET AJAX ou envoyer des données à partir d’une page à un système back-end. Tout ceci peut être effectuée sans avoir recours pour les opérations de publication (postback).

Alors que le contrôle UpdatePanel d’ASP.NET AJAX fournit un moyen simple de AJAX activer toutes les pages ASP.NET, il peut arriver que vous deviez dynamiquement accéder aux données sur le serveur sans utiliser un UpdatePanel. Dans cet article, vous verrez comment accomplir cette tâche en créant et consommer des Services Web dans les pages ASP.NET AJAX.

Cet article se concentrera sur les fonctionnalités disponibles dans les Extensions ASP.NET AJAX de base, ainsi que d’un contrôle de Service Web est activé dans la boîte à outils ASP.NET AJAX est appelé le AutoCompleteExtender. Les rubriques couvertes incluent définissant les Services Web compatibles AJAX, créer des proxys clients et appel de Services Web avec JavaScript. Vous verrez également comment les appels de Service Web peuvent être effectués directement à des méthodes de page ASP.NET.

## <a name="web-services-configuration"></a>Configuration des Services Web

Lorsqu’un nouveau projet de Site Web est créé avec Visual Studio 2008, le fichier web.config présente un certain nombre de nouveaux ajouts qui peuvent être peu aux utilisateurs de versions antérieures de Visual Studio. Certaines de ces modifications mappent le préfixe « asp » pour les contrôles ASP.NET AJAX pour qu’ils puissent être utilisées dans les pages alors que d’autres définissent requis HttpHandlers et HttpModules. Le listing 1 illustre les modifications apportées à la `<httpHandlers>` élément dans le fichier web.config qui affecte les appels de Service Web. La valeur par défaut que HttpHandler utilisé pour traiter les appels .asmx est supprimée et remplacée par une classe ScriptHandlerFactory située dans l’assembly System.Web.Extensions.dll. System.Web.Extensions.dll contient toutes les fonctionnalités de base utilisée par ASP.NET AJAX.

**La liste 1. Configuration du Gestionnaire de Service Web ASP.NET AJAX**

[!code-xml[Main](understanding-asp-net-ajax-web-services/samples/sample1.xml)]

Ce remplacement HttpHandler est effectué pour permettre les appels JavaScript Objet Notation (JSON) être constitué à partir de pages ASP.NET AJAX pour les Services Web à l’aide d’un proxy de Service Web de JavaScript. ASP.NET AJAX envoie des messages JSON aux Services Web plutôt que les appels SOAP Simple Object Access Protocol () standard associés généralement avec les Services Web. Cela entraîne plus petit messages de demande et réponse globales. Il permet également d’un traitement côté client plus efficace des données depuis la bibliothèque ASP.NET AJAX JavaScript est optimisée pour travailler avec des objets JSON. Liste des 2 et des exemples d’afficher de liste 3 Service Web messages de demande et réponse sérialisés au format JSON. Le message de demande indiqué dans la liste 2 passe un paramètre de pays avec une valeur de « Belgique » alors que le message de réponse dans la liste 3 passe un tableau d’objets de client et leurs propriétés associées.

**Liste 2. Message de demande de Service Web sérialisé au format JSON**

[!code-json[Main](understanding-asp-net-ajax-web-services/samples/sample2.json)]

> *>[!NOTE] le nom de l’opération est défini dans le cadre de l’URL du service web ; en outre, les messages de demande ne sont pas toujours envoyées via JSON. Les Services Web peuvent utiliser l’attribut ScriptMethod avec le paramètre UseHttpGet défini sur true, ce qui entraîne des paramètres à passer un les paramètres de chaîne de requête.*


**La liste 3. Message de réponse de Service Web sérialisé au format JSON**

[!code-json[Main](understanding-asp-net-ajax-web-services/samples/sample3.json)]

Dans la section suivante, vous apprendrez à créer des Services Web compatibles avec la gestion des messages de demande JSON et répond avec les types simples et complexes.

## <a name="creating-ajax-enabled-web-services"></a>Créer des Services Web compatibles AJAX

L’infrastructure ASP.NET AJAX fournit plusieurs façons d’appeler des Services Web. Vous pouvez utiliser le contrôle AutoCompleteExtender (disponible dans ASP.NET AJAX Toolkit) ou JavaScript. Toutefois, avant d’appeler un service vous devez activer AJAX il afin qu’il peut être appelé par le code de script client.

Vous utilisez des Services Web ASP.NET, ou non, vous trouverez il simples à créer et les services AJAX-enable. Le .NET framework prend en charge la création de Services Web ASP.NET depuis sa version initiale dans 2002 et les Extensions ASP.NET AJAX fournissent des fonctionnalités AJAX supplémentaires qui s’appuie sur l’ensemble de fonctionnalités par défaut du .NET framework. Version bêta 2 de Visual Studio .NET 2008 prend en charge pour la création des fichiers du Service Web .asmx et dérive automatiquement le code associé à côté des classes à partir de la classe System.Web.Services.WebService. Lorsque vous ajoutez des méthodes dans la classe, vous devez appliquer l’attribut WebMethod dans pour pouvoir être appelée par les consommateurs de services Web.

La liste 4 montre un exemple d’application de l’attribut WebMethod à une méthode nommée GetCustomersByCountry().

**La liste 4. À l’aide de l’attribut WebMethod dans un Service Web**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample4.cs)]

La méthode GetCustomersByCountry() accepte un paramètre de pays et renvoie un client de tableau d’objets. La valeur du pays passée dans la méthode est transférée à une classe de couche d’entreprise qui à son tour appelle une classe de la couche données pour récupérer les données à partir de la base de données, remplir des propriétés de l’objet client de données et retourner un tableau.

## <a name="using-the-scriptservice-attribute"></a>À l’aide de l’attribut ScriptService

Lors de l’ajout de la méthode Web attribut permet à la méthode GetCustomersByCountry() à être appelé par les clients qui envoient des messages SOAP standard pour le Service Web, il n’autorise pas des appels JSON à partir des applications ASP.NET AJAX. Pour permettre les appels JSON à apporter vous devez appliquer l’Extension AJAX ASP.NET `ScriptService` d’attribut à la classe de Service Web. Cela permet à un Service Web envoyer des messages de réponse au format JSON et permet à un script côté client appeler un service en envoyant des messages JSON.

La liste 5 montre un exemple d’application de l’attribut ScriptService à une classe de Service Web nommée CustomersService.

**La liste 5. À l’aide de l’attribut ScriptService AJAX-activer un Service Web**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample5.cs)]

L’attribut ScriptService agit comme un marqueur indiquant qu’il peut être appelé à partir du code de script AJAX. Il réellement ne gère pas les tâches de sérialisation ou la désérialisation JSON qui se produisent en arrière-plan. Le ScriptHandlerFactory (configuré dans le fichier web.config) et autres classes connexes exécutent la majeure partie du traitement de JSON.

## <a name="using-the-scriptmethod-attribute"></a>À l’aide de l’attribut ScriptMethod

L’attribut ScriptService est le seul attribut d’ASP.NET AJAX qui doit être défini dans un Service Web de .NET pour qu’il doit utiliser les pages ASP.NET AJAX. Toutefois, un autre attribut nommé ScriptMethod peut également être appliqué directement à des méthodes Web dans un service. ScriptMethod définit trois propriétés, y compris `UseHttpGet`, `ResponseFormat` et `XmlSerializeString`. Modification des valeurs de ces propriétés peut être utile dans les cas où le type de demande acceptée par une méthode Web doit être remplacé par GET, une méthode Web doit renvoyer les données XML brutes sous la forme d’un `XmlDocument` ou `XmlElement` objet ou lorsque les données retournées par un  service doit toujours être sérialisé au format XML au lieu de JSON.

La propriété peut être utilisée lorsqu’une méthode Web doit accepter de UseHttpGet obtenir des demandes par opposition aux demandes POST. Les demandes sont envoyées à l’aide d’une URL avec des paramètres d’entrée de méthode Web convertis en paramètres de chaîne de requête. Le UseHttpGet propriété par défaut est false et doit uniquement être définie sur `true` lorsque les opérations sont connues pour être sécurisée et lorsque les données sensibles ne sont pas passées à un Service Web. La liste 6 montre un exemple d’utilisation de l’attribut ScriptMethod avec la propriété UseHttpGet.

**La liste 6. À l’aide de l’attribut ScriptMethod avec la propriété UseHttpGet.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample6.cs)]

Un exemple d’en-têtes envoyés lorsque la méthode Web HttpGetEcho indiqué dans la liste 6 est appelée sont montre l’illustration suivante :

`GET /CustomerViewer/DemoService.asmx/HttpGetEcho?input=%22Input Value%22 HTTP/1.1`

Outre l’octroi des méthodes Web à accepter les demandes HTTP GET, l’attribut ScriptMethod peut également servir lorsque les réponses XML doivent être retournés à partir d’un service plutôt que de JSON. Par exemple, un Service Web peut récupérer un flux RSS à partir d’un site distant et le retourner comme un objet XmlDocument ou XmlElement. Le traitement des données XML donnée peut ensuite se produire sur le client.

La liste 7 montre un exemple d’utilisation de la propriété ResponseFormat pour spécifier que les données XML doivent être renvoyées à partir d’une méthode Web.

**La liste 7. À l’aide de l’attribut ScriptMethod avec la propriété ResponseFormat.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample7.cs)]

La propriété ResponseFormat peut également être utilisée avec la propriété XmlSerializeString. La propriété XmlSerializeString a la valeur false, ce qui signifie que tous les types de retour sauf les chaînes renvoyées à partir d’une méthode Web sont sérialisés au format XML par défaut lors de la `ResponseFormat` est définie sur `ResponseFormat.Xml`. Lorsque `XmlSerializeString` a la valeur `true`, tous les types retournés à partir d’une méthode Web sont sérialisées en XML, y compris les types de chaîne. Si la propriété ResponseFormat a la valeur `ResponseFormat.Json` la propriété XmlSerializeString est ignorée.

Liste 8 montre un exemple d’utilisation de la propriété XmlSerializeString pour forcer les chaînes d’être sérialisées au format XML.

**Liste 8. À l’aide de l’attribut ScriptMethod avec la propriété XmlSerializeString**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample8.cs)]

La valeur retournée par la méthode GetXmlString Web indiqué dans la liste 8 est montre l’illustration suivante :

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample9.cs)]

Bien que le format JSON par défaut réduit la taille globale de messages de demande et de réponse et est plus facilement consommé par les clients ASP.NET AJAX d’une manière entre les navigateurs, les propriétés ResponseFormat et XmlSerializeString peuvent être utilisée lorsque client applications telles que Microsoft Internet Explorer 5 ou version ultérieure attendent que les données XML à retourner à partir d’une méthode Web.

## <a name="working-with-complex-types"></a>Utilisation des Types complexes

La liste 5 a donné lieu à un exemple de retourner un type complexe nommé client à partir d’un Service Web. La classe Customer définit plusieurs types simples en interne en tant que propriétés telles que FirstName et LastName. Les types complexes utilisés en tant que paramètre d’entrée ou le type de retour dans une méthode Web compatibles AJAX sont automatiquement sérialisés en JSON avant leur envoi au côté client. Toutefois, les types complexes imbriqués (ceux définis en interne dans un autre type) ne sont pas rendus disponibles pour le client comme objets autonomes par défaut.

Dans les cas où un type complexe imbriqué utilisé par un Service Web doit également être utilisé dans une page du client, l’attribut d’ASP.NET AJAX GenerateScriptType peut être ajouté au Service Web. Par exemple, la classe CustomerDetails indiquée dans la liste 9 contient les propriétés d’adresse et le sexe qui ***représentent des types complexes imbriqués.***

**Liste 9. La classe CustomerDetails ci-après contient deux types complexes imbriqués.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample10.cs)]

Les objets d’adresse et le sexe définis dans la classe CustomerDetails indiquée dans la liste 9 ne sont pas automatiquement être mis à disposition du côté client à l’aide de JavaScript, car ils sont des types imbriqués (adresse est une classe et le sexe est une énumération). Dans les situations où un type imbriqué est utilisé au sein d’un Service Web doit être disponible sur la côté client, l’attribut GenerateScriptType mentionné plus haut peut servir (voir le Listing 10). Cet attribut peut être ajouté plusieurs fois dans les cas où les différents types complexes imbriqués sont retournées à partir d’un service. Elle peut être appliquée directement à la classe de Service Web ou au-dessus de méthodes Web spécifiques.

**Liste des 10. À l’aide de l’attribut GenerateScriptService pour définir les types imbriqués qui doivent être disponibles pour le client.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample11.cs)]

En appliquant la `GenerateScriptType` attribut aux types de Service Web, l’adresse et le sexe automatiquement seront disponible pour une utilisation par du code ASP.NET AJAX JavaScript côté client. Liste 11 montre le code JavaScript qui est automatiquement généré et envoyé au client en ajoutant l’attribut GenerateScriptType sur un Service Web. Vous allez apprendre à utiliser des types complexes imbriqués plus loin dans l’article.

**La liste 11. Types complexes imbriqués mis à disposition d’une page ASP.NET AJAX.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample12.cs)]

Maintenant que vous avez vu comment créer des Services Web et les rendre accessibles aux pages ASP.NET AJAX, examinons comment créer et utiliser les proxies JavaScript afin que les données peuvent être récupérées ou envoyées aux Services Web.

## <a name="creating-javascript-proxies"></a>Création des Proxies JavaScript

Appeler un Service Web standard (.NET ou une autre plateforme) généralement implique la création d’un objet proxy qui vous protège contre les complexités de l’envoi de messages de demande et de réponse SOAP. Avec les appels de Service Web de ASP.NET AJAX, les proxies JavaScript peuvent être créées et utilisées pour appeler facilement des services sans se préoccuper de sérialiser et désérialiser les messages JSON. Proxies JavaScript peuvent être générés automatiquement en utilisant le contrôle ASP.NET AJAX ScriptManager.

Création d’un proxy JavaScript qui peut appeler des Services Web s’effectue à l’aide de la propriété de ScriptManager Services. Cette propriété vous permet de définir un ou plusieurs services pour une page ASP.NET AJAX pouvant appeler de façon asynchrone pour envoyer ou recevoir des données sans nécessiter d’opérations de publication (postback). Vous définissez un service à l’aide d’ASP.NET AJAX `ServiceReference` contrôle et l’affectation de l’URL du Service Web du contrôle `Path` propriété. Liste des 12 montre un exemple de référencement d’un service nommé CustomersService.asmx.

[!code-aspx[Main](understanding-asp-net-ajax-web-services/samples/sample13.aspx)]

**Liste des 12. Définition d’un Service Web utilisé dans une page ASP.NET AJAX.**

Ajout d’une référence à la CustomersService.asmx via le contrôle ScriptManager provoque un proxy JavaScript être générés dynamiquement et référencé par la page. Le proxy est incorporé à l’aide de la &lt;script&gt; de balise et chargées dynamiquement en appelant le fichier CustomersService.asmx et en ajoutant /js à la fin de celle-ci. L’exemple suivant montre comment le proxy JavaScript est incorporé dans la page lorsque le débogage est désactivé dans le fichier web.config :

[!code-html[Main](understanding-asp-net-ajax-web-services/samples/sample14.html)]

> *>[!NOTE] Si vous souhaitez voir le code de proxy JavaScript réel qui est généré vous pouvez taper l’URL du service Web .NET voulu dans la zone d’adresse d’Internet Explorer et ajouter /js à la fin de celle-ci.*


Si le débogage est activé dans le fichier web.config qu'une version debug du proxy JavaScript est incorporée dans la page que montre l’illustration suivante :

[!code-html[Main](understanding-asp-net-ajax-web-services/samples/sample15.html)]

Le proxy JavaScript créé par ScriptManager peuvent également être intégré directement dans la page plutôt que référencées à l’aide de la &lt;script&gt; attribut src de la balise. Cela est possible en définissant la propriété InlineScript de ServiceReference True (la valeur par défaut est false). Cela peut être utile lorsqu’un proxy n’est pas partagé sur plusieurs pages, et lorsque vous souhaitez réduire le nombre d’appels réseau vers le serveur. Lorsque InlineScript a la valeur true du script de proxy ne sont pas mises en cache par le navigateur afin de la valeur par défaut false est recommandée dans les cas où le proxy est utilisé par plusieurs pages dans une application ASP.NET AJAX. Voici un exemple d’utilisation de la propriété InlineScript suivant :

[!code-aspx[Main](understanding-asp-net-ajax-web-services/samples/sample16.aspx)]

## <a name="using-javascript-proxies"></a>À l’aide de Proxies JavaScript

Une fois qu’un Service Web est référencé par une page ASP.NET AJAX à l’aide du contrôle ScriptManager, un appel au Service Web et les données retournées peuvent être gérées à l’aide des fonctions de rappel. Un Service Web est appelé par faisant référence à son espace de noms (le cas échéant), nom de classe et le nom de la méthode Web. Tous les paramètres transmis au Service Web peuvent être définies avec une fonction de rappel qui gère les données retournées.

Un exemple d’utilisation d’un proxy JavaScript pour appeler une méthode Web appelée GetCustomersByCountry() est indiqué dans la liste 13. La fonction GetCustomersByCountry() est appelée lorsqu’un utilisateur final clique sur un bouton dans la page.

**Liste de 13. Appel d’un Service Web avec un proxy JavaScript.**

[!code-javascript[Main](understanding-asp-net-ajax-web-services/samples/sample17.js)]

Cet appel fait référence à l’espace de noms InterfaceTraining, CustomersService classe et méthode de GetCustomersByCountry Web définis dans le service. Il transmet une valeur pays obtenu à partir d’une zone de texte ainsi que d’une fonction de rappel nommé OnWSRequestComplete qui doit être appelé lors de l’appel asynchrone du Service Web retourne. OnWSRequestComplete gère le tableau d’objets de client renvoyé par le service et les convertit en une table qui est affichée dans la page. La sortie générée à partir de l’appel est illustrée dans la Figure 1.


[![Liaison de données obtenues en effectuant un appel AJAX asynchrone à un Service Web.](understanding-asp-net-ajax-web-services/_static/image2.png)](understanding-asp-net-ajax-web-services/_static/image1.png)

**Figure 1**: liaison de données obtenu en effectuant un appel AJAX asynchrone à un Service Web.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-web-services/_static/image3.png))


Proxies JavaScript peuvent également effectuer des appels unidirectionnels à des Services Web dans les cas où une méthode Web doit être appelée, mais que le proxy ne doit pas attendre une réponse. Par exemple, vous voudrez appeler un Service Web pour démarrer un processus tel qu’un flux de travail, mais attend pas une valeur de retour à partir du service. Dans les cas où un appel à sens unique doit être effectué à un service, la fonction de rappel indiquée dans la liste 13 peut simplement être omise. Dans la mesure où aucune fonction de rappel n’est définie l’objet proxy n’attendront pas que pour le Service Web retourner des données.

## <a name="handling-errors"></a>Gestion des erreurs

Les rappels asynchrones pour les Services Web peuvent rencontrer des différents types d’erreurs telles que le réseau de l’arrêt du service, le Service Web n’est pas disponible ou une exception qui est retourné. Heureusement, les objets de proxy JavaScript générés par ScriptManager permettent de rappels multiples doivent être définies pour gérer les erreurs et échecs en plus du rappel de réussite présentée précédemment. Une fonction de rappel d’erreur peut être définie immédiatement après la fonction de rappel standard dans l’appel à la méthode Web comme indiqué dans la liste de 14.

**Liste 14. Définition d’une fonction de rappel d’erreur et affichage des erreurs.**

[!code-javascript[Main](understanding-asp-net-ajax-web-services/samples/sample18.js)]

Toutes les erreurs qui se produisent lorsque le Service Web est appelé déclenchera la fonction de rappel OnWSRequestFailed() à appeler qui accepte un objet qui représente l’erreur en tant que paramètre. L’objet d’erreur expose plusieurs fonctions pour déterminer la cause de l’erreur également ou non l’appel a expiré. Liste 14 montre un exemple d’utilisation des fonctions d’erreur différents et la Figure 2 montre un exemple de la sortie générée par les fonctions.


[![Sortie générée par l’appel de fonctions d’erreur ASP.NET AJAX.](understanding-asp-net-ajax-web-services/_static/image5.png)](understanding-asp-net-ajax-web-services/_static/image4.png)

**Figure 2**: sortie générée en appelant des fonctions d’erreur ASP.NET AJAX.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-web-services/_static/image6.png))


## <a name="handling-xml-data-returned-from-a-web-service"></a>La gestion des données XML retournées à partir d’un Service Web

Vous avez vu précédemment comment une méthode Web peut retourner des données XML brutes à l’aide de l’attribut ScriptMethod, ainsi que sa propriété ResponseFormat. ResponseFormat est définie sur ResponseFormat.Xml, les données retournées par le Service Web sont sérialisées en tant que XML plutôt que JSON. Cela peut être utile lorsque les données XML doivent être passés directement au client pour le traitement à l’aide de JavaScript ou XSLT. À l’heure actuelle, Internet Explorer 5 ou version ultérieure fournit le meilleur modèle objet côté client pour l’analyse et de filtrage des données XML en raison de sa prise en charge intégrée pour MSXML.

Extraction de données XML à partir d’un Service Web n’est pas différente de la récupération des autres types de données. Commencez par appeler le proxy JavaScript pour appeler la fonction appropriée et de définir une fonction de rappel. Une fois que l’appel retourne vous pouvez ensuite traiter les données de la fonction de rappel.

Liste 15 montre un exemple d’appel d’une méthode Web appelée GetRssFeed() qui retourne un objet XmlElement. GetRssFeed() accepte un seul paramètre qui représente l’URL pour le flux RSS pour récupérer.

**Liste 15. Utilisation des données XML retournées à partir d’un Service Web.**

[!code-html[Main](understanding-asp-net-ajax-web-services/samples/sample19.html)]

Cet exemple passe une URL à un flux RSS et traite les données XML renvoyées dans la fonction OnWSRequestComplete(). OnWSRequestComplete() vérifie d’abord si le navigateur est Internet Explorer pour connaître l’analyseur MSXML est disponible ou non. S’il s’agit, une instruction XPath est utilisée pour localiser tous les &lt;élément&gt; balises dans le flux RSS. Chaque élément est ensuite parcouru et associé &lt;titre&gt; et &lt;lien&gt; balises sont situés et traités pour afficher les données de chaque élément. La figure 3 montre un exemple de la sortie générée d’apporter une ASP.NET AJAX d’appeler via un proxy JavaScript à la méthode Web GetRssFeed().

## <a name="handling-complex-types"></a>La gestion des Types complexes

Les types complexes accepté ou retourné par un Service Web sont automatiquement exposés via un proxy JavaScript. Toutefois, les types complexes imbriqués ne sont pas directement accessibles sur la côté client, sauf si l’attribut GenerateScriptType est appliqué au service, comme indiqué précédemment. Pourquoi serez vous souhaitez utiliser un type complexe imbriqué côté client ?

Pour répondre à cette question, supposons qu’une page ASP.NET AJAX affiche les données client et permet aux utilisateurs de mettre à jour l’adresse d’un client. Si le Service Web spécifie que le type d’adresse (un type complexe défini dans une classe CustomerDetails) peut être envoyé au client le processus de mise à jour peut être divisé en deux fonctions distinctes pour le code une réutilisation.


[![Création à partir de l’appel d’un Service Web qui retourne les données RSS de sortie.](understanding-asp-net-ajax-web-services/_static/image8.png)](understanding-asp-net-ajax-web-services/_static/image7.png)

**Figure 3**: création à partir de l’appel d’un Service Web qui retourne les données RSS de sortie.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-web-services/_static/image9.png))


Liste 16 montre un exemple de code côté client qui appelle un objet d’adresse défini dans un espace de noms de modèle, il remplit avec les données mises à jour et affecte à la propriété d’adresse d’un objet CustomerDetails. L’objet CustomerDetails est ensuite transmise au Service Web pour traitement.

**Liste de 16. À l’aide des types complexes imbriqués**

[!code-javascript[Main](understanding-asp-net-ajax-web-services/samples/sample20.js)]

## <a name="creating-and-using-page-methods"></a>Création et utilisation des méthodes de Page

Services Web constituent un excellent moyen d’exposer des services réutilisables à une variété de clients, notamment des pages ASP.NET AJAX. Toutefois, il peut arriver dans lequel une page a besoin pour récupérer des données qui ne sont jamais utilisées ou partagées par d’autres pages. Dans ce cas, qui effectue un fichier .asmx pour accéder aux données de la page peut sembler excessifs, car le service est uniquement utilisé par une seule page.

ASP.NET AJAX fournit un autre mécanisme d’appel de type de Service Web sans créer de fichiers .asmx d’autonome. Cela permet à l’aide d’une technique appelée « méthodes de page ». Les méthodes de page sont les méthodes statiques (partagées dans VB.NET) directement incorporés dans un fichier de page ou code-beside qui ont l’attribut WebMethod. En appliquant l’attribut WebMethod, elles peuvent être appelées à l’aide d’un objet JavaScript spécial nommé PageMethods créée dynamiquement lors de l’exécution. L’objet PageMethods agit comme un proxy qui vous protège le processus de sérialisation/désérialisation JSON. Notez que, pour pouvoir utiliser l’objet PageMethods vous devez définir la propriété de ScriptManager EnablePageMethods sur true.

[!code-aspx[Main](understanding-asp-net-ajax-web-services/samples/sample21.aspx)]

Liste de 17 montre un exemple de définition des deux méthodes de page dans une classe code-beside ASP.NET. Ces méthodes récupèrent des données à partir d’une classe de couche métier située dans l’application\_dossier de Code du site Web.

**Liste 17. Définition des méthodes de page.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample22.cs)]

Lorsque ScriptManager détecte la présence des méthodes Web dans la page, il génère une référence dynamique à l’objet PageMethods mentionné précédemment. Appel d’une méthode Web s’effectue en faisant référence à la classe PageMethods suivie du nom de la méthode et toutes les données de paramètre requis qui doivent être passées. Liste 18 présente des exemples de l’appel des deux méthodes de page présentées précédemment.

**Liste 18. Appel de méthodes de page avec l’objet PageMethods JavaScript.**

[!code-javascript[Main](understanding-asp-net-ajax-web-services/samples/sample23.js)]

À l’aide de l’objet PageMethods est très similaire à l’aide d’un objet proxy JavaScript. Vous spécifiez tout d’abord toutes les données de paramètre qui doivent être passées à la méthode de la page, puis définissent la fonction de rappel qui doit être appelée lorsque l’appel asynchrone retourne une valeur. Un rappel d’échec peut également être spécifié (reportez-vous à 14 d’affichage de la liste pour obtenir un exemple de gestion des échecs).

## <a name="the-autocompleteextender-and-the-aspnet-ajax-toolkit"></a>Le AutoCompleteExtender et ASP.NET AJAX Toolkit

ASP.NET AJAX Toolkit (disponible à partir de [http://ajax.asp.net](http://ajax.asp.net)) offre plusieurs contrôles qui peuvent être utilisés pour accéder aux Services Web. Plus précisément, la boîte à outils contienne un contrôle utile nommé `AutoCompleteExtender` qui peut être utilisé pour appeler des Services Web et afficher les données dans les pages sans écrire de code JavaScript du tout.

Le contrôle de AutoCompleteExtender peut être utilisé pour étendre les fonctionnalités existantes d’une zone de texte et aident les utilisateurs à localiser facilement les données recherchées. En tapant dans une zone de texte du contrôle peut être utilisé pour interroger un Service Web et affiche les résultats sous la zone de texte dynamiquement. La figure 4 montre un exemple d’utilisation du contrôle AutoCompleteExtender pour afficher les ID de client pour une application prise en charge. Comme l’utilisateur tape des caractères différents dans la zone de texte, les différents éléments seront affichera en dessous en fonction de leur entrée. Les utilisateurs peuvent ensuite sélectionner l’id de client souhaité.

À l’aide de la AutoCompleteExtender au sein d’une page ASP.NET AJAX requiert que l’assembly AjaxControlToolkit.dll ajouté au dossier d’emplacement du site Web. Une fois que l’assembly de boîte à outils a été ajouté, que vous souhaitez référencer dans le fichier web.config afin que les contrôles qu’il contient sont disponibles pour toutes les pages dans une application. Cela est possible en ajoutant la balise suivante au sein de web.config &lt;contrôles&gt; balise :

[!code-xml[Main](understanding-asp-net-ajax-web-services/samples/sample24.xml)]

Dans le cas où vous devez uniquement utiliser le contrôle dans une page spécifique, vous pouvez le référencer en ajoutant la directive de référence vers le haut d’une page comme indiqué ci-après au lieu de la mise à jour de web.config :

[!code-aspx[Main](understanding-asp-net-ajax-web-services/samples/sample25.aspx)]


[![Utilisation du contrôle AutoCompleteExtender.](understanding-asp-net-ajax-web-services/_static/image11.png)](understanding-asp-net-ajax-web-services/_static/image10.png)

**Figure 4**: utilisation du contrôle AutoCompleteExtender.  ([Cliquez pour afficher l’image en taille réelle](understanding-asp-net-ajax-web-services/_static/image12.png))


Une fois que le site Web a été configuré pour utiliser ASP.NET AJAX Toolkit, un contrôle AutoCompleteExtender peut être ajouté dans la page autant que vous ajouteriez un contrôle de serveur ASP.NET normal. Liste 19 montre un exemple d’utilisation du contrôle pour appeler un Service Web.

**Liste 19. Utilisation du contrôle ASP.NET AJAX Toolkit AutoCompleteExtender.**

[!code-aspx[Main](understanding-asp-net-ajax-web-services/samples/sample26.aspx)]

Le AutoCompleteExtender a plusieurs propriétés différentes, notamment les propriétés d’ID et runat standards trouvées sur les contrôles serveur. Outre ces options, il vous permet de définir le nombre de caractères de données sont recherchés dans un type d’utilisateur final avant que le Service Web. La propriété MinimumPrefixLength indiquée dans la liste de 19, le service d’être appelée chaque fois qu’un caractère est tapé dans la zone de texte. Vous souhaiterez veillez à définir cette valeur dans la mesure où chaque fois que l’utilisateur tape un caractère, le Service Web sera appelée pour rechercher des valeurs qui correspondent aux caractères dans la zone de texte. Le Service Web à appeler, ainsi que la méthode Web cible est définie à l’aide des propriétés ServicePath et ServiceMethod respectivement. Enfin, la propriété TargetControlID identifie quelle zone de texte pour raccorder l’AutoCompleteExtender du contrôle.

Le Service Web appelé doit avoir l’attribut ScriptService appliquée comme indiqué précédemment, et la méthode Web cible doit accepter deux paramètres nommés prefixText et nombre. Le paramètre prefixText représente les caractères tapés par l’utilisateur final et le paramètre du nombre représente le nombre d’éléments pour retourner (la valeur par défaut est 10). Liste de 20 montre un exemple de la méthode Web GetCustomerIDs appelée par le contrôle AutoCompleteExtender indiqué plus haut dans la liste de 19. La méthode Web appelle une méthode de couche métier qu’à son tour appelle une couche données méthode qui gère le filtrage des données et de retourner les résultats de correspondance. Le code pour la méthode de la couche données est indiqué dans la liste des 21.

**Liste de 20. Le filtrage des données envoyées à partir du contrôle AutoCompleteExtender.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample27.cs)]

**Liste 21. Filtrage des résultats en fonction de l’entrée d’utilisateur de la fin.**

[!code-csharp[Main](understanding-asp-net-ajax-web-services/samples/sample28.cs)]

## <a name="conclusion"></a>Conclusion

ASP.NET AJAX fournit un excellent support pour appeler des Services Web sans écrire beaucoup de code JavaScript personnalisé pour gérer les messages de demande et de réponse. Dans cet article, vous avez vu comment AJAX-activer les Services Web pour les activer pour traiter les messages JSON et comment définir les proxies JavaScript à l’aide du contrôle ScriptManager. Vous avez également vu comment JavaScript proxys peuvent servir à appeler des Services Web, gérer des types simples et complexes et traiter les échecs. Enfin, vous avez vu comment les méthodes de page peuvent être utilisées pour simplifier le processus de création et d’effectuer des appels de Service Web et comment le contrôle AutoCompleteExtender peut fournir une aide pour les utilisateurs finaux en tapant. Même si UpdatePanel disponible dans ASP.NET AJAX sera certainement le contrôle de choix pour de nombreux programmeurs AJAX en raison de sa simplicité, le fait de savoir comment appeler des Services Web via les proxies JavaScript peut être utile dans de nombreuses applications.

## <a name="bio"></a>BIO

Dan Wahlin (Microsoft Most Valuable Professional pour ASP.NET et les Services Web XML) est développement du formateur et architecture consultant .NET à la formation de l’Interface ([http://www.interfacett.com](http://www.interfacett.com)). Dan a créé le code XML pour le site Web des développeurs ASP.NET ([www.XMLforASP.NET](http://www.XMLforASP.NET)), se trouve sur le Bureau de l’INETA et participe à plusieurs conférences. Dan coauteur ADN Windows Professionnel (Wrox), ASP.NET : conseils, didacticiels et Code (SAM), ASP.NET 1.1 Insider Solutions, ASP.NET 2.0 AJAX Professionnel (Wrox), ASP.NET 2.0 MVP Hacks et XML créé pour les développeurs ASP.NET (SAM). Lorsqu’il n’écrit pas de code, des articles ou des livres, Dan bénéficie de l’écriture et l’enregistrement de musique et lecture de golf et basket avec sa femme et les enfants.

Scott caté travaille avec les technologies Microsoft Web depuis 1997 et est le directeur de myKB.com ([www.myKB.com](http://www.myKB.com)) où il est spécialisé dans l’écriture d’ASP.NET en fonction des applications axées sur les solutions logicielles de la Base de connaissances. Scott peut être contacté par courrier électronique en [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) ou son blog à [ScottCate.com](http://ScottCate.com)

>[!div class="step-by-step"]
[Précédent](understanding-asp-net-ajax-localization.md)
[Suivant](understanding-asp-net-ajax-debugging-capabilities.md)
