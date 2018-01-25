---
uid: web-forms/overview/older-versions-security/admin/recovering-and-changing-passwords-vb
title: "La récupération et la modification des mots de passe (VB) | Documents Microsoft"
author: rick-anderson
description: "ASP.NET inclut deux contrôles Web qui contribuent à la récupération et la modification des mots de passe. Le contrôle PasswordRecovery permet à un visiteur de récupérer son pa perdu..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/01/2008
ms.topic: article
ms.assetid: f9adcb5d-6d70-4885-a3bf-ed95efb4da1a
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/admin/recovering-and-changing-passwords-vb
msc.type: authoredcontent
ms.openlocfilehash: b78469858483a9501a0f73d1c894e29ae0a99122
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="recovering-and-changing-passwords-vb"></a>La récupération et la modification des mots de passe (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/6/0/e/60e1bd94-e5f9-4d5a-a079-f23c98f4f67d/VB.13.zip) ou [télécharger le PDF](http://download.microsoft.com/download/6/0/e/60e1bd94-e5f9-4d5a-a079-f23c98f4f67d/aspnet_tutorial13_ChangingPasswords_vb.pdf)

> ASP.NET inclut deux contrôles Web qui contribuent à la récupération et la modification des mots de passe. Le contrôle PasswordRecovery permet un visiteur récupérer son mot de passe perdu. Le contrôle ChangePassword permet à l’utilisateur à mettre à jour son mot de passe. Comme les autres contrôles associées à la connexion de Web, nous avons vu tout au long de cette série de didacticiels, le PasswordRecovery et ChangePassword des contrôles avec l’infrastructure de l’appartenance en arrière-plan pour réinitialiser ou modifier des mots de passe utilisateurs.


## <a name="introduction"></a>Introduction

Entre les sites Web pour mon bank société, opérateur, les comptes de messagerie et les portails web personnalisés, I, comme la plupart des personnes, ont des dizaines de différents mots de passe à mémoriser. Avec des informations d’identification autant à mémoriser ces jours, il n’est pas rare qu’oublient leur mot de passe. Pour prendre en compte, sites Web qui offrent des comptes d’utilisateur doivent inclure un moyen pour un utilisateur de récupérer son mot de passe. Ce processus implique généralement la génération d’un nouveau mot de passe aléatoire et le courrier à l’adresse de messagerie de l’utilisateur sur le fichier. Après avoir reçu le mot de passe, la plupart des utilisateurs revenez sur le site et modifier leur mot de passe à partir de le généré de manière aléatoire à un plus facile à mémoriser.

ASP.NET inclut deux contrôles Web qui contribuent à la récupération et la modification des mots de passe. Le contrôle PasswordRecovery permet un visiteur récupérer son mot de passe perdu. Le contrôle ChangePassword permet à l’utilisateur à mettre à jour son mot de passe. Comme les autres contrôles associées à la connexion de Web, nous avons vu tout au long de cette série de didacticiels, le PasswordRecovery et ChangePassword des contrôles avec l’infrastructure de l’appartenance en arrière-plan pour réinitialiser ou modifier des mots de passe utilisateurs.

Dans ce didacticiel, nous allons examiner à l’aide de ces deux contrôles. Nous allons voir également comment modifier par programmation et de réinitialisation de mot de passe d’un utilisateur via le `MembershipUser` de classe `ChangePassword` et `ResetPassword` méthodes.

## <a name="step-1-helping-users-recover-lost-passwords"></a>Étape 1 : Aider les utilisateurs Recover perdu les mots de passe

Tous les sites Web qui prennent en charge les comptes d’utilisateurs doivent fournir aux utilisateurs un mécanisme pour récupérer leur mot de passe oublié. La bonne nouvelle est que l’implémentation de ces fonctionnalités dans ASP.NET est un jeu d’enfant grâce aux contrôle PasswordRecovery Web. Le contrôle PasswordRecovery restitue une interface qui vous invite à entrer leur nom d’utilisateur de l’utilisateur et, si nécessaire, la réponse à leurs questions de sécurité. Il envoie ensuite l’utilisateur son mot de passe.

> [!NOTE]
> Étant donné que les messages électroniques sont transmis sur le réseau en texte brut présente des risques de sécurité à l’envoi d’un mot de passe utilisateur par courrier électronique.


Le contrôle PasswordRecovery se compose de trois vues :

- **Nom d’utilisateur** -demande leur nom d’utilisateur. Il s’agit de la vue initiale.
- **Question**-affiche la question de sécurité et le nom d’utilisateur de l’utilisateur sous forme de texte, ainsi que d’une zone de texte pour l’utilisateur à entrer la réponse à sa question de sécurité.
- **Réussite**-affiche un message informant l’utilisateur que son mot de passe a été envoyé par e-mail.

Les vues affichent et actions effectuées par le contrôle PasswordRecovery varient selon les paramètres de configuration d’appartenance suivantes :

- `RequiresQuestionAndAnswer`
- `EnablePasswordRetrieval`
- `EnablePasswordReset`

L’infrastructure de l’appartenance `RequiresQuestionAndAnswer` paramètre indique si les utilisateurs doivent spécifier une question de sécurité et une réponse lors de l’inscription d’un compte. Comme expliqué dans la <a id="_msoanchor_1"> </a> [ *création de comptes utilisateur* ](../membership/creating-user-accounts-vb.md) if (didacticiel), `RequiresQuestionAndAnswer` a la valeur True (valeur par défaut) les interface de CreateUserWizard inclut la zone de texte contrôles pour le nouvel utilisateur question de sécurité et de la réponse ; Si `RequiresQuestionAndAnswer` est False, aucune information n’est collectée. De même, si `RequiresQuestionAndAnswer` a la valeur True, le contrôle PasswordRecovery affiche à la Question afficher une fois que l’utilisateur a entré son nom d’utilisateur, le mot de passe est récupérée uniquement si l’utilisateur entre la réponse de sécurité correct. Si `RequiresQuestionAndAnswer` est False, toutefois, le contrôle PasswordRecovery passe directement à partir de la vue du nom d’utilisateur à la vue de la réussite.

Une fois que l’utilisateur a entré son nom d’utilisateur - ou une réponse de son nom d’utilisateur et de sécurité, si `RequiresQuestionAndAnswer` est True - le PasswordRecovery e-mail est envoyé à l’utilisateur son mot de passe. Si le `EnablePasswordRetrieval` option est définie sur True, puis l’utilisateur est envoyé par courrier électronique leur mot de passe actuel. Si elle est définie sur False et `EnablePasswordReset` est définie sur True, le contrôle PasswordRecovery génère un nouveau mot de passe aléatoire pour l’utilisateur, puis envoie par courrier électronique à ce nouveau mot de passe pour les. Si les deux `EnablePasswordRetrieval` et `EnablePasswordReset` ont la valeur False, le contrôle PasswordRecovery lève une exception.

> [!NOTE]
> N’oubliez pas que le `SqlMembershipProvider` stocke les mots de passe utilisateurs dans un des trois formats : effacer, Hashed (la valeur par défaut) ou le chiffrement. Le mécanisme de stockage utilisé dépend des paramètres de configuration de l’appartenance ; l’application de démonstration utilise le format de mot de passe haché. Lorsque vous utilisez le format de mot de passe haché la `EnablePasswordRetrieval` option doit être définie sur False, car le système ne peut pas déterminer le mot de passe réel de l’utilisateur à partir de la version de hachage stockée dans la base de données.


La figure 1 illustre comment l’interface et le comportement de la PasswordRecovery est influencé par la configuration de l’appartenance.


[![Les RequiresQuestionAndAnswer, EnablePasswordRetrieval et EnablePasswordReset influencent l’apparence et le comportement du contrôle PasswordRecovery](recovering-and-changing-passwords-vb/_static/image2.png)](recovering-and-changing-passwords-vb/_static/image1.png)

**Figure 1**: le `RequiresQuestionAndAnswer`, `EnablePasswordRetrieval`, et `EnablePasswordReset` influencer l’apparence et le comportement du contrôle PasswordRecovery ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image3.png))


