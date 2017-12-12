---
uid: web-pages/overview/performance-and-traffic/14-analyzing-traffic
title: Le suivi des informations sur les visiteurs (Analytique) pour ASP.NET Web Pages (Razor) Site | Documents Microsoft
author: tfitzmac
description: Une fois que vous avez obtenu votre site Web va, vous souhaiterez analyser le trafic de votre site Web.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/17/2014
ms.topic: article
ms.assetid: 360bc6e1-84c5-4b8e-a84c-ea48ab807aa4
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/performance-and-traffic/14-analyzing-traffic
msc.type: authoredcontent
ms.openlocfilehash: 9a381ebaed30325fdfa5f0f558910d3002c61559
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="tracking-visitor-information-analytics-for-an-aspnet-web-pages-razor-site"></a>Informations de suivi visiteur (Analytique) pour un Site de Pages (Razor) Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cet article décrit comment utiliser un programme d’assistance pour ajouter analytique de site Web vers les pages dans un site Web ASP.NET Web Pages (Razor).
> 
> Ce que vous allez apprendre :
> 
> - Comment envoyer des informations sur le trafic de votre site Web à un fournisseur de l’analytique.
> 
> Il s’agit de programmation des fonctionnalités introduites dans l’article ASP.NET :
> 
> - Le `Analytics` helper.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 2
> - ASP.NET Web Helpers Library (package NuGet)


Analytique est un terme général qui désigne la technologie qui mesure le trafic sur votre site Web pour vous pouvez de comprendre comment les personnes utilisent le site. De nombreux services analytique sont disponibles, y compris les services à partir de Google, Yahoo, StatCounter et d’autres.

L’analytique de façon fonctionne est que vous vous inscrivez pour un compte avec le fournisseur analytique, dans lequel vous inscrivez le site que vous souhaitez effectuer le suivi. Le fournisseur envoie un extrait de code JavaScript qui inclut un ID ou le code de votre compte de suivi. Vous ajoutez l’extrait de code JavaScript pour les pages web sur le site que vous souhaitez suivre. (Vous ajoutez généralement l’extrait de code analytique à une page mise en page ou de pied de page ou un autre balisage HTML qui s’affiche sur chaque page de votre site.) Lorsque des utilisateurs demandent une page qui contient l’un de ces extraits de code JavaScript, l’extrait de code envoie des informations sur la page actuelle au fournisseur analytique, qui enregistre les détails des différents sur la page.

Lorsque vous souhaitez examiner vos statistiques de site, vous connecter au site Web du fournisseur de l’analytique. Vous pouvez ensuite afficher toutes sortes de rapports sur votre site, tels que :

- Le nombre de vues de page pour des pages individuelles. Vous pouvez en déduire (environ) combien de personnes visite du site et les pages de votre site sont les plus populaires.
- La durée pendant laquelle des personnes passent sur des pages spécifiques. Cela peut vous indiquer les choses comme si votre page d’accueil consiste à maintenir l’intérêt des utilisateurs.
- Les personnes de sites ont été avant que les visiteurs de votre site. Cela vous permet de comprendre si votre trafic provient de liens, à partir de la recherche et ainsi de suite.
- Lorsque les utilisateurs visitent votre site et la durée pendant laquelle elles restent.
- Quels sont les pays les visiteurs de votre proviennent.
- Les navigateurs et les systèmes d’exploitation utilisent les visiteurs.

    ![Ch14traffic-1](14-analyzing-traffic/_static/image1.jpg)

## <a name="using-a-helper-to-add-analytics-to-a-page"></a>À l’aide d’un programme d’assistance pour ajouter l’Analytique sur une Page

Les Pages Web ASP.NET inclut plusieurs programmes d’assistance d’analytique (`Analytics.GetGoogleHtml`, `Analytics.GetYahooHtml`, et `Analytics.GetStatCounterHtml`) qui la rendent facile à gérer les extraits de code JavaScript utilisés pour l’analytique. Au lieu d’essayer de comprendre comment et où placer le code JavaScript, il vous suffit consiste à ajouter l’application d’assistance à une page. Les seules informations que vous devez fournir sont votre nom de compte, un ID ou un code de suivi. (Pour StatCounter, vous pouvez fournir des valeurs supplémentaires quelques.)

Dans cette procédure, vous allez créer une page de disposition qui utilise le `GetGoogleHtml` helper. Si vous avez déjà un compte avec l’un des autres fournisseurs analytique, vous pouvez utiliser ce compte à la place et ajuster légèrement en fonction des besoins.

> [!NOTE]
> Lorsque vous créez un compte analytique, vous inscrire l’URL du site que vous souhaitez suivre. Si vous testez tous les éléments sur votre ordinateur local, vous ne le suivi du trafic réel (est le seul trafic vous), donc vous ne pourrez enregistrement et affichage des statistiques de site. Mais cette procédure montre comment vous ajoutez une application d’assistance de l’analytique sur une page. Lorsque vous publiez votre site, le site actif envoie des informations à votre fournisseur d’analytique.


1. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas déjà ajouté.
2. Créer un compte avec Google Analytique et enregistrez le nom de compte.
3. Créer une page de disposition nommée *Analytics.cshtml* et ajoutez le balisage suivant :

    [!code-cshtml[Main](14-analyzing-traffic/samples/sample1.cshtml)]

    > [!NOTE]
    > Vous devez placer l’appel à la `Analytics` helper dans le corps de votre page web (avant les `</body>` balise). Sinon, le navigateur n’exécute le script.

    Si vous utilisez un fournisseur analytique différents, utilisez une des applications auxiliaires :

    - (Yahoo)`@Analytics.GetYahooHtml("myaccount")`
    - (StatCounter)`@Analytics.GetStatCounterHtml("project", "security")`
4. Remplacez `myaccount` avec le nom du compte, ID ou du code de suivi que vous avez créé à l’étape 1.
5. Exécutez la page dans le navigateur. (Assurez-vous que la page est sélectionnée dans le **fichiers** espace de travail avant de l’exécuter.)
6. Dans le navigateur, affichez la source de la page. Vous serez en mesure de voir le code de rendu analytique :

    [!code-html[Main](14-analyzing-traffic/samples/sample2.html)]
7. Connectez-vous au site Google Analytique et examiner les statistiques de votre site. Si vous utilisez la page sur un site en direct, vous voyez une entrée qui enregistre la visite à votre page.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

- [Site de Google Analytique](https://www.google.com/analytics/)
- [Yahoo! Site Web d’Analytique](http://help.yahoo.com/l/us/yahoo/ywa/)
- [StatCounter site](http://statcounter.com/)
