---
uid: mvc/overview/older-versions-1/security/authenticating-users-with-forms-authentication-vb
title: "L’authentification des utilisateurs avec des formulaires d’authentification (VB) | Documents Microsoft"
author: microsoft
description: "Découvrez comment utiliser l’attribut [Authorize] de mot de passe protéger certaines pages dans votre application MVC. Vous apprenez à utiliser le Site Web Administration trop..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 4341f5b1-6fe5-44c5-8b8a-18fa84f80177
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/security/authenticating-users-with-forms-authentication-vb
msc.type: authoredcontent
ms.openlocfilehash: c7d52e51158575c674264efd19c81de9b077d27b
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="authenticating-users-with-forms-authentication-vb"></a>L’authentification des utilisateurs avec l’authentification par formulaire (VB)
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment utiliser l’attribut [Authorize] de mot de passe protéger certaines pages dans votre application MVC. Vous apprenez à utiliser l’outil d’Administration de Site Web pour créer et gérer des utilisateurs et des rôles. Vous apprenez également à configurer où sont stockées les informations de compte et le rôle d’utilisateur.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez utiliser les formulaires l’authentification de mot de passe protéger les vues dans vos applications ASP.NET MVC. Vous apprenez à utiliser l’outil d’Administration de Site Web pour créer des utilisateurs et des rôles. Vous apprenez également à empêcher les utilisateurs non autorisés d’appeler des actions de contrôleur. Enfin, vous apprenez à configurer dans lequel les noms d’utilisateur et mots de passe sont stockés.

#### <a name="using-the-web-site-administration-tool"></a>À l’aide de l’outil d’Administration de Site Web

Avant de le faire autre chose, nous devons commencer par créer des utilisateurs et des rôles. Pour créer des rôles et les nouveaux utilisateurs, le plus simple consiste à tirer parti de l’outil d’Administration de Site Web Visual Studio 2008. Vous pouvez lancer cet outil en sélectionnant l’option de menu **projet, la Configuration ASP.NET**. Ou bien, vous pouvez lancer l’outil d’Administration de Site Web en cliquant sur l’icône (quelque peu inquiétant) du marteau atteignez le monde qui s’affiche en haut de la fenêtre de l’Explorateur de solutions (voir Figure 1).

**Figure 1 : lancement de l’outil d’Administration de Site Web**

![clip_image002 [4]](authenticating-users-with-forms-authentication-vb/_static/image1.jpg)

Dans l’outil Administration de Site Web, créer des utilisateurs et rôles en sélectionnant l’onglet sécurité. Cliquez sur le **créer un utilisateur** lien pour créer un nouvel utilisateur nommé Stephen (voir Figure 2). Fournir à l’utilisateur Stephen avec n’importe quel mot de passe (par exemple, *secret*).

**Figure 2 : création d’un nouvel utilisateur**

![clip_image004 [4]](authenticating-users-with-forms-authentication-vb/_static/image2.jpg)

Créer de nouveaux rôles par la première activation de rôles et de la définition d’un ou plusieurs rôles. Activer les rôles en cliquant sur le **Active les rôles** lien. Ensuite, créez un rôle nommé *administrateurs* en cliquant sur le **créer ou gérer des rôles** lier (voir Figure 3).

**Figure 3 : création d’un rôle**

![clip_image006 [4]](authenticating-users-with-forms-authentication-vb/_static/image3.jpg)

Enfin, créez un utilisateur nommé Sarah et Sarah associer le rôle d’administrateur en cliquant sur le lien Créer un utilisateur et en sélectionnant les administrateurs lors de la création de Sarah (voir Figure 4).

**Figure 4 : ajout d’un utilisateur à un rôle**

![clip_image008 [4]](authenticating-users-with-forms-authentication-vb/_static/image4.jpg)

Lorsque toutes les est dite et en fait, vous devez avoir deux nouveaux utilisateurs nommés Stephen et Sarah. Vous devez également disposer d’un nouveau rôle Administrateurs. Catherine est membre du rôle Administrateurs et Stephen n’est pas.

#### <a name="requiring-authorization"></a>Nécessitant une autorisation

Vous pouvez exiger un utilisateur soit authentifié avant que l’utilisateur appelle une action du contrôleur en ajoutant l’attribut [Authorize] à l’action. Vous pouvez appliquer l’attribut [Authorize] à une action de contrôleur individuels, ou vous pouvez appliquer cet attribut à une classe de contrôleur entière.