> [!NOTE]
> Dans le <a id="_msoanchor_2"> </a> [ *création du schéma de l’appartenance dans SQL Server* ](../membership/creating-the-membership-schema-in-sql-server-vb.md) didacticiel, nous avons configuré le fournisseur d’appartenances en définissant `RequiresQuestionAndAnswer` sur True, `EnablePasswordRetrieval` à La valeur est False, et `EnablePasswordReset` sur True.


### <a name="using-the-passwordrecovery-control"></a>L’utilisation du contrôle PasswordRecovery

Examinons à présent à l’aide du contrôle PasswordRecovery dans une page ASP.NET. Ouvrez `RecoverPassword.aspx` et faites glisser et déposez un contrôle PasswordRecovery à partir de la boîte à outils vers le concepteur ; définir son `ID` à `RecoverPwd`. Comme les contrôles de connexion et CreateUserWizard Web, les vues du contrôle PasswordRecovery restituent une riche interface composite qui inclut des étiquettes, des zones de texte, des boutons et des contrôles de validation. Vous pouvez personnaliser l’apparence des vues via les propriétés de style du contrôle ou en convertissant les vues dans des modèles. J’ai laisser ce champ comme un exercice pour le lecteur intéressé.

Lorsqu’un utilisateur visite cette page, elle sera entrer son nom d’utilisateur et cliquez sur le bouton Envoyer. Étant donné que nous avons défini le `RequiresQuestionAndAnswer` propriété sur True dans nos paramètres de configuration de l’appartenance, le PasswordRecovery contrôle va ensuite afficher la vue de la Question. Une fois que l’utilisateur entre son réponse de sécurité appropriées et clique sur Envoyer, le contrôle PasswordRecovery mettre à jour le mot de passe et d’une généré de façon aléatoire et ce mot de passe à l’adresse de messagerie sur le fichier de messagerie. Tout ceci est possible sans nous avoir à écrire une seule ligne de code !

