---
uid: web-forms/overview/older-versions-security/roles/assigning-roles-to-users-cs
title: "Attribution de rôles aux utilisateurs (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons créer deux pages ASP.NET pour faciliter la gestion de ce que les utilisateurs appartiennent à quels rôles. La première page inclut des fonctionnalités de voir ce qui..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/24/2008
ms.topic: article
ms.assetid: d522639a-5aca-421e-9a76-d73f95607f57
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/roles/assigning-roles-to-users-cs
msc.type: authoredcontent
ms.openlocfilehash: 15d2b427e6fccfc82eab535200ba6878ab41b72e
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="assigning-roles-to-users-c"></a>Attribution de rôles aux utilisateurs (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/CS.10.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/0/3/6032582f-360d-4739-b935-38721fdb86ea/aspnet_tutorial10_AssigningRoles_cs.pdf)

> Dans ce didacticiel, nous allons créer deux pages ASP.NET pour faciliter la gestion de ce que les utilisateurs appartiennent à quels rôles. La première page inclut des fonctionnalités pour voir ce que les utilisateurs appartiennent à un rôle donné, les rôles qu’un utilisateur particulier appartient et la capacité d’affecter ou supprimer un utilisateur particulier à partir d’un rôle particulier. Dans la deuxième page nous sera augmenter le contrôle CreateUserWizard afin qu’il inclue une étape permettant de spécifier à quels rôles appartient l’utilisateur nouvellement créé. Cela est utile dans les scénarios où un administrateur est en mesure de créer de nouveaux comptes d’utilisateur.


## <a name="introduction"></a>Introduction

Le <a id="_msoanchor_1"> </a> [didacticiel précédent](creating-and-managing-roles-cs.md) examiné l’infrastructure de rôles et les `SqlRoleProvider`; nous avons vu comment utiliser le `Roles` classe à créer, récupérer et supprimer des rôles. En plus de la création et la suppression de rôles, nous devons pouvoir affecter ou supprimer des utilisateurs d’un rôle. Malheureusement, ASP.NET n’est pas livré avec tous les contrôles Web pour la gestion de ce que les utilisateurs appartiennent à quels rôles. Au lieu de cela, nous devons créer notre propre pages ASP.NET pour gérer ces associations. La bonne nouvelle est que l’ajout et suppression d’utilisateurs aux rôles est très simple. La `Roles` classe contient un nombre de méthodes d’ajout d’un ou plusieurs utilisateurs à un ou plusieurs rôles.

Dans ce didacticiel, nous allons créer deux pages ASP.NET pour faciliter la gestion de ce que les utilisateurs appartiennent à quels rôles. La première page inclut des fonctionnalités pour voir ce que les utilisateurs appartiennent à un rôle donné, les rôles qu’un utilisateur particulier appartient et la capacité d’affecter ou supprimer un utilisateur particulier à partir d’un rôle particulier. Dans la deuxième page nous sera augmenter le contrôle CreateUserWizard afin qu’il inclue une étape permettant de spécifier à quels rôles appartient l’utilisateur nouvellement créé. Cela est utile dans les scénarios où un administrateur est en mesure de créer de nouveaux comptes d’utilisateur.

C’est parti !

## <a name="listing-what-users-belong-to-what-roles"></a>Liste de ce que les utilisateurs appartiennent à quels rôles

La première chose pour ce didacticiel est de créer une page web à partir de laquelle les utilisateurs puissent être affectés aux rôles. Avant que nous nous préoccuper comment affecter des utilisateurs aux rôles, nous allons d’abord vous concentrer sur la façon de déterminer ce que les utilisateurs appartiennent à quels rôles. Il existe deux manières d’afficher ces informations : « par rôle » ou « par utilisateur. » Nous pouvons autoriser le visiteur à sélectionner un rôle et les afficher tous les utilisateurs qui appartiennent au rôle (l’affichage « par rôle »), ou nous avons invite le visiteur pour sélectionner un utilisateur et de les afficher les rôles attribués à cet utilisateur (affichage « par utilisateur »).

La vue « par rôle » est utile dans les cas où le visiteur souhaite connaître l’ensemble des utilisateurs qui appartiennent à un rôle particulier ; la vue « par utilisateur » est idéale lorsque le visiteur doit connaître l’ou les rôles d’un utilisateur particulier. Nous allons inclure dans notre page « par un rôle » et « par utilisateur » interfaces.

Nous allons commencer par la création de l’interface « par utilisateur ». Cette interface se compose d’une liste déroulante et d’une liste de cases à cocher. La liste déroulante sera remplie avec l’ensemble des utilisateurs dans le système ; les cases à cocher énumère les rôles. Sélection d’un utilisateur dans la liste déroulante vérifie ces rôles de que l’utilisateur appartient. La personne visitant la page, puis vérifiez ou désactivez les cases à cocher pour ajouter ou supprimer l’utilisateur sélectionné à partir de rôles correspondants.

> [!NOTE]
> À l’aide d’une liste à la liste déroulante les comptes d’utilisateur n’est pas un choix idéal pour les sites Web où il peut y avoir des centaines de comptes d’utilisateur. Une liste déroulante est conçue pour autoriser un utilisateur à sélectionner un élément à partir d’une liste relativement courte des options. Il peut devenir complexe à mesure que le nombre d’éléments de liste augmente. Si vous créez un site Web qui auront un grand nombre de comptes d’utilisateur, vous souhaiterez à l’aide d’une autre interface utilisateur, telles que les un GridView paginable ou une interface filtrable qui répertorie les invites pour indiquer une lettre, puis uniquement Affiche les utilisateurs dont nom d’utilisateur commence par la lettre sélectionnée.


