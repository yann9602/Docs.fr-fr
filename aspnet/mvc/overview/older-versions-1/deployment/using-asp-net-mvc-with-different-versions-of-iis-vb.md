---
uid: mvc/overview/older-versions-1/deployment/using-asp-net-mvc-with-different-versions-of-iis-vb
title: "À l’aide d’ASP.NET MVC avec différentes Versions d’IIS (VB) | Documents Microsoft"
author: microsoft
description: "Dans ce didacticiel, vous allez apprendre à utiliser ASP.NET MVC et le routage d’URL, avec différentes versions d’Internet Information Services. Obtenir des informations différentes stratégies."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/19/2008
ms.topic: article
ms.assetid: 1c1283b2-6956-4937-b568-d30de432ce23
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/deployment/using-asp-net-mvc-with-different-versions-of-iis-vb
msc.type: authoredcontent
ms.openlocfilehash: 6c9c3bf004b13677728c7c6bf2f5adf6a264dc49
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="using-aspnet-mvc-with-different-versions-of-iis-vb"></a>À l’aide d’ASP.NET MVC avec différentes Versions d’IIS (VB)
====================
par [Microsoft](https://github.com/microsoft)

> Dans ce didacticiel, vous allez apprendre à utiliser ASP.NET MVC et le routage d’URL, avec différentes versions d’Internet Information Services. Vous découvrez des stratégies différentes pour l’utilisation d’ASP.NET MVC avec des versions antérieures d’IIS, IIS 6.0 et IIS 7.0 (en mode classique).


L’infrastructure ASP.NET MVC dépend le routage ASP.NET pour acheminer les requêtes de navigateur pour les actions de contrôleur. Pour tirer parti du routage ASP.NET, vous devrez peut-être effectuer des étapes de configuration supplémentaires sur votre serveur web. Tout dépend de la version d’Internet Information Services (IIS) et le mode de votre application de traitement des demandes.

Voici un résumé des différentes versions d’IIS :

- IIS 7.0 (mode intégré) - aucune configuration spéciale nécessaire d’utiliser le routage ASP.NET.
- IIS 7.0 (mode classique) - vous devez effectuer une configuration spéciale pour utiliser le routage ASP.NET.
- IIS 6.0 ou ci-dessous - vous devez effectuer une configuration spéciale pour utiliser le routage ASP.NET.

La dernière version d’IIS est la version 7.5 (sur Win7). IIS 7 d’IIS est inclus avec Windows Server 2008 et VISTA SP1 et versions ultérieures. Vous pouvez également installer IIS 7.0 sur n’importe quelle version du système d’exploitation Vista sauf Édition Familiale Basique (consultez [https://technet.microsoft.com/library/cc731179%28WS.10%29.aspx](https://technet.microsoft.com/library/cc731179%28WS.10%29.aspx)).

IIS 7.0 prend en charge deux modes de traitement des demandes. Vous pouvez utiliser le mode intégré ou en mode classique. Vous n’avez pas besoin effectuer les étapes de configuration spéciales lors de l’utilisation d’IIS 7.0 en mode intégré. Toutefois, vous n’avez pas besoin effectuer une configuration supplémentaire lors de l’utilisation d’IIS 7.0 en mode classique.

Microsoft Windows Server 2003 inclut IIS 6.0. Vous ne peut pas mettre à niveau IIS 6.0 vers IIS 7.0 lors de l’utilisation du système d’exploitation Windows Server 2003. Vous devez effectuer les étapes de configuration supplémentaires lorsque vous utilisez IIS 6.0.

Microsoft Windows XP Professionnel inclut IIS 5.1. Vous devez effectuer les étapes de configuration supplémentaires lors de l’utilisation d’IIS 5.1.

Enfin, Microsoft Windows 2000 et Microsoft Windows 2000 Professionnel inclut IIS 5.0. Vous devez effectuer les étapes de configuration supplémentaires lorsque vous utilisez IIS 5.0.

## <a name="integrated-versus-classic-mode"></a>Integrated et en Mode classique

IIS 7.0 peut traiter les demandes à l’aide de deux modes de traitement de demande différentes : intégrés et classiques. En mode intégré fournit de meilleures performances et davantage de fonctionnalités. En mode classique est inclus pour assurer la compatibilité avec les versions antérieures d’IIS.

Le mode de traitement de demande est déterminé par le pool d’applications. Vous pouvez déterminer le mode de traitement est utilisé par une application web particulière en déterminant le pool d’applications associé à l’application. Procédez comme suit :

1. Lancez le Gestionnaire des Services Internet
2. Dans la fenêtre Connexions, sélectionnez une application
3. Dans la fenêtre Actions, cliquez sur le **les paramètres de base** lien pour ouvrir la boîte de dialogue Modifier l’Application boîte (voir Figure 1)
4. Prenez note du pool d’applications sélectionné.

Par défaut, IIS est configuré pour prendre en charge deux pools d’applications : **DefaultAppPool** et **Classic .NET AppPool**. Si DefaultAppPool est sélectionné, votre application s’exécute en mode de traitement de requêtes intégré. Si Classic .NET AppPool est sélectionné, votre application s’exécute en mode de traitement de la requête classique.


[![La boîte de dialogue Nouveau projet](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image1.jpg)](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image1.png)

**Figure 1**: détection du mode de traitement de demande ([cliquez pour afficher l’image en taille réelle](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image2.png))


Notez que vous pouvez modifier le mode de traitement de la requête dans la boîte de dialogue Modifier l’Application. Cliquez sur le bouton Sélectionner et modifier le pool d’applications associé à l’application. Notez qu’il n’y a des problèmes de compatibilité lors de la modification d’une application ASP.NET à partir de classique vers le mode intégré. Pour plus d’informations, consultez les articles suivants :

- Mise à niveau de ASP.NET 1.1 vers IIS 7.0 sur Windows Vista et Windows Server 2008-- [https://www.iis.net/learn/application-frameworks/building-and-running-aspnet-applications/upgrading-aspnet-11-to-iis-on-windows-vista-and-windows-server-2008](https://www.iis.net/learn/application-frameworks/building-and-running-aspnet-applications/upgrading-aspnet-11-to-iis-on-windows-vista-and-windows-server-2008)

- Intégration d’ASP.NET à IIS 7.0 - [https://www.iis.net/learn/application-frameworks/building-and-running-aspnet-applications/aspnet-integration-with-iis](https://www.iis.net/learn/application-frameworks/building-and-running-aspnet-applications/aspnet-integration-with-iis)


Si une application ASP.NET utilise DefaultAppPool, vous n’avez pas besoin effectuer les étapes supplémentaires pour obtenir le routage ASP.NET (et par conséquent, ASP.NET MVC) fonctionne. Toutefois, si l’application ASP.NET est configurée pour utiliser Classic .NET AppPool puis poursuivez votre lecture, vous avez plus de travail à effectuer.

## <a name="using-aspnet-mvc-with-older-versions-of-iis"></a>À l’aide d’ASP.NET MVC avec les Versions antérieures d’IIS

Si vous devez utiliser ASP.NET MVC avec une version antérieure d’IIS 7.0 de IIS, ou vous devez utiliser IIS 7.0 en mode classique, vous avez deux options. Tout d’abord, vous pouvez modifier la table d’itinéraires pour utiliser les extensions de fichier. Par exemple, au lieu de demander une URL telle que /Store/Details, vous demandait une URL telle que /Store.aspx/Details.

La deuxième option consiste à créer un élément appelé un *mappage de script générique*. Un mappage de script générique permet de mapper chaque demande dans l’infrastructure ASP.NET.

Si vous n’avez pas accès à votre serveur web (par exemple, votre ASP.NET MVC application est hébergée par un fournisseur de services Internet) vous devrez utiliser la première option. Si vous ne souhaitez pas modifier l’apparence de votre URL, et que vous avez accès à votre serveur web, vous pouvez utiliser la deuxième option.

Nous allons étudier chaque option en détail dans les sections suivantes.

## <a name="adding-extensions-to-the-route-table"></a>Ajout d’Extensions à la Table d’itinéraires

Pour obtenir le routage ASP.NET pour travailler avec les versions antérieures d’IIS, le plus simple consiste à modifier votre table de routage dans le fichier Global.asax. La valeur par défaut et le fichier Global.asax non modifié dans la liste 1 configure un itinéraire nommé l’itinéraire par défaut.

**La liste 1 - Global.asax (non modifié)**

[!code-vb[Main](using-asp-net-mvc-with-different-versions-of-iis-vb/samples/sample1.vb)]

L’itinéraire par défaut configuré dans la liste 1 vous permet des URL de routage ressemblent à ceci :

/ Home/Index

/ Produits/détails/3

/ Produit

Malheureusement, les versions antérieures d’IIS ne communiquera pas ces demandes à l’infrastructure ASP.NET. Par conséquent, ces demandes ne seront acheminés vers un contrôleur. Par exemple, si vous effectuez une demande de navigateur pour l’URL /Home/Index vous allez obtenir la page d’erreur dans la Figure 2.


[![La boîte de dialogue Nouveau projet](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image2.jpg)](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image3.png)

**Figure 2**: réception d’une erreur 404 introuvable ([cliquez pour afficher l’image en taille réelle](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image4.png))


Les versions antérieures d’IIS mappent uniquement certaines demandes à l’infrastructure ASP.NET. La demande doit être une URL avec l’extension de fichier correct. Par exemple, une demande de /SomePage.aspx Obtient le mappage à l’infrastructure ASP.NET. Toutefois, une demande de /SomePage.htm ne fait pas.

Par conséquent, pour obtenir le routage ASP.NET fonctionne, nous devons modifier l’itinéraire par défaut afin qu’il inclue une extension de fichier qui est mappée à l’infrastructure ASP.NET.

Cette opération est effectuée à l’aide d’un script nommé `registermvc.wsf`. Il a été inclus avec la version d’ASP.NET MVC 1 dans `C:\Program Files\Microsoft ASP.NET\ASP.NET MVC\Scripts`, mais à compter d’ASP.NET 2 ce script a été déplacé vers le ASP.NET Futures, disponible à l’adresse [http://aspnet.codeplex.com/releases/view/39978](http://aspnet.codeplex.com/releases/view/39978).

L’exécution de ce script d’enregistre une nouvelle extension .mvc avec IIS. Après avoir inscrit l’extension .mvc, vous pouvez modifier votre itinéraires dans le fichier Global.asax afin que les itinéraires utilisent l’extension .mvc.

Le fichier Global.asax modifié dans la liste 2 fonctionne avec les versions antérieures d’IIS.

**Listing 2 - Global.asax (modifié avec les extensions)**

[!code-vb[Main](using-asp-net-mvc-with-different-versions-of-iis-vb/samples/sample2.vb)]


Important : n’oubliez pas générer votre Application ASP.NET MVC après la modification du fichier Global.asax.


Il existe deux modifications importantes apportées au fichier Global.asax dans le Listing 2. Il existe maintenant deux itinéraires définis dans le fichier Global.asax. Le modèle d’URL pour l’itinéraire par défaut, le premier itinéraire ressemble à :

{controller}.mvc/{action}/{id}

L’ajout de l’extension .mvc modifie le type de fichiers que le module de routage ASP.NET intercepte. Avec cette modification, l’application ASP.NET MVC maintenant achemine les demandes à ce qui suit :

/Home.mvc/Index/

/Product.mvc/Details/3

/Product.mvc/

La deuxième route, l’itinéraire racine, est une nouveauté. Ce modèle d’URL pour l’itinéraire racine est une chaîne vide. Cet itinéraire est nécessaire pour la correspondance des demandes effectuées par rapport à la racine de votre application. Par exemple, l’itinéraire racine correspondra à une requête qui ressemble à ceci :

[http://www.YourApplication.com/](http://www.YourApplication.com/)

Après avoir apporté ces modifications à votre table de routage, vous devez vous assurer que tous les liens dans votre application sont compatibles avec ces nouveaux modèles d’URL. En d’autres termes, assurez-vous que tous vos liens incluent l’extension .mvc. Si vous utilisez la méthode d’assistance Html.ActionLink() pour générer vos liens, vous devez inutile d’apporter des modifications.


Au lieu d’utiliser le script registermvc.wcf, vous pouvez ajouter une nouvelle extension à IIS qui est mappé à l’infrastructure ASP.NET manuellement. Lorsque vous ajoutez une nouvelle extension vous-même, assurez-vous que la case à cocher intitulée **vérifier l’existence du fichier** n’est pas vérifiée.


## <a name="hosted-server"></a>Serveur hébergé

Vous n’avez toujours pas accès à votre serveur web. Par exemple, si vous hébergez votre application ASP.NET MVC à l’aide d’un fournisseur d’hébergement Internet, puis vous ne sont pas nécessairement l’accès à IIS.

Dans ce cas, vous devez utiliser une des extensions de fichiers existantes qui sont mappées à l’infrastructure ASP.NET. Le .aspx, .axd et .ashx extensions sont des exemples d’extensions de fichiers mappées à ASP.NET.

Par exemple, le fichier Global.asax modifié dans la liste 3 utilise l’extension .aspx au lieu de l’extension .mvc.

**La liste 3 - Global.asax (modifié avec les extensions .aspx)**

[!code-vb[Main](using-asp-net-mvc-with-different-versions-of-iis-vb/samples/sample3.vb)]

Le fichier Global.asax dans la liste 3 est exactement le même que le fichier Global.asax, à l’exception du fait qu’il utilise l’extension .aspx au lieu de l’extension .mvc. Vous n’êtes pas obligé d’effectuer n’importe quel programme d’installation sur votre serveur web à distance à utiliser l’extension .aspx.

## <a name="creating-a-wildcard-script-map"></a>Création d’un mappage de scripts générique

Si vous ne souhaitez pas modifier l’URL de votre application ASP.NET MVC, et que vous avez accès à votre serveur web, vous disposez alors d’une option supplémentaire. Vous pouvez créer un mappage de script générique qui mappe toutes les demandes au serveur web à l’infrastructure ASP.NET. De cette façon, vous pouvez utiliser la valeur par défaut de la table d’itinéraires ASP.NET MVC avec IIS 7.0 (en mode classique) ou IIS 6.0.

N’oubliez pas que cette option entraîne l’intercepter chaque demande adressée au serveur web IIS. Cela comprend les demandes pour les images, les pages ASP classiques et les pages HTML. Par conséquent, l’activation d’un caractère générique à ASP.NET un mappage de scripts a en matière de performances.

Voici comment vous activez un mappage de script générique pour IIS 7.0 :

1. Sélectionnez votre application dans la fenêtre Connexions
2. Assurez-vous que le **fonctionnalités** vue est sélectionnée.
3. Double-cliquez sur le **mappages de gestionnaires** bouton
4. Cliquez sur le **ajouter un mappage de Script générique** lier (voir Figure 3)
5. Entrez le chemin d’accès pour le compte aspnet\_fichier isapi.dll (vous pouvez copier ce chemin d’accès à partir du mappage de scripts PageHandlerFactory)
6. Entrez le nom de MVC
7. Cliquez sur le **OK** bouton


[![La boîte de dialogue Nouveau projet](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image3.jpg)](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image5.png)

**Figure 3**: création d’un mappage de script générique avec IIS 7.0 ([cliquez pour afficher l’image en taille réelle](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image6.png))


Suivez ces étapes pour créer un mappage de script générique avec IIS 6.0 :

1. Cliquez sur un site Web et sélectionnez Propriétés
2. Sélectionnez le **répertoire de base** onglet
3. Cliquez sur le **Configuration** bouton
4. Sélectionnez le **mappages** onglet
5. Cliquez sur le **insérer** bouton (voir Figure 4)
6. Collez le chemin d’accès pour le compte aspnet\_isapi.dll dans le champ exécutable (vous pouvez copier ce chemin d’accès à partir du mappage de scripts pour les fichiers .aspx)
7. Désactivez la case à cocher **vérifier l’existence du fichier**
8. Cliquez sur le **OK** bouton


[![La boîte de dialogue Nouveau projet](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image4.jpg)](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image7.png)

**Figure 4**: création d’un mappage de script générique avec IIS 6.0 ([cliquez pour afficher l’image en taille réelle](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image8.png))


Après avoir activé les mappages de scripts génériques, vous devez modifier la table de routage dans le fichier Global.asax afin qu’il inclue un itinéraire de racine. Dans le cas contraire, vous obtenez la page d’erreur dans la Figure 5 lorsque vous faites une demande pour la page de la racine de votre application. Vous pouvez utiliser le fichier Global.asax modifié dans la liste 4.


[![La boîte de dialogue Nouveau projet](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image5.jpg)](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image9.png)

**Figure 5**: erreur d’itinéraire manquant de racine ([cliquez pour afficher l’image en taille réelle](using-asp-net-mvc-with-different-versions-of-iis-vb/_static/image10.png))


**La liste 4 - Global.asax (modifié avec itinéraire de la racine)**

[!code-vb[Main](using-asp-net-mvc-with-different-versions-of-iis-vb/samples/sample4.vb)]

Après avoir activé un mappage de script générique pour IIS 7.0 ou IIS 6.0, vous pouvez apporter les demandes qui fonctionnent avec la table d’itinéraires par défaut qui se présenter comme suit :

/

/ Home/Index

/ Produits/détails/3

/ Produit

## <a name="summary"></a>Récapitulatif

L’objectif de ce didacticiel est d’expliquer comment vous pouvez utiliser ASP.NET MVC lors de l’utilisation d’une version antérieure de IIS (ou IIS 7.0 en mode classique). Nous avons parlé des deux méthodes de mise en route du routage ASP.NET pour travailler avec les versions antérieures d’IIS : modifier la table d’itinéraires par défaut ou créer un mappage de script générique.

La première option vous oblige à modifier les URL utilisées dans votre application ASP.NET MVC. L’un des avantages de cette première option très important sont que vous n’avez pas de l’accès à un serveur web afin de modifier la table d’itinéraires. Cela signifie que vous pouvez utiliser cette première option même lors de l’hébergement de votre application ASP.NET MVC via une connexion Internet société d’hébergement.

La deuxième option consiste à créer un mappage de script générique. L’avantage de cette deuxième option est que vous n’avez pas besoin de modifier votre URL. L’inconvénient de cette deuxième option est qu’elle peut affecter les performances de votre application ASP.NET MVC.

>[!div class="step-by-step"]
[Précédent](using-asp-net-mvc-with-different-versions-of-iis-cs.md)
