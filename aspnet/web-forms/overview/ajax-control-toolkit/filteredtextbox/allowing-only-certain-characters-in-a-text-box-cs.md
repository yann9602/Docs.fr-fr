---
uid: web-forms/overview/ajax-control-toolkit/filteredtextbox/allowing-only-certain-characters-in-a-text-box-cs
title: "Autoriser uniquement certains caractères dans une zone de texte (c#) | Documents Microsoft"
author: wenz
description: "Contrôles de validation ASP.NET peuvent garantir que seuls certains caractères sont autorisés dans l’entrée d’utilisateur. Toutefois cela toujours n’empêche pas les utilisateurs de la saisie non valides..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: fd2a1c52-d717-44af-8a61-67c8279bb26e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/filteredtextbox/allowing-only-certain-characters-in-a-text-box-cs
msc.type: authoredcontent
ms.openlocfilehash: 246c3b5dd55ceb0f47ad1f4982ae5b3bf855e747
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="allowing-only-certain-characters-in-a-text-box-c"></a>Autoriser uniquement certains caractères dans une zone de texte (c#)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/4/c/2/4c2def7a-0d23-4055-91f9-1f18504167d7/FilteredTextBox0.cs.zip) ou [télécharger le PDF](http://download.microsoft.com/download/b/6/a/b6ae89ee-df69-4c87-9bfb-ad1eb2b23373/filteredtextbox0CS.pdf)

> Contrôles de validation ASP.NET peuvent garantir que seuls certains caractères sont autorisés dans l’entrée d’utilisateur. Toutefois cela toujours n’empêche pas les utilisateurs de taper des caractères non valides et essayez d’envoyer le formulaire.


## <a name="overview"></a>Vue d'ensemble

Contrôles de validation ASP.NET peuvent garantir que seuls certains caractères sont autorisés dans l’entrée d’utilisateur. Toutefois cela toujours n’empêche pas les utilisateurs de taper des caractères non valides et essayez d’envoyer le formulaire.

## <a name="steps"></a>Étapes

Les outils de contrôle ASP.NET AJAX contient le `FilteredTextBox` contrôle qui étend une zone de texte. Une fois activé, uniquement un certain jeu de caractères peut être entré dans le champ.

Pour ce faire, nous devons comme d’habitude ASP.NET AJAX `ScriptManager` qui charge les bibliothèques JavaScript qui sont également utilisés par les outils de contrôle ASP.NET AJAX :

[!code-aspx[Main](allowing-only-certain-characters-in-a-text-box-cs/samples/sample1.aspx)]

Ensuite, nous avons besoin d’une zone de texte :

[!code-aspx[Main](allowing-only-certain-characters-in-a-text-box-cs/samples/sample2.aspx)]

Enfin, le `FilteredTextBoxExtender` contrôle prend en charge de limiter les caractères que l’utilisateur est autorisé à taper. Tout d’abord, définissez le `TargetControlID` d’attribut pour le `ID` de la `TextBox` contrôle. Ensuite, choisissez une des `FilterType` valeurs :

- `Custom`valeur par défaut ; Vous devez fournir une liste de caractères valides
- `LowercaseLetters`lettres minuscules uniquement
- `Numbers`uniquement des chiffres
- `UppercaseLetters`uniquement des lettres majuscules

Si le `Custom FilterType` est utilisé, le `ValidChars` propriété doit être défini et fournir une liste de caractères qui peuvent être tapés. La façon dont : Si vous essayez de coller du texte dans la zone de texte, tous les caractères non valides sont supprimés.

Voici le balisage de la `FilteredTextBoxExtender` contrôle qui permet uniquement de chiffres (ce qui aurait également été possible avec `FilterType="Numbers"`) :

[!code-aspx[Main](allowing-only-certain-characters-in-a-text-box-cs/samples/sample3.aspx)]

Exécutez la page, puis réessayez d’entrer une lettre si JavaScript est activé, il ne fonctionnera pas. Toutefois, les chiffres s’affichent dans la page. Notez, cependant que la protection `FilteredTextBox` fournit n’est pas à toute épreuve : si JavaScript est activé, toutes les données peuvent être entrées dans la zone de texte, donc vous devez utiliser une validation supplémentaire signifie, par exemple, ASP. Contrôles de validation du réseau.


[![Seuls les chiffres peuvent être entrés.](allowing-only-certain-characters-in-a-text-box-cs/_static/image2.png)](allowing-only-certain-characters-in-a-text-box-cs/_static/image1.png)

Seuls les chiffres peuvent être entrés ([cliquez pour afficher l’image en taille réelle](allowing-only-certain-characters-in-a-text-box-cs/_static/image3.png))

>[!div class="step-by-step"]
[Next](allowing-only-certain-characters-in-a-text-box-vb.md)
