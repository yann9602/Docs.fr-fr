---
uid: web-forms/overview/older-versions-security/admin/unlocking-and-approving-user-accounts-cs
title: "Déverrouillage et l’approbation des comptes d’utilisateur (c#) | Documents Microsoft"
author: rick-anderson
description: "Ce didacticiel montre comment générer une page web pour les administrateurs à gérer les utilisateurs verrouillés et approuvé les États. Nous allons également voir comment approuver les nouveaux utilisateurs o..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2008
ms.topic: article
ms.assetid: 5346aab1-9974-489f-a065-ae3883b8a350
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/admin/unlocking-and-approving-user-accounts-cs
msc.type: authoredcontent
ms.openlocfilehash: 65d32309cbd8bed6decbba4c5027d8e10a558ae8
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="unlocking-and-approving-user-accounts-c"></a>L’approbation et de déverrouillage des comptes d’utilisateur (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/6/0/e/60e1bd94-e5f9-4d5a-a079-f23c98f4f67d/CS.14.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/0/e/60e1bd94-e5f9-4d5a-a079-f23c98f4f67d/aspnet_tutorial14_UnlockAndApprove_cs.pdf)

> Ce didacticiel montre comment générer une page web pour les administrateurs à gérer les utilisateurs verrouillés et approuvé les États. Nous allons également voir comment approuver les nouveaux utilisateurs uniquement une fois qu’ils ont vérifié leur adresse de messagerie.


## <a name="introduction"></a>Introduction

Avec un nom d’utilisateur, le mot de passe et le courrier électronique, chaque compte d’utilisateur a deux champs de statut qui déterminent si l’utilisateur peut se connecter au site : verrouillé et approuvé. Un utilisateur est automatiquement verrouillé s’ils fournissent des informations d’identification non valides, un nombre spécifié de fois au cours d’un nombre spécifié de minutes (les paramètres par défaut verrouiller un utilisateur après 5 tentatives de connexion non valide dans les 10 minutes). L’état approuvé est utile dans les scénarios où une action doit s’écouler avant un nouvel utilisateur est en mesure de se connecter au site. Par exemple, un utilisateur peut avoir besoin pour tout d’abord vérifier que leur adresse de messagerie ou être approuvé par un administrateur avant d’être en mesure de connexion.

Car un verrouillé non approuvés ou de l’utilisateur ne peut pas se connecter, il est naturel uniquement à vous demander comment ces États peuvent être réinitialisés. ASP.NET n’inclut pas toutes les fonctionnalités intégrées ou les contrôles Web pour la gestion des utilisateurs verrouillés et approuvé les États, en partie parce que ces décisions ont besoin d’être gérés sur une base site par site. Certains sites peuvent approuver automatiquement tous les nouveaux comptes d’utilisateur (le comportement par défaut). D’autres ont un administrateur approuve les nouveaux comptes ou n’approuve pas les utilisateurs jusqu'à ce qu’ils visitent un lien envoyé à l’adresse de messagerie fournie lors de leur inscription. De même, certains sites peuvent verrouiller les utilisateurs jusqu'à ce qu’un administrateur réinitialise leur état, tandis que les autres sites envoyer un courrier électronique à l’utilisateur verrouillé par une URL qu’ils peuvent visiter pour déverrouiller son compte.

Ce didacticiel montre comment générer une page web pour les administrateurs à gérer les utilisateurs verrouillés et approuvé les États. Nous allons également voir comment approuver les nouveaux utilisateurs uniquement une fois qu’ils ont vérifié leur adresse de messagerie.

## <a name="step-1-managing-users-locked-out-and-approved-statuses"></a>Étape 1 : Gestion des utilisateurs verrouillé et approuvé les États

Dans le <a id="Tutorial12"> </a> [ *création d’une Interface pour sélectionner un compte d’utilisateur d’un grand nombre* ](building-an-interface-to-select-one-user-account-from-many-cs.md) didacticiel, nous avons construit une page qui répertoriée chaque compte d’utilisateur dans paginé, filtrées GridView. La grille répertorie chaque nom d’utilisateur et par courrier électronique, leurs États approuvées et verrouillés, qu’ils soient actuellement en ligne et des commentaires sur l’utilisateur. Pour gérer des utilisateurs approuvés et verrouillé États, nous pouvons modifier cette grille. Pour modifier le statut d’un utilisateur, l’administrateur est d’abord localiser le compte d’utilisateur et puis modifiez la ligne correspondante de GridView, activant ou désactivant la case à cocher approuvé. Nous avons également gérer les États approuvées et verrouillés par une page ASP.NET distincte.