Par exemple, le contrôleur dans la liste 1 expose une action nommée CompanySecrets(). Étant donné que cette action est décorée avec l’attribut [Authorize], cette action ne peut pas être appelée, sauf si un utilisateur est authentifié.

**La liste 1 – Controllers\HomeController.vb**

[!code-vb[Main](authenticating-users-with-forms-authentication-vb/samples/sample1.vb)]

Si vous appelez l’action CompanySecrets() en entrant l’URL /Home/CompanySecrets dans la barre d’adresses de votre navigateur, vous n’êtes pas un utilisateur authentifié, puis vous allez être redirigé automatiquement à la vue de la connexion (voir Figure 5).

**Figure 5 : la vue de la connexion**

![clip_image010 [4]](authenticating-users-with-forms-authentication-vb/_static/image5.jpg)

Vous pouvez utiliser la vue de la connexion à entrer votre nom d’utilisateur et un mot de passe. Si vous n’êtes pas un utilisateur enregistré, vous pouvez cliquer sur le **inscrire** lien pour accéder au Registre afficher (voir Figure 6). Vous pouvez utiliser la vue de Registre pour créer un nouveau compte d’utilisateur.

**Figure 6 – la vue de Registre**

![clip_image012](authenticating-users-with-forms-authentication-vb/_static/image6.jpg)

Une fois que vous connecter avec succès, vous pouvez afficher le CompanySecrets (voir la Figure 7). Par défaut, vous continuerez à être connecté jusqu'à ce que vous fermiez la fenêtre du navigateur.

**Figure 7 – la vue CompanySecrets**

![clip_image014](authenticating-users-with-forms-authentication-vb/_static/image7.jpg)

#### <a name="authorizing-by-user-name-or-user-role"></a>Autorisation par rôle d’utilisateur ou le nom d’utilisateur

Vous pouvez utiliser l’attribut [Authorize] pour restreindre l’accès à une action de contrôleur à un ensemble particulier d’utilisateurs ou d’un ensemble de rôles d’utilisateur particulier. Par exemple, le contrôleur Home modifié dans la liste 2 contient deux nouvelles actions nommées StephenSecrets() et AdministratorSecrets().

**Listing 2 – Controllers\HomeController.vb**

[!code-vb[Main](authenticating-users-with-forms-authentication-vb/samples/sample2.vb)]

Seul un utilisateur avec le nom d’utilisateur Stephen peut appeler l’action StephenSecrets(). Tous les autres utilisateurs redirigés vers la vue de la connexion. La propriété Users accepte une liste séparée par des virgules des noms de compte d’utilisateur.

Seuls les utilisateurs dans le rôle Administrateurs peuvent appeler l’action AdministratorSecrets(). Par exemple, étant donné que Catherine est membre du groupe Administrateurs, elle peut appeler l’action AdministratorSecrets(). Tous les autres utilisateurs redirigés vers la vue de la connexion. La propriété de rôles accepte une liste séparée par des virgules des noms de rôle.

#### <a name="configuring-authentication"></a>Configuration de l’authentification

À ce stade, vous vous demandez peut-être stockage les informations de compte et le rôle d’utilisateur. Par défaut, les informations sont stockées dans une base de données (RANU) SQL Express nommé ASPNETDB.mdf situé dans les applications de votre application MVC\_dossier de données. Cette base de données est généré automatiquement par l’infrastructure ASP.NET lorsque vous démarrez à l’aide de l’appartenance.

Pour voir la base de données ASPNETDB.mdf dans la fenêtre de l’Explorateur de solutions, vous devez tout d’abord sélectionner l’option de menu projet, afficher tous les fichiers.

À l’aide de la base de données SQL Express par défaut convient lorsque vous développez une application. Très probablement, toutefois, ne voulez-vous utiliser la base de données ASPNETDB.mdf par défaut pour une application de production. Dans ce cas, vous pouvez modifier le stockage des informations de compte d’utilisateur en effectuant le des deux étapes suivantes :

1. Ajouter les objets de base de données des Services d’Application pour votre base de données de production, modifiez votre chaîne de connexion d’application pour pointer vers votre base de données de production

La première étape consiste à ajouter tous les objets de base de données nécessaires (tables et procédures stockées) à votre base de données de production. Pour ajouter ces objets à une base de données le plus simple consiste à tirer parti de l’Assistant Installation de ASP.NET SQL Server (voir la Figure 8). Vous pouvez lancer cet outil en ouvrant l’invite de commandes de Visual Studio 2008 à partir du groupe de programmes Microsoft Visual Studio 2008 et en exécutant la commande suivante à partir de l’invite de commandes :

