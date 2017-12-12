---
uid: web-pages/overview/ui-layouts-and-themes/installing-helpers
title: "L’installation d’un programme d’assistance dans une application Web Pages (Razor) Site | Documents Microsoft"
author: tfitzmac
description: "Cet article décrit comment installer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor). Un programme d’assistance est un composant réutilisable qui inclut le code et le balisage pour par..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/18/2014
ms.topic: article
ms.assetid: 5e968ead-906a-45ea-ac2a-c70e57e1a9b1
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/installing-helpers
msc.type: authoredcontent
ms.openlocfilehash: 842c5a56d14314217c1e6ad6d48ded28d3cc5b4e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="installing-a-helper-in-an-aspnet-web-pages-razor-site"></a>L’installation d’un programme d’assistance dans un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment installer un programme d’assistance dans un site Web ASP.NET Web Pages (Razor). A *assistance* est un composant réutilisable qui inclut le code et le balisage pour effectuer une tâche qui peut être fastidieux ou complexe.
> 
> Ce que vous allez apprendre :
> 
> - Comment installer un programme d’assistance d’un site Web créé à l’aide de WebMatrix 3.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - WebMatrix 3


## <a name="overview-of-helpers"></a>Vue d’ensemble des applications d’assistance

Certaines tâches que les utilisateurs souhaitent souvent sur les pages web nécessitent une grande quantité de code ou nécessitent des connaissances supplémentaires. Exemples incluent l’affichage d’un graphique pour les données ; placer un bouton « Suivre » de Twitter sur une page ; envoi de courrier électronique à partir de votre site Web ; rognage ou redimensionner des images ; à l’aide de PayPal pour votre site. Pour faciliter la faire de ces types d’éléments différents, les Pages Web ASP.NET vous permet d’utiliser *programmes d’assistance*. Il s’agit de composants que vous installez pour un site et qui permettent de vous effectuent les tâches classiques à l’aide de seulement une ou deux lignes de code Razor.

Les Pages Web ASP.NET a plusieurs programmes d’assistance intégrées. Toutefois, les nombreuses applications auxiliaires sont disponibles dans des packages (compléments) qui sont fournies à l’aide du Gestionnaire de package NuGet. NuGet vous permet de sélectionner un package à installer et puis elle s’occupe de tous les détails de l’installation.

## <a name="installing-a-helper-in-webmatrix-3"></a>L’installation d’un programme d’assistance dans WebMatrix 3

1. Dans WebMatrix, 3, cliquez sur le **NuGet** bouton.

    ![Boîte de dialogue de la galerie NuGet dans WebMatrix](installing-helpers/_static/image1.png)
2. Cela lance le Gestionnaire de package NuGet et affiche les packages disponibles. Dans la zone de recherche, entrez un mot clé pour l’application d’assistance que vous souhaitez installer.

    ![Boîte de dialogue de la galerie NuGet dans WebMatrix](installing-helpers/_static/image2.png)
- Sélectionnez le package, puis cliquez **installer**. Cliquez sur **Oui** lorsque vous êtes invité à installer le package et d’indiquer que vous acceptez les termes du contrat.

    S’il s’agit de la première fois que vous avez installé une application d’assistance, NuGet crée des dossiers dans votre site Web pour le code de l’application d’assistance.
- Pour désinstaller un programme d’assistance, cliquez sur le **galerie** , sur le **installé** onglet et sélectionnez le package que vous souhaitez désinstaller.

## <a name="installing-the-twitter-helper"></a>L’installation de l’application auxiliaire Twitter

La dernière version de l’API Twitter n’est pas compatible avec l’application d’assistance Twitter qu'installer via NuGet. Au lieu de cela, consultez le [application auxiliaire Twitter avec WebMatrix](twitter-helper.md) rubrique pour plus d’informations sur la façon de configurer l’application d’assistance de Twitter dans votre projet.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[Présentation ASP.NET Web Pages 2 - notions de base de programmation](../getting-started/introducing-razor-syntax-c.md)

[Application auxiliaire WebMatrix Twitter](twitter-helper.md)
