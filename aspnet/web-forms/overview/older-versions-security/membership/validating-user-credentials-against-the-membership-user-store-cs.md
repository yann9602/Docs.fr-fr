---
uid: web-forms/overview/older-versions-security/membership/validating-user-credentials-against-the-membership-user-store-cs
title: "Validation des informations d’identification de l’utilisateur dans le magasin d’utilisateurs d’appartenance (c#) | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous allons examiner comment valider des informations d’identification d’un utilisateur sur le magasin d’utilisateur d’appartenance à l’aide de moyens par programme et le contrôle de connexion en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/18/2008
ms.topic: article
ms.assetid: 61aa4e08-aa81-4aeb-8ebe-19ba7a65e04c
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/membership/validating-user-credentials-against-the-membership-user-store-cs
msc.type: authoredcontent
ms.openlocfilehash: 8f8f4db63ba8c1f1c1df7c1c5c1f92184bf6841d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="validating-user-credentials-against-the-membership-user-store-c"></a>Validation des informations d’identification de l’utilisateur dans le magasin d’utilisateurs d’appartenance (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/ASPNET_Security_Tutorial_06_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/3/f/5/3f5a8605-c526-4b34-b3fd-a34167117633/aspnet_tutorial06_LoggingIn_cs.pdf)

> Dans ce didacticiel, nous allons examiner comment valider des informations d’identification d’un utilisateur sur le magasin d’utilisateur d’appartenance à l’aide de moyens par programme et le contrôle de connexion. Nous examinerons également comment personnaliser l’apparence et le comportement du contrôle de connexion.


## <a name="introduction"></a>Introduction

Dans le <a id="Tutorial05"> </a> [didacticiel précédent](creating-user-accounts-cs.md) nous avons examiné comment créer un nouveau compte d’utilisateur dans le cadre de l’appartenance. Nous avons tout d’abord à créer par programme des comptes d’utilisateur via la `Membership` la classe `CreateUser` (méthode) et ensuite examiné à l’aide du contrôle CreateUserWizard Web. Toutefois, la page de connexion valide actuellement les informations d’identification fournies par rapport à une liste codée en dur de paires nom d’utilisateur et mot de passe. Nous devons mettre à jour la logique de la page de connexion afin qu’il valide les informations d’identification dans le magasin de l’utilisateur de l’infrastructure d’appartenance.

Beaucoup telles que de créer des comptes d’utilisateur, informations d’identification peuvent être validées par programme ou de façon déclarative. L’API d’appartenance inclut une méthode de validation par programmation les informations d’identification d’un utilisateur par rapport au magasin de l’utilisateur. Et ASP.NET est fourni avec le contrôle de connexion Web, ce qui rend une interface utilisateur avec des zones de texte pour le nom d’utilisateur et mot de passe et un bouton pour vous connecter.

Dans ce didacticiel, nous allons examiner comment valider des informations d’identification d’un utilisateur sur le magasin d’utilisateur d’appartenance à l’aide de moyens par programme et le contrôle de connexion. Nous examinerons également comment personnaliser l’apparence et le comportement du contrôle de connexion. C’est parti !

## <a name="step-1-validating-credentials-against-the-membership-user-store"></a>Étape 1 : Validation des informations d’identification dans le magasin d’utilisateur d’appartenance

Pour les sites web qui utilisent l’authentification par formulaire, un utilisateur ouvre une session sur le site Web en accédant à une page de connexion et en entrant leurs informations d’identification. Ces informations d’identification sont ensuite comparées par rapport au magasin de l’utilisateur. Si elles sont valides, l’utilisateur a un ticket d’authentification forms, qui est un jeton de sécurité qui indique l’identité et l’authenticité du visiteur.

Pour valider un utilisateur par rapport à l’infrastructure d’appartenance, utilisez la `Membership` de classe [ `ValidateUser` méthode](https://msdn.microsoft.com/library/system.web.security.membership.validateuser.aspx). Le `ValidateUser` méthode accepte deux paramètres d’entrée -  *`username`*  et  *`password`*  - et retourne une valeur booléenne qui indique si les informations d’identification sont valides. Par exemple lors de la `CreateUser` méthode que nous avons examiné dans le didacticiel précédent, le `ValidateUser` méthode délègue la validation réelle pour le fournisseur d’appartenances configuré.

Le `SqlMembershipProvider` valide les informations d’identification fournies en obtenant le mot de passe de l’utilisateur spécifié par le `aspnet_Membership_GetPasswordWithFormat` procédure stockée. N’oubliez pas que le `SqlMembershipProvider` stocke les mots de passe utilisateurs à l’aide d’un des trois formats : clair, chiffrés ou hachés. Le `aspnet_Membership_GetPasswordWithFormat` procédure stockée retourne le mot de passe dans son format brut. Les mots de passe chiffrés ou hachés, le `SqlMembershipProvider` transforme le  *`password`*  valeur passée dans le `ValidateUser` méthode en son équivalent chiffré ou haché d’état et les compare avec ce qui a été retourné à partir de la base de données. Si le mot de passe stocké dans la base de données correspond à la mise en forme mot de passe entré par l’utilisateur, les informations d’identification sont valides.

Nous allons mettre à jour notre page de connexion (~ /`Login.aspx`) afin qu’il valide les informations d’identification fournies sur le magasin d’utilisateur d’appartenance framework. Nous avons créé cette page de connexion dans le <a id="Tutorial02"> </a> [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-cs.md) didacticiel, la création d’une interface avec deux zones de texte pour le nom d’utilisateur et un mot de passe, un Mémoriser mes informations de case à cocher et un bouton de connexion (voir Figure 1). Le code valide les informations d’identification entrées par rapport à une liste codée en dur de paires nom d’utilisateur et mot de passe (Scott/mot de passe, Jisun/mot de passe et Sam/mot de passe). Dans le <a id="Tutorial03"> </a> [ *Configuration de l’authentification de formulaires et les rubriques avancées* ](../introduction/forms-authentication-configuration-and-advanced-topics-cs.md) didacticiel nous mis à jour le code de la page de connexion pour stocker des informations supplémentaires dans les formulaires ticket d’authentification `UserData` propriété.


[![Interface de la Page connexion inclut deux zones de texte, une liste case à cocher et un bouton](validating-user-credentials-against-the-membership-user-store-cs/_static/image2.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image1.png)

**Figure 1**: Interface inclut deux zones de texte de la Page connexion, une liste case à cocher et un bouton ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image3.png))