Avant de tester cette page, il existe un dernier élément de configuration à ont tendance à : nous avons besoin spécifier les paramètres de remise de courrier dans `Web.config`. Le contrôle PasswordRecovery s’appuie sur ces paramètres pour envoyer le courrier électronique.

La configuration de remise du courrier est spécifiée via la [ `<system.net>` élément](https://msdn.microsoft.com/library/6484zdc1.aspx)de [ `<mailSettings>` élément](https://msdn.microsoft.com/library/w355a94k.aspx). Utilisez le [ `<smtp>` élément](https://msdn.microsoft.com/library/ms164240.aspx) pour indiquer que la méthode de remise et la valeur par défaut à partir de l’adresse. Le balisage suivant configure les paramètres de messagerie pour utiliser un serveur SMTP sur le réseau nommé `smtp.example.com` sur le port 25 et avec les informations d’identification de nom d’utilisateur/mot de passe du nom d’utilisateur et mot de passe.

> [!NOTE]
> `<system.net>`est un élément enfant de la racine de `<configuration>` élément et un frère du `<system.web>`. Par conséquent, ne placez pas le `<system.net>` élément dans le `<system.web>` élément ; au lieu de cela, placez-le au même niveau.


[!code-xml[Main](recovering-and-changing-passwords-vb/samples/sample1.xml)]

En plus d’utiliser un serveur SMTP sur le réseau, vous pouvez également spécifier un répertoire de collecte où envoyer des messages par courrier électronique doivent être déposés.

Une fois que vous avez configuré les paramètres SMTP, visitez le `RecoverPassword.aspx` page via un navigateur. Essayez tout d’abord entrer un nom d’utilisateur qui n’existe pas dans le magasin de l’utilisateur. Comme le montre la Figure 2, le contrôle PasswordRecovery affiche un message indiquant que les informations utilisateur ne sont pas accessible. Le texte du message peut être personnalisé par le biais du contrôle [ `UserNameFailureText` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.passwordrecovery.usernamefailuretext.aspx).


[![Un Message d’erreur s’affiche si un nom d’utilisateur non valide est entré.](recovering-and-changing-passwords-vb/_static/image5.png)](recovering-and-changing-passwords-vb/_static/image4.png)

**Figure 2**: un Message d’erreur s’affiche si un nom d’utilisateur non valide est entré ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image6.png))


Maintenant, entrez un nom d’utilisateur. Utilisez le nom d’utilisateur d’un compte dans le système avec une adresse de messagerie que vous pouvez accéder et répondre de dont la sécurité vous connaissez. Une fois en entrant le nom d’utilisateur et clique sur Envoyer, le contrôle PasswordRecovery affiche sa vue de la Question. Comme l’affichage du nom d’utilisateur, si vous entrez incorrecte répondre à ce contrôle de PasswordRecovery affiche un message d’erreur (voir Figure 3). Utilisez le [ `QuestionFailureText` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.passwordrecovery.questionfailuretext.aspx) pour personnaliser ce message d’erreur.


[![Un Message d’erreur s’affiche si l’utilisateur entre une réponse de sécurité non valide](recovering-and-changing-passwords-vb/_static/image8.png)](recovering-and-changing-passwords-vb/_static/image7.png)

**Figure 3**: un Message d’erreur s’affiche si l’utilisateur entre une réponse de sécurité non valide ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image9.png))


Enfin, entrez la réponse de sécurité appropriées et cliquez sur Envoyer. En arrière-plan, le contrôle PasswordRecovery génère un mot de passe aléatoire, il attribue au compte d’utilisateur, envoie un message électronique pour informer l’utilisateur de son nouveau mot de passe (voir Figure 4), puis affiche la vue de la réussite.


[![L’utilisateur reçoit un message électronique avec son nouveau mot de passe](recovering-and-changing-passwords-vb/_static/image11.png)](recovering-and-changing-passwords-vb/_static/image10.png)

**Figure 4**: l’utilisateur est envoyé un courrier électronique avec son nouveau mot de passe ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image12.png))


### <a name="customizing-the-email"></a>Personnalisation de l’adresse de messagerie

La messagerie par défaut envoyé par le contrôle PasswordRecovery est plutôt terne (voir Figure 4). Le message est envoyé à partir du compte spécifié dans le `<smtp>` l’élément `from` attribut avec le mot de passe de l’objet et le corps de texte brut :

Retournez au site et connectez-vous en utilisant les informations suivantes.

Nom d’utilisateur : *nom d’utilisateur*

