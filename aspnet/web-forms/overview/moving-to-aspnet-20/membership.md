---
uid: web-forms/overview/moving-to-aspnet-20/membership
title: "L’appartenance | Documents Microsoft"
author: microsoft
description: "L’appartenance d’ASP.NET s’appuie sur la réussite du modèle d’authentification de formulaires à partir de ASP.NET 1.x. Authentification par formulaire ASP.NET fournit un moyen pratique d’incorp..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: f2339485-5d78-4c5e-8c0a-dc9b8a315345
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/membership
msc.type: authoredcontent
ms.openlocfilehash: 1a5a495845b60f9aac51c9776311af67f5dc8767
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
<a name="membership"></a>Appartenance
====================
par [Microsoft](https://github.com/microsoft)

> L’appartenance d’ASP.NET s’appuie sur la réussite du modèle d’authentification de formulaires à partir de ASP.NET 1.x. Authentification par formulaire ASP.NET fournit un moyen pratique pour incorporer un formulaire de connexion de votre application ASP.NET et valider des utilisateurs par rapport à une base de données ou une autre banque de données.


L’appartenance d’ASP.NET s’appuie sur la réussite du modèle d’authentification de formulaires à partir de ASP.NET 1.x. Authentification par formulaire ASP.NET fournit un moyen pratique pour incorporer un formulaire de connexion de votre application ASP.NET et valider des utilisateurs par rapport à une base de données ou une autre banque de données. Les membres de la classe FormsAuthentication permettent de gérer les cookies pour l’authentification, vérifier une connexion valide, connecte un utilisateur out etc. Toutefois, l’implémentation de l’authentification par formulaire dans une application ASP.NET 1.x peut nécessiter une grande quantité de code.

L’appartenance dans ASP.NET 2.0 est une avancée majeure à l’utilisation de l’authentification par formulaire uniquement. (L’appartenance est plus fiable en cas d’association avec l’authentification par formulaire, mais à l’aide de l’authentification par formulaire n’est pas obligatoire). Comme vous le verrez bientôt, vous pouvez utiliser l’appartenance d’ASP.NET et les contrôles de connexion dans ASP.NET 2.0 pour implémenter un système d’appartenance puissantes sans écrire la quantité de code.

## <a name="implementing-membership-in-aspnet-20"></a>L’implémentation de l’appartenance dans ASP.NET 2.0

L’appartenance est implémentée en suivant quatre étapes. Gardez à l’esprit qu’il n’y a plusieurs sous-étapes sont impliqués, ainsi que la configuration facultative qui peut également être implémentée. Ces étapes sont destinés à illustrer la vue d’ensemble de la configuration de l’appartenance.

1. Créer votre base de données d’appartenance (si SQL Server est utilisé comme magasin de l’appartenance.)
2. Spécifiez les options de l’appartenance dans vos fichiers de configuration des applications. (L’appartenance est activée par défaut).
3. Déterminer le type de magasin d’appartenance que vous souhaitez utiliser. Les options sont : 

    - Microsoft SQL Server (version 7.0 ou version ultérieure)
    - Magasin Active Directory
    - Fournisseur d’appartenances personnalisé
4. Configurer l’application pour l’authentification par formulaire ASP.NET. Une fois encore, l’appartenance est conçue pour tirer parti de l’authentification par formulaire, mais à l’aide de l’authentification par formulaire n’est pas obligatoire.
5. Définissez les comptes d’utilisateur pour l’appartenance et configurer des rôles si vous le souhaitez.

## <a name="creating-the-membership-database"></a>Création de la base de données d’appartenance

Si vous utilisez SQL Server 7.0 ou ultérieure en tant que votre magasin d’appartenance, vous pouvez utiliser le compte aspnet\_utilitaire regsql (disponible plus facilement à partir de l’invite Visual Studio .NET 2005 commande) pour configurer votre base de données. Le compte aspnet\_utilitaire de regsql peut être utilisé comme un outil de ligne de commande ou via un Assistant d’interface utilisateur. La méthode de l’Assistant est le moyen le plus simple pour configurer votre base de données. Pour accéder à l’Assistant, exécutez simplement la commande suivante :

`aspnet_regsql W`

Une fois que vous exécutez cette commande, s’affiche avec l’Assistant Installation de ASP.NET SQL Server comme indiqué ci-dessous.


![](membership/_static/image1.jpg)

**Figure 1**


L’Assistant Installation de ASP.NET SQL Server crée le site Web de l’instance que vous spécifiez dans l’Assistant. Toutefois, ASP.NET utilise la chaîne de connexion dans le fichier machine.config pour se connecter à votre base de données. Par défaut, cette chaîne de connexion pointe vers une instance de SQL Server 2005, donc si vous utilisez une instance de SQL Server 2000 ou SQL Server 7.0, vous devez modifier la chaîne de connexion dans le fichier machine.config. Cette chaîne de connexion peut être située ici :

[!code-xml[Main](membership/samples/sample1.xml)]

Malheureusement, si vous ne modifiez pas la chaîne de connexion, ASP.NET ne vous donnera une erreur descriptive. Simplement, il continuera à se plaindre indiquant que vous n’avez pas créé la base de données. Dans le cas ci-dessus, j’ai modifié la chaîne de connexion pour pointer vers mon instance locale de SQL Server 2000.

## <a name="specifying-configuration-and-adding-users-and-roles"></a>Spécification de Configuration et ajouter des utilisateurs et des rôles

L’étape suivante de la configuration de l’appartenance consiste à ajouter les informations nécessaires au fichier web.config de l’application. Dans ASP.NET 1.x, en modifiant le fichier web.config a été parfois difficile en raison de l’utilisation de lowerCamelCase et l’absence d’Intellisense. Visual Studio .NET 2005 facilite la tâche avec Intellisense pour les fichiers de configuration, mais ASP.NET 2.0 va encore plus loin en fournissant une interface Web pour la modification des fichiers de configuration.

Vous pouvez lancer l’interface Web en cliquant sur le bouton de Configuration ASP.NET dans la barre d’outils Explorateur de solutions, comme indiqué ci-dessous. Vous pouvez également lancer l’interface Web via les fenêtres contextuelles qui s’affichent lors de l’insertion de contrôles de connexion.


![](membership/_static/image2.jpg)

**Figure 2**


Cette opération lance l’outil d’Administration de Site Web ASP.NET indiqué ci-dessous. L’Administration de Site Web ASP.NET est une interface de quatre-onglet qui facilite la gestion des paramètres de l’application. Les onglets suivants sont disponibles :

- **Accueil**
- **Sécurité** configurer des utilisateurs, rôles et l’accès.
- **Application** configurer les paramètres de l’application.
- **Fournisseur** configurer et tester votre fournisseur d’appartenances applications.

L’outil d’Administration de Site Web vous permet de facilement créer des utilisateurs, créer des rôles et gérer les utilisateurs et les rôles. Cette possibilité n’est pas disponible dans l’interface Windows. L’interface Windows vous permet de définir facilement les paramètres d’autorisation et d’ajouter, supprimer et gérer les fournisseurs, les fonctionnalités qui ne sont pas dans l’outil Administration de Site Web.

Pour lancer l’interface Windows, ouvrez le composant logiciel enfichable Internet Information Services, avec le bouton droit sur votre application et choisissez Propriétés. Cliquez sur l’onglet ASP.NET, puis sur le bouton Modifier la Configuration. (L’application doit être en cours d’exécution sous ASP.NET 2.0 pour le bouton Modifier la Configuration doit être activée. Vous pouvez configurer la version d’ASP.NET dans la boîte de dialogue ASP.NET ainsi.) La boîte de dialogue Paramètres de Configuration ASP.NET s’affiche comme illustré ci-dessous.


![](membership/_static/image3.jpg)

**Figure 3**


Sous l’onglet Général, les chaînes de connexion et les paramètres d’application sont répertoriées. Tous les paramètres en italique sont définis dans un fichier de configuration parent (le fichier machine.config ou un fichier web.config à un niveau supérieur) et sont des paramètres pas en italique à partir du fichier de configuration d’applications. Si un paramètre est ajouté, supprimé ou modifié au niveau de l’application, ASP.NET sera ajouter, supprimer ou modifier la valeur de paramètre web.config de niveaux d’application au lieu de supprimer le paramètre du fichier de configuration à partir de laquelle elle est héritée.

L’onglet authentification est indiqué ci-dessous. Il s’agit sur lequel vous allez configurer vos paramètres d’appartenance. Paramètres d’authentification, les fournisseurs d’appartenances, de formulaires et les fournisseurs de rôles peuvent être configurées ici.


![](membership/_static/image4.jpg)

**Figure 4**


## <a name="implementing-membership-in-your-application"></a>Implémentation de l’appartenance dans votre Application

Pour implémenter l’appartenance d’ASP.NET 2.0 dans votre application, le plus simple consiste à utiliser les contrôles d’ouverture de session fournis. Cette méthode vous permet d’implémenter les principes fondamentaux de l’appartenance d’ASP.NET 2.0 sans écrire de code du tout.

Les contrôles d’ouverture de session suivantes sont disponibles dans ASP.NET 2.0 :

## <a name="login-control"></a>Contrôle de connexion

Le contrôle de connexion fournit une interface pour un utilisateur de se connecter à votre système d’appartenance. Il fournit un textboxt nom d’utilisateur et mot de passe et un bouton de connexion. Nombreuses autres fonctionnalités telles qu’un lien pour vous inscrire à des personnes qui les n'ont pas encore fait, une case à cocher qui permet à l’utilisateur de connexion automatiquement lors des visites suivantes, un lien pour un rappel de mot de passe, un etc. courantes. Toutes les fonctionnalités du contrôle de connexion sont personnalisables via les propriétés du contrôle.

Dans ASP.NET 1.x, les développeurs devaient écrire une grande quantité de code pour effectuer une recherche lors de l’utilisation de l’authentification par formulaire. Avec l’appartenance d’ASP.NET 2.0, vous pouvez valider les utilisateurs sans écrire de code du tout. ASP.NET effectuera automatiquement la recherche de l’utilisateur pour vous. (Si vous utilisez le contrôle de connexion sans l’appartenance d’ASP.NET, vous pouvez utiliser la **OnAuthenticate** méthode pour valider l’utilisateur.)

## <a name="loginview-control"></a>Contrôle LoginView

Le contrôle LoginView est un contrôle basé sur un modèle qui fournit deux modèles par défaut ; AnonymousTemplate et LoggedInTemplate. Le modèle est affiché est déterminé par ou non l’utilisateur est connecté à votre système d’appartenance. Ce contrôle est généralement utilisé pour afficher un contrôle de connexion lorsqu’un utilisateur n’est pas encore connecté et d’un contrôle LoginStatus et/ou d’autres contrôles de connexion lorsque l’utilisateur s’est connecté. Si vous utilisez la gestion des rôles dans votre application ASP.NET, le contrôle LoginView peut afficher un modèle spécifique en fonction du rôle des utilisateurs. (Plus sur ASP.NET gestion des rôles est abordée plus loin.)

## <a name="passwordrecovery-control"></a>Contrôle PasswordRecovery

Le contrôle PasswordRecovery permet aux utilisateurs de recevoir un courrier électronique avec son propre mot de passe actuel ou de réinitialiser son mot de passe. Texte clair et les mots de passe chiffrés peuvent récupérés et envoyé par courrier électronique aux utilisateurs. Si le mot de passe est haché, il ne peut pas être récupéré. Au lieu de cela, l’utilisateur sera nécessaire pour effectuer une réinitialisation de mot de passe.

## <a name="loginstatus-control"></a>Contrôle LoginStatus

Le contrôle LoginStatus est utilisé pour afficher un indicateur de connexion pour les utilisateurs qui ne sont pas connectés et un indicateur de fermeture de session pour les utilisateurs qui sont actuellement connectés. La propriété Request.IsAuthenticated est utilisée pour déterminer quels indicateur à afficher. L’indicateur affiché par le contrôle LoginStatus peut être du texte (implémentées via la **LoginText** et **LogoutText** propriétés) ou des images (implémentées via la **LoginImageUrl**et **LogoutImageUrl** propriétés.)

Lorsqu’un utilisateur se déconnecte via le contrôle LoginStatus, il est redirigé vers l’URL spécifiée par la **LogoutPageUrl** propriété. Si cette propriété n’est pas définie, la page actuelle est actualisée. Étant donné que le site est probablement protégé par l’authentification par formulaire, l’actualisation de la page actuelle redirige l’utilisateur vers la page de connexion pour le site.

## <a name="loginname-control"></a>Contrôle LoginName

Le contrôle LoginName affiche le nom d’utilisateur de l’utilisateur actuellement connecté au site.

## <a name="createuserwizard-control"></a>Contrôle CreateUserWizard

Le contrôle CreateUserWizard fournit aux utilisateurs un moyen pratique pour vous inscrire à votre système d’appartenance. Vous pouvez ajouter des étapes (implémentées comme une collection de WizardSteps) via l’interface indiqué ci-dessous.


![](membership/_static/image5.jpg)

**Figure 5**


CreateUserWizard est un contrôle basé sur un modèle qui dérive de la classe de l’Assistant et fournit les modèles suivants :

- **HeaderTemplate** ce modèle de contrôle l’apparence de l’en-tête de l’Assistant.
- **SidebarTemplate** ce modèle de contrôle l’apparence de la barre latérale de l’Assistant.
- **StartNavigationTemplate** ce contrôle modèle l’apparence de la navigation de l’Assistant à l’étape de démarrage.
- **StepNavigationTemplate** ce modèle de contrôle l’apparence de la zone de navigation lorsque dans l’étape de début ou de fin.
- **FinishNavigationTemplate** ce modèle de contrôle l’apparence de la zone de navigation à l’étape finale.

En outre, pour chaque étape que vous ajoutez à l’Assistant, ASP.NET créera un modèle personnalisé qui contient un ContentTemplate et un CustomNavigationTemplate pour cette étape. Pour plus d’informations sur la personnalisation de CreateUserWizard, consultez la documentation de VS.NET 2005 :

## <a name="changepassword-control"></a>Contrôle ChangePassword

Le contrôle ChangePassword permet aux utilisateurs de modifier son mot de passe. Si la propriété DisplayUserName est true (valeur est false par défaut), l’utilisateur peut modifier son mot de passe lorsqu’ils ne sont pas connectés. Si l’utilisateur *est* déjà connecté et que la propriété DisplayUserName est true, l’utilisateur sera en mesure de modifier le mot de passe d’un autre utilisateur en fournissant leur connaissant l’ID d’utilisateur de cet utilisateur n’est pas connecté.

Gardez à l’esprit que si vous souhaitez que les utilisateurs soient en mesure de modifier les mots de passe sans avoir à se connecter, vous devez vous assurer que la page sur laquelle figure le contrôle ChangePassword autorise l’accès anonyme. Évidemment, les utilisateurs devront fournir leur ancien mot de passe afin de modifier leur mot de passe.

## <a name="role-management"></a>Gestion des rôles

Gestion des rôles permet d’affecter des utilisateurs à un rôle particulier et limitez l’accès à certains fichiers ou les dossiers en fonction de ce rôle. Gestion des rôles fournit également une API afin que vous pouvez déterminer le rôle de someones ou déterminer tous les utilisateurs dans un rôle particulier et réagir en conséquence par programme.

Gestion des rôles n’est pas obligatoire dans l’appartenance d’ASP.NET, ni l’appartenance au groupe est obligatoire pour pouvoir utiliser la gestion des rôles. Toutefois, les deux complètent mutuellement correcte et il est probable que les développeurs utiliseront les conjointement avec eux.

Pour activer la gestion des rôles dans votre application, apportez la modification suivante dans votre fichier web.config :

[!code-xml[Main](membership/samples/sample2.xml)]

Lorsque le **cacheRolesInCookie** attribut est défini sur true, ASP.NET met en cache une appartenance de rôle des utilisateurs dans un cookie sur le client. Cela permet des recherches de rôle sans appels dans le fournisseur RoleProvider. Lorsque vous utilisez cet attribut, les développeurs sont invités à s’assurer que les **cookieProtection** attribut est défini sur tous. (Il s’agit du paramètre par défaut). Cela garantit que les données de cookie sont chiffrées et permettant de se pour assurer que le contenu des cookies n’ont pas été modifié. Rôles peuvent être ajoutés à l’aide de l’outil d’Administration de Site Web. Il vous permet facilement définir des rôles, configurer l’accès à des parties du site en fonction de ces rôles et affecter des utilisateurs aux rôles.


![](membership/_static/image6.jpg)

**Figure 6**


Comme indiqué ci-dessus, vous pouvant ajouter de nouveaux rôles simplement entrer le nom du rôle, puis cliquez sur Ajouter un rôle. Rôles existants peuvent être gérés ou supprimés en cliquant sur le lien approprié dans la liste des rôles existants.

Lorsque vous gérez un rôle, vous pouvez ajouter ou supprimer des utilisateurs comme indiqué ci-dessous.


![](membership/_static/image7.jpg)

**Figure 7**


En activant la case à cocher de l’utilisateur est dans le rôle, vous pouvez facilement ajouter un utilisateur à un rôle spécifique. ASP.NET met automatiquement à jour votre base de données d’appartenance avec les entrées appropriées. Vous devez également configurer des règles d’accès pour votre application. Les développeurs ASP.NET 1.x sont familiarisés avec cela via le &lt;autorisation&gt; élément dans le fichier web.config et cette option est toujours disponible dans ASP.NET 2.0. Toutefois, son plus facile à gérer l’accès à des règles à l’aide de l’outil Site Web Administration comme indiqué ci-dessous.


![](membership/_static/image8.jpg)

**Figure 8**


Dans ce cas, le dossier d’Administration est mis en surbrillance (son difficile, car l’outil met en évidence en gris clair) et le rôle Administrateurs a été accordé l’accès. Tous les autres utilisateurs sont refusées. Vous pouvez cliquer sur l’icône principal pour sélectionner une règle et puis utilisez les boutons Monter et Descendre pour réorganiser les règles. Comme avec ASP.NET &lt;autorisation&gt; élément, les règles sont traitées dans l’ordre dans lequel elles apparaissent. En d’autres termes, si l’ordre des règles dans la capture ci-dessus ont été annulée, n’auraient pas accès au dossier d’Administration, car la première règle ASP.NET se produirait serait la règle qui refuse tout le monde dans le dossier.

ASP.NET 2.0 ajoute un fichier web.config dans le dossier pour lequel vous spécifiez une règle d’accès. Règles d’accès peuvent être modifiés via le fichier de configuration ou via l’outil d’Administration de Site Web. En d’autres termes, l’outil d’Administration de Site Web est simplement une interface par le biais duquel le fichier de configuration peut être modifié dans un environnement convivial.

## <a name="using-roles-in-code"></a>À l’aide de rôles dans le Code

L’API de gestion des rôles n’a pas changé depuis la version 1.x. Le **IsInRole** méthode est utilisée pour déterminer si un utilisateur est dans un rôle particulier.

[!code-csharp[Main](membership/samples/sample3.cs)]

ASP.NET crée également une instance RolePrincipal en tant que membre du contexte actuel. L’objet RolePrincipal peut être utilisé pour obtenir tous les rôles auxquels l’utilisateur appartient comme suit :

[!code-csharp[Main](membership/samples/sample4.cs)]

## <a name="using-rolegroups-with-the-loginview-control"></a>À l’aide de RoleGroups avec le contrôle LoginView

Maintenant que vous avez une compréhension de la gestion des rôles et d’appartenance, permet de décrivent brièvement comment le contrôle LoginView tire parti de cette possibilité dans ASP.NET 2.0. Comme nous l’avons vu précédemment, le contrôle LoginView est un contrôle basé sur un modèle qui contient les deux modèles par défaut ; AnonymousTemplate et LoggedInTemplate. Dans les tâches LoginView boîte de dialogue est un lien (voir ci-dessous) qui permet de modifier les RoleGroups.


![](membership/_static/image9.jpg)

**Figure 9**


Chaque objet RoleGroup contient un tableau de chaînes qui définit les rôles RoleGroup s’applique à. Pour ajouter un nouveau RoleGroup au contrôle LoginView, cliquez sur le lien Modifier les RoleGroups. Dans l’image ci-dessus, vous pouvez voir que j’ai ajouté un RoleGroup nouveau pour les administrateurs. En sélectionnant cette RoleGroup (RoleGroup[0]) dans la liste déroulante des vues, je peux configurer un modèle qui sera affiché uniquement aux membres du rôle Administrateurs. Dans l’image ci-dessous, j’ai ajouté un RoleGroup nouvelle qui s’applique aux membres du rôle Sales et le rôle de Distribution. Cette opération ajoute un deuxième RoleGroup dans la liste déroulante des vues dans la boîte de dialogue de tâches LoginView et tout élément ajouté à ce modèle est visible par tout utilisateur de la vente ou la Distribution rôle.


![](membership/_static/image10.jpg)

**Figure 10**


## <a name="overriding-the-existing-membership-provider"></a>Substitution du fournisseur d’appartenances existantes

Il existe plusieurs méthodes que vous pouvez étendre les fonctionnalités de l’appartenance d’ASP.NET. Tout d’abord, vous pouvez évidemment modifier les fonctionnalités existantes de la classe SqlMembershipProvider en hérite de celui-ci et en remplaçant ses méthodes. Par exemple, si vous souhaitez implémenter votre propre fonctionnalité lorsque les utilisateurs sont créés, vous pouvez créer votre propre classe qui hérite de SqlMembershipProvider comme suit :

[!code-csharp[Main](membership/samples/sample5.cs)]

Si, en revanche, vous souhaitez créer votre propre fournisseur (pour stocker vos informations d’appartenance dans une base de données Access, par exemple), vous pouvez créer votre propre fournisseur.

## <a name="creating-your-own-membership-provider"></a>Créer votre propre fournisseur d’appartenances

Pour créer votre propre fournisseur d’appartenances, vous devez d’abord créer une classe qui hérite de la classe MembershipProvider. Si vous utilisez VB.NET, Visual Studio 2005 ajoute les stubs pour toutes les méthodes que vous devez substituer. Si vous utilisez c#, jusqu'à son vous permet d’ajouter les stubs.

Vous devez remplacer les éléments suivants :

- Propriété ApplicationName
- ChangePassword (fonction)
- ChangePasswordQuestionAndAnswer (fonction)
- CreateUser (fonction)
- DeleteUser (fonction)
- Propriété de EnablePasswordReset
- Propriété de EnablePasswordRetrieval
- FindUsersByEmail (fonction)
- FindUsersByName (fonction)
- GetAllUsers (fonction)
- GetNumberOfUsersOnline (fonction)
- GetPassword (fonction)
- GetUser (fonction)
- GetUserNameByEmail (fonction)
- Propriété de MaxInvalidPasswordAttempts
- MinRequiredNonAlphanumericCharacters propriété
- Propriété de MinRequiredPasswordLength
- Propriété de PasswordAttemptWindow
- Propriété de PasswordFormat
- Propriété de PasswordStrengthRegularExpression
- Propriété RequiresQuestionAndAnswer
- Propriété de RequiresUniqueEmail
- ResetPassword (fonction)
- Déverrouillage de la fonction utilisateur
- UpdateUser (fonction)
- ValidateUser (fonction)

Vous avez liste à implémenter en tant que développeur c#. Il peut s’avérer plus facile de créer la classe dans VB.NET sans aucune implémentation et ensuite utiliser .NET Reflector ou un outil similaire pour convertir le code en c#.

La chaîne de connexion et d’autres propriétés doivent être définies à leurs valeurs par défaut dans la méthode Initialize. (La méthode Initialize est déclenchée lorsque le fournisseur est chargé au moment de l’exécution.) Le deuxième paramètre à la méthode Initialize est de type System.Collections.Specialized.NameValueCollection et est une référence à la &lt;ajouter&gt; élément qui est associé à votre fournisseur personnalisé dans le fichier web.config. Cette entrée ressemble à ceci :

[!code-xml[Main](membership/samples/sample6.xml)]

Voici un exemple de la méthode Initialize.

[!code-csharp[Main](membership/samples/sample7.cs)]

Pour valider l’utilisateur lorsqu’ils envoient à votre formulaire de connexion, vous devez utiliser la méthode ValidateUser. Cette méthode se déclenche lorsque l’utilisateur clique sur le bouton de connexion dans le contrôle de connexion. Vous devez placer votre code qui effectue la recherche de l’utilisateur dans cette méthode.

Comme vous pouvez le voir, l’écriture de votre propre fournisseur d’appartenances n’est pas difficile et vous permet d’étendre cette fonctionnalité puissante d’ASP.NET 2.0.