Interface utilisateur de la page de connexion peut rester inchangée, mais nous devons remplacer le bouton de connexion `Click` Gestionnaire d’événements avec le code qui valide l’utilisateur sur le magasin d’utilisateur d’appartenance framework. Mettre à jour le Gestionnaire d’événements afin que le code s’affiche comme suit :

[!code-csharp[Main](validating-user-credentials-against-the-membership-user-store-cs/samples/sample1.cs)]

Ce code est extrêmement simple. Nous allons commencer en appelant le `Membership.ValidateUser` méthode, en passant le nom d’utilisateur fourni et un mot de passe. Si cette méthode retourne la valeur true, l’utilisateur est connecté au site via le `FormsAuthentication` méthode RedirectFromLoginPage de la classe. (Comme expliqué dans la <a id="Tutorial2"> </a> [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-cs.md) (didacticiel), le `FormsAuthentication.RedirectFromLoginPage` crée les formulaires ticket d’authentification et redirige ensuite l’utilisateur à la page appropriée.) Si les informations d’identification ne sont pas valides, toutefois, le `InvalidCredentialsMessage` étiquette s’affiche, informant l’utilisateur que leur nom d’utilisateur ou le mot de passe est incorrect.

C’est tout cela !

Pour vérifier que la page de connexion fonctionne comme prévu, tentative de connexion avec l’un des comptes d’utilisateur que vous avez créé dans le didacticiel précédent. Ou, si vous n’avez pas encore créé un compte, pour commencer, créez-en un à partir de la `~/Membership/CreatingUserAccounts.aspx` page.