Pour ce didacticiel, nous allons utiliser deux pages ASP.NET : `ManageUsers.aspx` et `UserInformation.aspx`. L’idée est que `ManageUsers.aspx` répertorie les comptes d’utilisateur dans le système, tandis que `UserInformation.aspx` permet à l’administrateur gérer les États approuvées et verrouillés pour un utilisateur spécifique. Notre première chose consiste à augmenter le contrôle GridView dans `ManageUsers.aspx` pour inclure un HyperLinkField, ce qui est rendu sous la forme d’une colonne de liens. Nous souhaitons chaque lien pour pointer vers `UserInformation.aspx?user=UserName`, où *nom d’utilisateur* est le nom de l’utilisateur à modifier.

> [!NOTE]
> Si vous avez téléchargé le code pour le <a id="Tutorial13"> </a> [ *récupération et modification des mots de passe* ](recovering-and-changing-passwords-cs.md) didacticiel, vous avez peut-être remarqué que la `ManageUsers.aspx` page contient déjà un ensemble de » Gérer les liens » et le `UserInformation.aspx` page fournit une interface pour la modification de mot de passe de l’utilisateur sélectionné. J’ai décidé de ne pas répliquer ces fonctionnalités dans le code associé à ce didacticiel, car cela a fonctionné par contournement de l’API d’appartenance et fonctionne directement avec la base de données SQL Server pour modifier le mot de passe d’un utilisateur. Ce didacticiel commence à partir de zéro avec la `UserInformation.aspx` page.


### <a name="adding-manage-links-to-theuseraccountsgridview"></a>Ajout de « gérer » des liens vers les`UserAccounts`GridView

Ouvrez le `ManageUsers.aspx` page et ajoutez un HyperLinkField à la `UserAccounts` GridView. Définir le HyperLinkField `Text` propriété sur « Gérer » et ses `DataNavigateUrlFields` et `DataNavigateUrlFormatString` propriétés `UserName` et « UserInformation.aspx?user={0} », respectivement. Ces paramètres configurent le HyperLinkField telles que tous les liens hypertexte affichent le texte « Gérer », mais chaque lien passe dans les zones appropriées *nom d’utilisateur* valeur dans la chaîne de requête.

Après avoir ajouté le HyperLinkField au GridView, prenez un moment pour afficher la `ManageUsers.aspx` page via un navigateur. Comme le montre la Figure 1, chaque ligne GridView inclut maintenant un lien « Gérer ». Le lien « Gérer » pour Bruce pointe vers `UserInformation.aspx?user=Bruce`, alors que le lien « Gérer » pour Dave pointe vers `UserInformation.aspx?user=Dave`.


[![Le HyperLinkField ajoute un](unlocking-and-approving-user-accounts-cs/_static/image2.png)](unlocking-and-approving-user-accounts-cs/_static/image1.png)

**Figure 1**: le HyperLinkField ajoute un lien « Gérer » pour chaque compte d’utilisateur ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image3.png))