ASPNET\_regsql

**Figure 8 : l’Assistant Installation du serveur SQL d’ASP.NET**

![clip_image016](authenticating-users-with-forms-authentication-vb/_static/image8.jpg)

L’Assistant Installation de ASP.NET SQL Server vous permet de sélectionner une base de données SQL Server sur votre réseau et d’installer tous les objets de base de données requis par les services d’application ASP.NET. Le serveur de base de données n’est pas requis se trouve sur votre ordinateur local.

> [!NOTE]
> Si vous ne souhaitez pas utiliser l’Assistant Installation de ASP.NET SQL Server, vous pouvez trouver des scripts SQL pour ajouter les objets de base de données d’application services dans le dossier suivant :


> C:\Windows\Microsoft.NET\Framework\v2.0.50727


Après avoir créé les objets de base de données nécessaires, vous devez modifier la connexion de base de données utilisée par votre application MVC. Modifier la chaîne de connexion ApplicationServices dans votre fichier de configuration (web.config) web afin qu’il pointe vers la base de données de production. Par exemple, la connexion modifiée dans la liste 3 pointe vers une base de données nommée MyProductionDB (la chaîne de connexion ApplicationServices d’origine a été commentée).

**La liste 3 – Web.config**

[!code-xml[Main](authenticating-users-with-forms-authentication-vb/samples/sample3.xml)]

#### <a name="configuring-database-permissions"></a>Configuration des autorisations de base de données

Si vous utilisez la sécurité intégrée pour se connecter à votre base de données vous devez ajouter le compte d’utilisateur Windows approprié en tant que connexion à votre base de données. Le compte approprié dépend de si vous utilisez le serveur de développement ASP.NET ou Internet Information Services en tant que votre serveur web. Le compte d’utilisateur approprié dépend également de votre système d’exploitation.

Si vous utilisez le serveur de développement ASP.NET (le serveur web par défaut utilisé par Visual Studio) votre application s’exécute dans le contexte de votre compte d’utilisateur Windows. Dans ce cas, vous devez ajouter votre compte d’utilisateur Windows en tant qu’une connexion au serveur de base de données.

Ou bien, si vous utilisez Internet Information Services vous devez ajouter le compte ASPNET ou le compte de SERVICE de réseau/autorité NT en tant qu’une connexion au serveur de base de données. Si vous utilisez Windows XP, ajoutez le compte ASPNET en tant que connexion à votre base de données. Si vous utilisez un système d’exploitation plus récent, tel que Windows Vista ou Windows Server 2008, puis ajoutez le compte de SERVICE de réseau/autorité NT en tant que la connexion de base de données.

Vous pouvez ajouter un nouveau compte d’utilisateur à votre base de données à l’aide de Microsoft SQL Server Management Studio (voir la Figure 9).

**Figure 9 : création d’une nouvelle connexion de Microsoft SQL Server**

![clip_image018](authenticating-users-with-forms-authentication-vb/_static/image9.jpg)

Après avoir créé la connexion requise, vous devez mapper la connexion à un utilisateur de base de données avec les rôles de base de données à droite. Double-cliquez sur la connexion, puis sélectionnez l’onglet mappage de l’utilisateur. Sélectionnez un ou plusieurs rôles de base de données d’application services. Par exemple, pour authentifier les utilisateurs, vous devez activer le compte aspnet\_appartenance\_rôle de base de données BasicAccess. Pour créer de nouveaux utilisateurs, vous devez activer le compte aspnet\_appartenance\_rôle de base de données FullAccess (voir la Figure 10).

**La figure 10 – Ajout de rôles de base de données des Services d’Application**

![clip_image020](authenticating-users-with-forms-authentication-vb/_static/image10.jpg)

#### <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à utiliser l’authentification par formulaire lors de la création d’une application ASP.NET MVC. Tout d’abord, vous avez appris à créer de nouveaux utilisateurs et rôles en tirant parti de l’outil Administration de Site Web. Ensuite, vous avez appris à utiliser l’attribut [Authorize] pour empêcher les utilisateurs non autorisés d’appeler des actions de contrôleur. Enfin, vous avez appris comment configurer votre application MVC pour stocker les informations d’utilisateur et rôle dans une base de données de production.

>[!div class="step-by-step"]
[Précédent](preventing-javascript-injection-attacks-cs.md)
[Suivant](authenticating-users-with-windows-authentication-vb.md)