## <a name="step-1-building-the-by-user-user-interface"></a>Étape 1 : Création de l’Interface utilisateur « Par utilisateur »

Ouvrez le `UsersAndRoles.aspx` page. En haut de la page, ajoutez un contrôle Web Label nommé `ActionStatus` et effacer son `Text` propriété. Nous utiliserons cette étiquette pour fournir des commentaires sur les actions effectuées, affichage des messages comme, « Tito de l’utilisateur a été ajouté au rôle Administrateurs » ou « Jisun de l’utilisateur a été supprimé du rôle superviseurs. » Afin de rendre ces messages apparaissent, définie l’étiquette `CssClass` propriété « Important ».

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample1.aspx)]

Ensuite, ajoutez la définition de classe CSS suivante pour le `Styles.css` feuille de style :

[!code-css[Main](assigning-roles-to-users-cs/samples/sample2.css)]

Cette définition CSS indique au navigateur pour afficher l’étiquette à l’aide d’une grande police rouge. La figure 1 illustre cet effet par le biais du concepteur Visual Studio.


[![La propriété l’étiquette CssClass entraîne une grande police rouge](assigning-roles-to-users-cs/_static/image2.png)](assigning-roles-to-users-cs/_static/image1.png)

**Figure 1**: l’étiquette de la `CssClass` propriété entraîne une grande, la police de rouge ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image3.png))


Ensuite, ajoutez une liste déroulante à la page, affectez à sa `ID` propriété `UserList`et définissez son `AutoPostBack` True à la propriété. Nous utiliserons cette DropDownList pour répertorier tous les utilisateurs dans le système. Cette DropDownList sera lié à une collection d’objets de MembershipUser. Étant donné que nous souhaitons DropDownList pour afficher la propriété de nom d’utilisateur de l’objet MembershipUser (et l’utiliser en tant que la valeur des éléments de liste), définir de DropDownList `DataTextField` et `DataValueField` propriétés « UserName ».

Sous DropDownList, ajoutez un répéteur nommé `UsersRoleList`. Cette répétition répertorie tous les rôles dans le système comme une série de cases à cocher. Définir de répéteur `ItemTemplate` à l’aide de la balise déclarative suivante :

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample3.aspx)]

Le `ItemTemplate` balisage inclut un contrôle de case à cocher Web unique nommé `RoleCheckBox`. La case à cocher `AutoPostBack` est définie sur True et la `Text` propriété est liée à `Container.DataItem`. La raison de la syntaxe de liaison de données est simplement `Container.DataItem` est parce que le framework rôles retourne la liste des noms de rôle en tant que tableau de chaînes, et il s’agit de ce tableau de chaînes qui nous sera contraignant pour répéteur. Obtenir une description détaillée de la raison pour laquelle cette syntaxe est utilisée pour afficher le contenu d’un tableau lié à un contrôle Web de données est dépasse le cadre de ce didacticiel. Pour plus d’informations sur ce sujet, reportez-vous à [un tableau scalaire de liaison à un contrôle Web de données](http://aspnet.4guysfromrolla.com/articles/082504-1.aspx).

À ce stade balisage déclaratif de votre interface de « par utilisateur » doit ressembler à ce qui suit :

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample4.aspx)]

Nous sommes maintenant prêts à écrire le code pour lier l’ensemble des comptes d’utilisateur à DropDownList et l’ensemble de rôles pour le répéteur. Dans la classe code-behind de la page, ajoutez une méthode nommée `BindUsersToUserList` et une autre nommée `BindRolesList`, utilisant le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample5.cs)]

Le `BindUsersToUserList` méthode récupère tous les comptes d’utilisateur dans le système via la [ `Membership.GetAllUsers` méthode](https://msdn.microsoft.com/library/dy8swhya.aspx). Cette opération retourne un [ `MembershipUserCollection` objet](https://msdn.microsoft.com/library/system.web.security.membershipusercollection.aspx), qui est une collection de [ `MembershipUser` instances](https://msdn.microsoft.com/library/system.web.security.membershipuser.aspx). Cette collection est ensuite liée à la `UserList` DropDownList. Le `MembershipUser` cette composition de la collection contient un certain nombre de propriétés, telles que des instances `UserName`, `Email`, `CreationDate`, et `IsOnline`. Afin d’indiquer à DropDownList pour afficher la valeur de la `UserName` propriété, vérifiez que le `UserList` de DropDownList `DataTextField` et `DataValueField` propriétés ont été définies pour « UserName ».

> [!NOTE]
> Le `Membership.GetAllUsers` méthode possède deux surcharges : une qui n’accepte aucun paramètre d’entrée et retourne tous les utilisateurs et une qui accepte les valeurs entières pour les index et la taille de la page et retourne uniquement le sous-ensemble spécifié d’utilisateurs. Lorsqu’il existe de grandes quantités de comptes d’utilisateur s’affichés dans un élément d’interface utilisateur paginable, la deuxième surcharge permet de parcourir plus efficacement les utilisateurs, car elle retourne uniquement le sous-ensemble précis de comptes d’utilisateur plutôt que tous les.


Le `BindRolesToList` méthode démarre en appelant le `Roles` la classe [ `GetAllRoles` méthode](https://msdn.microsoft.com/library/system.web.security.roles.getallroles.aspx), qui retourne un tableau de chaînes contenant les rôles dans le système. Ce tableau de chaînes est ensuite lié au répéteur.

Enfin, nous devons appeler ces deux méthodes lorsque la page est chargée. Ajoutez le code suivant au gestionnaire d'événements `Page_Load` :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample6.cs)]

Avec ce code en place, prenez un moment pour consulter la page via un navigateur ; votre écran doit ressembler à la Figure 2. Tous les comptes d’utilisateur sont remplis dans la liste déroulante et, en dessous, chaque rôle apparaît sous la forme d’une case à cocher. Étant donné que nous avons défini le `AutoPostBack` propriétés des DropDownList et des cases à cocher pour la valeur est True, la modification de l’utilisateur sélectionné ou en activant ou en désactivant un rôle provoque une publication (postback). Aucune action n’est effectuée, toutefois, étant donné que nous avons encore écrire du code pour gérer ces actions. Nous allons aborder ces tâches dans les deux sections suivantes.


[![La Page affiche les utilisateurs et rôles](assigning-roles-to-users-cs/_static/image5.png)](assigning-roles-to-users-cs/_static/image4.png)

**Figure 2**: la Page affiche les utilisateurs et les rôles ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image6.png))