Nous allons en créer l’interface utilisateur et du code pour le `UserInformation.aspx` page dans un moment, mais le premier nous allons parler sur comment modifier par programme un utilisateur verrouillé et approuvé les États. Le [ `MembershipUser` classe](https://msdn.microsoft.com/en-us/library/system.web.security.membershipuser.aspx) a [ `IsLockedOut` ](https://msdn.microsoft.com/en-us/library/system.web.security.membershipuser.islockedout.aspx) et [ `IsApproved` propriétés](https://msdn.microsoft.com/en-us/library/system.web.security.membershipuser.isapproved.aspx). Le `IsLockedOut` propriété est en lecture seule. Il n’existe aucun mécanisme de verrouillage par programme à un utilisateur ; Pour déverrouiller un utilisateur, utilisez la `MembershipUser` de classe [ `UnlockUser` méthode](https://msdn.microsoft.com/en-us/library/system.web.security.membershipuser.unlockuser.aspx). Le `IsApproved` propriété est accessible en lecture et en écriture. Pour enregistrer les modifications à cette propriété, il faut appeler la `Membership` de classe [ `UpdateUser` méthode](https://msdn.microsoft.com/en-us/library/system.web.security.membership.updateuser.aspx), en passant le texte modifié `MembershipUser` objet.

Étant donné que le `IsApproved` propriété est accessible en lecture et en écriture, un contrôle de case à cocher est probablement la meilleure élément d’interface utilisateur pour la configuration de cette propriété. Toutefois, une case à cocher ne fonctionnera pas pour le `IsLockedOut` propriété, car un administrateur ne peut pas verrouiller un utilisateur, elle peut uniquement déverrouiller un utilisateur. Une interface utilisateur appropriée pour le `IsLockedOut` propriété est un bouton qui, lorsque vous cliquez dessus, déverrouille le compte d’utilisateur. Ce bouton doit être activé uniquement si l’utilisateur est verrouillé.

### <a name="creating-theuserinformationaspxpage"></a>Création de le`UserInformation.aspx`Page

Nous sommes maintenant prêts à implémenter l’interface utilisateur dans `UserInformation.aspx`. Ouvrez cette page et ajoutez les contrôles Web suivants :

- Un lien hypertexte de contrôle qui, lorsque vous cliquez dessus, retourne l’administrateur de le `ManageUsers.aspx` page.
- Un contrôle Web Label pour afficher le nom de l’utilisateur sélectionné. Valeur de cette étiquette `ID` à `UserNameLabel` et effacer son `Text` propriété.
- Un contrôle de case à cocher nommé `IsApproved`. Définir son `AutoPostBack` propriété `true`.
- Date du dernier verrouillage d’un contrôle Label pour l’affichage de l’utilisateur. Nommez cette étiquette `LastLockedOutDateLabel` et effacer son `Text` propriété.
- Un bouton pour le déverrouillage de l’utilisateur. Nom de ce bouton `UnlockUserButton` et définir son `Text` propriété « Déverrouiller utilisateur ».
- Un contrôle Label pour afficher les messages d’état, telles que « statut de l’utilisateur a été mis à jour. » Nom de ce contrôle `StatusMessage`, désactivez les sa `Text` propriété et définissez son `CssClass` propriété `Important`. (Le `Important` classe CSS est définie dans le `Styles.css` fichier de feuille de style ; il affiche le texte correspondant dans une grande police rouge.)

Après avoir ajouté ces contrôles, la vue de conception dans Visual Studio doit ressembler à la capture d’écran de la Figure 2.


[![Créer l’Interface utilisateur pour UserInformation.aspx](unlocking-and-approving-user-accounts-cs/_static/image5.png)](unlocking-and-approving-user-accounts-cs/_static/image4.png)

**Figure 2**: créer l’Interface utilisateur de `UserInformation.aspx` ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image6.png))


Avec l’interface utilisateur est terminée, la tâche suivante consiste à définir le `IsApproved` case à cocher et autres contrôles en fonction des informations de l’utilisateur sélectionné. Créer un gestionnaire d’événements de la page `Load` événement et ajoutez le code suivant :

[!code-csharp[Main](unlocking-and-approving-user-accounts-cs/samples/sample1.cs)]

Le code ci-dessus démarre en vous assurant qu’il s’agit de la première visite de la page et pas d’une publication ultérieure. Il lit ensuite le nom d’utilisateur passé le `user` champ querystring et récupère des informations sur ce compte d’utilisateur via la `Membership.GetUser(username)` (méthode). Si aucun nom d’utilisateur a été fourni par la chaîne de requête, ou si l’utilisateur spécifié est introuvable, l’administrateur est renvoyé à la `ManageUsers.aspx` page.

Le `MembershipUser` l’objet `UserName` valeur est ensuite affichée dans le `UserNameLabel` et le `IsApproved` case à cocher est activée en fonction de la `IsApproved` valeur de propriété.

