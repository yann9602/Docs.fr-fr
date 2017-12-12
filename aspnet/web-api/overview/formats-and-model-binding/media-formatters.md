---
uid: web-api/overview/formats-and-model-binding/media-formatters
title: "Formateurs de médias dans ASP.NET Web API 2 | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/20/2014
ms.topic: article
ms.assetid: 4c56f64a-086a-44ce-99c2-4c69604cd7fd
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/formats-and-model-binding/media-formatters
msc.type: authoredcontent
ms.openlocfilehash: 7d85b995cd577d0ff90fe96bce508c7fbdc6ebbb
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="media-formatters-in-aspnet-web-api-2"></a>Formateurs de médias dans ASP.NET Web API 2
====================
par [Mike Wasson](https://github.com/MikeWasson)

Ce didacticiel montre comment prennent en charge que les formats supplémentaires dans l’API Web ASP.NET.

## <a name="internet-media-types"></a>Types de média Internet

Un type de média, également appelé un type MIME, identifie le format d’un élément de données. HTTP, les types de médias décrivent le format du corps du message. Un type de média se compose de deux chaînes, un type et un sous-type. Exemple :

- texte/html
- image/png
- application/json.

Lorsqu’un message HTTP contienne un corps d’entité, l’en-tête Content-Type spécifie le format du corps du message. Cela indique le récepteur comment analyser le contenu du corps du message.

Par exemple, si une réponse HTTP contient une image PNG, la réponse peut avoir les en-têtes suivants.

[!code-console[Main](media-formatters/samples/sample1.cmd)]

Lorsque le client envoie un message de demande, il peut inclure un en-tête Accept. L’en-tête Accept indique que le serveur les media type (s) le client souhaite à partir du serveur. Exemple :

[!code-console[Main](media-formatters/samples/sample2.cmd)]

Cet en-tête indique au serveur que le client veut HTML, XHTML ou XML.

Le type de média détermine la manière dont les API Web sérialise et désérialise le corps du message HTTP. API Web est prise en charge intégrée pour XML, JSON, BSON et les données de formulaire-urlencoded, et peut prendre en charge les types de médias supplémentaires en écrivant un *formateur de média*.

Pour créer un formateur de médias, dérivez de l’une de ces classes :

- [MediaTypeFormatter](https://msdn.microsoft.com/en-us/library/system.net.http.formatting.mediatypeformatter.aspx). Cette classe utilise la lecture asynchrone et les méthodes d’écriture.
- [BufferedMediaTypeFormatter](https://msdn.microsoft.com/en-us/library/system.net.http.formatting.bufferedmediatypeformatter.aspx). Cette classe dérive **MediaTypeFormatter** , mais utilise des méthodes maintient en lecture/écriture.

Dérivation de **BufferedMediaTypeFormatter** est plus simple, car il n’existe aucun code asynchrone, mais il signifie également que le thread appelant peut bloquer pendant les e/s.

## <a name="example-creating-a-csv-media-formatter"></a>Exemple : Création d’un module de formatage du support CSV

L’exemple suivant montre un formateur de type de média qui peut sérialiser un objet de produit dans un format de valeurs séparées par des virgules (CSV). Cet exemple utilise le type de produit défini dans le didacticiel [création d’une API Web qui prend en charge les opérations CRUD](../older-versions/creating-a-web-api-that-supports-crud-operations.md). Voici la définition de l’objet de produit :

[!code-csharp[Main](media-formatters/samples/sample3.cs)]

Pour implémenter un module de formatage du volume partagé de cluster, définissez une classe qui dérive de **BufferedMediaTypeFormater**:

[!code-csharp[Main](media-formatters/samples/sample4.cs)]

Dans le constructeur, ajoutez les types de médias qui prend en charge par le formateur. Dans cet exemple, le formateur prend en charge un seul type de support, &quot;texte/csv&quot;:

[!code-csharp[Main](media-formatters/samples/sample5.cs)]

Remplacer la **CanWriteType** méthode pour indiquer leurs types le module de formatage peut sérialiser :

[!code-csharp[Main](media-formatters/samples/sample6.cs)]

Dans cet exemple, le module de formatage peut sérialiser unique `Product` objets ainsi que des collections de `Product` objets.

De même, remplacez le **CanReadType** méthode pour indiquer leurs types le formateur peut désérialiser. Dans cet exemple, le formateur ne prend pas en charge la désérialisation, la méthode retourne simplement **false**.

[!code-csharp[Main](media-formatters/samples/sample7.cs)]

Enfin, vous pouvez substituer le **WriteToStream** (méthode). Cette méthode sérialise un type en l’écrivant dans un flux de données. Si votre formateur prend en charge la désérialisation, vous devez également substituer la **ReadFromStream** (méthode).

[!code-csharp[Main](media-formatters/samples/sample8.cs)]

## <a name="adding-a-media-formatter-to-the-web-api-pipeline"></a>Ajout d’un module de formatage du support pour le Pipeline de l’API Web

Pour ajouter un support de type module de formatage par le pipeline d’API Web, utilisez le **formateurs** propriété sur le **HttpConfiguration** objet.

[!code-csharp[Main](media-formatters/samples/sample9.cs)]

## <a name="character-encodings"></a>Encodages de caractères

Si vous le souhaitez, un formateur de média peut prendre en charge plusieurs codages de caractères, telles que UTF-8 ou ISO 8859-1.

Dans le constructeur, ajoutez un ou plusieurs [System.Text.Encoding](https://msdn.microsoft.com/en-us/library/system.text.encoding.aspx) types à la **SupportedEncodings** collection. Placez le premier de codage par défaut.

[!code-csharp[Main](media-formatters/samples/sample10.cs?highlight=6-7)]

Dans le **WriteToStream** et **ReadFromStream** appeler des méthodes, [MediaTypeFormatter.SelectCharacterEncoding](https://msdn.microsoft.com/en-us/library/hh969054.aspx) pour sélectionner l’encodage de caractères par défaut. Cette méthode met en correspondance les en-têtes de demande par rapport à la liste de codages pris en charge. Utilisez retourné **codage** lorsque vous lire ou écrire dans le flux :

[!code-csharp[Main](media-formatters/samples/sample11.cs?highlight=3,5)]