> [!NOTE]
> Lorsque l’utilisateur entre ses informations d’identification et envoie le formulaire de la page de connexion, les informations d’identification, y compris son mot de passe sont transmises via Internet au serveur web dans *en texte brut*. Cela signifie que les pirates informatiques espionnant le trafic réseau peut voir le nom d’utilisateur et un mot de passe. Pour éviter cela, il est essentiel pour chiffrer le trafic réseau à l’aide de [SSL Secure Socket Layers ()](http://en.wikipedia.org/wiki/Secure_Sockets_Layer). Cela garantit que les informations d’identification (comme balisage HTML de la page entière) est chiffré dès l’instant où qu'ils quittent le navigateur jusqu'à ce qu’ils sont reçus par le serveur web.


### <a name="how-the-membership-framework-handles-invalid-login-attempts"></a>Comment le Framework d’appartenance gère les tentatives de connexion non valide

Lorsqu’un visiteur atteint la page de connexion et envoie ses informations d’identification, son navigateur effectue une requête HTTP à la page de connexion. Si les informations d’identification sont valides, la réponse HTTP inclut le ticket d’authentification dans un cookie. Par conséquent, un pirate tente de s’introduire dans votre site pourrait créer un programme qui envoie des requêtes HTTP à la page de connexion avec un nom d’utilisateur valide et une estimation sur le mot de passe de manière exhaustive. Si l’estimation de mot de passe est correcte, la page de connexion retourne le cookie de ticket d’authentification, alors le programme sait qu’il a persisté lors d’une paire nom d’utilisateur/mot de passe valide. En force brute, un tel programme pourrez faites-nous de mot de passe d’un utilisateur, en particulier si le mot de passe est faible.

Pour empêcher ces attaques en force brute, l’infrastructure d’appartenance verrouille un utilisateur s’il existe un certain nombre de tentatives de connexion ayant échoué dans un certain temps. Les paramètres exacts sont configurables via les paramètres de configuration de fournisseur de l’appartenance deux suivantes :

- `maxInvalidPasswordAttempts`-Spécifie le mot de passe non valide combien tentatives sont autorisées pour l’utilisateur au sein de la période de temps avant que le compte est verrouillé. La valeur par défaut est 5.
- `passwordAttemptWindow`-Indique la période de temps en minutes pendant lesquelles le nombre spécifié de tentatives de connexion non valide provoque le compte peuvent être verrouillés. La valeur par défaut est 10.

Si un utilisateur a été verrouillé, elle ne peut pas se connecter jusqu'à ce qu’un administrateur déverrouille son compte. Lorsqu’un utilisateur est verrouillé, le `ValidateUser` méthode sera *toujours* retourner `false`, même si les informations d’identification valides sont fournies. Alors que ce comportement réduit la probabilité qu’un pirate s’arrêtera dans votre site via des méthodes de force brute, il peut finir de verrouillage d’un utilisateur valide qui a oublié simplement son mot de passe ou accidentellement a verrouillage des majuscules ou ayant un jour de la saisie incorrect.

Malheureusement, il n’existe aucun outil intégré pour le déverrouillage d’un compte d’utilisateur. Pour déverrouiller un compte, vous pouvez modifier la base de données directement - modifier la `IsLockedOut` champ dans le `aspnet_Membership` de table pour le compte d’utilisateur approprié - ou créer une interface basée sur le web répertorie les comptes avec des options pour les déverrouiller verrouillée. Nous allons examiner la création d’interfaces d’administration permettant d’accomplir des tâches liées au compte et un rôle d’utilisateur courantes dans un didacticiel futures.

> [!NOTE]
> Un des inconvénients de la `ValidateUser` méthode est que lorsque les informations d’identification fournies ne sont pas valides, il ne fournit pas d’explications sur la raison pour laquelle. Les informations d’identification peuvent être non valides, car il n’existe aucune paire nom d’utilisateur/mot de passe correspondant dans le magasin de l’utilisateur, ou parce que l’utilisateur n’a pas encore été approuvé, ou parce que l’utilisateur a été verrouillé. À l’étape 4, nous verrons comment afficher un message plus détaillé à l’utilisateur lors de leur tentative de connexion échoue.


## <a name="step-2-collecting-credentials-through-the-login-web-control"></a>Étape 2 : Collecte des informations d’identification via le contrôle Web de connexion

Le [contrôle de connexion Web](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.aspx) restitue une interface utilisateur par défaut très similaire à celui que nous avons créés dans le <a id="SKM5"> </a> [ *une vue d’ensemble de l’authentification par formulaire* ](../introduction/an-overview-of-forms-authentication-cs.md) didacticiel. L’utilisation du contrôle de connexion nous enregistre les travaux de devoir créer l’interface pour collecter les informations d’identification de visiteur s. En outre, le contrôle de connexion se connecte automatiquement l’utilisateur (en supposant que les informations d’identification soumises sont valides), ainsi l’enregistrement nous évite de devoir écrire du code.

Nous allons mettre à jour `Login.aspx`, en remplaçant l’interface créée manuellement et de code avec un contrôle de connexion. Commencez par supprimer la balise existante et du code dans `Login.aspx`. Vous pouvez supprimer la ferme ou simplement commentez-le. Pour commenter le balisage déclaratif, placez-la entre avec la `<%--` et `--%>` délimiteurs. Vous pouvez entrer manuellement ces délimiteurs, ou, comme le montre la Figure 2, vous pouvez sélectionner le texte à placer en commentaire, puis cliquez sur le commentaire pour l’icône de lignes sélectionnées dans la barre d’outils. De même, vous pouvez utiliser le commentaire pour l’icône de lignes sélectionnées en commentaire le code sélectionné dans la classe code-behind.


[![Commentez l’existant un balisage déclaratif et Code Source dans Login.aspx](validating-user-credentials-against-the-membership-user-store-cs/_static/image5.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image4.png)

**Figure 2**: commentaire à l’existant balisage déclaratif et le Code Source dans `Login.aspx` ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image6.png))


> [!NOTE]
> Le commentaire pour l’icône de lignes sélectionnées n’est pas disponible lorsque vous affichez le balisage déclaratif dans Visual Studio 2005. Si vous n’utilisez pas Visual Studio 2008, vous devez ajouter manuellement le `<%--` et `--%>` délimiteurs.


Ensuite, faites glisser un contrôle de connexion à partir de la boîte à outils sur la page et définissez ses `ID` propriété `myLogin`. À ce stade votre écran doit ressembler à la Figure 3. Notez que l’interface par défaut du contrôle connexion inclut des contrôles de zone de texte pour le nom d’utilisateur et mot de passe, un mémoriser case à cocher et un bouton dans le journal. Il existe également `RequiredFieldValidator` contrôles pour les deux zones de texte.


[![Ajouter un contrôle de la connexion à la Page](validating-user-credentials-against-the-membership-user-store-cs/_static/image8.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image7.png)

**Figure 3**: ajouter un contrôle de la connexion à la Page ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image9.png))


Et nous avons terminé ! Clic sur bouton se connecter du contrôle de connexion, une publication (postback) se produit et le contrôle de connexion appellera la `Membership.ValidateUser` méthode, en passant le nom d’utilisateur entré et le mot de passe. Si les informations d’identification ne sont pas valides, le contrôle de connexion affiche un message. Si, toutefois, les informations d’identification sont valides, le contrôle de connexion crée les formulaires ticket d’authentification et redirige l’utilisateur vers la page appropriée.

Le contrôle de connexion utilise quatre facteurs pour déterminer la page appropriée pour rediriger l’utilisateur lors d’une connexion réussie :