Le `MembershipUser` l’objet [ `LastLockoutDate` propriété](https://msdn.microsoft.com/en-us/library/system.web.security.membershipuser.lastlockoutdate.aspx) retourne un `DateTime` indiquant quand l’utilisateur a été verrouillé. Si l’utilisateur n’a jamais été verrouillé, la valeur retournée varie selon le fournisseur d’appartenances. Lorsqu’un nouveau compte est créé, le `SqlMembershipProvider` définit le `aspnet_Membership` la table `LastLockoutDate` au champ `1754-01-01 12:00:00 AM`. Le code ci-dessus affiche une chaîne vide dans le `LastLockoutDateLabel` si le `LastLockoutDate` propriété se produit avant l’année 2000 ; sinon, la partie date de la `LastLockoutDate` propriété s’affiche dans l’étiquette. Le `UnlockUserButton'` s `Enabled` est définie sur l’utilisateur verrouillé d’état, ce qui signifie que que ce bouton est uniquement activé si l’utilisateur est verrouillé.

Prenez un moment pour tester le `UserInformation.aspx` page via un navigateur. Vous, bien entendu, devez commencent à `ManageUsers.aspx` et sélectionnez un compte d’utilisateur à gérer. Lors de la réception sur `UserInformation.aspx`, notez que le `IsApproved` case à cocher est activée uniquement si l’utilisateur est approuvé. Si l’utilisateur a déjà été verrouillé, leur dernière verrouillé date s’affiche. Le bouton de déverrouillage de l’utilisateur est activé uniquement si l’utilisateur est actuellement verrouillé. Activant ou désactivant la `IsApproved` case à cocher ou en cliquant sur le bouton de déverrouillage de l’utilisateur entraîne une publication, mais aucun modifications ne sont apportées au compte d’utilisateur, car nous avons encore pour créer des gestionnaires d’événements pour ces événements.

Revenez à Visual Studio et créer des gestionnaires d’événements pour le `IsApproved` la case à cocher `CheckedChanged` événement et le `UnlockUser` du bouton `Click` événement. Dans le `CheckedChanged` Gestionnaire d’événements, définir l’utilisateur `IsApproved` propriété le `Checked` propriétés de la case à cocher, puis enregistrez les modifications via un appel à `Membership.UpdateUser`. Dans le `Click` Gestionnaire d’événements, il vous suffit d’appel du `MembershipUser` l’objet `UnlockUser` (méthode). Dans les deux gestionnaires d’événements, afficher un message approprié dans le `StatusMessage` étiquette.

[!code-csharp[Main](unlocking-and-approving-user-accounts-cs/samples/sample2.cs)]

### <a name="testing-theuserinformationaspxpage"></a>Test de le`UserInformation.aspx`Page

Ces gestionnaires d’événements en place, revoir la page et non approuvés un utilisateur. Comme le montre la Figure 3, vous devez voir un résumé de message dans la page indiquant que l’utilisateur `IsApproved` propriété a été correctement modifiée.


[![Chris a été approuvé](unlocking-and-approving-user-accounts-cs/_static/image8.png)](unlocking-and-approving-user-accounts-cs/_static/image7.png)

**Figure 3**: Chris a été approuvé ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image9.png))


Ensuite, déconnexion et essayez d’ouvrir une session en tant que l’utilisateur dont le compte a été simplement non approuvés. Étant donné que l’utilisateur n’est pas approuvé, ils ne peut pas se connecter. Par défaut, le contrôle de connexion affiche le message même si l’utilisateur ne peut pas ouvrir une session, quelle que soit la raison. Mais, dans le <a id="Tutorial6"> </a> [ *validation utilisateur informations d’identification sur le magasin d’appartenance utilisateur* ](../membership/validating-user-credentials-against-the-membership-user-store-cs.md) didacticiel, nous avons étudié améliorer le contrôle de connexion pour afficher un message plus approprié. Comme le montre la Figure 4, Chris s’affiche un message qui explique qu’il ne peut pas se connecter, car son compte n’est pas encore approuvée.


[![Chris impossible, car son compte de connexion est non approuvé](unlocking-and-approving-user-accounts-cs/_static/image11.png)](unlocking-and-approving-user-accounts-cs/_static/image10.png)

**Figure 4**: Chris impossible, car son compte de connexion est non approuvé ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image12.png))


Pour tester la fonctionnalité verrouillée, tenter de se connecter en tant qu’un utilisateur approuvé, mais d’utiliser un mot de passe incorrect. Répétez ce processus, le nombre de fois jusqu'à ce que le compte d’utilisateur a été verrouillé nécessaires. Le contrôle de connexion a été également mise à jour et personnalisée de message si la tentative de connexion à partir d’un compte verrouillé. Vous savez qu’un compte a été verrouillé une fois que vous voyez le message suivant à la page de connexion : « votre compte a été verrouillé en raison de trop nombreuses tentatives de connexion non valide. Contactez l’administrateur pour votre compte a été déverrouillée. »

