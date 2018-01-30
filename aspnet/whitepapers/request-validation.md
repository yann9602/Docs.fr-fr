---
uid: whitepapers/request-validation
title: "Validation - prévention des attaques de Script de requête | Documents Microsoft"
author: rick-anderson
description: "Ce document décrit la fonctionnalité de validation de demande d’ASP.NET où, par défaut, l’application ne peut pas le traitement non codé soumettre des contenus de HTML..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: fa429113-5f8f-4ef4-97c5-5c04900a19fa
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/request-validation
msc.type: content
ms.openlocfilehash: 0b24fe2193d2c7a858667505bad9ed0b1d70a328
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="request-validation---preventing-script-attacks"></a>Validation - prévention des attaques de Script de requête
====================
> Ce document décrit la fonctionnalité de validation de demande d’ASP.NET où, par défaut, l’application ne peut pas le traitement du contenu HTML non codé envoyé au serveur. Cette fonctionnalité de validation de demande peut être désactivée lors de l’application a été conçue pour traiter les données HTML en toute sécurité.
> 
> S’applique à ASP.NET 1.1 et ASP.NET 2.0.


Validation de la demande, une fonctionnalité d’ASP.NET depuis la version 1.1, permet d’éviter que le serveur accepte le contenu HTML de non encodée contenant. Cette fonctionnalité est conçue pour aider à empêcher des attaques d’injection de script : code de script client ou HTML permettre être envoyée involontairement à un serveur, stockée et ensuite présenté à d’autres utilisateurs. Nous vous recommandons fortement que vous validez les données d’entrée et encoder en HTML, le cas échéant.

Par exemple, vous créez une page Web qui demande l’adresse de messagerie d’un utilisateur, puis stocke cette adresse dans une base de données de messagerie. Si l’utilisateur entre &lt;SCRIPT&gt;alerte (« hello depuis un script »)&lt;/SCRIPT&gt; au lieu d’une adresse de messagerie valide, lorsque ces données sont présentées, ce script peut être exécuté si le contenu n’était pas codé correctement. La fonctionnalité de validation de demande d’ASP.NET permet d’éviter ce problème persiste.

## <a name="why-this-feature-is-useful"></a>Raison pour laquelle cette fonctionnalité est utile

De nombreux sites ne sont pas conscients qu’ils sont ouverts aux attaques par injection de script simple. Si l’objectif de ces attaques est de modifier le site en affichant le code HTML ou potentiellement exécuter un script client pour rediriger l’utilisateur vers le site d’un pirate informatique, les attaques par injection de script sont un problème pour les développeurs Web doivent rivaliser avec.

Les attaques par injection de script constituent une préoccupation de tous les développeurs web, si elles le sont à l’aide d’autres technologies de développement web, ASP ou ASP.NET.

La fonctionnalité de validation de requête ASP.NET empêche proactive ces attaques en n’autorisant ne pas un contenu HTML non codé être traités par le serveur, sauf si le développeur décide d’autoriser ce contenu.

## <a name="what-to-expect-error-page"></a>À quoi s’attendre : Page d’erreur

La capture d’écran ci-dessous montre un exemple de code ASP.NET :

![](request-validation/_static/image1.png)

Exécute les résultats de ce code dans une page simple qui vous permet d’entrer du texte dans la zone de texte, cliquez sur le bouton et affiche le texte dans le contrôle d’étiquette :

![](request-validation/_static/image2.png)

Toutefois, ont été JavaScript, telles que `<script>alert("hello!")</script>` soit entré et soumis vous obtenez une exception :

![](request-validation/_static/image3.png)

Le message d’erreur indique qu’un « potentiellement dangereux Request.Form valeur a été détectée » et fournit plus de détails dans la description d’exactement ce qui s’est produite et comment modifier le comportement. Exemple :

Validation de la demande a détecté une valeur d’entrée du client potentiellement dangereux, et le traitement de la demande a été abandonné. Cette valeur peut indiquer une tentative de compromettre la sécurité de votre application, tel qu’une attaque de script entre sites. Vous pouvez désactiver la validation de la demande en définissant `validateRequest=false` dans la directive de Page ou dans la section de configuration. Toutefois, il est recommandé que votre application vérifie explicitement toutes les entrées dans ce cas.

## <a name="disabling-request-validation-on-a-page"></a>La désactivation de la validation de la demande sur une page

Pour désactiver la validation de requête sur une page, vous devez définir le `validateRequest` attribut de la directive Page `false`:

[!code-aspx[Main](request-validation/samples/sample1.aspx)]

> [!CAUTION]
> Lors de la validation de la demande est désactivée, le contenu peut être envoyé vers une page ; Il est la responsabilité du développeur de page pour vous assurer que le contenu est encodée ou traitée correctement.

## <a name="disabling-request-validation-for-your-application"></a>La désactivation de la validation de la demande pour votre application

Pour désactiver la validation de la demande pour votre application, vous devez modifier ou créer un fichier Web.config pour votre application et définir le meilleur de la `<pages />` section `false`:

[!code-xml[Main](request-validation/samples/sample2.xml)]

Si vous souhaitez désactiver la validation de la demande pour toutes les applications sur votre serveur, vous pouvez effectuer cette modification dans votre fichier Machine.config.

> [!CAUTION]
> Lors de la validation de la demande est désactivée, le contenu peut être envoyé à votre application ; Il est la responsabilité du développeur de l’application pour vous assurer que le contenu est encodée ou traitée correctement.

Le code ci-dessous est modifié pour désactiver la validation de la demande :

![](request-validation/_static/image4.png)

Maintenant, si le code JavaScript suivant a été entré dans la zone de texte `<script>alert("hello!")</script>` le résultat serait :

![](request-validation/_static/image5.png)

Pour éviter cela, avec la validation de la demande mises hors tension, nous devons au format HTML encoder le contenu.

## <a name="how-to-html-encode-content"></a>Comment encoder du contenu au format HTML

Si vous avez désactivé la validation de la demande, il est conseillé de contenu encoder en HTML qui sera stocké à un usage ultérieur. L’encodage HTML remplace automatiquement les '&lt;'ou'&gt;' (ainsi que plusieurs autres symboles) avec leur HTML correspondant représentation codée. Par exemple, «&lt;« est remplacé par »&amp;lt ;' et '&gt;« est remplacé par »&amp;gt ;'. Les navigateurs utilisent ces codes spéciaux pour afficher la «&lt;'ou'&gt;' dans le navigateur.

Le contenu peut être facilement codée en HTML sur le serveur en utilisant le `Server.HtmlEncode(string)` API. Le contenu peut également être facilement décodée en HTML, autrement dit, restaurer HTML standard à l’aide du `Server.HtmlDecode(string)` (méthode).

![](request-validation/_static/image6.png)

Ce qui entraîne :

![](request-validation/_static/image7.png)