- Si le contrôle de connexion est sur la page de connexion comme défini par `loginUrl` est de valeur de valeur par défaut de ce paramètre dans la configuration de l’authentification de formulaires ;`Login.aspx`
- La présence d’un `ReturnUrl` paramètre querystring
- La valeur du contrôle de connexion [ `DestinationUrl` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.destinationpageurl.aspx)
- Le `defaultUrl` la valeur spécifiée dans les formulaires, les paramètres de configuration de l’authentification ; la valeur par défaut de ce paramètre est`Default.aspx`

La figure 4 illustre la façon dont le contrôle de connexion utilise ces quatre paramètres pour arriver à la décision de la page appropriée.


[![Ajouter un contrôle de la connexion à la Page](validating-user-credentials-against-the-membership-user-store-cs/_static/image11.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image10.png)

**Figure 4**: ajouter un contrôle de la connexion à la Page ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image12.png))


Prenez un moment pour tester le contrôle de connexion en visitant le site via un navigateur et connectez-vous en tant qu’un utilisateur existant dans le cadre de l’appartenance.

Interface de rendu du contrôle de la connexion est hautement configurable. Un certain nombre de propriétés qui influencent son apparence ; de plus, le contrôle de connexion peut être converti les éléments d’interface utilisateur dans un modèle pour un contrôle précis sur la disposition. Le reste de cette étape montre comment personnaliser l’apparence et la disposition.

### <a name="customizing-the-login-controls-appearance"></a>Personnaliser l’apparence du contrôle de connexion

Paramètres de propriété par défaut du contrôle de connexion restituent une interface utilisateur avec un titre (journal), contrôles d’étiquette et de zone de texte pour les entrées de nom d’utilisateur et mot de passe, un Mémoriser mes informations au prochain case à cocher et un bouton se connecter. Les apparences de ces éléments sont tous configurables via les propriétés de nombreux du contrôle de la connexion. En outre, les éléments d’interface utilisateur - telles qu’un lien vers une page pour créer un nouveau compte d’utilisateur - peuvent être ajoutés en définissant une propriété ou deux.

Prenons quelques instants pour gussy l’apparence de notre contrôle de connexion. Étant donné que le `Login.aspx` page comporte déjà du texte en haut de la page indiquant que l’ouverture de session, le titre du contrôle de la connexion est superflue. Par conséquent, effacer le [ `TitleText` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.titletext.aspx) valeur afin de supprimer le titre du contrôle de la connexion.