### <a name="checking-the-roles-the-selected-user-belongs-to"></a>Vérifier les rôles de l’utilisateur sélectionné appartient à

Lorsque la page est chargée, ou chaque fois que le visiteur sélectionne un nouvel utilisateur dans la liste déroulante, nous devons mettre à jour le `UsersRoleList`de cases à cocher afin qu’une case à cocher rôle donné est activée uniquement si l’utilisateur sélectionné appartient à ce rôle. Pour ce faire, créez une méthode nommée `CheckRolesForSelectedUser` avec le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample7.cs)]

Le code ci-dessus commence par déterminer qui est l’utilisateur sélectionné. Il utilise ensuite la classe de rôles [ `GetRolesForUser(userName)` méthode](https://msdn.microsoft.com/library/system.web.security.roles.getrolesforuser.aspx) pour renvoyer l’ensemble de l’utilisateur spécifié des rôles en tant que tableau de chaînes. Ensuite, les éléments de répéteur sont énumérées et chaque élément `RoleCheckBox` case à cocher est référencé par programmation. La case à cocher est activée uniquement si le rôle qu’il correspond à est contenu dans le `selectedUsersRoles` tableau de chaînes.

> [!NOTE]
> Le `selectedUserRoles.Contains<string>(...)` syntaxe compilera pas si vous n’utilisez pas ASP.NET version 2.0. Le `Contains<string>` méthode fait partie de la [bibliothèque LINQ](http://en.wikipedia.org/wiki/Language_Integrated_Query), qui est une nouveauté dans ASP.NET 3.5. Si vous utilisez toujours la version 2.0 d’ASP.NET, utilisez le [ `Array.IndexOf<string>` méthode](https://msdn.microsoft.com/library/eha9t187.aspx) à la place.


Le `CheckRolesForSelectedUser` méthode doit être appelée dans deux cas : lorsque la page est chargée et chaque fois que le `UserList` de DropDownList sélectionné est modifié. Par conséquent, appelez cette méthode à partir de la `Page_Load` Gestionnaire d’événements (après les appels à `BindUsersToUserList` et `BindRolesToList`). En outre, créer un gestionnaire d’événements pour de DropDownList `SelectedIndexChanged` événement et d’appeler cette méthode à partir de là.

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample8.cs)]

Avec ce code en place, vous pouvez tester la page via le navigateur. Toutefois, étant donné que le `UsersAndRoles.aspx` page ne dispose pas actuellement la possibilité d’affecter des utilisateurs aux rôles, aucun utilisateur n’a de rôles. Nous allons créer l’interface pour affecter les utilisateurs aux rôles dans un instant, afin de prendre mon mot ce code fonctionne et vérifiez qu’il le fait plus tard, ou vous pouvez ajouter manuellement des utilisateurs aux rôles en insérant des enregistrements dans la `aspnet_UsersInRoles` table afin de tester cette functi onality maintenant.

### <a name="assigning-and-removing-users-from-roles"></a>Affectation et suppression des rôles d’utilisateurs

Lorsque le visiteur ou une case à cocher dans la `UsersRoleList` répéteur, nous devons ajouter ou supprimer de l’utilisateur sélectionné dans le rôle correspondant. La case à cocher `AutoPostBack` propriété est actuellement définie sur True, ce qui provoque une publication (postback) chaque fois qu’une case à cocher dans le répéteur est activée ou désactivée. En résumé, nous devons créer un gestionnaire d’événements pour la case à cocher `CheckChanged` événement. Étant donné que la case à cocher est dans un contrôle du répéteur, nous devons ajouter manuellement les éléments de gestionnaire d’événements. Commencez par ajouter le Gestionnaire d’événements à la classe code-behind comme un `protected` (méthode), comme suit :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample9.cs)]

