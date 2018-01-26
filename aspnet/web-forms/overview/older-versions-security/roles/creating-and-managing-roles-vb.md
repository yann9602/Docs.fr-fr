---
uid: web-forms/overview/older-versions-security/roles/creating-and-managing-roles-vb
title: "Création et la gestion des rôles (VB) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel examine les étapes nécessaires à la configuration de l’infrastructure de rôles. Après cela, nous allons créer des pages web pour créer et supprimer des rôles."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/24/2008
ms.topic: article
ms.assetid: 83af9f5f-9a00-4f83-8afc-e98bdd49014e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/roles/creating-and-managing-roles-vb
msc.type: authoredcontent
ms.openlocfilehash: 0b1132c6d782cd85edb8cbee98c8ab95a15171ac
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="creating-and-managing-roles-vb"></a>Création et la gestion des rôles (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/VB.09.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/aspnet_tutorial09_CreatingRoles_vb.pdf)

> Ce didacticiel examine les étapes nécessaires à la configuration de l’infrastructure de rôles. Après cela, nous allons créer des pages web pour créer et supprimer des rôles.


## <a name="introduction"></a>Introduction

Dans le <a id="_msoanchor_1"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel, nous avons examiné à l’aide de l’autorisation d’URL pour empêcher certains utilisateurs un ensemble de pages et exploré déclarative et techniques de programmation pour régler les fonctionnalités d’une page ASP.NET en fonction de l’utilisateur visite. L’octroi d’autorisation pour accéder à la page ou des fonctionnalités sur une base par utilisateur, cependant, peut devenir un cauchemar de maintenance dans les scénarios où il existe plusieurs comptes d’utilisateur ou lorsque les privilèges des utilisateurs changent souvent. Lorsqu’un utilisateur parvient à ou perd l’autorisation pour effectuer une tâche particulière, l’administrateur doit mettre à jour les règles d’autorisation URL appropriées, le balisage déclaratif et le code.

Elle permet généralement de classer les utilisateurs en groupes ou *rôles* , puis appliquer des autorisations sur une base par rôle de rôle. Par exemple, la plupart des applications web ont un certain ensemble de pages ou des tâches qui sont réservés uniquement pour les utilisateurs administratifs. En utilisant les techniques apprises dans le *d’autorisation basée sur l’utilisateur* didacticiel, nous devons ajouter les règles d’autorisation URL appropriées, le balisage déclaratif et le code pour autoriser les comptes d’utilisateur spécifié effectuer des tâches d’administration. Mais si un administrateur a été ajouté ou si un administrateur existant est nécessaire pour que ses droits d’administration révoqués, il faut retourner et mettre à jour les fichiers de configuration et les pages web. Avec les rôles, toutefois, nous pouvons créer un rôle appelé Administrateurs et affecter ces utilisateurs approuvés au rôle Administrateurs. Ensuite, nous devez ajouter les règles d’autorisation URL appropriées, le balisage déclaratif et le code pour autoriser le rôle Administrateurs effectuer les tâches d’administration différents. Grâce à cette infrastructure en place, l’ajout de nouveaux administrateurs au site ou supprimant des types existants est aussi simple qu’incluant ou en supprimant l’utilisateur du rôle Administrateurs. Aucune configuration, balisage déclaratif ou les modifications du code ne sont nécessaires.

ASP.NET propose une infrastructure de rôles pour la définition des rôles et de les associer à des comptes d’utilisateur. Avec l’infrastructure de rôles nous pouvons créer et supprimer des rôles, ajouter ou supprimer des utilisateurs d’un rôle, déterminer l’ensemble des utilisateurs qui appartiennent à un rôle particulier et indiquer si un utilisateur appartient à un rôle particulier. Une fois que l’infrastructure de rôles a été configuré, nous pouvons limiter l’accès aux pages sur une base par rôle de rôle via des règles d’autorisation d’URL et afficher ou masquer des informations supplémentaires ou fonctionnalités sur une page basée sur les rôles de l’utilisateur actuellement connecté.

Ce didacticiel examine les étapes nécessaires à la configuration de l’infrastructure de rôles. Après cela, nous allons créer des pages web pour créer et supprimer des rôles. Dans le <a id="_msoanchor_2"> </a> [ *affectation de rôles aux utilisateurs* ](assigning-roles-to-users-vb.md) didacticiel, nous allons examiner comment ajouter et supprimer des utilisateurs dans des rôles. Puis, dans le <a id="_msoanchor_3"> </a> [ *d’autorisation basée sur le rôle* ](role-based-authorization-vb.md) didacticiel, nous allons voir comment limiter l’accès aux pages sur une base par rôle de rôle, ainsi que comment ajuster en fonction de la fonctionnalité de page rôle de l’utilisateur visite. C’est parti !

