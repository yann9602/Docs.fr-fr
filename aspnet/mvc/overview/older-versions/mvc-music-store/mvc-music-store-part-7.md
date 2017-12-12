---
uid: mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-7
title: "Partie 7 : L’appartenance et l’autorisation | Documents Microsoft"
author: jongalloway
description: "Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 7 couvre l’appartenance et l’autorisation."
ms.author: aspnetcontent
manager: wpickett
ms.date: 10/13/2010
ms.topic: article
ms.assetid: c8511ebe-68bc-4240-87c3-d5ced84a3f37
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-7
msc.type: authoredcontent
ms.openlocfilehash: 064f2d6eca087fae8c796d1dde78d5079d3803ca
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="part-7-membership-and-authorization"></a>Partie 7 : L’appartenance et l’autorisation
====================
par [Jon Galloway](https://github.com/jongalloway)

> Le magasin de musique MVC est une application du didacticiel qui présente et explique étape par étape comment utiliser ASP.NET MVC et Visual Studio pour le développement web.  
>   
> Le magasin de musique MVC est une implémentation de magasin exemple léger qui vend des albums de musique en ligne et implémente l’administration de site de base, authentification de l’utilisateur et les fonctionnalités de panier d’achat.  
>   
> Cette série de didacticiels détaille toutes les mesures prises pour générer l’exemple d’application de magasin de musique ASP.NET MVC. Partie 7 couvre l’appartenance et l’autorisation.


Notre contrôleur du Gestionnaire de magasin est actuellement accessible à toute personne visitant notre site. Nous allons modifier cette option pour restreindre l’accès aux administrateurs de site.

## <a name="adding-the-accountcontroller-and-views"></a>Ajout du AccountController et vues

Une différence entre le modèle d’Application Web de ASP.NET MVC 3 complet et le modèle d’Application ASP.NET MVC 3 vide Web est que le modèle vide n’inclut pas un contrôleur de compte. Nous allons ajouter un contrôleur de compte en copiant quelques fichiers à partir d’une application ASP.NET MVC à partir du modèle d’Application Web de ASP.NET MVC 3 complet.

Créez une application ASP.NET MVC en utilisant le modèle d’Application Web de ASP.NET MVC 3 complète et copiez les fichiers suivants dans les mêmes répertoires dans notre projet :

1. Copiez AccountController.cs dans le répertoire de contrôleurs
2. Copiez AccountModels dans le répertoire de modèles
3. Créer un répertoire de compte dans le répertoire de vues et de copier toutes les quatre affichages dans

Modifier l’espace de noms pour les classes de contrôleur et le modèle afin qu’ils commencent par MvcMusicStore. La classe AccountController doit utiliser l’espace de noms MvcMusicStore.Controllers, et la classe AccountModels doit utiliser l’espace de noms MvcMusicStore.Models.

*Remarque : Ces fichiers sont également disponibles dans le téléchargement MvcMusicStore-Assets.zip à partir de laquelle nous notre site conception fichiers copiés au début du didacticiel. Les fichiers d’appartenance sont situés dans le répertoire de Code.*

La solution de mise à jour doit se présenter comme suit :

![](mvc-music-store-part-7/_static/image1.png)

## <a name="adding-an-administrative-user-with-the-aspnet-configuration-site"></a>Ajout d’un utilisateur administratif avec le site de Configuration ASP.NET

Avant que nous avons besoin d’autorisation dans notre site Web, nous devrons créer un utilisateur avec accès. Pour créer un utilisateur, le plus simple consiste à utiliser le site Web de Configuration ASP.NET intégré.

Lancez le site Web de Configuration d’ASP.NET en cliquant sur Suivant l’icône dans l’Explorateur de solutions.

![](mvc-music-store-part-7/_static/image2.png)

Cela permet de lancer un site Web de configuration. Cliquez sur l’onglet sécurité de l’écran d’accueil, puis cliquez sur le lien « Activer les rôles » dans le centre de l’écran.

![](mvc-music-store-part-7/_static/image3.png)

Cliquez sur le lien « Créer ou gérer des rôles ».

![](mvc-music-store-part-7/_static/image4.png)

Entrez le nom de rôle « Administrateur » et appuyez sur le bouton Ajouter un rôle.

![](mvc-music-store-part-7/_static/image5.png)

Cliquez sur le bouton précédent, puis cliquez sur le lien de création d’utilisateur sur le côté gauche.

![](mvc-music-store-part-7/_static/image6.png)

Renseignez les champs des informations sur la gauche en utilisant les informations suivantes :

| **Champ** | **Valeur** |
| --- | --- |
| **Nom d'utilisateur** | Administrateur |
| **Mot de passe** | password123 ! |
| **Confirmer le mot de passe** | password123 ! |
| **Message électronique** | (n’importe quelle adresse de messagerie fonctionne) |
| **Question de sécurité** | (comme vous le souhaitez) |
| **Réponse de sécurité** | (comme vous le souhaitez) |

*Remarque : Vous pouvez utiliser naturellement de n’importe quel mot de passe. Les paramètres de sécurité de mot de passe par défaut requièrent un mot de passe est de 7 caractères et contient un caractère non alphanumérique.*

Sélectionnez le rôle d’administrateur pour cet utilisateur, puis cliquez sur le bouton Créer un utilisateur.

![](mvc-music-store-part-7/_static/image7.png)

À ce stade, vous devez voir un message indiquant que l’utilisateur a été créé avec succès.

![](mvc-music-store-part-7/_static/image8.png)

Vous pouvez maintenant fermer la fenêtre du navigateur.

## <a name="role-based-authorization"></a>Autorisation basée sur un rôle

Nous pouvons désormais restreindre l’accès à la StoreManagerController à l’aide de l’attribut [Authorize], qui spécifie que l’utilisateur doit être dans le rôle d’administrateur pour accéder à toute action de contrôleur dans la classe.

[!code-csharp[Main](mvc-music-store-part-7/samples/sample1.cs)]

*Remarque : L’attribut [Authorize] peut être placé sur les méthodes d’action spécifique, ainsi qu’au niveau de la classe de contrôleur.*

Maintenant accédant à /StoreManager affiche une boîte de dialogue Ouverture de session :

![](mvc-music-store-part-7/_static/image9.png)

Après l’ouverture de session avec notre nouveau compte d’administrateur, nous sommes en mesure de passer à l’écran Album modifier comme avant.

>[!div class="step-by-step"]
[Précédent](mvc-music-store-part-6.md)
[Suivant](mvc-music-store-part-8.md)