Va renvoyer pour écrire le code pour ce gestionnaire d’événements dans un instant. Mais tout d’abord nous allons effectuer les mécanismes de gestion d’événements. À partir de la case à cocher au sein du répéteur `ItemTemplate`, ajoutez `OnCheckedChanged="RoleCheckBox_CheckChanged"`. Cette syntaxe relie le `RoleCheckBox_CheckChanged` Gestionnaire d’événements à la `RoleCheckBox`de `CheckedChanged` événements.

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample10.aspx)]

La dernière tâche consiste à effectuer la `RoleCheckBox_CheckChanged` Gestionnaire d’événements. Nous avons besoin démarrer en référençant le contrôle de case à cocher qui a déclenché l’événement, car cette instance de la case à cocher indique à quel rôle a été activée ou désactivée via son `Text` et `Checked` propriétés. À l’aide de ces informations ainsi que le nom d’utilisateur de l’utilisateur sélectionné, nous ajouter ou supprimer l’utilisateur du rôle via le `Roles` de classe [ `AddUserToRole` ](https://msdn.microsoft.com/library/system.web.security.roles.addusertorole.aspx) ou [ `RemoveUserFromRole` méthode](https://msdn.microsoft.com/library/system.web.security.roles.removeuserfromrole.aspx).

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample11.cs)]

Le code ci-dessus commence par programme faisant référence à la case à cocher qui a déclenché l’événement, qui est disponible via le `sender` paramètre d’entrée. Si la case à cocher est activée, l’utilisateur sélectionné est ajouté au rôle spécifié, sinon qu'ils sont supprimés du rôle. Dans les deux cas, le `ActionStatus` étiquette affiche un message de synthèse de l’action qui vient d’être exécutée.

Prenez un moment pour tester cette page via un navigateur. Sélectionnez utilisateur Tito, puis ajoutez Tito aux rôles à la fois les administrateurs et les superviseurs.


[![Tito a été ajouté pour les administrateurs et les rôles de superviseurs](assigning-roles-to-users-cs/_static/image8.png)](assigning-roles-to-users-cs/_static/image7.png)

**Figure 3**: Tito a été ajouté pour les administrateurs et les rôles de superviseurs ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image9.png))


Ensuite, sélectionnez utilisateur Bruce dans la liste déroulante. Il existe une publication (postback) et les cases à cocher de répéteur sont mis à jour le `CheckRolesForSelectedUser`. Étant donné que Bruce n’appartient pas encore à tous les rôles, les deux cases à cocher sont désactivées. Ensuite, ajoutez Bruce au rôle superviseurs.


[![Bruce a été ajouté au rôle superviseurs](assigning-roles-to-users-cs/_static/image11.png)](assigning-roles-to-users-cs/_static/image10.png)

**Figure 4**: Bruce a été ajouté au rôle superviseurs ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image12.png))


Pour vérifier les fonctionnalités de la `CheckRolesForSelectedUser` (méthode), sélectionnez un utilisateur autre que Tito ou Bruce. Notez la façon dont les cases à cocher sont automatiquement désactivés, ce qui indique qu’ils n’appartiennent pas à tous les rôles. Revenir à Tito. Les cases à cocher à la fois les administrateurs et les superviseurs doit être vérifiée.

## <a name="step-2-building-the-by-roles-user-interface"></a>Étape 2 : Création de l’Interface utilisateur « Par les rôles »

À ce stade, nous l’interface « par « utilisateurs de s’est terminé et êtes prêts à commencer la résolution de l’interface « par les rôles ». L’interface « par les rôles » invite l’utilisateur à sélectionner un rôle dans la liste déroulante, puis affiche l’ensemble des utilisateurs qui appartiennent à ce rôle dans un GridView.

Ajoutez un autre contrôle DropDownList pour le `UsersAndRoles.aspx` page. Placer celle-ci sous le contrôle du répéteur, nommez-le `RoleList`et définissez son `AutoPostBack` True à la propriété. En dessous, ajoutez un GridView et nommez-le `RolesUserList`. Ce contrôle GridView répertorie les utilisateurs qui appartiennent au rôle sélectionné. Définir le contrôle de GridView `AutoGenerateColumns` propriété sur False, ajouter TemplateField à la grille `Columns` collection et définissez son `HeaderText` propriété aux « Utilisateurs ». Définir le TemplateField `ItemTemplate` afin qu’il affiche la valeur de l’expression de liaison de données `Container.DataItem` dans les `Text` propriété d’une étiquette nommée `UserNameLabel`.

Après avoir ajouté et configuré le GridView, balisage déclaratif de votre interface de « par un rôle » doit ressembler à ce qui suit :

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample12.aspx)]

Nous avons besoin remplir le `RoleList` DropDownList avec l’ensemble de rôles dans le système. Pour ce faire, mettez à jour le `BindRolesToList` (méthode), afin que celle-ci lie le tableau de chaînes retourné par la `Roles.GetAllRoles` méthode à la `RolesList` DropDownList (ainsi que le `UsersRoleList` répéteur).

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample13.cs)]

Les deux dernières lignes dans le `BindRolesToList` méthode ont été ajoutés pour lier l’ensemble de rôles à le `RoleList` contrôle DropDownList. La figure 5 illustre le résultat final, lorsqu’ils sont affichés via un navigateur : une liste déroulante remplie avec les rôles du système.


[![Les rôles sont affichés dans RoleList DropDownList](assigning-roles-to-users-cs/_static/image14.png)](assigning-roles-to-users-cs/_static/image13.png)