## <a name="step-1-adding-new-aspnet-pages"></a>Étape 1 : Ajout de nouvelles Pages ASP.NET

Dans ce didacticiel et les deux, nous étudierons diverses fonctions liées aux rôles et fonctionnalités. Nous devons une série de pages ASP.NET pour implémenter les rubriques examinées dans l’ensemble de ces didacticiels. Nous allons créer ces pages et mettre à jour le plan de site.

Commencez par créer un nouveau dossier dans le projet nommé `Roles`. Ensuite, ajoutez quatre pages ASP.NET pour le `Roles` dossier, lier chaque page avec le `Site.master` page maître. Nommez les pages :

- `ManageRoles.aspx`
- `UsersAndRoles.aspx`
- `CreateUserWizardWithRoles.aspx`
- `RoleBasedAuthorization.aspx`

À ce stade l’Explorateur de votre projet de solutions doit ressembler à l’écran : WordGame indiqué dans la Figure 1.


[![Quatre nouvelles Pages ont été ajoutés au dossier rôles](creating-and-managing-roles-vb/_static/image2.png)](creating-and-managing-roles-vb/_static/image1.png)

**Figure 1**: quatre nouvelles Pages ont été ajoutés à la `Roles` dossier ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image3.png))


Chaque page doit avoir à ce stade, les deux contrôles de contenu, une pour chaque ContentPlaceHolders de la page maître : `MainContent` et `LoginContent`.

[!code-aspx[Main](creating-and-managing-roles-vb/samples/sample1.aspx)]

N’oubliez pas que le `LoginContent` balisage de valeur par défaut du ContentPlaceHolder affiche un lien pour se connecter ou se déconnecter du site, en fonction de l’utilisateur authentifié. La présence de la `Content2` contenu contrôle dans la page ASP.NET, toutefois, substitue balisage par défaut de la page maître. Comme expliqué dans <a id="_msoanchor_4"> </a> [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-vb.md) didacticiel, en remplaçant le balisage par défaut est utile dans les pages où nous ne souhaitez pas afficher associées à la connexion Options de la colonne de gauche.

Pour ces quatre pages, toutefois, nous souhaitons afficher les marques de valeur par défaut de la page maître pour la `LoginContent` ContentPlaceHolder. Par conséquent, supprimez le balisage déclaratif pour la `Content2` contrôle de contenu. Après cela, chaque du balisage de la page quatre doit contenir qu’un contrôle de contenu.

Enfin, mettons à jour le plan de site (`Web.sitemap`) à inclure ces nouvelles pages web. Ajoutez le code XML suivant après le `<siteMapNode>` nous avons ajouté pour les didacticiels de l’appartenance.

[!code-xml[Main](creating-and-managing-roles-vb/samples/sample2.xml)]

Avec le plan de site mis à jour, visitez le site via un navigateur. Comme le montre la Figure 2, le volet de navigation gauche maintenant inclut des éléments pour les didacticiels de rôles.


[![Quatre nouvelles Pages ont été ajoutés au dossier rôles](creating-and-managing-roles-vb/_static/image5.png)](creating-and-managing-roles-vb/_static/image4.png)

**Figure 2**: quatre nouvelles Pages ont été ajoutés à la `Roles` dossier ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image6.png))


## <a name="step-2-specifying-and-configuring-the-roles-framework-provider"></a>Étape 2 : Spécification et la configuration du fournisseur d’infrastructure de rôles

