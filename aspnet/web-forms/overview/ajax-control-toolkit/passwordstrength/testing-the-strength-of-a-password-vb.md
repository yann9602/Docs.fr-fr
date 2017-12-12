---
uid: web-forms/overview/ajax-control-toolkit/passwordstrength/testing-the-strength-of-a-password-vb
title: Test de la force de mot de passe (VB) | Documents Microsoft
author: wenz
description: "Presque n’importe où, les mots de passe sont requis afin que les utilisateurs différées ont tendance à choisir des mots de passe simples, faciles à arrêter. Le contrôle PasswordStrength dans ASP. N...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 9215a37f-3133-4887-8ed2-3689f3a53551
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/passwordstrength/testing-the-strength-of-a-password-vb
msc.type: authoredcontent
ms.openlocfilehash: 7f09a05fd4b5771b7ab532d40476fe45cbd3fe38
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="testing-the-strength-of-a-password-vb"></a>Test de la force de mot de passe (VB)
====================
par [Christian Wenz](https://github.com/wenz)

[Télécharger le Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/PasswordStrength0.vb.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/passwordstrength0VB.pdf)

> Presque n’importe où, les mots de passe sont requis afin que les utilisateurs différées ont tendance à choisir des mots de passe simples, faciles à arrêter. Le contrôle PasswordStrength dans les outils de contrôle ASP.NET AJAX peut vérifier la qualité est d’un mot de passe.


## <a name="overview"></a>Vue d'ensemble

Presque n’importe où, les mots de passe sont requis afin que les utilisateurs différées ont tendance à choisir des mots de passe simples, faciles à arrêter. Le `PasswordStrength` contrôle dans la boîte à outils de contrôle ASP.NET AJAX peut vérifier la qualité est d’un mot de passe.

## <a name="steps"></a>Étapes

Le `PasswordStrength` contrôle étend une zone de texte et vérifie si le mot de passe est suffisant. Il offre de nombreuses options via des attributs ; Voici quelques-unes d'entre elles :

- `MinimumNumericCharacters`nombre minimal de caractères numériques requis dans le mot de passe
- `MinimumSymbolCharacters`nombre minimal de caractères de symbole (pas des lettres et chiffres) requis dans le mot de passe
- `PreferredPasswordLength`longueur minimale du mot de passe
- `RequiresUpperAndLowerCaseCharacters`Indique si le mot de passe doit utiliser des caractères majuscules et minuscules

Le `StrengthIndicatorType` fournit les informations comment présenter la force du mot de passe, sous forme de texte (valeur `"Text"`) ou comme une sorte de barre de progression (valeur `"BarIndicator"`). Dans la `DisplayPosition` attribut, vous configurez dans lequel les informations s’affichent. Voici un exemple complet, y compris ASP.NET AJAX `ScriptManager` (contrôle), le `PasswordStrength` contrôle et, bien sûr, une zone de texte dans lequel l’utilisateur peut entrer un mot de passe. Pour des raisons de démonstration, le champ de formulaire de ce dernier est un champ de texte standard et pas un champ de mot de passe afin que vous puissiez voir pendant le développement ce que vous tapez.

[!code-aspx[Main](testing-the-strength-of-a-password-vb/samples/sample1.aspx)]

Exécuter la page et le type de suite : uniquement une fois que vous avez entré des lettres minuscules, lettres majuscules, chiffres et symboles, le mot de passe est considéré comme insécable.


[![À présent, le mot de passe () bien](testing-the-strength-of-a-password-vb/_static/image2.png)](testing-the-strength-of-a-password-vb/_static/image1.png)

À présent, le mot de passe () bien ([cliquez pour afficher l’image en taille réelle](testing-the-strength-of-a-password-vb/_static/image3.png))

>[!div class="step-by-step"]
[Précédent](testing-the-strength-of-a-password-cs.md)