mot de passe : *mot de passe*

Ce message peut être personnalisé par programme via un gestionnaire d’événements du contrôle PasswordRecovery [ `SendingMail` événement](https://msdn.microsoft.com/library/system.web.ui.webcontrols.passwordrecovery.sendingmail.aspx), ou de façon déclarative par la [ `MailDefinition` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.passwordrecovery.maildefinition.aspx). Examinons ces deux options.

Le `SendingMail` événement est déclenché juste avant que le message électronique est envoyé et est notre dernière possibilité ajustez par programmation le message électronique. Lorsque cet événement est déclenché, le Gestionnaire d’événements est passé à un objet de type [ `MailMessageEventArgs` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.mailmessageeventargs.aspx), dont `Message` propriété contient une référence à l’e-mail sur le point d’être envoyé.

Créer un gestionnaire d’événements pour le `SendingMail` événement et ajoutez le code suivant, qui ajoute par programmation `webmaster@example.com` à la liste CC.

[!code-vb[Main](recovering-and-changing-passwords-vb/samples/sample2.vb)]

Le message électronique peut également être configuré via moyen déclaratif. De la PasswordRecovery `MailDefinition` propriété est un objet de type [ `MailDefinition` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.maildefinition.aspx). Le `MailDefinition` classe possède des propriétés de courrier électronique, y compris `From`, `CC`, `Priority`, `Subject`, `IsBodyHtml`, `BodyFileName`et d’autres. Pour commencer, définissez la [ `Subject` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.maildefinition.subject.aspx) un nom plus descriptif que celui utilisé par défaut (mot de passe), telles que votre mot de passe a été réinitialisé en cours...

Pour personnaliser le corps du message électronique, que nous devons créer un fichier de modèle par courrier électronique séparé qui contient le contenu de. Commencez par créer un nouveau dossier dans le site Web nommé `EmailTemplates`. Ensuite, ajoutez un nouveau fichier texte dans ce dossier nommé `PasswordRecovery.txt` et ajoutez le contenu suivant :

[!code-aspx[Main](recovering-and-changing-passwords-vb/samples/sample3.aspx)]

Notez l’utilisation des espaces réservés `<%UserName%>` et `<%Password%>`. Le contrôle PasswordRecovery remplace automatiquement ces deux espaces réservés avec le nom d’utilisateur et mot de passe récupéré avant d’envoyer le message électronique de l’utilisateur.

Enfin, pointez le `MailDefinition`de [ `BodyFileName` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.maildefinition.bodyfilename.aspx) pour le modèle de courrier électronique que nous venons de créer (`~/EmailTemplates/PasswordRecovery.txt`).

Une fois ces modifications réexaminer les `RecoverPassword.aspx` page et entrez votre nom d’utilisateur et de sécurité, répondez-y. Vous recevez doit un e-mail qui ressemble à celui de la Figure 5. Notez que `webmaster@example.com` a été CC serait et que l’objet et le corps ont été mis à jour.


[![Le sujet, le corps et la liste CC ont été mis à jour](recovering-and-changing-passwords-vb/_static/image14.png)](recovering-and-changing-passwords-vb/_static/image13.png)

**Figure 5**: l’objet, le corps et CC liste ont été mis à jour ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image15.png))