Comme l’infrastructure d’appartenance, l’infrastructure de rôles est construit sur le modèle de fournisseur. Comme indiqué dans le <a id="_msoanchor_5"> </a> [ *principes élémentaires de sécurité et la prise en charge ASP.NET* ](../introduction/security-basics-and-asp-net-support-vb.md) (didacticiel), le .NET Framework est fourni avec trois fournisseurs de rôles intégrés : [ `AuthorizationStoreRoleProvider` ](https://msdn.microsoft.com/library/system.web.security.authorizationstoreroleprovider.aspx) , [ `WindowsTokenRoleProvider` ](https://msdn.microsoft.com/library/system.web.security.windowstokenroleprovider.aspx), et [ `SqlRoleProvider` ](https://msdn.microsoft.com/library/system.web.security.sqlroleprovider.aspx). Cette série de didacticiels se concentre sur la `SqlRoleProvider`, qui utilise une base de données Microsoft SQL Server comme magasin de rôle.

Dans les coulisses l’infrastructure de rôles et `SqlRoleProvider` Professionnel comme l’infrastructure de l’appartenance et `SqlMembershipProvider`. Le .NET Framework contient un `Roles` classe qui sert à l’API pour l’infrastructure de rôles. Le `Roles` classe a partagé des méthodes, telles que `CreateRole`, `DeleteRole`, `GetAllRoles`, `AddUserToRole`, `IsUserInRole`, et ainsi de suite. Lorsqu’une de ces méthodes est appelée, la `Roles` classe délègue l’appel au fournisseur configuré. Le `SqlRoleProvider` fonctionne avec les tables spécifiques au rôle (`aspnet_Roles` et `aspnet_UsersInRoles`) dans la réponse.

Pour pouvoir utiliser le `SqlRoleProvider` fournisseur dans notre application, nous devons spécifier ce que la base de données à utiliser comme magasin. Le `SqlRoleProvider` attend le magasin de rôle spécifié d’avoir certaines tables de base de données, les vues et les procédures stockées. Ces objets de base de données requis peuvent être ajoutés à l’aide de la [ `aspnet_regsql.exe` outil](https://msdn.microsoft.com/library/ms229862.aspx). À ce stade nous avons déjà une base de données avec le schéma nécessaire pour le `SqlRoleProvider`. Dans le <a id="_msoanchor_6"> </a> [ *création du schéma de l’appartenance dans SQL Server* ](../membership/creating-the-membership-schema-in-sql-server-vb.md) didacticiel, nous avons créé une base de données nommée `SecurityTutorials.mdf` et `aspnet_regsql.exe` pour ajouter l’application services incluant les objets de base de données requis par le `SqlRoleProvider`. Par conséquent, nous devons indiquer à l’infrastructure de rôles pour activer la prise en charge du rôle et d’utiliser le `SqlRoleProvider` avec la `SecurityTutorials.mdf` base de données en tant que magasin de rôles.

L’infrastructure de rôles est configuré via la `<roleManager>` élément dans l’application `Web.config` fichier. Par défaut, la prise en charge du rôle est désactivé. Pour l’activer, vous devez définir le [ `<roleManager>` ](https://msdn.microsoft.com/library/ms164660.aspx) l’élément `enabled` attribut `true` comme suit :

[!code-xml[Main](creating-and-managing-roles-vb/samples/sample3.xml)]

Par défaut, toutes les applications web ont un fournisseur de rôles nommé `AspNetSqlRoleProvider` de type `SqlRoleProvider`. Ce fournisseur par défaut est enregistré dans `machine.config` (situé dans `%WINDIR%\Microsoft.Net\Framework\v2.0.50727\CONFIG`) :

[!code-xml[Main](creating-and-managing-roles-vb/samples/sample4.xml)]

Du fournisseur `connectionStringName` attribut spécifie le magasin de rôles qui est utilisé. Le `AspNetSqlRoleProvider` fournisseur définit cet attribut `LocalSqlServer`, qui est également défini dans `machine.config` et des points, par défaut, une base de données SQL Server 2005 Express Edition dans la `App_Data` dossier nommé `aspnet.mdf`.

Par conséquent, si nous avons simplement permettre à l’infrastructure de rôles sans spécification de toutes les informations de fournisseur de notre application `Web.config` fichier, l’application utilise le fournisseur de rôles par défaut enregistré, `AspNetSqlRoleProvider`. Si le `~/App_Data/aspnet.mdf` base de données n’existe pas, le runtime ASP.NET crée automatiquement et ajoutez le schéma de services d’application. Toutefois, nous ne souhaitez pas utiliser le `aspnet.mdf` base de données ; au lieu de cela, nous souhaitons utiliser le `SecurityTutorials.mdf` que nous avons déjà créé et ajouté le schéma de services d’application de base de données. Cette modification peut être accomplie de deux manières :

- **Spécifiez une valeur pour le ***`LocalSqlServer`*** nom de chaîne de connexion dans ***`Web.config`***.** En remplaçant le `LocalSqlServer` valeur du nom de chaîne de connexion `Web.config`, nous pouvons utiliser le fournisseur de rôles par défaut enregistré (`AspNetSqlRoleProvider`) et qu’il fonctionne correctement avec le `SecurityTutorials.mdf` base de données. Pour plus d’informations sur cette technique, consultez [Scott Guthrie](https://weblogs.asp.net/scottgu/)du billet de blog, [configurer les Services d’Application ASP.NET 2.0 pour utiliser SQL Server 2000 ou SQL Server 2005](https://weblogs.asp.net/scottgu/archive/2005/08/25/423703.aspx).
- **Ajouter un nouveau fournisseur inscrit de type ***`SqlRoleProvider`*** et configurer ses ***`connectionStringName`*** paramètre pour pointer vers le ***`SecurityTutorials.mdf`*** base de données.** C’est l’approche recommandée et de l’utilisées dans le <a id="_msoanchor_7"> </a> [ *création du schéma de l’appartenance dans SQL Server* ](../membership/creating-the-membership-schema-in-sql-server-vb.md) didacticiel et il est l’approche, vous allez utiliser dans ce didacticiel également.

Ajoutez le balisage de configuration de rôles suivant à la `Web.config` fichier. Ce balisage enregistre un nouveau fournisseur nommé`SecurityTutorialsSqlRoleProvider.`

[!code-xml[Main](creating-and-managing-roles-vb/samples/sample5.xml)]

Le balisage ci-dessus définit les `SecurityTutorialsSqlRoleProvider` en tant que le fournisseur par défaut (via la `defaultProvider` d’attribut dans le `<roleManager>` élément). Il définit également la `SecurityTutorialsSqlRoleProvider`de `applicationName` à `SecurityTutorials`, qui est le même `applicationName` paramètre utilisé par le fournisseur d’appartenances (`SecurityTutorialsSqlMembershipProvider`). Tandis que les ne pas ici, le [ `<add>` élément](https://msdn.microsoft.com/library/ms164662.aspx) pour le `SqlRoleProvider` peut également contenir un `commandTimeout` attribut pour spécifier la durée du délai d’attente de la base de données, en secondes. La valeur par défaut est 30.

Ce balisage configuration en place, nous sommes prêts à démarrer à l’aide de la fonctionnalité de rôle au sein de notre application.

> [!NOTE]
> Le balisage de configuration ci-dessus illustre l’utilisation de la `<roleManager>` l’élément `enabled` et `defaultProvider` attributs. Il existe plusieurs autres attributs qui affectent comment le framework rôles associe des informations sur les rôles sur une base par utilisateur. Nous allons examiner ces paramètres dans le <a id="_msoanchor_8"> </a> [ *d’autorisation basée sur le rôle* ](role-based-authorization-vb.md) didacticiel.


## <a name="step-3-examining-the-roles-api"></a>Étape 3 : Examen des rôles de l’API

Les fonctionnalités de l’infrastructure de rôles sont exposées la [ `Roles` classe](https://msdn.microsoft.com/library/system.web.security.roles.aspx), qui contient des méthodes partagées treize pour effectuer des opérations en fonction du rôle. Lorsque nous intéresser à la création et la suppression de rôles dans les étapes 4 et 6, nous allons utiliser la [ `CreateRole` ](https://msdn.microsoft.com/library/system.web.security.roles.createrole.aspx) et [ `DeleteRole` ](https://msdn.microsoft.com/library/system.web.security.roles.deleterole.aspx) méthodes, ajouter ou supprimer un rôle à partir du système.

Pour obtenir une liste de tous les rôles dans le système, utilisez le [ `GetAllRoles` méthode](https://msdn.microsoft.com/library/system.web.security.roles.getallroles.aspx) (voir l’étape 5). Le [ `RoleExists` méthode](https://msdn.microsoft.com/library/system.web.security.roles.roleexists.aspx) retourne une valeur booléenne indiquant si un rôle spécifié existe.

Dans l’étape suivante du didacticiel, nous allons examiner comment associer des utilisateurs aux rôles. Le `Roles` de classe [ `AddUserToRole` ](https://msdn.microsoft.com/library/system.web.security.roles.addusertorole.aspx), [ `AddUserToRoles` ](https://msdn.microsoft.com/library/system.web.security.roles.addusertoroles.aspx), [ `AddUsersToRole` ](https://msdn.microsoft.com/library/system.web.security.roles.adduserstorole.aspx), et [ `AddUsersToRoles` ](https://msdn.microsoft.com/library/system.web.security.roles.adduserstoroles.aspx) méthodes d’ajoutent un ou plusieurs utilisateurs à un ou plusieurs rôles. Pour supprimer les utilisateurs des rôles, utilisez le [ `RemoveUserFromRole` ](https://msdn.microsoft.com/library/system.web.security.roles.removeuserfromrole.aspx), [ `RemoveUserFromRoles` ](https://msdn.microsoft.com/library/system.web.security.roles.removeuserfromroles.aspx), [ `RemoveUsersFromRole` ](https://msdn.microsoft.com/library/system.web.security.roles.removeusersfromrole.aspx), ou [ `RemoveUsersFromRoles` ](https://msdn.microsoft.com/library/system.web.security.roles.removeusersfromroles.aspx) méthodes.

Dans le <a id="_msoanchor_9"> </a> [ *d’autorisation basée sur le rôle* ](role-based-authorization-vb.md) didacticiel, nous allons examiner comment afficher ou masquer des fonctionnalités basées sur le rôle de l’utilisateur actuellement connecté par programme. Pour ce faire, nous pouvons utiliser la classe de rôle [ `FindUsersInRole` ](https://msdn.microsoft.com/library/system.web.security.roles.findusersinrole.aspx), [ `GetRolesForUser` ](https://msdn.microsoft.com/library/system.web.security.roles.getrolesforuser.aspx), [ `GetUsersInRole` ](https://msdn.microsoft.com/library/system.web.security.roles.getusersinrole.aspx), ou [ `IsUserInRole` ](https://msdn.microsoft.com/library/system.web.security.roles.isuserinrole.aspx) méthodes.

> [!NOTE]
> N’oubliez pas que dès que l’un de ces méthodes est appelée, la `Roles` classe délègue l’appel au fournisseur configuré. Dans notre cas, cela signifie que l’appel est envoyé à la `SqlRoleProvider`. Le `SqlRoleProvider` puis effectue l’opération de base de données appropriée en fonction de la méthode appelée. Par exemple, le code `Roles.CreateRole("Administrators")` entraîne la `SqlRoleProvider` l’exécution de la `aspnet_Roles_CreateRole` procédure stockée, qui insère un nouvel enregistrement dans le `aspnet_Roles` table nommée administrateurs.


Le reste de ce didacticiel présente à l’aide de la `Roles` de classe `CreateRole`, `GetAllRoles`, et `DeleteRole` méthodes pour gérer les rôles dans le système.

## <a name="step-4-creating-new-roles"></a>Étape 4 : Création de nouveaux rôles

Rôles offrent un moyen pour arbitrairement regrouper les utilisateurs, et plus souvent ce regroupement est utilisé pour un moyen plus pratique appliquer des règles d’autorisation. Pour pouvoir utiliser les rôles comme un mécanisme d’autorisation nous devons tout d’abord définir les rôles qui existent dans l’application. Malheureusement, ASP.NET n’inclut pas un contrôle CreateRoleWizard. Pour ajouter de nouveaux rôles, nous devons créer une interface utilisateur appropriée et appeler l’API de rôles nous-mêmes. La bonne nouvelle est qu’il s’agit très simple à réaliser.

> [!NOTE]
> Il n’existe aucun contrôle CreateRoleWizard Web, il est le [outil Administration de Site Web ASP.NET](https://msdn.microsoft.com/library/ms228053.aspx), qui est une application ASP.NET locale, conçue pour vous aider à afficher et gérer la configuration de votre application web. Toutefois, je ne suis pas un grand fan de l’outil Administration de Site Web ASP.NET pour deux raisons. Tout d’abord, il est un peu bogué et l’expérience utilisateur laisse beaucoup à désirer. En second lieu, l’outil d’Administration de Site Web ASP.NET est conçu pour fonctionner uniquement localement, ce qui signifie que vous devrez générer des pages web de gestion de votre propre rôle si vous avez besoin gérer à distance les rôles sur un site en direct. Ces deux raisons, ce didacticiel et la suivante seront concentrera sur la création du rôle des outils de gestion dans une page web au lieu d’utiliser l’outil d’Administration de Site Web ASP.NET.


Ouvrez le `ManageRoles.aspx` page dans le `Roles` dossier et ajouter une zone de texte et un contrôle bouton Web à la page. Définir le contrôle de zone de texte `ID` propriété `RoleName` et du bouton `ID` et `Text` propriétés `CreateRoleButton` et créer un rôle, respectivement. À ce stade, balisage déclaratif de votre page doit ressembler à ce qui suit :

[!code-aspx[Main](creating-and-managing-roles-vb/samples/sample6.aspx)]

Ensuite, double-cliquez sur le `CreateRoleButton` bouton contrôle dans le concepteur pour créer un `Click` Gestionnaire d’événements, puis ajoutez le code suivant :

[!code-vb[Main](creating-and-managing-roles-vb/samples/sample7.vb)]

Le code ci-dessus démarre en attribuant le nom de rôle ajustée dans le `RoleName` zone de texte pour le `newRoleName` variable. Ensuite, le `Roles` de classe `RoleExists` méthode est appelée pour déterminer si le rôle `newRoleName` existe déjà dans le système. Si le rôle n’existe pas, il est créé via un appel à la `CreateRole` (méthode). Si le `CreateRole` un nom de rôle existe déjà dans le système, est passé à la méthode un `ProviderException` exception est levée. C’est pourquoi le code vérifie d’abord pour vous assurer que le rôle n’existe pas déjà dans le système avant d’appeler `CreateRole`. Le `Click` conclusion de gestionnaire d’événements en supprimant le `RoleName` la zone de texte `Text` propriété.

> [!NOTE]
> Vous vous demandez peut-être ce qui se produira si l’utilisateur n’entre pas n’importe quelle valeur dans la `RoleName` zone de texte. Si la valeur passée dans le `CreateRole` méthode est `Nothing` ou une chaîne vide, une exception est levée. De même, si le nom de rôle contient une virgule, une exception est levée. Par conséquent, la page doit contenir des contrôles de validation pour vous assurer que l’utilisateur entre un rôle, et qu’il ne contient pas de virgules. J’ai laisser en guise d’exercice pour le lecteur.


Nous allons créer un rôle Administrateurs. Visitez le `ManageRoles.aspx` page via un navigateur, tapez les administrateurs dans la zone de texte (voir Figure 3), puis cliquez sur le bouton Créer un rôle.


[![Créer un rôle d’administrateurs](creating-and-managing-roles-vb/_static/image8.png)](creating-and-managing-roles-vb/_static/image7.png)

**Figure 3**: créer un rôle d’administrateurs ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image9.png))


Que se passe-t-il ? Une publication (postback) se produit, mais il n’existe aucun indice visuel qui le rôle a réellement été ajouté au système. Nous mettrons à jour cette page à l’étape 5 pour inclure une rétroaction visuelle. Pour l’instant, toutefois, vous pouvez vérifier que le rôle a été créé en accédant à la `SecurityTutorials.mdf` base de données et afficher les données à partir de la `aspnet_Roles` table. Comme le montre la Figure 4, le `aspnet_Roles` table contient un enregistrement pour les rôles d’administrateurs juste ajoutés.


[![La Table aspnet_Roles contient une ligne pour les administrateurs](creating-and-managing-roles-vb/_static/image11.png)](creating-and-managing-roles-vb/_static/image10.png)

**Figure 4**: le `aspnet_Roles` Table contient une ligne pour les administrateurs ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image12.png))


## <a name="step-5-displaying-the-roles-in-the-system"></a>Étape 5 : Afficher les rôles dans le système

Nous allons améliorer la `ManageRoles.aspx` page incluent la liste des rôles en cours dans le système. Pour ce faire, ajoutez un contrôle GridView à la page et définissez ses `ID` propriété `RoleList`. Ensuite, ajoutez une méthode à la classe code-behind de la page nommée `DisplayRolesInGrid` utilisant le code suivant :

[!code-vb[Main](creating-and-managing-roles-vb/samples/sample8.vb)]

Le `Roles` de classe `GetAllRoles` méthode retourne tous les rôles du système sous forme de tableau de chaînes. Ce tableau de chaînes est ensuite lié au GridView. Pour lier la liste des rôles pour le contrôle GridView lors du premier chargement de la page, nous devons appeler le `DisplayRolesInGrid` méthode à partir de la page `Page_Load` Gestionnaire d’événements. Le code suivant appelle cette méthode lorsque la page est visitée en premier, mais pas sur les publications ultérieures.

[!code-vb[Main](creating-and-managing-roles-vb/samples/sample9.vb)]

Avec ce code en place, visitez la page via un navigateur. Comme le montre la Figure 5, vous devez voir une grille avec une seule colonne intitulée élément. La grille contient une ligne pour le rôle Administrateurs que nous avons ajouté à l’étape 4.


[![Le contrôle GridView affiche les rôles dans une seule colonne](creating-and-managing-roles-vb/_static/image14.png)](creating-and-managing-roles-vb/_static/image13.png)

**Figure 5**: le contrôle GridView affiche les rôles dans une colonne unique ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image15.png))


Le contrôle GridView affiche une colonne d’isolés étiquetée élément car du GridView `AutoGenerateColumns` propriété est définie sur True (valeur par défaut), ce qui entraîne le contrôle GridView à créer automatiquement une colonne pour chaque propriété de son `DataSource`. Un tableau a une propriété unique qui représente les éléments du tableau, par conséquent, la colonne unique dans le GridView.

Lors de l’affichage des données avec un GridView, je préfère explicitement définir mon colonnes au lieu des générer implicitement par le contrôle GridView. En définissant explicitement les colonnes, il est beaucoup plus facile à mettre en forme les données, réorganiser les colonnes et effectuer d’autres tâches courantes. Par conséquent, nous allons mettre à jour la balise déclarative de GridView afin que ses colonnes sont explicitement définies.

Commencez par définir de GridView `AutoGenerateColumns` propriété sur False. Ensuite, ajoutez TemplateField à la grille, de définir son `HeaderText` propriété aux rôles et configurer ses `ItemTemplate` afin qu’il affiche le contenu du tableau. Pour ce faire, ajoutez un contrôle Web Label nommé `RoleNameLabel` à la `ItemTemplate` et lier sa `Text` propriété`Container.DataItem.`

Ces propriétés et le `ItemTemplate`du contenu peut être défini de façon déclarative ou par le biais champs boîte de dialogue du GridView et l’interface de modifier les modèles. Pour atteindre les champs de la boîte de dialogue, cliquez sur le lien Modifier les colonnes dans la balise active du GridView. Ensuite, désactivez la génération automatique de champs pour définir la `AutoGenerateColumns` propriété sur False et la définition de l’ajouter TemplateField au GridView, son `HeaderText` propriété au rôle. Pour définir le `ItemTemplate`du contenu, choisissez l’option Modifier les modèles à partir de la balise active du GridView. Faites glisser un contrôle Web Label dans le `ItemTemplate`, définissez son `ID` propriété `RoleNameLabel`et configurer les paramètres de liaison de données afin que ses `Text` propriété est liée à `Container.DataItem`.

Quel que soit l’approche que vous utilisez, balisage déclaratif du GridView résultant doit ressembler à ce qui suit lorsque vous avez terminé.

[!code-aspx[Main](creating-and-managing-roles-vb/samples/sample10.aspx)]

> [!NOTE]
> Le contenu du tableau est affiché à l’aide de la syntaxe de liaison de données `<%# Container.DataItem %>`. Obtenir une description détaillée de la raison pour laquelle cette syntaxe est utilisée lors de l’affichage du contenu d’un tableau lié au GridView est dépasse le cadre de ce didacticiel. Pour plus d’informations sur ce sujet, reportez-vous à [un tableau scalaire de liaison à un contrôle Web de données](http://aspnet.4guysfromrolla.com/articles/082504-1.aspx).


Actuellement, le `RoleList` GridView est lié uniquement à la liste des rôles lorsque la page est visitée en premier. Nous devons actualiser la grille chaque fois qu’un nouveau rôle est ajouté. Pour ce faire, mettez à jour le `CreateRoleButton` du bouton `Click` Gestionnaire d’événements afin qu’il appelle la `DisplayRolesInGrid` méthode si un nouveau rôle est créé.

[!code-vb[Main](creating-and-managing-roles-vb/samples/sample11.vb)]

Maintenant lorsque l’utilisateur ajoute un nouveau rôle le `RoleList` GridView montre le rôle juste ajoutés lors de la publication, en fournissant une rétroaction visuelle que le rôle a été correctement créé. Pour illustrer cela, visitez le `ManageRoles.aspx` page via un navigateur et ajouter un rôle appelé superviseurs. Lorsque vous cliquez sur le bouton Créer un rôle, résulte d’une publication (postback) et la grille met à jour pour inclure les administrateurs, ainsi que le nouveau rôle, par exemple.


[![Le rôle de superviseurs a été ajouté](creating-and-managing-roles-vb/_static/image17.png)](creating-and-managing-roles-vb/_static/image16.png)

**Figure 6**: rôle superviseurs a été ajouté ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image18.png))


## <a name="step-6-deleting-roles"></a>Étape 6 : Suppression de rôles

À ce stade un utilisateur peut créer un nouveau rôle et afficher tous les rôles existants à partir de la `ManageRoles.aspx` page. Nous allons permettre aux utilisateurs de supprimer des rôles. Le `Roles.DeleteRole` méthode possède deux surcharges :

- [`DeleteRole(roleName)`](https://msdn.microsoft.com/library/ek4sywc0.aspx)-Supprime le rôle *roleName*. Une exception est levée si le rôle contient un ou plusieurs membres.
- [`DeleteRole(roleName, throwOnPopulatedRole)`](https://msdn.microsoft.com/library/38h6wf59.aspx)-Supprime le rôle *roleName*. Si *throwOnPopulateRole* est `True`, une exception est levée si le rôle contient un ou plusieurs membres. Si *throwOnPopulateRole* est `False`, le rôle est supprimée si elle contient des membres ou non. En interne, le `DeleteRole(roleName)` les appels de méthode `DeleteRole(roleName, True)`.

Le `DeleteRole` méthode lève également une exception si *roleName* est `Nothing` ou une chaîne vide ou si *roleName* contient une virgule. Si *roleName* n’existe pas dans le système, `DeleteRole` échoue en mode silencieux, sans lever d’exception.

Nous allons augmenter le contrôle GridView dans `ManageRoles.aspx` à inclure un bouton Supprimer qui, lorsque vous cliquez dessus, supprime le rôle sélectionné. Commencez par ajouter un bouton Supprimer pour le contrôle GridView en accédant à la boîte de dialogue champs et ajout d’un bouton Supprimer, ce qui se trouve sous l’option CommandField. Rendre la suppression de bouton de la colonne de gauche et définissez son `DeleteText` propriété à supprimer un rôle.


[![Ajouter un bouton Supprimer pour le contrôle RoleList GridView](creating-and-managing-roles-vb/_static/image20.png)](creating-and-managing-roles-vb/_static/image19.png)

**Figure 7**: ajouter un bouton Supprimer pour le `RoleList` GridView ([cliquez pour afficher l’image en taille réelle](creating-and-managing-roles-vb/_static/image21.png))


Après avoir ajouté le bouton de suppression, la balise déclarative du contrôle GridView, ses doit ressembler à ce qui suit :

[!code-aspx[Main](creating-and-managing-roles-vb/samples/sample12.aspx)]

Ensuite, créez un gestionnaire d’événements pour le contrôle du GridView `RowDeleting` événement. Il s’agit de l’événement est déclenché lors de la publication lorsque l’utilisateur clique sur le bouton Supprimer le rôle. Ajoutez le code suivant au gestionnaire d’événements.

[!code-vb[Main](creating-and-managing-roles-vb/samples/sample13.vb)]

Le code commence par référencer par programme le `RoleNameLabel` contrôle dans la ligne dont bouton Supprimer un rôle Web. Le `Roles.DeleteRole` méthode est ensuite appelée, en passant le `Text` de la `RoleNameLabel` et `False`, ce qui supprimer le rôle indépendamment du fait que n’importe quel utilisateur n’est associé au rôle. Enfin, le `RoleList` GridView est actualisé afin que le rôle juste en cours de suppression n’apparaît plus dans la grille.

> [!NOTE]
> Le bouton Supprimer le rôle ne nécessite pas de confirmation de l’utilisateur avant de supprimer le rôle quelconque. Une des méthodes plus simples pour confirmer une action est via une boîte de dialogue de confirmation du côté client. Pour plus d’informations sur cette technique, consultez [Ajout côté Client Confirmation lors de la suppression de](https://asp.net/learn/data-access/tutorial-42-vb.aspx).


## <a name="summary"></a>Récapitulatif

De nombreuses applications web ont certaines règles d’autorisation ou des fonctionnalités au niveau de la page qui sont uniquement disponible pour certaines catégories d’utilisateurs. Par exemple, il peut être un ensemble de pages web accessible uniquement aux administrateurs. Souvent au lieu de la définition de ces règles d’autorisation sur une base par utilisateur, il est plus pratique de définir les règles basées sur un rôle. Au lieu d’autoriser explicitement les utilisateurs Scott et Jisun pour accéder aux pages web d’administration, une approche plus facile à gérer est d’autoriser les membres du rôle Administrateurs à accéder à ces pages, puis pour dénoter Scott et Jisun en tant qu’utilisateurs appartenant à la Rôle des administrateurs.

L’infrastructure de rôles rend facile de créer et gérer des rôles. Dans ce didacticiel, nous avons examiné comment configurer l’infrastructure de rôles pour utiliser le `SqlRoleProvider`, qui utilise une base de données Microsoft SQL Server comme magasin de rôle. Nous avons créé également une page web qui répertorie les rôles existants dans le système et autorise de nouveaux rôles doit être créé et existants à supprimer. Dans les didacticiels suivants, nous verrons comment affecter des utilisateurs aux rôles et comment appliquer l’autorisation par rôle.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Examen de ASP.NET de 2.0 l’appartenance, rôles et profil](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Comment : Utiliser le Gestionnaire de rôles dans ASP.NET 2.0](https://msdn.microsoft.com/library/ms998314.aspx)
- [Fournisseurs de rôles](https://msdn.microsoft.com/library/aa478950.aspx)
- [Restauration de votre propre outil d’Administration de site Web](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx)
- [Documentation technique pour le `<roleManager>` élément](https://msdn.microsoft.com/library/ms164660.aspx)
- [À l’aide de l’appartenance et les API de gestionnaire de rôle](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/security/membership.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel incluent Alicja Maziarz, Suchi Banerjee et Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](role-based-authorization-cs.md)
[Suivant](assigning-roles-to-users-vb.md)