Retour à la `ManageUsers.aspx` page, puis cliquez sur le lien gérer pour l’utilisateur verrouillé. Comme le montre la Figure 5, vous devez voir une valeur dans la `LastLockedOutDateLabel` le bouton de déverrouillage de l’utilisateur doit être activé. Cliquez sur le bouton de déverrouillage de l’utilisateur pour déverrouiller le compte d’utilisateur. Une fois que vous avez déverrouillé l’utilisateur, ils seront en mesure de se connecter à nouveau.


[![Dave a été verrouillé pour le système](unlocking-and-approving-user-accounts-cs/_static/image14.png)](unlocking-and-approving-user-accounts-cs/_static/image13.png)

**Figure 5**: Dave a été verrouillé hors du système ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image15.png))


## <a name="step-2-specifying-new-users-approved-status"></a>Étape 2 : Spécification de nouveaux utilisateurs approuvés état

L’état approuvé est utile dans les scénarios où vous souhaitez que des actions à effectuer avant un nouvel utilisateur est en mesure de se connecter et accéder aux fonctionnalités spécifiques à l’utilisateur du site. Par exemple, vous exécutez peut-être un site privé où toutes les pages, sauf pour les pages de connexion et d’abonnement, sont accessibles uniquement aux utilisateurs authentifiés. Mais que se passe-t-il si une personne atteint votre site Web, de recherche de la page d’abonnement et crée un compte ? Pour éviter ce problème, vous pouvez déplacer la page d’abonnement à un `Administration` dossier et nécessitent qu’un administrateur créer manuellement chaque compte. Vous pouvez également autoriser tout le monde à l’inscription, mais interdire l’accès au site jusqu'à ce qu’un administrateur approuve le compte d’utilisateur.

