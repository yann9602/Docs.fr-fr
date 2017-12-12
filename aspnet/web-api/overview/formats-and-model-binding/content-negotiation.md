---
uid: web-api/overview/formats-and-model-binding/content-negotiation
title: "Dans l’API Web ASP.NET la négociation de contenu | Documents Microsoft"
author: MikeWasson
description: "Décrit comment ASP.NET Web API implémente la négociation de contenu HTTP."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/20/2012
ms.topic: article
ms.assetid: 0dd51b30-bf5a-419f-a1b7-2817ccca3c7d
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/formats-and-model-binding/content-negotiation
msc.type: authoredcontent
ms.openlocfilehash: ca373af6754e82889dc100b63f73b76aaa4e4f27
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="content-negotiation-in-aspnet-web-api"></a>Négociation de contenu dans l’API Web ASP.NET
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cet article décrit la façon dont ASP.NET Web API implémente la négociation de contenu.

La spécification HTTP (RFC 2616) définit la négociation de contenu en tant que « le processus de sélection de la représentation sous forme de meilleures de réponse donnée quand il existe plusieurs représentations ». Le principal mécanisme de négociation de contenu HTTP sont ces en-têtes de demande :

- **Accepter :** les types de médias sont admis pour la réponse, telles que « application/json », « application/xml », ou un type de média personnalisé tel que &quot;application/vnd.example+xml&quot;
- **Accept-Charset :** les jeux de caractères sont acceptables, telles que UTF-8 ou ISO 8859-1.
- **Encodage :** les encodages de contenu sont acceptables, tel que gzip.
- **Accept-Language :** la langue naturel, tels que « en-us ».

Le serveur peut également consulter les autres parties de la requête HTTP. Par exemple, si la demande contient un en-tête X-Requested-With, indiquant une requête AJAX, le serveur peuvent être par défaut au format JSON s’il n’existe aucun en-tête Accept.

Dans cet article, nous allons étudier comment API Web utilise les en-têtes Accept et Accept-Charset. (À ce stade, il n’est pas prise en charge intégrée pour Accept-Encoding ou Accept-Language.)

## <a name="serialization"></a>Sérialisation

Si un contrôleur d’API Web retourne une ressource en tant que type CLR, le pipeline sérialise la valeur de retour et les écrit dans le corps de réponse HTTP.

Par exemple, considérez l’action du contrôleur suivant :

[!code-csharp[Main](content-negotiation/samples/sample1.cs)]

Un client peut envoyer cette demande HTTP :

[!code-console[Main](content-negotiation/samples/sample2.cmd)]

En réponse, le serveur peut envoyer :

[!code-console[Main](content-negotiation/samples/sample3.cmd)]

Dans cet exemple, le client a demandé JSON, Javascript ou « tout » (\*/\*). Le serveur répond avec une représentation JSON de la `Product` objet. Notez que l’en-tête Content-Type dans la réponse est défini sur &quot;application/json&quot;.

Un contrôleur peut également retourner un **HttpResponseMessage** objet. Pour spécifier un objet CLR pour le corps de réponse, appelez le **CreateResponse** méthode d’extension :

[!code-csharp[Main](content-negotiation/samples/sample4.cs)]

Cette option vous donne davantage de contrôle sur les détails de la réponse. Vous pouvez définir le code d’état, ajouter des en-têtes HTTP et ainsi de suite.

L’objet qui sérialise la ressource est appelé un *formateur de média*. Formateurs de médias dérivent la **MediaTypeFormatter** classe. API Web fournit les formateurs de média pour XML et JSON, et vous pouvez créer des formateurs personnalisés pour prendre en charge d’autres types de médias. Pour plus d’informations sur l’écriture d’un formateur personnalisé, consultez [support formateurs](media-formatters.md).

## <a name="how-content-negotiation-works"></a>Contenu de la négociation fonctionne

Tout d’abord, le pipeline Obtient le **IContentNegotiator** service à partir de la **HttpConfiguration** objet. Il obtient également la liste de formateurs de support à partir de la **HttpConfiguration.Formatters** collection.

Ensuite, le pipeline appelle **IContentNegotiatior.Negotiate**, en passant dans :

- Le type d’objet à sérialiser
- La collection de formateurs de médias
- La requête HTTP

Le **Negotiate** méthode retourne deux informations :

- Le formateur à utiliser
- Le type de média pour la réponse

Si aucun formateur n’est trouvée, le **Negotiate** méthode retourne **null**et le client reçoit HTTP 406 (non Acceptable).

Le code suivant montre comment un contrôleur peut appeler directement la négociation de contenu :

[!code-csharp[Main](content-negotiation/samples/sample5.cs)]

Ce code est équivalent à quoi le pipeline effectue automatiquement.

## <a name="default-content-negotiator"></a>Négociateur de contenu par défaut

Le **DefaultContentNegotiator** classe fournit l’implémentation par défaut de **IContentNegotiator**. Elle utilise plusieurs critères pour sélectionner un module de formatage.

Tout d’abord, le formateur doit être capable de sérialiser le type. Cela est vérifié en appelant **MediaTypeFormatter.CanWriteType**.

Ensuite, le négociateur de contenu examine chaque formateur et évalue la manière dont il correspond à la requête HTTP. Pour évaluer la correspondance, le négociateur de contenu examine deux choses sur le module de formatage :

- Le **SupportedMediaTypes** collection qui contient une liste de types de médias pris en charge. Négociateur de contenu essaie de correspondre à cette liste par rapport à l’en-tête Accept de la demande. Notez que l’en-tête Accept peut inclure des plages. Par exemple, « text/plain » est une correspondance pour le texte /\* ou \* / \*.
- Le **MediaTypeMappings** collection qui contient une liste de **MediaTypeMapping** objets. Le **MediaTypeMapping** classe fournit un moyen générique pour faire correspondre des requêtes HTTP avec les types de médias. Par exemple, il peut mapper un en-tête HTTP personnalisé à un type de média spécifique.

S’il existe plusieurs correspond à, la correspondance avec le service wins de facteur de qualité la plus élevée. Exemple :

[!code-console[Main](content-negotiation/samples/sample6.cmd)]

Dans cet exemple, application/json est un facteur implicite de qualité de la version 1.0, afin qu’il est préférable à application/xml.

Si aucune correspondance n’est trouvée, le négociateur de contenu essaie de faire correspondre sur le type de média du corps de la demande, le cas échéant. Par exemple, si la requête contient des données JSON, négociateur de contenu recherche un formateur JSON.

S’il n’y a toujours aucune correspondance, le négociateur de contenu choisit simplement le premier formateur pouvant sérialiser le type.

## <a name="selecting-a-character-encoding"></a>Sélection d’un encodage de caractères

Après avoir sélectionné un formateur, négociateur de contenu choisit le meilleur codage de caractères en examinant le **SupportedEncodings** propriété sur le module de formatage et correspondant à l’en-tête Accept-Charset dans la demande (le cas échéant).