**Figure 5**: les rôles s’affichent dans le `RoleList` DropDownList ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image15.png))


### <a name="displaying-the-users-that-belong-to-the-selected-role"></a>Afficher les utilisateurs qui appartiennent au rôle sélectionné

Lorsque la page est chargée, ou lorsqu’un nouveau rôle est sélectionné à partir de la `RoleList` DropDownList, nous avons besoin afficher la liste des utilisateurs qui appartiennent à ce rôle dans le GridView. Créez une méthode nommée `DisplayUsersBelongingToRole` utilisant le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample14.cs)]

Cette méthode démarre en obtenant le rôle sélectionné à partir de la `RoleList` DropDownList. Il utilise ensuite la [ `Roles.GetUsersInRole(roleName)` méthode](https://msdn.microsoft.com/library/system.web.security.roles.getusersinrole.aspx) pour récupérer un tableau de chaînes des noms d’utilisateur des utilisateurs qui appartiennent à ce rôle. Ce tableau est ensuite lié à la `RolesUserList` GridView.

Cette méthode doit être appelée dans deux cas : lorsque la page est initialement chargée et lorsque le rôle sélectionné dans la `RoleList` DropDownList modifications. Par conséquent, mettre à jour le `Page_Load` Gestionnaire d’événements afin que cette méthode est appelée après l’appel à `CheckRolesForSelectedUser`. Ensuite, créez un gestionnaire d’événements pour le `RoleList`de `SelectedIndexChanged` événement, appelez cette méthode à partir de là, trop.

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample15.cs)]

Ce code en place, le `RolesUserList` GridView doit afficher les utilisateurs qui appartiennent au rôle sélectionné. Comme le montre la Figure 6, le rôle de l’exemple se compose de deux membres : Bruce et Tito.


[![Le contrôle GridView répertorie les utilisateurs qui appartiennent au rôle sélectionné](assigning-roles-to-users-cs/_static/image17.png)](assigning-roles-to-users-cs/_static/image16.png)

**Figure 6**: le contrôle GridView répertorie les utilisateurs qu’appartiennent les au rôle sélectionné ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image18.png))


### <a name="removing-users-from-the-selected-role"></a>Suppression d’utilisateurs dans le rôle sélectionné

Nous allons améliorer la `RolesUserList` GridView afin qu’elle comporte une colonne de « supprimer » de boutons. En cliquant sur le bouton « Supprimer » pour un utilisateur particulier sera les supprimer de ce rôle.

Commencez par ajouter un champ de bouton de suppression pour le contrôle GridView. Rendre ce champ apparaît en tant que gauche plus archivée et modifier son `DeleteText` propriété à partir de « Supprimer » (la valeur par défaut) à « Supprimer ».


[![Ajoutez le](assigning-roles-to-users-cs/_static/image20.png)](assigning-roles-to-users-cs/_static/image19.png)

**Figure 7**: ajouter le bouton « Supprimer » pour le contrôle GridView ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image21.png))


Lorsque vous cliquez sur le bouton « Supprimer » une publication (postback) s’ensuit et du GridView `RowDeleting` événement est déclenché. Nous devons créer un gestionnaire d’événements pour cet événement et d’écrire du code qui supprime l’utilisateur au rôle sélectionné. Créer le Gestionnaire d’événements et ajoutez le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample16.cs)]

Le code commence par déterminer le nom du rôle sélectionné. Puis par programme références le `UserNameLabel` contrôle à partir de la ligne dont bouton « Supprimer » a été utilisé afin de déterminer le nom d’utilisateur de l’utilisateur à supprimer. L’utilisateur est supprimé du rôle via un appel à la `Roles.RemoveUserFromRole` (méthode). Le `RolesUserList` GridView puis est actualisé, et un message s’affiche la `ActionStatus` contrôle d’étiquette.

> [!NOTE]
> Le bouton « Supprimer » ne nécessite pas de confirmation de l’utilisateur avant de supprimer le rôle de l’utilisateur quelconque. Je vous invite à ajouter un certain degré de confirmation de l’utilisateur. Une des méthodes plus simples pour confirmer une action est via une boîte de dialogue de confirmation du côté client. Pour plus d’informations sur cette technique, consultez [Ajout côté Client Confirmation lors de la suppression de](https://asp.net/learn/data-access/tutorial-42-cs.aspx).


Figure 8 illustre la page une fois que l’utilisateur Tito a été supprimé du groupe par exemple.


[![Hélas, Tito n’est plus un superviseur](assigning-roles-to-users-cs/_static/image23.png)](assigning-roles-to-users-cs/_static/image22.png)

**Figure 8**: Hélas, Tito n’est plus un superviseur ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image24.png))


### <a name="adding-new-users-to-the-selected-role"></a>Ajout de nouveaux utilisateurs au rôle sélectionné

En même temps que la suppression d’utilisateurs dans le rôle sélectionné, le visiteur à cette page doit également être en mesure d’ajouter un utilisateur au rôle sélectionné. La meilleure interface pour ajouter un utilisateur au rôle sélectionné dépend du nombre de comptes d’utilisateur de que vous prévoyez. Si votre site Web hébergera seulement quelques comptes d’utilisateur de dizaines ou moins, vous pouvez utiliser une liste déroulante ici. S’il peut y avoir des milliers de comptes d’utilisateur, vous souhaitez inclure une interface utilisateur qui autorise le visiteur pour parcourir les comptes, la recherche d’un compte spécifique, ou de filtrer les comptes d’utilisateur d’une autre façon.

