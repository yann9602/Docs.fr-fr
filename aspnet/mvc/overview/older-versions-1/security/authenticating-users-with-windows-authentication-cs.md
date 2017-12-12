---
uid: mvc/overview/older-versions-1/security/authenticating-users-with-windows-authentication-cs
title: "L’authentification des utilisateurs avec l’authentification Windows (c#) | Documents Microsoft"
author: microsoft
description: "Découvrez comment utiliser l’authentification Windows dans le contexte d’une application MVC. Vous apprenez à activer l’authentification Windows au sein de la quantité de co de votre application web..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: 418bb07e-f369-4119-b4b0-08f890f7abb2
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/security/authenticating-users-with-windows-authentication-cs
msc.type: authoredcontent
ms.openlocfilehash: 575fb382cc758efb101485bd5aece461bf995bfc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="authenticating-users-with-windows-authentication-c"></a>L’authentification des utilisateurs avec l’authentification Windows (c#)
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment utiliser l’authentification Windows dans le contexte d’une application MVC. Vous découvrez comment activer l’authentification Windows dans le fichier de configuration de votre application web et comment configurer l’authentification avec IIS. Enfin, vous apprenez à utiliser l’attribut [Authorize] pour restreindre l’accès aux actions de contrôleur pour les utilisateurs Windows particuliers ou des groupes.


L’objectif de ce didacticiel est d’expliquer comment vous pouvez tirer parti de la sécurité de fonctionnalités intégrées dans Internet Information Services pour le mot de passe protègent les vues dans vos applications MVC. Vous apprenez autoriser des actions de contrôleur devant être appelé uniquement par les utilisateurs Windows particuliers ou les utilisateurs qui sont membres de groupes Windows spécifiques.

À l’aide de l’authentification Windows de sens lorsque vous créez un site Web d’entreprise interne (un site intranet) et que vous souhaitez que vos utilisateurs en mesure d’utiliser leurs noms d’utilisateur Windows standard et les mots de passe lors de l’accès du site Web. Si vous générez une sortie d’accès au site Web (un site Internet) envisagez d’utiliser l’authentification par formulaire à la place.

#### <a name="enabling-windows-authentication"></a>L’activation de l’authentification Windows

Lorsque vous créez une application ASP.NET MVC, l’authentification Windows n’est pas activée par défaut. L’authentification par formulaire est le type d’authentification par défaut activé pour les applications MVC. Vous devez activer l’authentification Windows en modifiant le fichier de configuration (web.config) de web de votre application MVC. Rechercher les &lt;authentification&gt; section et modifiez-le pour utiliser Windows au lieu de l’authentification par formulaire comme suit :

[!code-xml[Main](authenticating-users-with-windows-authentication-cs/samples/sample1.xml)]

Lorsque vous activez l’authentification Windows, votre serveur web devient responsable de l’authentification des utilisateurs. En règle générale, il existe des deux types de serveurs web que vous utilisez lors de la création et déploiement d’une application ASP.NET MVC.

Tout d’abord, lorsqu’il développe une application MVC, vous utilisez le serveur Web de développement ASP.NET fourni avec Visual Studio. Par défaut, le serveur Web de développement ASP.NET exécute toutes les pages dans le contexte du compte Windows actuel (le compte utilisé pour vous connecter à Windows).

Le serveur Web de développement ASP.NET prend également en charge l’authentification NTLM. Vous pouvez activer l’authentification NTLM en double-cliquant sur le nom de votre projet dans la fenêtre de l’Explorateur de solutions et en sélectionnant Propriétés. Ensuite, sélectionnez l’onglet Web et activez la case à cocher NTLM (voir Figure 1).

**Figure 1 : l’activation de l’authentification NTLM pour le serveur Web de développement ASP.NET**

![clip_image002](authenticating-users-with-windows-authentication-cs/_static/image1.jpg)

Pour une application web de production, la part, vous utilisez IIS en tant que votre serveur web. IIS prend en charge plusieurs types d’authentification, notamment :

- L’authentification de base – définies dans le cadre du protocole HTTP 1.0. Envoie les noms d’utilisateur et mots de passe en texte clair (codé en Base64) via Internet. -L’authentification digest – envoie un hachage de mot de passe, au lieu du mot de passe, via internet. -Authentification intégrée Windows (NTLM) – le meilleur type d’authentification à utiliser dans les environnements intranet à l’aide de windows. -Certificats authentification – Active l’authentification à l’aide d’un certificat côté client. Le certificat est mappé à un compte d’utilisateur Windows.

> [!NOTE] 
> 
> Pour obtenir une présentation plus détaillée de ces différents types d’authentification, consultez [https://msdn.microsoft.com/en-us/library/aa292114(VS.71).aspx](https://msdn.microsoft.com/en-us/library/aa292114(VS.71).aspx).


Vous pouvez utiliser le Gestionnaire des Services Internet pour activer un type particulier d’authentification. N’oubliez pas que tous les types d’authentification ne sont pas disponibles dans le cas de tous les systèmes d’exploitation. En outre, si vous utilisez IIS 7.0 avec Windows Vista, vous devez activer les différents types de l’authentification Windows avant d’apparaître dans le Gestionnaire des Services Internet. Ouvrez **le panneau de configuration, programmes, programmes et fonctionnalités, activer des fonctionnalités Windows ou désactiver**, développez le nœud Internet Information Services (voir Figure 2).

**Figure 2 : fonctions d’activation Windows IIS**

![clip_image004](authenticating-users-with-windows-authentication-cs/_static/image2.jpg)

À l’aide d’Internet Information Services, vous pouvez activer ou désactiver les différents types d’authentification. Par exemple, la Figure 3 illustre l’authentification anonyme pour désactiver et activer l’authentification intégrée Windows (NTLM) lors de l’utilisation d’IIS 7.0.

**Figure 3 : l’activation de l’authentification Windows intégrée**

![clip_image006](authenticating-users-with-windows-authentication-cs/_static/image3.jpg)

#### <a name="authorizing-windows-users-and-groups"></a>Autorisation Windows utilisateurs et groupes

Après avoir activé l’authentification Windows, vous pouvez utiliser l’attribut [Authorize] pour contrôler l’accès aux contrôleurs ou les actions de contrôleur. Cet attribut peut être appliqué à un contrôleur MVC entière ou une action de contrôleur spécifique.

Par exemple, le contrôleur Home dans la liste 1 expose trois actions nommées Index(), CompanySecrets() et StephenSecrets(). Toute personne peut appeler l’action Index(). Toutefois, seuls les membres du groupe Windows local gestionnaires peuvent appeler l’action CompanySecrets(). Enfin, seul l’utilisateur de domaine Windows nommé Stephen (dans le domaine Redmond) permettre appeler l’action StephenSecrets().

**La liste 1 – Controllers\HomeController.cs**

[!code-csharp[Main](authenticating-users-with-windows-authentication-cs/samples/sample2.cs)]

> [!NOTE] 
> 
> En raison de Windows compte contrôle utilisateur (UAC), lorsque vous travaillez avec Windows Vista ou Windows Server 2008, le groupe Administrateurs local se comportent différemment des autres groupes. L’attribut [Authorize] correctement ne reconnaîtra pas un membre du groupe Administrateurs local, sauf si vous modifiez les paramètres de compte d’utilisateur de votre ordinateur.


Exactement ce qui se passe lorsque vous essayez d’appeler une action de contrôleur sans être les autorisations appropriées varie selon le type d’authentification est activée. Par défaut, lorsque vous utilisez le serveur de développement ASP.NET, vous obtenez simplement une page vierge. La page est traitée avec une **401 non autorisé** état de la réponse HTTP.

Si, en revanche, vous utilisez IIS avec l’authentification anonyme est désactivée et l’authentification de base activée, puis vous continuez à recevoir une invite de boîte de dialogue de connexion chaque fois que vous demandez la page protégée (voir Figure 4).

**Figure 4 – boîte de dialogue de connexion d’authentification de base**

![clip_image008](authenticating-users-with-windows-authentication-cs/_static/image4.jpg)

#### <a name="summary"></a>Résumé

Ce didacticiel explique comment vous pouvez utiliser l’authentification Windows dans le contexte d’une application ASP.NET MVC. Vous avez appris à activer l’authentification Windows dans le fichier de configuration de votre application web et comment configurer l’authentification avec IIS. Enfin, vous avez appris à utiliser l’attribut [Authorize] pour restreindre l’accès aux actions de contrôleur pour les utilisateurs Windows particuliers ou des groupes.

>[!div class="step-by-step"]
[Précédent](authenticating-users-with-forms-authentication-cs.md)
[Suivant](preventing-javascript-injection-attacks-cs.md)