: Le nom d’utilisateur et mot de passe : étiquettes à gauche des deux contrôles de zone de texte peuvent être personnalisés par le biais du [ `UserNameLabelText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.usernamelabeltext.aspx) et [ `PasswordLabelText` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.passwordlabeltext.aspx), respectivement. Nous allons modifier le nom d’utilisateur : étiquette de lire le nom d’utilisateur :. Les styles de l’étiquette et la zone de texte sont configurables via les [ `LabelStyle` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.labelstyle.aspx) et [ `TextBoxStyle` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.textboxstyle.aspx), respectivement.

Mémoriser mon adresse Text (propriété) suivant temps la case à cocher peut être définie via le contrôle de connexion [ `RememberMeText property` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.remembermetext.aspx), et sa valeur par défaut vérifié l’état est configurable via le [ `RememberMeSet property` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.remembermeset.aspx) (qui par défaut, False). Pour commencer, définissez le `RememberMeSet` propriété sur True afin que la mémorisation au prochain case à cocher sera vérifiée par défaut.

Le contrôle de connexion offre deux propriétés pour ajuster la disposition de ses contrôles d’interface utilisateur. Le [ `TextLayout property` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.textlayout.aspx) indique si le nom d’utilisateur : mot de passe et : étiquettes s’affichent à gauche de leurs zones de texte correspondante (la valeur par défaut), ou supérieur. Le [ `Orientation property` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.orientation.aspx) indique si les entrées de nom d’utilisateur et mot de passe sont situées verticalement (un au-dessus de l’autre) ou horizontalement. Je vais laisser ces deux propriétés leurs valeurs par défaut, mais nous vous invitons à essayer de définir ces deux propriétés à leurs valeurs par défaut pour voir l’effet obtenu.

> [!NOTE]
> Dans la section suivante, la configuration de mise en page de connexion du contrôle, nous allons nous intéresser à l’aide de modèles pour définir la disposition précise d’éléments d’interface utilisateur du contrôle de disposition.


Encapsuler des paramètres de propriété du contrôle de connexion en définissant le [ `CreateUserText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.createusertext.aspx) et [ `CreateUserUrl` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.createuserurl.aspx) à ne pas encore inscrit ? Créer un compte ! et `~/Membership/CreatingUserAccounts.aspx`, respectivement. Cela ajoute un lien hypertexte à l’interface du contrôle de connexion pointant vers la page est créée au cours de la <a id="SKM6"> </a> [didacticiel précédent](creating-user-accounts-cs.md). Le contrôle de connexion [ `HelpPageText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.helppagetext.aspx) et [ `HelpPageUrl` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.helppageurl.aspx) et [ `PasswordRecoveryText` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.passwordrecoverytext.aspx) et [ `PasswordRecoveryUrl` depropriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.passwordrecoveryurl.aspx) fonctionnent de la même manière, des liens vers une page d’aide et une page de récupération du mot de passe de rendu.

Après avoir apporté ces modifications de propriété, balisage déclaratif et l’apparence de votre contrôle de connexion doivent ressembler à celui illustré dans la Figure 5.


[![Valeurs des propriétés du contrôle de connexion déterminent son apparence](validating-user-credentials-against-the-membership-user-store-cs/_static/image14.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image13.png)

**Figure 5**: valeurs de dicter son apparence propriétés du contrôle connexion ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image15.png))


### <a name="configuring-the-login-controls-layout"></a>Configuration de mise en page du contrôle de connexion

Interface d’utilisateur du contrôle de la connexion Web par défaut est l’interface dans le code HTML disposé `<table>`. Mais que se passe-t-il si nous avons besoin de mieux contrôler la sortie rendue ? Peut-être que vous voulez remplacer le `<table>` avec une série de `<div>` balises. Ou bien, que se passe-t-il si notre application nécessite des informations d’identification supplémentaires pour l’authentification ? Par exemple, de nombreux sites Web financiers, imposent aux utilisateurs non seulement un nom d’utilisateur et mot de passe, mais également un numéro d’Identification personnel (PIN) ou autres informations d’identification. Quel que soit les raisons, il est possible de convertir le contrôle de connexion dans un modèle à partir de laquelle nous pouvons définir explicitement balisage déclaratif de l’interface.

Nous devons faire deux choses pour mettre à jour le contrôle de connexion pour collecter les informations d’identification supplémentaires :

1. Mettre à jour l’interface du contrôle de connexion pour inclure les contrôles Web pour collecter les informations d’identification supplémentaires.
2. Substituer la logique d’authentification interne du contrôle de connexion afin qu’un utilisateur est authentifié uniquement si leur nom d’utilisateur et un mot de passe est valide et leurs informations d’identification supplémentaires sont valides, trop.

Pour mener à bien la première tâche, nous devons convertir le contrôle de connexion dans un modèle et ajouter des contrôles Web nécessaires. Comme pour la deuxième tâche, la connexion logique du contrôle d’authentification peut être remplacée en créant un gestionnaire d’événements du contrôle [ `Authenticate` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.authenticate.aspx).

Nous allons mettre à jour le contrôle de connexion afin qu’il demande aux utilisateurs leur nom d’utilisateur, le mot de passe et l’adresse de messagerie et uniquement s’authentifie l’utilisateur si l’adresse de messagerie fournie correspond à leur adresse de messagerie sur le fichier. Nous devons d’abord convertir l’interface de contrôle de la connexion à un modèle. À partir de la balise du contrôle de connexion, sélectionnez l’option Convertir en modèle.


[![Convertir le contrôle de connexion à un modèle](validating-user-credentials-against-the-membership-user-store-cs/_static/image17.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image16.png)

**Figure 6**: convertir le contrôle de connexion à un modèle ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image18.png))


> [!NOTE]
> Pour rétablir le contrôle de la connexion à sa version préalable template, cliquez sur le lien de réinitialisation de la balise du contrôle.


Convertir le contrôle de connexion à un modèle ajoute une `LayoutTemplate` à la balise du contrôle déclarative avec les éléments HTML et les contrôles Web définition de l’interface utilisateur. Comme le montre la Figure 7, la conversion du contrôle à un modèle supprime un nombre de propriétés à partir de la fenêtre Propriétés, telles que `TitleText`, `CreateUserUrl`, et ainsi de suite, étant donné que ces valeurs de propriété sont ignorés lors de l’utilisation d’un modèle.


[![Moins de propriétés sont que disponibles lorsque le contrôle de connexion est converti en un modèle](validating-user-credentials-against-the-membership-user-store-cs/_static/image20.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image19.png)

**Figure 7**: moins de propriétés sont disponibles lorsque le contrôle de connexion est converti en un modèle ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image21.png))


Le balisage HTML de la `LayoutTemplate` peut être modifié en fonction des besoins. De même, n’hésitez pas à ajouter de nouveaux contrôles Web au modèle. Toutefois, il est important de contrôles Web de base du contrôle de cette connexion restent dans le modèle et conserver qui leur est affectée `ID` valeurs. En particulier, supprimez ou ne renommez pas le `UserName` ou `Password` zones de texte, le `RememberMe` case à cocher, le `LoginButton` bouton, le `FailureText` étiquette, ou le `RequiredFieldValidator` contrôles.

Pour collecter l’adresse de messagerie du visiteur, nous devons ajouter une zone de texte pour le modèle. Ajoutez le balisage déclaratif suivant entre la ligne de table (`<tr>`) qui contient le `Password` zone de texte et de la ligne de table qui contient le Mémoriser mes informations ensuite durée de case à cocher :

[!code-aspx[Main](validating-user-credentials-against-the-membership-user-store-cs/samples/sample2.aspx)]

Après avoir ajouté le `Email` zone de texte, visitez la page via un navigateur. Comme le montre la Figure 8, interface utilisateur du contrôle de connexion inclut désormais une troisième zone de texte.


[![Le contrôle de connexion inclut désormais une zone de texte pour l’adresse de messagerie de l’utilisateur](validating-user-credentials-against-the-membership-user-store-cs/_static/image23.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image22.png)

**Figure 8**: le contrôle de connexion inclut désormais une zone de texte pour l’adresse de messagerie de l’utilisateur ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image24.png))


À ce stade, le contrôle de connexion est toujours à l’aide de la `Membership.ValidateUser` méthode pour valider les informations d’identification fournies. En conséquence, la valeur entrée dans le `Email` zone de texte n’a aucune incidence sur indique si l’utilisateur peut se connecter. À l’étape 3, nous allons examiner comment remplacer la logique d’authentification du contrôle de connexion afin que les informations d’identification sont considéré comme valides si le nom d’utilisateur et un mot de passe sont valides et l’adresse de messagerie fournie correspond à l’adresse de messagerie sur le fichier.

## <a name="step-3-modifying-the-login-controls-authentication-logic"></a>Étape 3 : Modification de la connexion d’accès logique du contrôle d’authentification

Lorsqu’un visiteur fournit son informations d’identification et clique sur que le bouton se connecter, une publication (postback) s’ensuit et la connexion de contrôle passent de son flux de travail de l’authentification. Le flux de travail démarre en déclenchant le [ `LoggingIn` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.loggingin.aspx). Les gestionnaires d’événements associés à cet événement peut être annulé le journal dans l’opération en définissant le `e.Cancel` propriété `true`.

Si le journal dans l’opération n’est pas annulé, le flux de travail passe en déclenchant le [ `Authenticate` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.authenticate.aspx). S’il existe un gestionnaire d’événements pour le `Authenticate` événement, il est chargé de déterminer si les informations d’identification fournies sont valides ou non. Si aucun gestionnaire d’événements n’est spécifié, le contrôle de connexion utilise la `Membership.ValidateUser` méthode pour déterminer la validité des informations d’identification.

Si les informations d’identification fournies sont valides, puis le ticket d’authentification par formulaires est créé, le [ `LoggedIn` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.loggedin.aspx) est déclenché, et l’utilisateur est redirigé vers la page appropriée. Si, toutefois, les informations d’identification sont considérées comme non valides, puis le [ `LoginError` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.loginerror.aspx) est déclenché et un message s’affiche pour informer l’utilisateur que leurs informations d’identification n’étaient pas valides. Par défaut, en cas d’échec de la connexion contrôle simplement définit son `FailureText` étiqueter la propriété de texte du contrôle à un message d’échec (votre tentative de connexion n’a pas réussi. Veuillez réessayer). Toutefois, si le contrôle de connexion [ `FailureAction` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.failureaction.aspx) a la valeur `RedirectToLoginPage`, puis les problèmes de contrôle de connexion un `Response.Redirect` à la page de connexion que l’ajout du paramètre querystring `loginfailure=1` (ce qui entraîne l’ouverture de session contrôle à afficher le message d’échec).

Figure 9 propose un diagramme de flux du flux de travail d’authentification.


[![Flux de travail du contrôle de la connexion d’authentification](validating-user-credentials-against-the-membership-user-store-cs/_static/image26.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image25.png)

**Figure 9**: flux de travail du contrôle compte de connexion d’authentification ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image27.png))


> [!NOTE]
> Si vous vous demandez lorsque vous utiliseriez le `FailureAction`de `RedirectToLogin` option page, considérez le scénario suivant. Maintenant notre `Site.master` page maître a actuellement le texte Hello, stranger affiché dans la colonne de gauche lorsque visité par un utilisateur anonyme, mais supposons que nous voulons remplacer ce texte par un contrôle de connexion. Ainsi, un utilisateur anonyme pour se connecter à partir de n’importe quelle page sur le site, au lieu de demander à consulter la page de connexion directement. Toutefois, si un utilisateur n’a pas pu se connecter via le contrôle de connexion rendu par la page maître, il peut être judicieux de les rediriger vers la page de connexion (`Login.aspx`), car cette page probable comprend des instructions supplémentaires, les liens et les autres aide - tels que des liens pour créer un nouveau compte ou récupérer un mot de passe - qui n’ont pas été ajoutés à la page maître.


### <a name="creating-theauthenticateevent-handler"></a>Création de la`Authenticate`Gestionnaire d’événements

Pour brancher notre logique d’authentification personnalisée, nous devons créer un gestionnaire d’événements pour le contrôle de connexion `Authenticate` événement. Création d’un gestionnaire d’événements pour le `Authenticate` événement génère la définition de gestionnaire d’événements suivante :

[!code-csharp[Main](validating-user-credentials-against-the-membership-user-store-cs/samples/sample3.cs)]

Comme vous pouvez le voir, la `Authenticate` un objet de type est passé au gestionnaire d’événements [ `AuthenticateEventArgs` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.authenticateeventargs.aspx) comme deuxième paramètre d’entrée. Le `AuthenticateEventArgs` classe contient une propriété booléenne nommée `Authenticated` qui permet de spécifier si les informations d’identification fournies sont valides. Notre tâche, est ensuite, pour écrire du code ici qui détermine si les informations d’identification fournies sont valides ou non et pour définir le `e.Authenticate` propriété en conséquence.

### <a name="determining-and-validating-the-supplied-credentials"></a>Détermination et valider les informations d’identification fournies

Utilisez le contrôle de connexion [ `UserName` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.username.aspx) et [ `Password` propriétés](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.password.aspx) pour déterminer les informations d’identification de nom d’utilisateur et mot de passe entrées par l’utilisateur. Afin de déterminer les valeurs entrées dans les contrôles Web supplémentaires (telles que la `Email` TextBox, nous avons ajouté à l’étape précédente), utilisez  *`LoginControlID`*  `.FindControl`(« *`controlID`* ») pour obtenir par programmation de référence pour le contrôle Web dans le modèle dont `ID` propriété est égale à  *`controlID`* . Par exemple, pour obtenir une référence à la `Email` zone de texte, utilisez le code suivant :

`TextBox EmailTextBox = myLogin.FindControl("Email") as TextBox;`

Afin de valider les informations d’identification de l’utilisateur, nous devons faire deux choses :

1. Vérifiez que le nom d’utilisateur fourni et un mot de passe sont valides
2. Assurez-vous que les adresse de messagerie entrée correspond à l’adresse de messagerie sur le fichier pour l’utilisateur tente de se connecter

Pour accomplir la première vérification, nous pouvons utiliser simplement la `Membership.ValidateUser` méthode comme nous l’avons vu à l’étape 1. Pour le second contrôle, nous devons déterminer l’adresse de messagerie de l’utilisateur afin que nous pouvons le comparer à l’adresse de messagerie qu’ils entrés dans le contrôle de zone de texte. Pour obtenir plus d’informations sur un utilisateur particulier, utilisez le `Membership` de classe [ `GetUser` méthode](https://msdn.microsoft.com/library/system.web.security.membership.getuser.aspx).

Le `GetUser` méthode a plusieurs surcharges. Si utilisée sans passer tous les paramètres, il retourne des informations sur l’utilisateur actuellement connecté. Pour obtenir plus d’informations sur un utilisateur particulier, appelez `GetUser` en passant son nom d’utilisateur. Dans les deux cas, `GetUser` retourne un [ `MembershipUser` objet](https://msdn.microsoft.com/library/system.web.security.membershipuser.aspx), qui possède des propriétés comme `UserName`, `Email`, `IsApproved`, `IsOnline`, et ainsi de suite.

Le code suivant implémente ces deux contrôles. Si les deux passez, puis `e.Authenticate` a la valeur `true`, sinon il est affecté `false`.

[!code-csharp[Main](validating-user-credentials-against-the-membership-user-store-cs/samples/sample4.cs)]

Avec ce code en place, tentent de se connecter tant qu’utilisateur valid, entrez le nom d’utilisateur correct, le mot de passe et l’adresse de messagerie. Essayez de nouveau, mais cette fois utiliser délibérément une adresse de messagerie incorrect (voir la Figure 10). Enfin, essayez une troisième fois à l’aide d’un nom d’utilisateur n’existe pas. Dans le premier cas vous devez être correctement ouvert une session le site, mais dans les deux derniers cas vous devez voir le message d’informations d’identification non valide du contrôle de la connexion.


[![Tito ne peut pas se connecter lorsque vous fournissez une adresse de messagerie Incorrect](validating-user-credentials-against-the-membership-user-store-cs/_static/image29.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image28.png)

**La figure 10**: Tito ne peut pas journal dans lorsque en fournissant une adresse de messagerie Incorrect ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image30.png))


> [!NOTE]
> Comme indiqué dans la section Comment l’appartenance au Framework gère non valide tentatives de connexion à l’étape 1, lorsque la `Membership.ValidateUser` méthode est appelée et reçoit des informations d’identification non valides, il effectue le suivi de la tentative de connexion non valide et verrouille l’utilisateur si elles dépassent une certaine seuil de tentatives non valides dans une fenêtre de temps spécifié. Depuis notre logique d’authentification personnalisée appelle le `ValidateUser` (méthode), un mot de passe pour un nom d’utilisateur valide incrémente le compteur de tentatives de connexion non valide, mais ce compteur n’est pas incrémenté dans le cas où le nom d’utilisateur et un mot de passe sont valides, mais le adresse de messagerie est incorrecte. Sans doute, ce comportement est approprié, car il est peu probable qu’un pirate doit connaître le nom d’utilisateur et le mot de passe, mais devez utiliser des techniques de force brute pour déterminer l’adresse de messagerie de l’utilisateur.


## <a name="step-4-improving-the-login-controls-invalid-credentials-message"></a>Étape 4 : Amélioration de Message d’informations d’identification non valide du contrôle de connexion

Lorsqu’un utilisateur tente de se connecter avec des informations d’identification non valides, le contrôle de connexion affiche un message expliquant que la tentative de connexion a échoué. En particulier, le contrôle affiche le message spécifié par son [ `FailureText` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.failuretext.aspx), qui a une valeur par défaut de votre tentative de connexion n’a pas réussi. Réessayez.

Rappelez-vous qu’il existe de nombreuses raisons pour lesquelles les informations d’identification de l’utilisateur n’est pas valides :

- Le nom d’utilisateur n’existe pas
- Le nom d’utilisateur existe, mais le mot de passe n’est pas valide
- Le nom d’utilisateur et un mot de passe sont valides, mais l’utilisateur n’est pas encore approuvé.
- Le nom d’utilisateur et un mot de passe sont valides, mais l’utilisateur est verrouillé hors (très probablement parce qu’il a dépassé le nombre de tentatives de connexion non valide dans l’intervalle de temps spécifié)

Et peut avoir d’autres raisons lors de l’utilisation de logique d’authentification personnalisée. Par exemple, avec le code que nous a écrit à l’étape 3, le nom d’utilisateur et mot de passe est valide, mais l’adresse de messagerie est peut-être incorrecte.

Quelle que soit la raison pour laquelle les informations d’identification ne sont pas valides, le contrôle de connexion affiche le même message d’erreur. Ce manque de commentaires peut prêter à confusion pour un utilisateur dont le compte n’a pas encore été approuvé ou qui a été verrouillé. Avec un peu de travail, cependant, nous ayons le contrôle de connexion affiche un message plus approprié.

Chaque fois qu’un utilisateur tente de se connecter avec des informations d’identification non valides, le contrôle de connexion déclenche son `LoginError` événement. Créer un gestionnaire d’événements pour cet événement et ajoutez le code suivant :

[!code-csharp[Main](validating-user-credentials-against-the-membership-user-store-cs/samples/sample5.cs)]

Le code ci-dessus démarre en définissant le contrôle de connexion `FailureText` propriété la valeur par défaut (votre tentative de connexion n’a pas réussi. Veuillez réessayer). Il vérifie ensuite si le nom d’utilisateur fourni est mappé à un compte d’utilisateur existant. Si, par conséquent, il consulte résultant `MembershipUser` l’objet `IsLockedOut` et `IsApproved` propriétés afin de déterminer si le compte a été verrouillé ou n’a pas encore été approuvé. Dans les deux cas, le `FailureText` propriété est mise à jour à une valeur correspondante.

Pour tester ce code, essayez de délibérément connectez-vous en tant qu’un utilisateur existant, mais d’utiliser un mot de passe incorrect. Effectuez cette opération cinq fois dans une ligne dans un délai de 10 minutes et le compte est verrouillé. Comme le montre la Figure 11, passe tentatives seront toujours (même avec le mot de passe), mais ne maintenant affiche plus descriptif votre compte a été verrouillé en raison de trop nombreuses tentatives de connexion non valide. Contactez l’administrateur pour que votre message de déverrouillage de compte.


[![Tito effectué trop de tentatives de connexion non valide et a été verrouillé](validating-user-credentials-against-the-membership-user-store-cs/_static/image32.png)](validating-user-credentials-against-the-membership-user-store-cs/_static/image31.png)

**Figure 11**: Tito effectuée trop nombreuses tentatives non valides connexion et a été verrouillé ([cliquez pour afficher l’image en taille réelle](validating-user-credentials-against-the-membership-user-store-cs/_static/image33.png))


## <a name="summary"></a>Récapitulatif

Préalable ce didacticiel, notre page de connexion validé les informations d’identification fournies par rapport à une liste codée en dur de paires nom d’utilisateur/mot de passe. Dans ce didacticiel, nous avons mis à jour la page pour valider les informations d’identification dans le cadre de l’appartenance. À l’étape 1, nous avons étudié à l’aide de la `Membership.ValidateUser` méthode par programmation. À l’étape 2, nous avons remplacé notre interface utilisateur créé manuellement et le code avec le contrôle de connexion.

Le contrôle de connexion affiche une interface utilisateur de connexion standard et valide automatiquement les informations d’identification de l’utilisateur par rapport à l’infrastructure d’appartenance. En outre, en cas d’informations d’identification valides, le contrôle de connexion connecte l’utilisateur via l’authentification par formulaire. En bref, une expérience utilisateur de connexion entièrement fonctionnel est disponible en faisant simplement glisser le contrôle de connexion sur une page, aucun balisage déclaratif supplémentaire ou le code nécessaire. Plus, le contrôle de connexion est hautement personnalisable, ce qui permet un niveau précis de contrôle sur les deux le rendu utilisateur interface logique d’authentification et.

À ce stade les visiteurs de notre site Web peuvent créer un nouveau compte d’utilisateur et le journal dans le site, mais nous devons encore examiner de restreindre l’accès aux pages en fonction de l’utilisateur authentifié. Actuellement, aucun utilisateur, authentifié ou anonyme, permettre afficher n’importe quelle page sur notre site. En même temps que le contrôle de l’accès aux pages de notre site sur une base par utilisateur, nous pourrions avoir certaines pages dont la fonctionnalité dépend de l’utilisateur. Le didacticiel suivant explique comment limiter l’accès et les fonctionnalités dans la page en fonction de l’utilisateur connecté.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Affichage des Messages personnalisés à verrouillé et les utilisateurs Non approuvé](http://aspnet.4guysfromrolla.com/articles/050306-1.aspx)
- [Examen de ASP.NET de 2.0 l’appartenance, rôles et profil](http://aspnet.4guysfromrolla.com/articles/120705-1.aspx)
- [Comment : Créer une Page de connexion ASP.NET](https://msdn.microsoft.com/library/ms178331.aspx)
- [Documentation technique de contrôle de connexion](https://msdn.microsoft.com/library/system.web.ui.webcontrols.login.aspx)
- [L’utilisation des contrôles de connexion](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/security/login.aspx)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et Michael Olivero. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4guysfromrolla.com).

>[!div class="step-by-step"]
[Précédent](creating-user-accounts-cs.md)
[Suivant](user-based-authorization-cs.md)
