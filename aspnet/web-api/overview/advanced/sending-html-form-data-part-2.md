---
uid: web-api/overview/advanced/sending-html-form-data-part-2
title: "Envoi de données de formulaire HTML dans l’API Web ASP.NET : MIME en plusieurs parties et le téléchargement de fichiers | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/21/2012
ms.topic: article
ms.assetid: a7f3c1b5-69d9-4261-b082-19ffafa5f16a
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/advanced/sending-html-form-data-part-2
msc.type: authoredcontent
ms.openlocfilehash: 3df59aab2a0c43f4a4f5c59530b0655f68d95cc7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="sending-html-form-data-in-aspnet-web-api-file-upload-and-multipart-mime"></a>Envoi de données de formulaire HTML dans l’API Web ASP.NET : MIME en plusieurs parties et le téléchargement de fichiers
====================
par [Mike Wasson](https://github.com/MikeWasson)

## <a name="part-2-file-upload-and-multipart-mime"></a>Partie 2 : Téléchargement de fichiers et MIME à parties multiples

Ce didacticiel montre comment télécharger des fichiers dans une API web. Elle décrit également comment traiter les données MIME en plusieurs parties.

> [!NOTE]
> [Télécharger le projet terminé](https://code.msdn.microsoft.com/ASPNET-Web-API-File-Upload-a8c0fb0d).


Voici un exemple d’un formulaire HTML pour le téléchargement d’un fichier :

[!code-html[Main](sending-html-form-data-part-2/samples/sample1.html)]

![](sending-html-form-data-part-2/_static/image1.png)

Ce formulaire contient un contrôle d’entrée de texte et un contrôle d’entrée de fichier. Lorsqu’un formulaire contient un contrôle d’entrée de fichier, le **enctype** attribut doit toujours être &quot;multipart/form-data&quot;, qui spécifie que le formulaire est envoyé en tant qu’un message MIME en plusieurs parties.

Le format d’un message MIME à parties multiples est plus facile à comprendre en examinant un exemple de demande :

[!code-console[Main](sending-html-form-data-part-2/samples/sample2.cmd)]

Ce message est divisé en deux *parties*, un pour chaque contrôle de formulaire. Limites de la partie sont indiquées par les lignes qui commencent par des tirets.

> [!NOTE]
> La limite de la partie inclut un composant aléatoire (&quot;41184676334&quot;) pour vous assurer que la chaîne de limite n’apparaît pas par inadvertance à l’intérieur d’une partie de message.


Chaque partie de message contient un ou plusieurs en-têtes, suivies du contenu de la partie.

- L’en-tête Content-Disposition inclut le nom du contrôle. Pour les fichiers, il contient également le nom de fichier.
- L’en-tête Content-Type décrit les données dans la partie. Si cet en-tête est omis, la valeur par défaut est text/plain.

Dans l’exemple précédent, l’utilisateur a téléchargé un fichier nommé GrandCanyon.jpg, avec le type de contenu image/jpeg ; et la valeur du texte d’entrée a été &quot;vacances d’été&quot;.

## <a name="file-upload"></a>Téléchargement du fichier

Maintenant examinons un contrôleur d’API Web qui lit des fichiers à partir d’un message MIME en plusieurs parties. Le contrôleur de lit les fichiers de façon asynchrone. API Web prend en charge les actions asynchrones à l’aide de la [modèle de programmation basé sur des tâches](https://msdn.microsoft.com/library/dd460693.aspx). Tout d’abord, voici le code si vous ciblez .NET Framework 4.5, qui prend en charge la **async** et **await** mots clés.

[!code-csharp[Main](sending-html-form-data-part-2/samples/sample3.cs)]

Notez que l’action du contrôleur ne prend pas de paramètres. C’est parce que nous traiter le corps de la demande à l’intérieur de l’action, sans appeler un formateur de type de média.

Le **IsMultipartContent** méthode vérifie si la demande contient un message MIME en plusieurs parties. Si ce n’est pas le cas, le contrôleur retourne le code d’état HTTP 415 (Type de média non pris en charge).

Le **MultipartFormDataStreamProvider** classe est un objet d’assistance qui alloue des flux de fichiers pour les fichiers téléchargés. Pour lire le message MIME à parties multiples, appelez le **ReadAsMultipartAsync** (méthode). Cette méthode extrait toutes les parties de message et les écrit dans les flux fournis par le **MultipartFormDataStreamProvider**.

Lorsque la méthode est terminée, vous pouvez obtenir des informations sur les fichiers à partir de la **FileData** propriété, qui est une collection de **MultipartFileData** objets.

- **MultipartFileData.FileName** est le nom de fichier local sur le serveur où le fichier a été enregistré.
- **MultipartFileData.Headers** contient l’en-tête de la partie (*pas* l’en-tête de demande). Vous pouvez l’utiliser pour accéder au contenu\_les en-têtes Content-Type et la destruction.

Comme son nom l’indique, **ReadAsMultipartAsync** est une méthode asynchrone. Pour effectuer le travail une fois la méthode terminée, utilisez un [tâche de continuation](https://msdn.microsoft.com/en-us/library/ee372288.aspx) (.NET 4.0) ou le **await** (mot clé) (.NET 4.5).

Voici la version de .NET Framework 4.0 du code précédent :

[!code-csharp[Main](sending-html-form-data-part-2/samples/sample4.cs)]

## <a name="reading-form-control-data"></a>Lire des données de contrôle de formulaire

Le formulaire HTML qu’illustré précédemment avait un contrôle d’entrée de texte.

[!code-html[Main](sending-html-form-data-part-2/samples/sample5.html)]

Vous pouvez obtenir la valeur du contrôle à partir de la **FormData** propriété de la **MultipartFormDataStreamProvider**.

[!code-csharp[Main](sending-html-form-data-part-2/samples/sample6.cs?highlight=15)]

**FormData** est un **NameValueCollection** qui contient les paires nom/valeur pour les contrôles du formulaire. La collection peut contenir des clés en double. Prenez en compte ce formulaire :

[!code-html[Main](sending-html-form-data-part-2/samples/sample7.html)]

![](sending-html-form-data-part-2/_static/image2.png)

Le corps de requête peut ressembler à ceci :

[!code-console[Main](sending-html-form-data-part-2/samples/sample8.cmd)]

Dans ce cas, le **FormData** collection contient les paires clé/valeur suivantes :

- voyage : aller-retour
- options : sans interruption
- options : dates
- siège : fenêtre