Pour cette page permet d’utiliser une interface très simple qui fonctionne indépendamment du nombre de comptes d’utilisateur dans le système. À savoir, nous allons utiliser une zone de texte, en demandant le visiteur à taper le nom d’utilisateur de l’utilisateur que vous souhaitez ajouter au rôle sélectionné. Si aucun utilisateur portant ce nom existe, ou si l’utilisateur est déjà membre du rôle, nous affichons un message dans `ActionStatus` étiquette. Mais si l’utilisateur existe et n’est pas membre du rôle, nous allons les ajouter au rôle et actualiser la grille.

Ajouter une zone de texte et un bouton sous le contrôle GridView. Définir la zone de texte `ID` à `UserNameToAddToRole` et affectez à la `ID` et `Text` propriétés `AddUserToRoleButton` et « Ajouter au rôle d’utilisateur », respectivement.

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample17.aspx)]

Ensuite, créez un `Click` Gestionnaire d’événements pour le `AddUserToRoleButton` et ajoutez le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample18.cs)]

La plupart du code dans le `Click` Gestionnaire d’événements effectue plusieurs vérifications de validation. Elle garantit que le visiteur fourni un nom d’utilisateur dans le `UserNameToAddToRole` zone de texte, que l’utilisateur existe dans le système et qu’ils n’appartiennent pas déjà pour le rôle sélectionné. Si une de ces vérifications échoue, un message approprié s’affiche dans `ActionStatus` et quittez le Gestionnaire d’événements. Si toutes les vérifications réussissent, l’utilisateur est ajouté au rôle via le `Roles.AddUserToRole` (méthode). Après de cela, la zone de texte `Text` propriété soit retirée, le contrôle GridView est actualisé et le `ActionStatus` étiquette affiche un message indiquant que l’utilisateur spécifié a été ajouté au rôle sélectionné.

> [!NOTE]
> Pour vous assurer que l’utilisateur spécifié n’appartient pas déjà pour le rôle sélectionné, nous utilisons le [ `Roles.IsUserInRole(userName, roleName)` méthode](https://msdn.microsoft.com/library/system.web.security.roles.isuserinrole.aspx), qui retourne une valeur booléenne qui indique si *nom d’utilisateur* est un membre de *roleName*. Nous allons utiliser cette méthode à nouveau dans le <a id="_msoanchor_2"> </a> [didacticiel suivant](role-based-authorization-cs.md) lorsque nous intéresser à l’autorisation par rôle.


Visitez la page via un navigateur et sélectionnez le rôle par exemple à partir de la `RoleList` DropDownList. Essayez d’entrer un nom d’utilisateur non valide : vous devez voir un message expliquant que l’utilisateur n’existe pas dans le système.


[![Impossible d’ajouter un utilisateur inexistant à un rôle](assigning-roles-to-users-cs/_static/image26.png)](assigning-roles-to-users-cs/_static/image25.png)

**Figure 9**: vous ne pouvez pas ajouter un utilisateur inexistant à un rôle ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image27.png))


Essayez à présent ajouter un utilisateur valid. Pour commencer, ajoutez de nouveau Tito au rôle superviseurs.


[![Tito est à nouveau un superviseur !](assigning-roles-to-users-cs/_static/image29.png)](assigning-roles-to-users-cs/_static/image28.png)

**La figure 10**: Tito est à nouveau un superviseur !  ([Cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image30.png))


## <a name="step-3-cross-updating-the-by-user-and-by-role-interfaces"></a>Étape 3 : Cross-mise à jour de la « Par utilisateur » et « Par le rôle » Interfaces

Le `UsersAndRoles.aspx` page offre deux interfaces distinctes pour la gestion des utilisateurs et des rôles. Actuellement, ces deux interfaces agissent indépendamment les unes des autres, il est possible qu’une modification apportée à une interface est répercutée immédiatement dans l’autre. Par exemple, imaginez que le visiteur à la page Sélectionne le rôle de superviseurs à partir de la `RoleList` DropDownList, qui répertorie les Bruce et Tito en tant que ses membres. Ensuite, le visiteur sélectionne Tito à partir de la `UserList` DropDownList, qui vérifie les cases à cocher des contrôleurs et des administrateurs dans le `UsersRoleList` répéteur. Si le visiteur désactive ensuite le rôle superviseur de répéteur, Tito est supprimé du rôle superviseurs, mais cette modification n’est pas reflétée dans l’interface « par rôle ». Le contrôle GridView apparaît toujours Tito comme étant un membre du rôle superviseurs.

Pour y remédier, nous devons actualiser le GridView chaque fois qu’un rôle est activé ou désactivé à partir de la `UsersRoleList` répéteur. De même, nous devons actualiser le répéteur chaque fois qu’un utilisateur est supprimé ou ajouté à un rôle à partir de l’interface « par rôle ».

La répétition de l’interface « par utilisateur » est actualisée en appelant le `CheckRolesForSelectedUser` (méthode). L’interface « par rôle » peut être modifié dans le `RolesUserList` du GridView `RowDeleting` Gestionnaire d’événements et les `AddUserToRoleButton` du bouton `Click` Gestionnaire d’événements. Par conséquent, il faut appeler la `CheckRolesForSelectedUser` méthode à partir de chacune de ces méthodes.

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample19.cs)]

