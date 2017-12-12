---
uid: web-api/overview/advanced/sending-html-form-data-part-1
title: "Envoi de données de formulaire HTML dans l’API Web ASP.NET : les données de formulaire-urlencoded | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/15/2012
ms.topic: article
ms.assetid: 585351c4-809a-4bf5-bcbe-35d624f565fe
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/advanced/sending-html-form-data-part-1
msc.type: authoredcontent
ms.openlocfilehash: 0ed339c4f9d5854ab5a21cdd077a4d494987101f
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="sending-html-form-data-in-aspnet-web-api-form-urlencoded-data"></a>Envoi de données de formulaire HTML dans l’API Web ASP.NET : les données de formulaire-urlencoded
====================
par [Mike Wasson](https://github.com/MikeWasson)

## <a name="part-1-form-urlencoded-data"></a>Partie 1 : Données de formulaire-urlencoded

Cet article explique comment publier des données de formulaire-urlencoded sur un contrôleur d’API Web.

- [Vue d’ensemble de formulaires HTML](#overview_of_html_forms)
- [Envoi de Types complexes](#sending_complex_types)
- [Envoi de données de formulaire via AJAX](#sending_form_data_via_ajax)
- [Envoi de Types simples](#sending_simple_types)

> [!NOTE]
> [Télécharger le projet terminé](https://code.msdn.microsoft.com/ASPNET-Web-API-Sending-a6f9d007).


<a id="overview_of_html_forms"></a>
## <a name="overview-of-html-forms"></a>Vue d’ensemble de formulaires HTML

Utilisation de formulaires HTML à GET ou POST pour envoyer des données au serveur. Le **méthode** attribut de la **formulaire** élément donne la méthode HTTP :

[!code-html[Main](sending-html-form-data-part-1/samples/sample1.html)]

La méthode par défaut est GET. Si le formulaire utilise GET, le formulaire de données sont encodées dans l’URI en tant qu’une chaîne de requête. Si le formulaire utilise POST, les données du formulaire sont placées dans le corps de la demande. Pour les données de publication, le **enctype** attribut spécifie le format du corps de la demande :

| Enctype | Description |
| --- | --- |
| application/x-www-form-urlencoded | Les données de formulaire sont encodées sous forme de paires nom/valeur, similaires à une chaîne de requête URI. Il s’agit du format par défaut pour la publication. |
| multipart/form-data | Les données de formulaire sont encodées comme un message MIME en plusieurs parties. Utilisez ce format si vous téléchargez un fichier sur le serveur. |

Partie 1 de cet article se présente au format x--www-form-urlencoded. [Partie 2](sending-html-form-data-part-2.md) décrit MIME en plusieurs parties.

<a id="sending_complex_types"></a>
## <a name="sending-complex-types"></a>Envoi de Types complexes

En règle générale, vous allez envoyer un type complexe, composé de valeurs provenant de plusieurs contrôles de formulaire. Considérez le modèle ci-après qui représente une mise à jour d’état :

[!code-csharp[Main](sending-html-form-data-part-1/samples/sample2.cs)]

Voici un contrôleur d’API Web qui accepte une `Update` objet via POST.

[!code-csharp[Main](sending-html-form-data-part-1/samples/sample3.cs)]

> [!NOTE]
> Ce contrôleur utilise [le routage basé sur l’action](../web-api-routing-and-actions/routing-in-aspnet-web-api.md#routing_by_action_name), de sorte que le modèle d’itinéraire est &quot;api / {controller} / {action} / {id}&quot;. Le client valide les données à &quot;/api/updates/complex&quot;.


Maintenant nous allons écrire un formulaire HTML pour les utilisateurs à envoyer une mise à jour d’état.

[!code-html[Main](sending-html-form-data-part-1/samples/sample4.html)]

Notez que la **action** attribut sur le formulaire est l’URI d’action du contrôleur. Voici le formulaire avec des valeurs entrées dans :

![](sending-html-form-data-part-1/_static/image1.png)

Lorsque l’utilisateur clique sur Envoyer, le navigateur envoie une requête HTTP semblable au suivant :

[!code-console[Main](sending-html-form-data-part-1/samples/sample5.cmd)]

Notez que le corps de la demande contient les données du formulaire, la forme de paires nom/valeur. API Web convertit automatiquement les paires nom/valeur dans une instance de la `Update` classe.

<a id="sending_form_data_via_ajax"></a>
## <a name="sending-form-data-via-ajax"></a>Envoi de données de formulaire via AJAX

Lorsqu’un utilisateur soumet un formulaire, le navigateur quitte la page actuelle et restitue le corps du message de réponse. C’est OK lorsque la réponse est une page HTML. Avec une API web, toutefois, le corps de réponse est généralement soit vide ou contient des données structurées, telles que JSON. Dans ce cas, il vaut mieux pour envoyer la demandent de données de formulaire à l’aide d’un AJAX, afin que la page peut traiter la réponse.

Le code suivant montre comment valider des données de formulaire à l’aide de jQuery.

[!code-html[Main](sending-html-form-data-part-1/samples/sample6.html)]

JQuery **envoyer** fonction remplace l’action de formulaire par une nouvelle fonction. Cela remplace le comportement par défaut du bouton Envoyer. Le **sérialiser** fonction sérialise les données du formulaire en paires nom/valeur. Pour envoyer les données du formulaire sur le serveur, appelez `$.post()`.

Lorsque la demande est terminée, le `.success()` ou `.error()` gestionnaire affiche un message approprié pour l’utilisateur.

![](sending-html-form-data-part-1/_static/image2.png)

<a id="sending_simple_types"></a>
## <a name="sending-simple-types"></a>Envoi de Types simples

Dans les sections précédentes, nous vous avons envoyé un type complexe, API Web désérialisé en une instance d’une classe de modèle. Vous pouvez également envoyer des types simples, comme une chaîne.

> [!NOTE]
> Avant l’envoi d’un type simple, envisagez d’encapsuler la valeur dans un type complexe à la place. Cela offre les avantages de la validation du modèle sur le côté serveur et rend plus facile d’étendre votre modèle, si nécessaire.


Les étapes de base pour envoyer un type simple sont les mêmes, mais il existe deux différences subtiles. Tout d’abord, dans le contrôleur, vous devez décorer le nom du paramètre avec le **FromBody** attribut.

[!code-csharp[Main](sending-html-form-data-part-1/samples/sample7.cs?highlight=3)]

Par défaut, les API Web essaie d’obtenir des types simples à partir de l’URI de requête. Le **FromBody** attribut indique à l’API Web pour lire la valeur dans le corps de la demande.

> [!NOTE]
> API Web lit le corps de réponse au plus une fois qu’un seul paramètre d’une action peut provenir de corps de la demande. Si vous avez besoin obtenir plusieurs valeurs à partir du corps de la demande, définissez un type complexe.


En second lieu, le client doit envoyer la valeur au format suivant :

[!code-xml[Main](sending-html-form-data-part-1/samples/sample8.xml)]

Plus précisément, la partie du nom de la paire nom/valeur doit être vide pour un type simple. Pas de tous les navigateurs prennent en charge pour les formulaires HTML, mais vous créez ce format dans le script comme suit :

[!code-javascript[Main](sending-html-form-data-part-1/samples/sample9.js)]

Voici un exemple de formulaire :

[!code-html[Main](sending-html-form-data-part-1/samples/sample10.html)]

Et Voici le script pour envoyer la valeur de l’écran. La seule différence dans le script précédent est l’argument passé à la **valider** (fonction).

[!code-javascript[Main](sending-html-form-data-part-1/samples/sample11.js?highlight=2)]

Vous pouvez utiliser la même approche pour envoyer un tableau de types simples :

[!code-javascript[Main](sending-html-form-data-part-1/samples/sample12.js)]

## <a name="additional-resources"></a>Ressources supplémentaires

[Partie 2 : Téléchargement de fichiers et MIME à parties multiples](sending-html-form-data-part-2.md)