Par défaut, le contrôle CreateUserWizard approuve les nouveaux comptes. Vous pouvez configurer ce comportement à l’aide du contrôle [ `DisableCreatedUser` propriété](https://msdn.microsoft.com/en-gb/library/system.web.ui.webcontrols.createuserwizard.disablecreateduser.aspx). Définissez cette propriété sur `true` ne pas approuver des comptes d’utilisateur.

> [!NOTE]
> Par défaut du contrôle CreateUserWizard enregistre automatiquement sur le nouveau compte d’utilisateur. Ce comportement est dicté par le contrôle [ `LoginCreatedUser` propriété](https://msdn.microsoft.com/en-gb/library/system.web.ui.webcontrols.createuserwizard.logincreateduser.aspx). Étant donné que les utilisateurs non approuvés ne peut pas se connecter au site, lorsque `DisableCreatedUser` est `true` le nouveau compte d’utilisateur n’est pas connecté au site, quelle que soit la valeur de la `LoginCreatedUser` propriété.


Si vous créez par programme des nouveaux comptes d’utilisateur via la `Membership.CreateUser` pour créer un compte d’utilisateur non approuvé de méthode, utilisez une des surcharges qui acceptent le nouvel utilisateur `IsApproved` valeur de la propriété en tant que paramètre d’entrée.

## <a name="step-3-approving-users-by-verifying-their-email-address"></a>Étape 3 : Approuver les utilisateurs en vérifiant son adresse de messagerie

De nombreux sites Web qui prennent en charge les comptes d’utilisateur n’approuvent pas les nouveaux utilisateurs jusqu'à ce qu’elles vérifient l’adresse de messagerie qu’ils fournis lors de l’inscription. Ce processus de vérification est couramment utilisé pour bloquer les expéditeurs, des robots et autres ne'er-do-wells car elle requiert une adresse de messagerie unique et vérifiées et ajoute une étape supplémentaire dans le processus d’inscription. Avec ce modèle, lorsqu’un utilisateur se connecte, elles sont envoyées un message électronique qui inclut un lien vers une page de vérification. En accédant au lien, l’utilisateur a prouvé qu’ils reçu le message électronique et, par conséquent, que l’adresse de messagerie fournie n’est valide. La page de vérification est responsable de l’approbation de l’utilisateur. Cela se produit automatiquement, et approuver tout utilisateur ayant atteint cette page, ou uniquement une fois que l’utilisateur fournit des informations supplémentaires, comme un [CAPTCHA](http://en.wikipedia.org/wiki/Captcha).

Pour prendre en compte ce flux de travail, nous devons tout d’abord mettre à jour de la page de création de compte afin que les nouveaux utilisateurs sont approuvés. Ouvrez le `EnhancedCreateUserWizard.aspx` page dans le `Membership` dossier et l’ensemble du contrôle CreateUserWizard `DisableCreatedUser` propriété `true`.

Ensuite, nous avons besoin configurer le contrôle CreateUserWizard pour envoyer un courrier électronique au nouvel utilisateur avec des instructions sur la façon de vérifier son compte. En particulier, nous comprennent un lien dans le message électronique pour le `Verification.aspx` page (que nous encore à créer), en passant le nouvel utilisateur `UserId` via la chaîne de requête. Le `Verification.aspx` page de recherche de l’utilisateur spécifié et marquez-les approuvé.

### <a name="sending-a-verification-email-to-new-users"></a>Envoyer un message électronique de vérification pour les nouveaux utilisateurs

Pour envoyer un message électronique à partir du contrôle CreateUserWizard, configurer ses `MailDefinition` propriété correctement. Comme indiqué dans le <a id="Tutorial13"> </a> [didacticiel précédent](recovering-and-changing-passwords-cs.md), les contrôles ChangePassword et PasswordRecovery incluent un [ `MailDefinition` propriété](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.createuserwizard.maildefinition.aspx) qui fonctionne de la même manière que CreateUserWizard du contrôle.

> [!NOTE]
> Pour utiliser le `MailDefinition` options de propriété, vous devez spécifier la remise des messages dans `Web.config`. Pour plus d’informations, reportez-vous à [envoi de courrier électronique dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx).


Commencez par créer un nouveau modèle de courrier électronique nommé `CreateUserWizard.txt` dans le `EmailTemplates` dossier. Pour le modèle, utilisez le texte suivant :

[!code-aspx[Main](unlocking-and-approving-user-accounts-cs/samples/sample3.aspx)]

Définir le `MailDefinition'` s `BodyFileName` propriété « ~ / EmailTemplates/CreateUserWizard.txt » et ses `Subject` propriété « Bienvenue dans mon site Web ! Activez votre compte. »

Notez que la `CreateUserWizard.txt` modèle de courrier électronique inclut un `<%VerificationUrl%>` espace réservé. Cette propriété est si l’URL pour le `Verification.aspx` page sera placée. CreateUserWizard remplace automatiquement le `<%UserName%>` et `<%Password%>` des espaces réservés avec du nouveau compte nom d’utilisateur et mot de passe, mais il n’intègre aucune `<%VerificationUrl%>` espace réservé. Nous devons remplacer manuellement par l’URL de vérification appropriées.

Pour ce faire, créez un gestionnaire d’événements pour de CreateUserWizard [ `SendingMail` événements](https://msdn.microsoft.com/en-us/library/system.web.ui.webcontrols.createuserwizard.sendingmail.aspx) et ajoutez le code suivant :

[!code-csharp[Main](unlocking-and-approving-user-accounts-cs/samples/sample4.cs)]

Le `SendingMail` événement est déclenché après la `CreatedUser` événement, ce qui signifie que qu’au moment où le Gestionnaire d’événements ci-dessus exécute le nouvel utilisateur de compte a déjà été créé. Nous pouvons accéder à du nouvel utilisateur `UserId` valeur en appelant le `Membership.GetUser` méthode, en passant le `UserName` entré dans le contrôle CreateUserWizard. Ensuite, l’URL de vérification est formé. L’instruction `Request.Url.GetLeftPart(UriPartial.Authority)` retourne la `http://yourserver.com` partie de l’URL ; `Request.ApplicationPath` renvoie le chemin où associé à l’application. La vérification des URL est alors définie comme `Verification.aspx?ID=userId`. Ces deux chaînes sont ensuite concaténées pour former l’URL complète. Enfin, le corps du message électronique (`e.Message.Body`) a toutes les occurrences de `<%VerificationUrl%>` remplacé par l’URL complète.

L’effet net est que les nouveaux utilisateurs sont non approuvés, c'est-à-dire qu’ils ne peuvent pas se connecter au site. En outre, ils sont envoyés automatiquement un message électronique contenant un lien vers l’URL de vérification (voir Figure 6).


[![Le nouvel utilisateur reçoit un message électronique contenant un lien vers l’URL de vérification](unlocking-and-approving-user-accounts-cs/_static/image17.png)](unlocking-and-approving-user-accounts-cs/_static/image16.png)

**Figure 6**: le nouvel utilisateur reçoit un message électronique contenant un lien vers l’URL de vérification ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image18.png))


> [!NOTE]
> Étape de CreateUserWizard par défaut d' un contrôle CreateUserWizard affiche un message informant l’utilisateur de son compte a été créé et affiche un bouton Continuer. Cliquer sur ce bouton guide l’utilisateur à l’URL spécifiée par le contrôle `ContinueDestinationPageUrl` propriété. CreateUserWizard dans `EnhancedCreateUserWizard.aspx` est configuré pour envoyer aux nouveaux utilisateurs de la `~/Membership/AdditionalUserInfo.aspx`, ce qui invite l’utilisateur pour leur ville, l’URL de la page d’accueil et signature. Étant donné que ces informations peuvent uniquement être ajoutées par des utilisateurs connectés, il est judicieux de mettre à jour cette propriété pour envoyer aux utilisateurs sur la page d’accueil du site (`~/Default.aspx`). En outre, le `EnhancedCreateUserWizard.aspx` page ou l’étape CreateUserWizard doit être augmentée pour informer l’utilisateur que s’ils ont été envoyés à un message électronique de vérification et de leur compte n’est pas activé jusqu'à ce qu’ils suivent les instructions de cette adresse de messagerie. J’ai laissez ces modifications en guise d’exercice pour le lecteur.


### <a name="creating-the-verification-page"></a>Création de la Page Vérification

La dernière tâche consiste à créer le `Verification.aspx` page. Ajouter cette page dans le dossier racine, en association avec le `Site.master` page maître. Comme nous l’avons fait avec la plupart des pages de contenu précédents ajoutés au site, supprimez le contrôle de contenu qui fait référence à la `LoginContent` ContentPlaceHolder afin que la page de contenu utilise la page maître de contenu par défaut.

Ajouter un contrôle Web Label pour la `Verification.aspx` , définissez son `ID` à `StatusMessage` et effacer sa propriété text. Ensuite, créez le `Page_Load` Gestionnaire d’événements et ajoutez le code suivant :

[!code-csharp[Main](unlocking-and-approving-user-accounts-cs/samples/sample5.cs)]

La majeure partie du code ci-dessus vérifie que le `UserId` fourni par le biais de la chaîne de requête existe, qu’elle est valide `Guid` valeur, et qu’elle fait référence à un compte d’utilisateur existant. Si toutes ces vérifications réussissent, le compte d’utilisateur est approuvé ; Sinon, un message d’état approprié s’affiche.

La figure 7 montre le `Verification.aspx` page lorsque visité via un navigateur.


[![Le nouveau compte d’utilisateur est à présent approuvé](unlocking-and-approving-user-accounts-cs/_static/image20.png)](unlocking-and-approving-user-accounts-cs/_static/image19.png)

**Figure 7**: compte du nouvel utilisateur l’est à présent approuvé ([cliquez pour afficher l’image en taille réelle](unlocking-and-approving-user-accounts-cs/_static/image21.png))


## <a name="summary"></a>Résumé

Tous les comptes d’utilisateur d’appartenance ont deux États qui déterminent si l’utilisateur peut se connecter au site : `IsLockedOut` et `IsApproved`. Ces deux propriétés doivent être `true` pour l’utilisateur de connexion.

L’utilisateur bloqué de l’état est utilisé en tant que mesure de sécurité afin de réduire la probabilité d’un pirate informatique avec rupture dans un site via des méthodes de force brute. Plus précisément, un utilisateur est verrouillé s’il existe un certain nombre de tentatives de connexion non valide dans une certaine fenêtre de temps. Ces limites sont configurables via les paramètres du fournisseur d’appartenances dans `Web.config`.

L’état approuvé est couramment utilisé comme un moyen d’empêcher les nouveaux utilisateurs de se connecter jusqu'à ce qu’une action a été dépassé. Peut-être que le site nécessite tout d’abord être approuvé de nouveaux comptes par l’administrateur ou, comme nous l’avons vu à l’étape 3, en vérifiant son adresse de messagerie.

Bonne programmation !

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements particuliers à...

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](recovering-and-changing-passwords-cs.md)
[Suivant](building-an-interface-to-select-one-user-account-from-many-vb.md)