De même, le contrôle GridView dans l’interface « par rôle » est actualisé en appelant le `DisplayUsersBelongingToRole` (méthode) et l’interface « par utilisateur » est modifié via le `RoleCheckBox_CheckChanged` Gestionnaire d’événements. Par conséquent, il faut appeler la `DisplayUsersBelongingToRole` méthode à partir de ce gestionnaire d’événements.

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample20.cs)]

Avec ces modifications mineures au code, le « par utilisateur » et « par rôle » maintenant des interfaces correctement entre la mise à jour. Pour le vérifier, visitez la page via un navigateur, puis sélectionnez Tito et superviseurs à partir de la `UserList` et `RoleList` compréhension des listes, respectivement. Notez que lorsque vous désactivez le rôle superviseurs Tito à partir de la répétition de l’interface « par utilisateur », Tito est automatiquement supprimé du contrôle GridView dans l’interface « par rôle ». Ajout automatique Tito vers le rôle par exemple à partir de l’interface « par le rôle » vérifie à nouveau la case à cocher superviseurs dans l’interface « par utilisateur ».

## <a name="step-4-customizing-the-createuserwizard-to-include-a-specify-roles-step"></a>Étape 4 : Personnalisation CreateUserWizard pour inclure une étape « Spécifier des rôles »

Dans le <a id="_msoanchor_3"> </a> [ *création de comptes utilisateur* ](../membership/creating-user-accounts-cs.md) didacticiel, nous avons vu comment utiliser le contrôle CreateUserWizard Web pour fournir une interface pour la création d’un nouveau compte d’utilisateur. Le contrôle CreateUserWizard peut être utilisé de deux manières :

- Comme un moyen pour les visiteurs de créer leur propre compte d’utilisateur sur le site, et
- Comme un moyen pour les administrateurs de créer des comptes

Dans le premier cas d’utilisation, un visiteur est fourni sur le site et remplit CreateUserWizard, entrer leurs informations afin de s’inscrire sur le site. Dans le deuxième cas, un administrateur crée un nouveau compte pour une autre personne.

Lorsqu’un compte est créé par un administrateur d’une autre personne, il peut être utile d’autoriser l’administrateur de spécifier les rôles que le compte d’utilisateur appartient. Dans le <a id="_msoanchor_4"> </a> [ *stockage* *plus d’informations utilisateur* ](../membership/storing-additional-user-information-cs.md) didacticiel, nous avons vu comment personnaliser CreateUserWizard en ajoutant supplémentaires `WizardSteps`. Nous allons voir comment ajouter une étape supplémentaire pour CreateUserWizard afin de spécifier les rôles du nouvel utilisateur.

Ouvrez le `CreateUserWizardWithRoles.aspx` page et ajoutez un contrôle CreateUserWizard nommé `RegisterUserWithRoles`. Valeur du contrôle `ContinueDestinationPageUrl` propriété « ~ / Default.aspx ». Étant donné que l’idée est qu’un administrateur utiliseront ce contrôle CreateUserWizard pour créer de nouveaux comptes d’utilisateur, définissez du contrôle `LoginCreatedUser` propriété sur False. Cela `LoginCreatedUser` propriété indique si le visiteur est automatiquement connecté en tant que l’utilisateur nouvellement créé, et la valeur par défaut la valeur True. Nous affectez-lui la valeur False car lorsqu’un administrateur crée un nouveau compte nous souhaitons conserver lui connecté en tant que lui-même.

Ensuite, sélectionnez la « Ajout/suppression `WizardSteps`... » option à partir de la balise de CreateUserWizard et ajouter un nouveau `WizardStep`, ce qui affecte ses `ID` à `SpecifyRolesStep`. Déplacer le `SpecifyRolesStep WizardStep` afin qu’il vient après l’étape « Authentification de votre nouveau compte », mais avant l’étape « Terminée ». Définir le `WizardStep`de `Title` propriété « Spécifier rôles », son `StepType` propriété `Step`et son `AllowReturn` propriété sur False.


[![Ajoutez le](assigning-roles-to-users-cs/_static/image32.png)](assigning-roles-to-users-cs/_static/image31.png)

**Figure 11**: ajouter des « Rôles spécifier » `WizardStep` à CreateUserWizard ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image33.png))


Une fois cette modification balisage déclaratif de votre contrôle CreateUserWizard doit se présenter comme suit :

[!code-aspx[Main](assigning-roles-to-users-cs/samples/sample21.aspx)]

Dans le « spécifier les rôles » `WizardStep`, ajouter une liste case à cocher nommée `RoleList`. Cette liste case à cocher répertorie les rôles disponibles, l’activation de la personne visitant la page vérifier quels rôles appartient l’utilisateur nouvellement créé.

Nous avons toujours deux tâches de programmation : tout d’abord, nous devons remplir le `RoleList` liste case à cocher avec les rôles dans le système ; ensuite, nous devons ajouter l’utilisateur créé pour les rôles sélectionnés lorsque l’utilisateur déplace à partir de l’étape « Spécifier des rôles » à l’étape « Terminé ». Nous pouvons effectuer la première tâche dans la `Page_Load` Gestionnaire d’événements. Le code suivant par programme références le `RoleList` case à cocher dans la première, visitez la page et lie les rôles dans le système lui.

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample22.cs)]