Pour envoyer un courrier électronique au format HTML jeu [ `IsBodyHtml` ](https://msdn.microsoft.com/library/system.web.ui.webcontrols.maildefinition.isbodyhtml.aspx) True (la valeur par défaut est False) et la mise à jour le modèle de courrier électronique pour inclure du code HTML.

Le `MailDefinition` propriété n’est pas unique à la classe PasswordRecovery. Comme nous allons le voir dans l’étape 2, le contrôle ChangePassword offre également une `MailDefinition` propriété. En outre, le contrôle CreateUserWizard inclut une telle propriété que vous pouvez configurer pour envoyer automatiquement un message électronique de bienvenue aux nouveaux utilisateurs.

> [!NOTE]
> Actuellement il n’y a aucun lien dans le volet de navigation gauche pour atteindre le `RecoverPassword.aspx` page. Un utilisateur souhaitent uniquement sur cette page si elle n’a pas pu se connecter au site avec succès. Par conséquent, mettre à jour le `Login.aspx` page pour inclure un lien vers le `RecoverPassword.aspx` page.


### <a name="programmatically-resetting-a-users-password"></a>Par programmation la réinitialisation de mot de passe d’un utilisateur

La réinitialisation de mot de passe d’un utilisateur le PasswordRecovery lorsque des appels de contrôle le `MembershipUser` l’objet [ `ResetPassword` méthode](https://msdn.microsoft.com/library/system.web.security.membershipuser.resetpassword.aspx). Cette méthode présente deux surcharges :

- **[`ResetPassword`](https://msdn.microsoft.com/library/d94bdzz2.aspx)**-Réinitialise le mot de passe d’un utilisateur. Utilisez cette surcharge si `RequiresQuestionAndAnswer` a la valeur False.
- **[`ResetPassword(securityAnswer)`](https://msdn.microsoft.com/library/d90zte4w.aspx)**-réinitialise uniquement si de mot de passe d’un utilisateur fourni *securityAnswer* est correct. Utilisez cette surcharge si `RequiresQuestionAndAnswer` a la valeur True.

Ces deux surcharges retournent le mot de passe de nouveau, généré de manière aléatoire.

Comme avec les autres méthodes dans le cadre de l’appartenance, les `ResetPassword` délégués de la méthode pour le fournisseur configuré. Le `SqlMembershipProvider` appelle la `aspnet_Membership_ResetPassword` procédure stockée, passant dans le nom d’utilisateur, le nouveau mot de passe et la réponse de mot de passe fourni, parmi les autres champs. La procédure stockée permet de s’assurer que la réponse de mot de passe correspond et met à jour le mot de passe.

Quelques remarques d’implémentation de bas niveau :

- Un utilisateur verrouillé Impossible de réinitialiser son mot de passe. Toutefois, un utilisateur non approuvé peut. Nous aborderons le verrouillage des et approuvé états plus en détail dans les <a id="_msoanchor_3"> </a> [ *Unlocking et approbation de l’utilisateur* ](unlocking-and-approving-user-accounts-vb.md) didacticiel de comptes.
- Si la réponse de mot de passe est incorrecte, le nombre de tentatives de réponse de mot de passe de l’utilisateur est incrémenté. Si un nombre spécifié de tentatives de réponse de sécurité non valides se produire dans une fenêtre de temps spécifié, l’utilisateur est verrouillé.

### <a name="a-word-on-how-the-random-passwords-are-generated"></a>Un mot sur la façon dont les mots de passe aléatoires sont générés.

Les mots de passe généré aléatoirement indiqués dans les messages électroniques dans les Figures 4 et 5 sont créés par la classe d’appartenance [ `GeneratePassword` méthode](https://msdn.microsoft.com/library/system.web.security.membership.generatepassword.aspx). Cette méthode accepte deux paramètres d’entrée d’entier - *longueur* et *numberOfNonAlphanumericCharacters* - et retourne une chaîne au moins *longueur* caractères long avec à la moins *numberOfNonAlphanumericCharacters* nombre de caractères non alphanumériques. Lorsque cette méthode est appelée à partir de dans les contrôles Web d’associées à la connexion ou de classes d’appartenance, les valeurs pour ces deux paramètres sont déterminées par la configuration de l’appartenance `MinRequiredPasswordLength` et `MinRequiredNonalphanumericCharacters` propriétés, ce qui nous la valeur 7 et 1, respectivement.

Le `GeneratePassword` méthode utilise un générateur de nombres aléatoires fort pour vous assurer qu’il n’y a aucun écart dans les caractères aléatoires sont sélectionnés. En outre, `GeneratePassword` est `Public`, ce qui signifie que vous pouvez l’utiliser directement à partir de votre application ASP.NET si vous avez besoin générer des chaînes aléatoires ou des mots de passe.

> [!NOTE]
> Le `SqlMembershipProvider` classe génère toujours un mot de passe aléatoire au moins 14 caractères, par conséquent, si `MinRequiredPasswordLength` est inférieure à 14 alors sa valeur est ignorée.


## <a name="step-2-changing-passwords"></a>Étape 2 : Modification des mots de passe

Les mots de passe généré de façon aléatoire sont difficiles à mémoriser. Envisagez le mot de passe indiqué dans la Figure 4 : `WWGUZv(f2yM:Bd`. Essayez de validation qui, à la mémoire ! Bien entendu, après un mot de passe généré de façon aléatoire de ce type est envoyé à un utilisateur, elle vous souhaitez modifier le mot de passe pour un nom plus explicite.

Utilisez le contrôle ChangePassword pour créer une interface pour un utilisateur de modifier son mot de passe. Beaucoup comme le contrôle PasswordRecovery, le contrôle ChangePassword se compose de deux vues : changement de mot de passe et le succès. La vue de modification de mot de passe invite l’utilisateur pour leurs mots de passe anciennes et nouvelles. Fonction en fournissant le mot de passe et un nouveau mot de passe qui répond à la longueur minimale, les exigences de caractère non alphanumérique, le contrôle ChangePassword met à jour le mot de passe et affiche la vue de la réussite.

> [!NOTE]
> Le contrôle ChangePassword modifie le mot de passe en appelant le `MembershipUser` l’objet [ `ChangePassword` méthode](https://msdn.microsoft.com/library/system.web.security.membershipuser.changepassword.aspx). La méthode ChangePassword accepte deux `String` d’entrée de paramètres - *oldPassword* et *newPassword*- et met à jour le compte d’utilisateur avec le *newPassword*, en supposant que fourni *oldPassword* est correct.


Ouvrez le `ChangePassword.aspx` page et ajouter un contrôle ChangePassword à la page, en le nommant `ChangePwd`. À ce stade, la vue de conception doit afficher la modification de mot de passe permet d’afficher (voir Figure 6). Comme avec le contrôle PasswordRecovery, vous pouvez basculer entre les vues via la balise du contrôle. En outre, apparitions de ces volets sont personnalisables via les propriétés de style assorties ou en les convertissant en un modèle.


[![Ajouter un contrôle ChangePassword à la Page](recovering-and-changing-passwords-vb/_static/image17.png)](recovering-and-changing-passwords-vb/_static/image16.png)

**Figure 6**: ajouter un contrôle ChangePassword à la Page ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image18.png))


Le contrôle ChangePassword peut mettre à jour le mot de passe de l’utilisateur actuellement connecté *ou* le mot de passe d’un autre utilisateur spécifié. Comme le montre la Figure 6, la vue de modification de mot de passe par défaut affiche les contrôles de zone de texte seulement trois : un pour l’ancien mot de passe et deux pour le nouveau mot de passe. Cette interface par défaut est utilisée pour mettre à jour le mot de passe de l’utilisateur actuellement connecté.

Pour utiliser le contrôle ChangePassword pour mettre à jour le mot de passe d’un autre utilisateur, définissez du contrôle [ `DisplayUserName` propriété](https://msdn.microsoft.com/library/system.web.ui.webcontrols.changepassword.displayusername.aspx) sur True. Cela ajoute une quatrième zone de texte à la page invitant à dont un mot de passe pour modifier le nom d’utilisateur de l’utilisateur.

Paramètre `DisplayUserName` à la valeur True est utile si vous souhaitez permettre à un utilisateur déconnecté de modifier son mot de passe sans avoir à se connecter. Personnellement, je pense qu’il n’existe aucun problème avec nécessitant un utilisateur de connexion avant de lui permettant de modifier son mot de passe. Par conséquent, laissez `DisplayUserName` défini sur False (sa valeur par défaut). Dans cette décision, toutefois, nous essentiellement sommes empêchant les utilisateurs anonymes d’atteindre cette page. Mettre à jour les règles de l’autorisation d’URL du site afin de refuser aux utilisateurs anonymes de se connecter `ChangePassword.aspx`. Si vous avez besoin actualiser la mémoire sur la syntaxe de la règle d’autorisation URL, faire référence à la <a id="_msoanchor_4"> </a> [ *d’autorisation basée sur l’utilisateur* ](../membership/user-based-authorization-vb.md) didacticiel.

> [!NOTE]
> Il peut sembler que la `DisplayUserName` propriété est utile pour permettre aux administrateurs de modifier les mots de passe des autres utilisateurs. Toutefois, même lorsque `DisplayUserName` est définie sur True, le mot de passe doit être connu et entré. Nous aborderons les techniques permettant aux administrateurs de modifier le mot de passe à l’étape 3.


Visitez le `ChangePassword.aspx` page via un navigateur et de modifier votre mot de passe. Notez qu’un message d’erreur s’affiche si vous entrez un mot de passe ne respecte pas la longueur de mot de passe et des besoins de caractère non alphanumérique spécifiés dans la configuration de l’appartenance (voir la Figure 7).


[![Ajouter un contrôle ChangePassword à la Page](recovering-and-changing-passwords-vb/_static/image20.png)](recovering-and-changing-passwords-vb/_static/image19.png)

**Figure 7**: ajouter un contrôle ChangePassword à la Page ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image21.png))


Lors de l’entrée du mot de passe et un nouveau mot de passe valide, connecté à l’utilisateur un mot de passe est modifié et afficher le succès.

### <a name="sending-a-confirmation-email"></a>Envoyer un message électronique de Confirmation

Par défaut, le contrôle ChangePassword n’envoie pas d’un message électronique à l’utilisateur, dont un mot de passe a été mis à jour. Si vous souhaitez envoyer un courrier électronique, il suffit de configurer le contrôle `MailDefinition` propriété. Permet de configurer le contrôle ChangePassword afin que l’utilisateur reçoit un message électronique au format HTML qui contient le mot de passe.

Commencez par créer un nouveau fichier dans le `EmailTemplates` dossier nommé `ChangePassword.htm`. Ajoutez le balisage suivant :

[!code-html[Main](recovering-and-changing-passwords-vb/samples/sample4.html)]

Ensuite, définissez le contrôle de ChangePassword `MailDefinition` la propriété `BodyFileName`, `IsBodyHtml`, et `Subject` propriétés ~ / EmailTemplates/ChangePassword.htm, la valeur True et votre mot de passe a changé !, respectivement.

Après avoir apporté ces modifications, revoir la page et modifier votre mot de passe à nouveau. Cette fois-ci, le contrôle ChangePassword envoie un e-mail au format HTML, personnalisé, à l’adresse de messagerie de l’utilisateur sur le fichier (voir la Figure 8).


[![Un Message électronique informe les utilisateurs que leur mot de passe a changé.](recovering-and-changing-passwords-vb/_static/image23.png)](recovering-and-changing-passwords-vb/_static/image22.png)

**Figure 8**: un Message électronique informe les utilisateurs que leur mot de passe a changé ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image24.png))


## <a name="step-3-allowing-administrators-to-change-users-passwords"></a>Étape 3 : Ce qui permet aux administrateurs de modifier les mots de passe utilisateurs

Une fonctionnalité courante dans les applications qui prennent en charge les comptes d’utilisateur est la possibilité pour un utilisateur administratif modifier les mots de passe des autres utilisateurs. Cette fonctionnalité est parfois nécessaire, car le système n’a pas la possibilité pour les utilisateurs à modifier leurs mots de passe. Dans ce cas, la seule façon pour un utilisateur récupérer leur mot de passe oublié serait de l’administrateur de leur affecter un nouveau mot de passe. Avec les contrôles PasswordRecovery et ChangePassword, toutefois, les utilisateurs administratifs ne doivent pas en cours d’eux-mêmes à des modifications de mots de passe utilisateurs, comme les utilisateurs sont capables d’effectuer eux-mêmes.

Mais que se passe-t-il si votre client insiste pour que les utilisateurs administratifs doivent être en mesure de modifier les mots de passe des autres utilisateurs ? Malheureusement, l’ajout de cette fonctionnalité peut être un peu de travail. Pour modifier le mot de passe d’un utilisateur, à la fois l’ancien et le nouveau mot de passe doit être fourni pour le `MembershipUser` l’objet `ChangePassword` (méthode), mais un administrateur ne doit pas obligé de connaître le mot de passe d’un utilisateur afin de le modifier.

Une solution de contournement consiste à tout d’abord réinitialiser le mot de passe, puis à nouveau mot de passe à l’aide de code semblable au suivant :

[!code-vb[Main](recovering-and-changing-passwords-vb/samples/sample5.vb)]

Ce code commence par récupérer des informations sur *nom d’utilisateur*, qui est l’utilisateur dont l’administrateur souhaite modifier le mot de passe. Ensuite, le `ResetPassword` méthode est appelée, les affecte et l’utilisateur un nouveau mot de passe aléatoire. Ce mot de passe généré de manière aléatoire est retourné par la méthode et stocké dans la variable `resetPwd`. Maintenant que nous savons ce mot de passe, nous pouvons la modifier via un appel à `ChangePassword`.

Le problème est que ce code ne fonctionne que si la valeur de la configuration du système d’appartenance telle que `RequiresQuestionAndAnswer` a la valeur False. Si `RequiresQuestionAndAnswer` est True, car il s’agit avec notre application, puis le `ResetPassword` (méthode) doit être transmis à la réponse de sécurité, sinon il lève une exception.

Si l’infrastructure d’appartenances est configuré pour exiger une question de sécurité et une réponse, et encore votre client insiste pour que les administrateurs pourront changer les mots de passe utilisateurs, vous disposez de trois options :

- Lever les mains dans l’air et indiquer à votre client qu’il s’agit simplement une chose qui ne peut pas être effectuée.
- Définir `RequiresQuestionAndAnswer` avec la valeur False. Cela entraîne une application moins sécurisée. Imaginez qu’un utilisateur malveillant a réussi à accéder à la boîte de réception d’un autre utilisateur par courrier électronique. Peut-être l’utilisateur compromis a quitté leur bureau d’aller déjeuner et n’a pas de verrouiller leur station de travail ou peut-être ils accessibles leur courrier électronique à partir d’un terminal public et n’a pas de se déconnecter. Dans les deux cas, l’utilisateur malveillant peut visiter le `RecoverPassword.aspx` page et entrez le nom d’utilisateur. Le système de messagerie sera puis le mot de passe récupéré sans demander la réponse de sécurité.
- Contournement de la couche d’abstraction créée par le framework d’appartenance et travailler directement sur la base de données SQL Server. Le schéma de l’appartenance inclut une procédure stockée nommée `aspnet_Membership_SetPassword` qui définit un mot de passe et ne nécessite pas de réponse de sécurité ou de l’ancien mot de passe afin d’effectuer ses tâches.

Aucune de ces options sont particulièrement attrayante, mais qui est la façon dont la durée de vie d’un développeur est parfois.

J’ai poursuivi et implémenté la troisième méthode, l’écriture de code qui ignore le `Membership` et `MembershipUser` des classes et qu’il fonctionne directement sur le `SecurityTutorials` base de données.

> [!NOTE]
> En travaillant directement avec la base de données, l’encapsulation fournie par l’infrastructure de l’appartenance est cassée. Cette décision lie nous le `SqlMembershipProvider`, rendre notre code moins portable. En outre, ce code peuvent ne pas fonctionne comme prévu dans les futures versions d’ASP.NET si le schéma de l’appartenance change. Cette approche est une solution de contournement et, comme la plupart des solutions de contournement n’est pas un exemple des meilleures pratiques.


Le code a des bits potentiels et est très long. Par conséquent, je ne souhaite pas encombrer ce didacticiel avec un examen approfondi de celui-ci. Si vous souhaitez en savoir plus, téléchargez le code pour ce didacticiel et de la visite du `~/Administration/ManageUsers.aspx` page. Cette page, nous avons créé dans le <a id="_msoanchor_5"> </a> [didacticiel précédent](building-an-interface-to-select-one-user-account-from-many-vb.md), répertorie tous les utilisateurs. J’ai mis à jour le contrôle GridView pour inclure un lien vers le `UserInformation.aspx` page, en passant le nom d’utilisateur de l’utilisateur via la chaîne de requête. Le `UserInformation.aspx` page affiche des informations sur l’utilisateur sélectionné et les zones de texte pour modifier leur mot de passe (voir la Figure 9).

Après l’entrée nouveau mot de passe et confirmer dans la deuxième zone de texte en cliquant sur le bouton d’un utilisateur de mise à jour, s’ensuit une publication (postback) et le `aspnet_Membership_SetPassword` procédure stockée est appelée, la mise à jour le mot de passe. Je conseille les lecteurs intéressés par cette fonctionnalité pour vous familiariser avec le code et réessayez d’étendre les fonctionnalités pour comprennent l’envoi d’un message électronique à l’utilisateur, dont un mot de passe a été modifié.


[![Un administrateur peut modifier le mot de passe d’un utilisateur](recovering-and-changing-passwords-vb/_static/image26.png)](recovering-and-changing-passwords-vb/_static/image25.png)

**Figure 9**: un administrateur peut modifier le mot de passe d’un utilisateur ([cliquez pour afficher l’image en taille réelle](recovering-and-changing-passwords-vb/_static/image27.png))


> [!NOTE]
> Le `UserInformation.aspx` page actuellement fonctionne uniquement si le cadre de l’appartenance est configuré pour stocker les mots de passe au format Clear ou Hashed. Il ne contient pas le code pour chiffrer le nouveau mot de passe, même si vous êtes invité à ajouter cette fonctionnalité. La je vous recommande d’ajouter le code nécessaire consiste à utiliser un décompilateur comme [Reflector](http://www.aisto.com/roeder/dotnet/) pour examiner le code source pour les méthodes dans le .NET Framework ; commencer par examiner le `SqlMembershipProvider` la classe `ChangePassword` (méthode). Il s’agit de la technique que j’ai utilisé pour écrire le code pour la création d’un hachage du mot de passe.


## <a name="summary"></a>Récapitulatif

ASP.NET propose deux contrôles permettant aux utilisateurs de gérer leur mot de passe. Le contrôle PasswordRecovery est utile pour ceux qui ont oublié leur mot de passe. Selon la configuration de l’infrastructure d’appartenance, l’utilisateur est soit envoyé par courrier électronique leur mot de passe ou un mot de passe de nouveau, généré de manière aléatoire. Le contrôle ChangePassword permet à un utilisateur à mettre à jour son mot de passe.

Comme les contrôles de connexion et CreateUserWizard, les contrôles PasswordRecovery et ChangePassword restituent une interface utilisateur élaborée sans devoir écrire la moindre ligne de balisage déclaratif ou de la ligne de code. Si l’interface utilisateur par défaut ne répond pas à vos besoins, vous pouvez personnaliser par le biais d’une variété de propriétés de style. Vous pouvez également les interfaces de contrôles peuvent être converties dans les modèles, pour un même meilleur niveau de contrôle. En arrière-plan, ces contrôles utilisent l’API d’appartenance, appelez le `MembershipUser` l’objet `ResetPassword` et `ChangePassword` méthodes.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Démarrages rapides du contrôle ChangePassword](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/login/changepassword.aspx)
- [Démarrages rapides du contrôle PasswordRecovery](https://quickstarts.asp.net/QuickStartv20/aspnet/doc/ctrlref/login/passwordrecovery.aspx)
- [Envoi de courrier électronique dans ASP.NET](http://aspnet.4guysfromrolla.com/articles/072606-1.aspx)
- [`System.Net.Mail`Foire aux questions](http://www.systemnetmail.com/)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel incluent Michael Emmings et Suchi Banerjee. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à[mitchell@4GuysFromRolla.com](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](building-an-interface-to-select-one-user-account-from-many-vb.md)
[Suivant](unlocking-and-approving-user-accounts-vb.md)