Le code ci-dessus doit sembler familière. Dans le <a id="_msoanchor_5"> </a> [ *stockage* *des informations utilisateur supplémentaires* ](../membership/storing-additional-user-information-cs.md) didacticiel, nous avons utilisé deux `FindControl` instructions pour référencer un contrôle Web à partir de personnalisé `WizardStep`. Et le code qui lie les rôles à la liste de case à cocher a été extraite précédemment dans ce didacticiel.

Pour effectuer la tâche de programmation deuxième que nous devons savoir quand l’étape « Spécifier des rôles » est terminé. Souvenez-vous que CreateUserWizard a un `ActiveStepChanged` événement qui se déclenche chaque fois que le visiteur accède à partir d’une seule étape à l’autre. Ici, nous pouvons déterminer si l’utilisateur a atteint l’étape « Terminé ». Dans ce cas, nous devons ajouter l’utilisateur aux rôles sélectionnés.

Créer un gestionnaire d’événements pour le `ActiveStepChanged` événement et ajoutez le code suivant :

[!code-csharp[Main](assigning-roles-to-users-cs/samples/sample23.cs)]

Si l’utilisateur a atteint simplement l’étape « Completed », le Gestionnaire d’événements énumère les éléments de la `RoleList` liste case à cocher et l’utilisateur nouvellement créé est affecté aux rôles sélectionnés.

Visitez cette page via un navigateur. La première étape de CreateUserWizard est l’étape « Authentification de votre nouveau compte » standard, ce qui invite à entrer le nouveau nom d’utilisateur, mot de passe, par courrier électronique et autres informations de clé. Entrez les informations pour créer un utilisateur nommé Wanda.


[![Créer un utilisateur nommé Wanda](assigning-roles-to-users-cs/_static/image35.png)](assigning-roles-to-users-cs/_static/image34.png)

**Figure 12**: créer un nouveau Wanda de nommé utilisateur ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image36.png))


Cliquez sur le bouton « Create User ». CreateUserWizard appelle en interne la `Membership.CreateUser` méthode, la création du nouveau compte d’utilisateur, puis passe à l’étape suivante, « spécifier rôles ». Les rôles de système répertoriés ici. Activez la case à cocher superviseurs et cliquez sur Suivant.


[![Faire Wanda un membre du rôle superviseurs](assigning-roles-to-users-cs/_static/image38.png)](assigning-roles-to-users-cs/_static/image37.png)

**Figure 13**: faites Wanda un membre du rôle superviseurs ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image39.png))


En cliquant sur suivant provoque une publication (postback) et les mises à jour le `ActiveStep` à l’étape « Terminé ». Dans la `ActiveStepChanged` Gestionnaire d’événements, le compte d’utilisateur récemment créé est affecté au rôle superviseurs. Pour vérifier cela, revenez à la `UsersAndRoles.aspx` page et sélectionner par exemple à partir de la `RoleList` DropDownList. Comme le montre la Figure 14, les superviseurs sont désormais constitués de trois utilisateurs : Bruce Tito et Wanda.


[![Bruce Tito et Wanda sont tous les superviseurs](assigning-roles-to-users-cs/_static/image41.png)](assigning-roles-to-users-cs/_static/image40.png)

**La figure 14**: Bruce Tito et Wanda sont tous les superviseurs ([cliquez pour afficher l’image en taille réelle](assigning-roles-to-users-cs/_static/image42.png))


## <a name="summary"></a>Récapitulatif

L’infrastructure de rôles offre des méthodes pour récupérer des informations sur les rôles d’un utilisateur particulier et les méthodes pour déterminer ce que les utilisateurs appartiennent à un rôle spécifié. Plus, il existe plusieurs méthodes pour ajouter et supprimer un ou plusieurs utilisateurs à un ou plusieurs rôles. Dans ce didacticiel, nous nous concentrés sur deux de ces méthodes : `AddUserToRole` et `RemoveUserFromRole`. Il existe des variantes supplémentaires conçues pour ajouter plusieurs utilisateurs à un rôle unique et d’attribuer plusieurs rôles à un seul utilisateur.

Ce didacticiel a également inclus un aspect à étendre le contrôle CreateUserWizard à inclure un `WizardStep` pour spécifier des rôles d’utilisateur nouvellement créé. Une étape de ce type peut aider l’administrateur à rationaliser le processus de création de comptes d’utilisateur pour les nouveaux utilisateurs.

À ce stade, nous avons vu comment créer et supprimer des rôles et comment ajouter et supprimer des utilisateurs dans des rôles. Mais nous avons encore d’examiner l’application d’autorisation basée sur le rôle. Dans le <a id="_msoanchor_6"> </a> [suivant le didacticiel](role-based-authorization-cs.md) nous allons nous intéresser à la définition des règles d’autorisation d’URL sur une base par rôle de rôle, ainsi que la manière de limiter les fonctionnalités au niveau de la page basée sur les rôles de l’utilisateur actuellement connecté.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Présentation de l’outil Administration Site Web ASP.NET](https://msdn.microsoft.com/library/ms228053.aspx)
- [Examen des ASP. L’appartenance, rôles et profil de NET](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Restauration de votre propre outil d’Administration de site Web](http://aspnet.4guysfromrolla.com/articles/052307-1.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Teresa Murphy. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](creating-and-managing-roles-cs.md)
[Suivant](role-based-authorization-cs.md)
