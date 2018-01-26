---
uid: whitepapers/aspnet4/breaking-changes
title: Modifications avec rupture 4 de ASP.NET | Documents Microsoft
author: rick-anderson
description: "Ce document décrit les modifications qui ont été apportées pour la version de .NET Framework version 4 qui est susceptible d’affecter les applications qui ont été créées à l’aide de..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: d601c540-f86b-4feb-890c-20c806b3da6c
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/aspnet4/breaking-changes
msc.type: content
ms.openlocfilehash: 98647830125670ee2ed43538d65fb3ce6ac40d0d
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-4-breaking-changes"></a>Modifications avec rupture 4 de ASP.NET
====================
> Ce document décrit les modifications qui ont été apportées pour la version de .NET Framework version 4 qui est susceptible d’affecter les applications qui ont été créées à l’aide de versions antérieures, y compris les versions d’ASP.NET 4 Beta 1 et 2 de la version bêta.
> 
> [Télécharger ce livre blanc](https://download.microsoft.com/download/7/1/A/71A105A9-89D6-4201-9CC5-AD6A3B7E2F22/ASP_NET_4_Breaking_Changes.pdf)


<a id="0.1__Toc256768952"></a><a id="0.1__Toc256770056"></a>

## <a name="contents"></a>Sommaire

[Paramètre ControlRenderingCompatibilityVersion dans le fichier Web.config](#0.1__Toc256770141 "_Toc256770141")  
[Les modifications ClientIDMode](#0.1__Toc256770142 "_Toc256770142")  
[HtmlEncode et codent en URL maintenant coder des guillemets simples](#0.1__Toc256770143 "_Toc256770143")  
[Page ASP.NET (.aspx) analyseur est Stricter](#0.1__Toc256770144 "_Toc256770144")  
[Mise à jour de fichiers de définition de navigateur](#0.1__Toc256770145 "_Toc256770145")  
[System.Web.Mobile.dll supprimée à partir du fichier de Configuration Web racine](#0.1__Toc256770146 "_Toc256770146")  
[Validation de requête ASP.NET](#0.1__Toc256770147 "_Toc256770147")  
[Par défaut de l’algorithme de hachage est désormais HMACSHA256](#0.1__Toc256770148 "_Toc256770148")  
[Erreurs de configuration liés à la nouvelle Configuration de racine 4 d’ASP.NET](#0.1__Toc256770149 "_Toc256770149")  
[Les Applications ASP.NET 4 enfant ne démarrent pas sous ASP.NET 2.0 ou ASP.NET 3.5 Applications](#0.1__Toc256770150 "_Toc256770150")  
[Échec de Sites Web ASP.NET 4 démarrer sur les ordinateurs où SharePoint est installé](#0.1__Toc256770151 "_Toc256770151")  
[La propriété HttpRequest.FilePath n’inclut plus les valeurs PathInfo](#0.1__Toc256770152 "_Toc256770152")  
[ASP.NET 2.0 Applications peuvent générer des erreurs de HttpException qui font référence à eurl.axd](#0.1__Toc256770153 "_Toc256770153")  
[Gestionnaires d’événements ne peuvent pas être déclenchés pas dans un Document par défaut dans IIS 7 ou IIS 7.5 Mode intégré](#0.1__Toc256770154 "_Toc256770154")  
[Modifications apportées à l’implémentation de la sécurité (CAS) de l’accès de Code ASP.NET](#0.1__Toc256770155 "_Toc256770155")  
[MembershipUser et autres Types dans le Namespace System.Web.Security ont été déplacés](#0.1__Toc256770156 "_Toc256770156")  
[Sortie mise en cache des modifications pour faire varier les \* en-tête HTTP](#0.1__Toc256770157 "_Toc256770157")  
[Types System.Web.Security pour Passport sont obsolètes](#0.1__Toc256770158 "_Toc256770158")  
[La propriété MenuItem.PopOutImageUrl ne fournit pas une Image dans ASP.NET 4](#0.1__Toc256770159 "_Toc256770159")  
[Menu.StaticPopOutImageUrl et échouent Menu.DynamicPopOutImageUrl pour restituer des Images lorsque les chemins d’accès contiennent des barres obliques inverses](#0.1__Toc256770160 "_Toc256770160")  
[Disclaimer](#0.1__Toc256770161 "_Toc256770161")

<a id="0.1__ControlRenderingCompatibilityVersio"></a><a id="0.1__Toc245724853"></a><a id="0.1__Toc255587630"></a><a id="0.1__Toc256770141"></a>

## <a name="controlrenderingcompatibilityversion-setting-in-the-webconfig-file"></a>Paramètre ControlRenderingCompatibilityVersion dans le fichier Web.config

Les contrôles ASP.NET ont été modifiés dans le .NET Framework version 4, afin de vous permettent de spécifier plus précisément comment ils restituent le balisage. Dans les versions précédentes du .NET Framework, certains contrôles émis balisage que vous n’aviez aucun moyen de désactiver. Par défaut, ASP.NET 4, ce type de balisage n’est plus généré.

Si vous utilisez Visual Studio 2010 pour mettre à niveau votre application à partir d’ASP.NET 2.0 ou ASP.NET 3.5, l’outil ajoute automatiquement un paramètre pour le `Web.config` fichier pour conserver le rendu hérité. Toutefois, si vous mettez à niveau une application en changeant le pool d’applications dans IIS pour cibler le .NET Framework 4, ASP.NET utilise le nouveau mode de rendu par défaut. Pour désactiver le nouveau mode de rendu, ajoutez le paramètre suivant dans le `Web.config` fichier :

[!code-xml[Main](breaking-changes/samples/sample1.xml)]

Les modifications de rendu principaux que le nouveau comportement met sont les suivantes :

- Le **Image** et **ImageButton** contrôles de rendu n’est plus un `border="0"` attribut.
- Le **BaseValidator** contrôles de validation et de la classe qui en dérivent rendent plus texte rouge par défaut.
- Le **HtmlForm** contrôle ne rend pas une **nom** attribut.
- Le **Table** de contrôle n’est plus restitue un `border="0"` attribut.
- Les contrôles qui ne sont pas conçus pour l’entrée d’utilisateur (par exemple, le **étiquette** contrôle) rendra plus le `disabled="disabled"` attribut si leur **activé** est définie sur **false**(ou s’ils héritent de ce paramètre à partir d’un contrôle conteneur).

<a id="0.1__Toc245724854"></a><a id="0.1__Toc255587631"></a><a id="0.1__Toc256770142"></a>

## <a name="clientidmode-changes"></a>Modifications ClientIDMode

Le **ClientIDMode** paramètre dans ASP.NET 4 vous permet de spécifier la façon dont ASP.NET génère le **id** attribut pour les éléments HTML. Dans les versions précédentes d’ASP.NET, le comportement par défaut était équivalent à la **AutoID** paramètre **ClientIDMode**. Cependant, le paramètre par défaut est désormais **prédictible**.

Si vous utilisez Visual Studio 2010 pour mettre à niveau votre application à partir d’ASP.NET 2.0 ou ASP.NET 3.5, l’outil ajoute automatiquement un paramètre pour le `Web.config` fichier pour conserver le comportement des versions antérieures du .NET Framework. Toutefois, si vous mettez à niveau une application en changeant le pool d’applications dans IIS pour cibler le .NET Framework 4, ASP.NET utilise le nouveau mode par défaut. Pour désactiver le nouveau mode ID client, ajoutez le paramètre suivant dans le `Web.config` fichier :

[!code-xml[Main](breaking-changes/samples/sample2.xml)]

<a id="0.1__Toc245724855"></a><a id="0.1__Toc255587632"></a><a id="0.1__Toc256770143"></a>

## <a name="htmlencode-and-urlencode-now-encode-single-quotation-marks"></a>HtmlEncode et codent en URL maintenant coder des guillemets simples

Dans ASP.NET 4, le **HtmlEncode** et **codent en URL** méthodes de la **HttpUtility** et **HttpServerUtility** classes ont été mis à jour pour coder le caractère guillemet simple (') comme suit :

- Le **HtmlEncode** méthode encode les instances du guillemet simple comme.
- Le **codent en URL** méthode encode les instances du guillemet simple sous la forme % 27.

<a id="0.1__Toc255587633"></a><a id="0.1__Toc256770144"></a><a id="0.1__Toc245724856"></a>

## <a name="aspnet-page-aspx-parser-is-stricter"></a>Page ASP.NET (.aspx) analyseur est Stricter

L’Analyseur de page pour les pages ASP.NET (`.aspx` fichiers) et les contrôles utilisateur (`.ascx` fichiers) est plus stricte dans ASP.NET 4 et rejette plusieurs instances d’un balisage non valide. Par exemple, les deux extraits de code suivants seraient analyser correctement dans les versions antérieures d’ASP.NET, mais génère maintenant des erreurs de l’analyseur dans ASP.NET 4.

[!code-aspx[Main](breaking-changes/samples/sample3.aspx)]

Notez le point-virgule non valide à la fin de la **HiddenField** balise.

[!code-aspx[Main](breaking-changes/samples/sample4.aspx)]

Notez le non fermée **style** attribut qui s’exécute dans le **CssClass** attribut.

<a id="0.1__Toc255587634"></a><a id="0.1__Toc256770145"></a>

## <a name="browser-definition-files-updated"></a>Mise à jour de fichiers de définition de navigateur

Les fichiers de définition de navigateur ont été mis à jour pour inclure des informations sur les navigateurs et les appareils récemment sortis et mis à jour. Des navigateurs et appareils anciens (comme Netscape Navigator) ont été supprimés tandis que de nouveaux navigateurs et appareils (comme Google Chrome et Apple iPhone) ont été ajoutés.

Si votre application contient des définitions de navigateur personnalisées qui héritent de l’une des définitions de navigateur supprimées, une erreur s’affiche. Par exemple, si le `App_Browsers` dossier contient une définition de navigateur qui hérite de la définition de navigateur IE2, vous recevez le message d’erreur de configuration suivantes :

- Impossible de trouver l’élément browser ou gateway avec l’ID 'IE2'.

> [!NOTE]
> Le **HttpBrowserCapabilities** objet (qui est exposé par la page **Request.Browser** propriété) est piloté par les fichiers de définition de navigateur. Par conséquent, les informations retournées par l’accès à une propriété de cet objet dans ASP.NET 4 peuvent être différentes de celles retournées dans une version antérieure d’ASP.NET.


Vous pouvez rétablir les anciens fichiers de définition de navigateur en copiant les fichiers de définition de navigateur dans le dossier suivant :

[!code-console[Main](breaking-changes/samples/sample5.cmd)]

Copiez les fichiers dans correspondant `\CONFIG\Browsers` dossier pour ASP.NET 4. Après avoir copié les fichiers, exécutez le compte Aspnet\_outil de ligne de commande regbrowsers.exe.

<a id="0.1__Toc255587635"></a><a id="0.1__Toc256770146"></a>

## <a name="systemwebmobiledll-removed-from-root-web-configuration-file"></a>System.Web.Mobile.dll supprimée du fichier de Configuration Web racine

Dans les versions précédentes d’ASP.NET, une référence à l’assembly System.Web.Mobile.dll a été incluse dans la racine de `Web.config` de fichiers dans le **assemblys** sous. Afin d’améliorer les performances, la référence à cet assembly a été supprimée.

L’assembly System.Web.Mobile.dll est incluse dans ASP.NET 4, mais elle est déconseillée. Si vous souhaitez utiliser des types de l’assembly System.Web.Mobile.dll, ajoutez une référence à cet assembly à la racine de le `Web.config` fichier ou à une application `Web.config` fichier. Par exemple, si vous souhaitez utiliser les contrôles mobiles ASP.NET (déconseillés), vous devez ajouter une référence à l’assembly System.Web.Mobile.dll à la `Web.config` fichier.

<a id="0.1__Toc245724857"></a><a id="0.1__Toc255587636"></a><a id="0.1__Toc256770147"></a>

## <a name="aspnet-request-validation"></a>Validation de requête ASP.NET

La fonctionnalité de validation de demande dans ASP.NET fournit un certain niveau de protection par défaut contre les attaques d’écriture de scripts entre sites (XSS). Dans les versions précédentes d’ASP.NET, la validation de la demande a été activée par défaut. Toutefois, elle appliquée uniquement aux pages ASP.NET (`.aspx` fichiers et leurs fichiers de classe) et uniquement lorsque les pages sont exécutaient.

Dans ASP.NET 4, par défaut, validation de la demande est activée pour toutes les demandes, car il est activé avant le **BeginRequest** phase d’une requête HTTP. Par conséquent, validation de la demande s’applique aux demandes pour toutes les ressources ASP.NET, pas seulement les demandes de pages .aspx. Cela inclut les demandes, telles que les appels de service Web et les gestionnaires HTTP personnalisés. Validation de la demande est également active lorsque des modules HTTP personnalisés sont lire le contenu d’une requête HTTP.

Par conséquent, les erreurs de validation de requête peuvent se produire maintenant pour les demandes qui précédemment non-déclenchement erreurs. Pour rétablir le comportement de la fonctionnalité de validation de requête ASP.NET 2.0, ajoutez le paramètre suivant dans le `Web.config` fichier :

[!code-xml[Main](breaking-changes/samples/sample6.xml)]

Toutefois, nous recommandons que vous analysez des erreurs de validation de demande pour déterminer si les gestionnaires existants, modules ou tout autre code personnalisé accède à potentiellement unsafe entrées HTTP qui pourraient être XSS les vecteurs d’attaque.

<a id="0.1__Toc245724858"></a><a id="0.1__Toc255587637"></a><a id="0.1__Toc256770148"></a>

## <a name="default-hashing-algorithm-is-now-hmacsha256"></a>Par défaut de l’algorithme de hachage est désormais HMACSHA256

ASP.NET utilise des algorithmes de chiffrement et de hachage pour sécuriser des données telles que les cookies d’authentification par formulaire et l’état d’affichage. Par défaut, ASP.NET 4 utilise désormais l’algorithme HMACSHA256 pour les opérations de hachage sur les cookies et l’état d’affichage. Les versions antérieures d’ASP.NET utilisaient l’ancien algorithme HMACSHA1.

Vos applications peuvent être affectées si vous exécutez mixte 2.0/ASP.NET ASP.NET 4 environnements où les données telles que les cookies d’authentification doivent fonctionner across.NET versions du Framework. Pour configurer une application Web ASP.NET 4 pour utiliser l’algorithme HMACSHA1 plus anciens, ajoutez le paramètre suivant dans le `Web.config` fichier :

[!code-xml[Main](breaking-changes/samples/sample7.xml)]

<a id="0.1__Toc245724859"></a><a id="0.1__Toc255587638"></a><a id="0.1__Toc256770149"></a>

## <a name="configuration-errors-related-to-new-aspnet-4-root-configuration"></a>Erreurs de configuration liées à la nouvelle Configuration de racine 4 d’ASP.NET

Les fichiers de configuration racine (le `machine.config` la racine et le fichier `Web.config` fichier) pour le .NET Framework 4 (et par conséquent, ASP.NET 4) ont été mis à jour pour inclure la plupart du code réutilisable des informations de configuration dans ASP.NET 3.5 a été trouvées dans le application `Web.config` fichiers. En raison de la complexité des systèmes de configuration IIS 7 et IIS 7.5 managés, l’exécution des applications ASP.NET 3.5 sous ASP.NET 4 et sous IIS 7 et IIS 7.5 peut entraîner dans ASP.NET ou IIS erreurs de configuration.

Nous vous recommandons de mettre à niveau les applications ASP.NET 3.5 vers ASP.NET 4 en utilisant les outils de mise à niveau de projet dans Visual Studio 2010, si cela est possible. Visual Studio 2010 modifie automatiquement l’application ASP.NET 3.5 `Web.config` fichier contienne les paramètres appropriés pour ASP.NET 4.

Toutefois, il est un scénario pris en charge pour exécuter des applications ASP.NET 3.5 à l’aide de .NET Framework 4 sans recompilation. Dans ce cas, vous devrez peut-être modifier manuellement l’application `Web.config` fichier avant d’exécuter l’application sous le .NET Framework 4 et sous IIS 7 ou IIS 7.5.

Les deux sections suivantes décrivent les modifications que vous devrez peut-être faire pour différentes combinaisons de logiciels.

**Windows Vista SP1 ou Windows Server 2008 SP1, où le correctif logiciel KB958854 ni SP2 sont installées.** Dans cette configuration, le système de configuration IIS 7 fusionne incorrectement configuration géré d’une application en comparant le niveau de l’application `Web.config` fichier de ASP.NET 2.0 `machine.config` fichiers. En raison de cet option, de niveau application `Web.config` les fichiers à partir de .NET Framework 3.5 ou version ultérieure doivent avoir un **system.web.extensions** définition de section de configuration (l’élément) pour ne pas provoquer l’échec de validation de d’IIS 7.

Toutefois, modifié manuellement au niveau de l’application `Web.config` les entrées de fichier qui ne correspondent pas avec précision les définitions de section de configuration standard d’origine qui ont été introduites avec Visual Studio 2008 peut provoquer des erreurs de configuration ASP.NET. (Les entrées de configuration par défaut qui sont générées par Visual Studio 2008 fonctionnent correctement). Un problème courant est celui modifié manuellement `Web.config` fichiers omettre la **allowDefinition** et **requirePermission** les attributs de configuration qui se trouvent dans la section de configuration différents définitions. Cela provoque une discordance entre la section de configuration abrégé de niveau application `Web.config` fichiers et la définition complète dans ASP.NET 4 `machine.config` fichier. Par conséquent, au moment de l’exécution, le système de configuration ASP.NET 4 lève une erreur de configuration.

**Windows Vista SP2, Windows Server 2008 SP2, Windows 7, Windows Server 2008 R2 et également Windows Vista SP1 et Windows Server 2008 SP1 sur lequel est installé le correctif KB958854.**

Dans ce scénario, le système de configuration native IIS 7 et IIS 7.5 renvoie une erreur de configuration, car elle effectue une comparaison de texte sur le **type** attribut qui est défini pour un gestionnaire de section de configuration managés. Étant donné que tous les `Web.config` les fichiers qui sont générés par Visual Studio 2008 et Visual Studio 2008 SP1 ont « 3.5 » dans la chaîne de type pour le **system.web.extensions** (et associés) gestionnaires de section de configuration et parce que ASP.NET 4 `machine.config` fichier a « 4.0 » dans le **type** d’attribut pour les gestionnaires de section de configuration même, les applications qui sont générées dans Visual Studio 2008 ou Visual Studio 2008 SP1 toujours échouent la validation de la configuration dans IIS 7 et IIS 7.5.

<a id="0.1__Toc251910248"></a>

### <a name="resolving-these-issues"></a>Résolution de ces problèmes

La solution de contournement pour le premier scénario consiste à mettre à jour le niveau de l’application `Web.config` fichier en incluant le texte de configuration standard d’un `Web.config` qui a été généré automatiquement par Visual Studio 2008.

Solution de contournement pour le premier scénario consiste à installer le Service Pack 2 pour Vista ou Windows Server 2008 sur votre ordinateur ou pour installer le correctif logiciel KB958854 ([https://support.microsoft.com/kb/958854](https://support.microsoft.com/kb/958854)) pour corriger incorrect comportement de la fusion de la configuration du système de configuration IIS. Toutefois, après avoir effectué une de ces actions, votre application rencontre probablement en raison du problème décrit dans le second cas, une erreur de configuration.

La solution de contournement pour le deuxième scénario consiste à supprimer ou commenter tous les **system.web.extensions** définitions de section de configuration et de la section de configuration du groupe de définitions à partir du niveau de l’application `Web.config` fichier. Ces définitions sont généralement en haut du niveau de l’application `Web.config` de fichiers et peut être identifiée par le **configSections** élément et ses enfants.

Pour les deux scénarios, il est recommandé de supprimer manuellement le **system.codedom** section, mais cela n’est pas obligatoire.

<a id="0.1__Toc252995490"></a><a id="0.1__Toc255587639"></a><a id="0.1__Toc256770150"></a><a id="0.1__Toc245724860"></a>

## <a name="aspnet-4-child-applications-fail-to-start-when-under-aspnet-20-or-aspnet-35-applications"></a>Les Applications ASP.NET 4 enfant ne démarrent pas sous ASP.NET 2.0 ou ASP.NET 3.5 Applications

Les applications ASP.NET 4 configurées comme enfants d’applications qui exécutent des versions antérieures d’ASP.NET risquent de ne pas démarrer en raison d’erreurs de configuration ou de compilation. L’exemple suivant montre une structure de répertoire pour une application affectée.

`/parentwebapp`(configuré pour utiliser ASP.NET 2.0 ou ASP.NET 3.5)  
`/childwebapp`(configuré pour utiliser ASP.NET 4)

L’application dans le `childwebapp` dossier ne pourront pas démarrer sur IIS 7 ou IIS 7.5 et signale une erreur de configuration. Le texte d’erreur inclut un message semblable au suivant :

- `The requested page cannot be accessed because the related configuration data for the page is invalid.`
  

- `The configuration section 'configSections' cannot be read because it is missing a section declaration.`

IIS 6, l’application dans le `childwebapp` dossier échouera également à démarrer, mais il va signaler une erreur différente. Par exemple, le texte d’erreur peut indiquer les éléments suivants :

- `The value for the 'compilerVersion' attribute in the provider options must be 'v4.0' or later if you are compiling for version 4.0 or later of the .NET Framework. To compile this Web application for version 3.5 or earlier of the .NET Framework, remove the 'targetFramework' attribute from the element of the Web.config file`

Ces scénarios se produisent, car les informations de configuration à partir de l’application parente dans le `parentwebapp` dossier fait partie de la hiérarchie des informations de configuration qui détermine les paramètres de configuration fusionnée finale qui sont utilisés par le site web enfant application dans le `childwebapp` dossier. Selon si l’application Web ASP.NET 4 est en cours d’exécution sur IIS 7 (ou IIS 7.5) ou IIS 6, le système de configuration IIS ou le système de compilation ASP.NET 4 retournera une erreur.

Les étapes à suivre pour résoudre ce problème et obtenir les enfants application ASP.NET 4 pour fonctionner varient selon que l’application ASP.NET 4 s’exécute sur IIS 6 ou IIS 7 (ou IIS 7.5).

### <a name="step-1-iis-7-or-iis-75-only"></a>Étape 1 (IIS 7 ou IIS 7.5)

Cette étape est nécessaire uniquement sur les systèmes d’exploitation qui exécutent IIS 7 ou IIS 7.5, qui inclut Windows Server 2008 R2, Windows 7, Windows Vista et Windows Server 2008.

Déplacer le **configSections** définition dans le `Web.config` fichier de l’application parente (l’application qui exécute ASP.NET 2.0 ou ASP.NET 3.5) dans la racine `Web.config` fichier pour le.NET Framework 2.0. Le système de configuration native IIS 7 et IIS 7.5 analyse le **configSections** élément lors de la fusion de la hiérarchie des fichiers de configuration. Déplacement de la **configSections** définition du parent application Web `Web.config` fichier à la racine `Web.config` fichier masque l’élément à partir du processus de fusion de configuration qui se produit pour l’enfant ASP.NET 4 application.

Sur un système d’exploitation 32 bits ou pour les pools d’application 32 bits, la racine `Web.config` fichier pour ASP.NET 2.0 et ASP.NET 3.5 est généralement situé dans le dossier suivant :

`C:\Windows\Microsoft.NET\Framework\v2.0.50727\CONFIG`

Sur un système d’exploitation de 64 bits, ou pour les pools d’application 64 bits, la racine `Web.config` fichier pour ASP.NET 2.0 et ASP.NET 3.5 est généralement situé dans le dossier suivant :

`C:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG`

Si vous exécutez des applications Web 32 bits et 64 bits sur un ordinateur 64 bits, vous devez déplacer la **configSections** d’élément racine `Web.config` fichiers pour les systèmes 64 bits et 32 bits.

Lorsque vous placez le **configSections** élément dans la racine de `Web.config` file, collez la section immédiatement après le **configuration** élément. L’exemple suivant montre la partie supérieure de la racine `Web.config` fichier doit se présenter comme lorsque vous avez terminé de déplacer les éléments.

> [!NOTE]
> Dans l’exemple suivant, les lignes ont été raccourcies pour une meilleure lisibilité.


[!code-xml[Main](breaking-changes/samples/sample8.xml)]

### <a name="step-2-all-versions-of-iis"></a>Étape 2 (toutes les versions d’IIS)

Cette étape est requise si l’enfant d’ASP.NET 4 application Web s’exécute sur IIS 6 ou IIS 7 (ou IIS 7.5).

Dans le `Web.config` ajouter des fichiers de l’application Web qui est en cours d’exécution 2 d’ASP.NET ou ASP.NET 3.5, parent un **emplacement** balise spécifie explicitement (pour les systèmes de configuration de l’IIS et ASP.NET) qui les entrées de configuration uniquement s’appliquent à l’application Web parente. L’exemple suivant montre la syntaxe de la **emplacement** élément à ajouter :

[!code-xml[Main](breaking-changes/samples/sample9.xml)]

L’exemple suivant montre comment la **emplacement** balise est utilisée pour encapsuler toutes les sections de configuration en commençant par le **appSettings** section et se terminant par **system.webServer**section.

[!code-xml[Main](breaking-changes/samples/sample10.xml)]

Lorsque vous avez terminé les étapes 1 et 2, les applications Web ASP.NET 4 enfant peut démarrer sans erreurs.

<a id="0.1__Toc252995491"></a><a id="0.1__Toc255587640"></a><a id="0.1__Toc256770151"></a>

## <a name="aspnet-4-web-sites-fail-to-start-on-computers-where-sharepoint-is-installed"></a>Échec de Sites Web ASP.NET 4 démarrer sur les ordinateurs où SharePoint est installé

Les serveurs Web exécutant SharePoint ont un `Web.config` fichier qui est déployé à la racine d’un site SharePoint Web (par exemple, `c:\inetpub\wwwroot\web.config` Site Web par défaut). Dans ce `Web.config` fichier, SharePoint définit une confiance partielle personnalisée niveau nommé WSS\_minimale.

Si vous essayez d’exécuter un site Web ASP.NET 4 qui est déployé en tant qu’enfant de ce type de site SharePoint Web, vous voyez l’erreur suivante :

`Could not find permission set named 'ASP.NET'.`

Cette erreur se produit parce que l’infrastructure de sécurité d’ASP.NET 4 code accès recherchant un jeu d’autorisations nommé ASP.NET. Toutefois, partielle approuver le fichier de configuration qui est référencé par WSS\_minimale ne contient-elle pas les jeux d’autorisations portant ce nom.

Il est actuellement pas une version de SharePoint qui est compatible avec ASP.NET. Par conséquent, vous ne devez pas essayer exécuter un site Web ASP.NET 4 comme un site enfant sous SharePoint Web sites.

<a id="0.1__Toc255587641"></a><a id="0.1__Toc256770152"></a>

## <a name="the-httprequestfilepath-property-no-longer-includes-pathinfo-values"></a>La propriété HttpRequest.FilePath n’inclut plus les valeurs PathInfo

Les versions précédentes d’ASP.NET inclus un **PathInfo** valeur dans la valeur retournée différentes propriétés du fichier chemin d’accès relatif, y compris **HttpRequest.FilePath**,  **HttpRequest.AppRelativeCurrentExecutionFilePath**, et **HttpRequest.CurrentExecutionFilePath**. ASP.NET 4 n’inclut plus la **PathInfo** valeur dans les valeurs de retour à partir de ces propriétés. Au lieu de cela, le **PathInfo** informations sont disponibles dans **HttpRequest.PathInfo**. Par exemple, imaginez le fragment d’URL suivant :

`/testapp/Action.mvc/SomeAction`

Dans les versions antérieures d’ASP.NET, **HttpRequest** propriétés ont les valeurs suivantes :

**HttpRequest.FilePath**:`/testapp/Action.mvc/SomeAction`

**HttpRequest.PathInfo**: (vide)

Dans ASP.NET 4, **HttpRequest** à la place, propriétés ont les valeurs suivantes :

**HttpRequest.FilePath**:`/testapp/Action.mvc`

**HttpRequest.PathInfo**:`SomeAction`

<a id="0.1__Toc252995493"></a><a id="0.1__Toc255587642"></a><a id="0.1__Toc256770153"></a><a id="0.1__Toc245724861"></a>

## <a name="aspnet-20-applications-might-generate-httpexception-errors-that-reference-eurlaxd"></a>ASP.NET 2.0 Applications peuvent générer des erreurs de HttpException qui font référence à eurl.axd

Une fois ASP.NET 4 a été activée sur IIS 6, les applications ASP.NET 2.0 qui s’exécutent sur IIS 6 (dans Windows Server 2003 ou Windows Server 2003 R2) peuvent générer des erreurs telles que les éléments suivants :

`System.Web.HttpException: Path '/[yourApplicationRoot]/eurl.axd/[Value]' was not found.`

Cette erreur se produit lorsque ASP.NET détecte qu’un site Web est configuré pour utiliser ASP.NET 4, un composant natif de 4 d’ASP.NET passe une URL sans extension à la partie managée de ASP.NET pour un traitement ultérieur. Toutefois, si les répertoires virtuels situés sous un site Web ASP.NET 4 sont configurés pour utiliser ASP.NET 2.0, traitement de l’URL sans extension dans les résultats de cette façon dans une URL modifiée qui contient la chaîne « eurl.axd ». Cette URL modifiée est ensuite envoyé à l’application ASP.NET 2.0. ASP.NET 2.0 ne peut pas reconnaître le format « eurl.axd ». Par conséquent, ASP.NET 2.0 tente de trouver un fichier nommé `eurl.axd` et exécutez-le. Étant donné que ce fichier n’existe, la demande échoue avec une **HttpException** exception.

Vous pouvez contourner ce problème en utilisant l’une des options suivantes.

### <a name="option-1"></a>Option 1

Si ASP.NET 4 n’est pas nécessaire pour exécuter le site Web, remappez le site pour utiliser ASP.NET 2.0 à la place.

### <a name="option-2"></a>Option 2

Si ASP.NET 4 est requis pour exécuter le site Web, déplacer les répertoires virtuels de ASP.NET 2.0 enfant à un autre site Web qui est mappé vers ASP.NET 2.0.

### <a name="option-3"></a>Option 3

S’il n’est pas pratique pour remapper le site Web pour ASP.NET 2.0 ou pour modifier l’emplacement d’un répertoire virtuel, désactivez explicitement des URL sans extension de traitement dans ASP.NET 4. Procédez comme suit :

1. Dans le Registre Windows, ouvrez le nœud suivant :

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ASP.NET\4.0.30319.0`

1. Créer un nouveau **DWORD** valeur nommée **EnableExtensionlessUrls**.
2. Définissez **EnableExtensionlessUrls** à 0. Cela désactive le comportement de l’URL sans extension.
3. Enregistrer la valeur de Registre et fermez l’Éditeur du Registre.
4. Exécutez le **iisreset** outil de ligne de commande, ce qui entraîne par IIS lire la valeur de Registre.

> [!NOTE]
> Paramètre **EnableExtensionlessUrls** 1 active le comportement de l’URL sans extension. Il s’agit du paramètre par défaut si aucune valeur n’est spécifiée.


<a id="0.1__Toc252995494"></a><a id="0.1__Toc255587643"></a><a id="0.1__Toc256770154"></a><a id="0.1__Toc245724862"></a>

## <a name="event-handlers-might-not-be-not-raised-in-a-default-document-in-iis-7-or-iis-75-integrated-mode"></a>Gestionnaires d’événements ne peuvent pas être déclenchés pas dans un Document par défaut dans IIS 7 ou IIS 7.5 Mode intégré

ASP.NET 4 inclut les modifications qui modifient la **action** attribut du code HTML **formulaire** élément est restitué quand une URL sans extension se traduit par un document par défaut. Un exemple d’une URL sans extension résolution à un document par défaut serait [http://contoso.com/](http://contoso.com/), se traduisant par une demande de [http://contoso.com/Default.aspx](http://contoso.com/Default.aspx).

ASP.NET 4 affiche maintenant le code HTML **formulaire** l’élément **action** valeur d’attribut comme une chaîne vide lorsqu’une demande est faite à une URL sans extension qui comporte un document par défaut mappé à elle. Par exemple, dans les versions antérieures d’ASP.NET, une demande de [http://contoso.com](http://contoso.com) entraînait une demande à `Default.aspx`. Dans ce document, l’ouverture **formulaire** balise serait restituée comme dans l’exemple suivant :

`<form action="Default.aspx" />`

Dans ASP.NET 4, une demande de [http://contoso.com](http://contoso.com) entraîne également une demande à `Default.aspx`. Toutefois, ASP.NET restitue maintenant l’ouverture HTML **formulaire** balise comme dans l’exemple suivant :

`<form action="" />`

Cette différence dans la façon dont le **action** attribut est rendu peut entraîner des modifications subtiles apportées dans le mode de traitement d’une publication de formulaire par IIS et ASP.NET. Lorsque le **action** attribut est une chaîne vide, IIS **DefaultDocumentModule** objet crée une demande enfant pour `Default.aspx`. Dans la plupart des conditions, cette demande enfant est transparente pour le code d’application et le `Default.aspx` page s’exécute normalement.

Toutefois, en raison d’une interaction potentielle entre le code managé et le mode intégré IIS 7 ou IIS 7.5, les pages .aspx managées peuvent cesser de fonctionner correctement pendant la demande enfant. Si les conditions suivantes sont remplies, la demande enfant pour un `Default.aspx` document entraîne une erreur ou un comportement inattendu :

1. Une page .aspx est envoyée au navigateur avec le **formulaire** l’élément **action** attribut la valeur « ».
2. Le formulaire est publié sur ASP.NET.
3. Un module HTTP managé lit une partie du corps d’entité. Par exemple, un module lit **Request.Form** ou **Request.Params**. Le corps d’entité de la demande POST est ainsi lu dans la mémoire managée. Le corps d’entité n’est donc plus disponible pour les modules de code natif qui s’exécutent en mode intégré IIS 7 ou IIS 7.5.
4. IIS **DefaultDocumentModule** objet finit par s’exécute et crée une demande enfant pour le `Default.aspx` document. Toutefois, étant donné que le corps d’entité a déjà été lu par une partie du code managé, aucun corps d’entité n’est disponible pour un envoi à la demande enfant.
5. Lorsque le pipeline HTTP s’exécute pour la demande enfant, le Gestionnaire de `.aspx` fichiers s’exécute pendant la phase d’exécution des gestionnaires.
6. Étant donné qu’aucun corps d’entité, il y a aucune variable de formulaire et aucun état d’affichage, et par conséquent, aucune information n’est disponible pour le Gestionnaire de page .aspx déterminer quel événement (le cas échéant) est censé être déclenché. Par conséquent, aucun des gestionnaires d’événements de publication pour la page .aspx concernée ne s’exécute.

Vous pouvez contourner ce comportement comme suit :

- Identifier le module HTTP qui accède au corps d’entité de la demande lors des demandes de document par défaut et déterminer si elle peut être configurée pour exécuter uniquement pour les demandes managés. En mode intégré pour IIS 7 et IIS 7.5, les modules HTTP peuvent être marqués pour s’exécuter uniquement pour les demandes managés en ajoutant l’attribut suivant pour le module **System.webServer/modules** entrée :

- `precondition="managedHandler"`

- Cette désactive le paramètre du module pour les demandes qu’IIS 7 et IIS 7.5 déterminent comme étant non géré des demandes. Pour les demandes de document par défaut, la première demande est une URL sans extension. Par conséquent, IIS ne s’exécute pas tous les modules managés qui sont marqués avec une condition préalable du gestionnaire managé pendant le traitement de la demande initiale. Par conséquent, les modules managés ne lira pas accidentellement le corps d’entité et par conséquent le corps d’entité est toujours disponible et est transmis à la demande enfant et au document par défaut.

- Si les modules HTTP problématiques ont à exécuter pour toutes les demandes (pour les fichiers statiques, des URL sans extension résolue en le **DefaultDocumentModule** objet, pour les demandes managés, etc.), modifie de manière explicite les pages .aspx affecté par définition de la **Action** propriété de la page **System.Web.UI.HtmlControls.HtmlForm** contrôle à une chaîne non vide. Par exemple, si le document par défaut est `Default.aspx`, modifier le code de la page pour définir explicitement la **HtmlForm** du contrôle **Action** propriété « Default.aspx ».

<a id="0.1__Toc255587644"></a><a id="0.1__Toc256770155"></a>

## <a name="changes-to-the-aspnet-code-access-security-cas-implementation"></a>Modifications apportées à l’implémentation de la sécurité (CAS) de l’accès de Code ASP.NET

ASP.NET 2.0, et par extension, les fonctionnalités d’ASP.NET qui ont été ajoutées à 3.5, utiliser le .NET Framework 1.1 et le modèle de sécurité (CAS) d’accès 2.0 et du code. Toutefois, l’implémentation de CAS dans ASP.NET 4 a été considérablement revue. Par conséquent, les applications ASP.NET de confiance partielle qui s’appuient sur le code de confiance en cours d’exécution dans le global assembly cache (GAC) peuvent échouer avec différentes exceptions de sécurité. Les applications de confiance partielle qui s’appuient sur des modifications importantes à la stratégie d’autorités de certification de l’ordinateur peuvent également échouer avec les exceptions de sécurité.

Vous pouvez rétablir la confiance partielle applications ASP.NET 4 pour le comportement ASP.NET 1.1 et 2.0 à l’aide de la nouvelle **legacyCasModel** d’attribut dans le **approbation** élément de configuration, comme indiqué dans l’exemple suivant :

`<trust level= "Medium" legacyCasModel="true" />`

Lorsque vous rétablissez le modèle CAS hérité, les comportements d’autorités de certification ancien suivants sont activés :

- Stratégie d’ordinateur autorités de certification est respectée.
- Plusieurs jeux d’autorisations différents dans un seul domaine d’application est autorisées.
- Assertions de l’autorisation explicite ne sont pas requises pour les assemblys dans le GAC qui sont invoquées lorsque ASP.NET ou tout autre code du .NET Framework se trouve sur la pile.

Un scénario ne peut pas être annulée dans le .NET Framework 4 : applications de confiance partielle non Web peuvent n’appellent plus certaines API dans System.Web.dll et System.Web.Extensions.dll. Dans les versions précédentes du .NET Framework, il était possible pour les applications de confiance partielle non Web pouvoir être accordé explicitement **autorisation AspNetHostingPermission** autorisations. Utilisent ensuite ces applications **System.Web.HttpUtility**, les types dans les **System.Web.ClientServices.\***  des types et des espaces de noms liés à l’appartenance, les rôles et les profils. Appel de ces types à partir d’applications de confiance partielle de non Web est plus pris en charge dans le .NET Framework 4.

> [!NOTE]
> Le **HtmlEncode** et **HtmlDecode** fonctionnalités de la **System.Web.HttpUtility** classe a été déplacé vers le nouveau .NET Framework 4  **System.Net.WebUtility** classe. Si c’était l’uniquement les fonctionnalités ASP.NET a été utilisée, modifier le code de l’application pour utiliser la nouvelle **WebUtility** classe à la place.


Voici un résumé succinct des modifications à l’implémentation d’autorités de certification par défaut dans ASP.NET 4 :

- Domaines d’application ASP.NET sont désormais des domaines d’application homogènes. Uniquement les jeux d’autorisations de confiance partielle et niveau de confiance totale sont disponibles dans un domaine d’application.
- Jeux d’autorisations de confiance partielle ASP.NET est indépendantes de toute stratégie d’autorités de certification de niveau entreprise, au niveau de l’ordinateur ou au niveau de l’utilisateur.
- Assemblys ASP.NET fournis dans 3.5 et 3.5 SP1 ont été convertis pour utiliser le modèle de transparence du .NET Framework 4.
- Utilisation d’ASP.NET **autorisation AspNetHostingPermission** attribut a été considérablement réduit. La plupart des instances de cet attribut ont été supprimés de la APIs ASP.NET publiques.
- Compilée dynamiquement les assemblys qui sont créés par les fournisseurs de générations ASP.NET ont été mis à jour pour marquer explicitement les assemblys comme étant transparent.
- Tous les assemblys ASP.NET sont désormais marqués de sorte que l’attribut APTCA est honorée uniquement dans les environnements d’hébergement Web. Environnements d’hébergement Web non niveau de confiance partielle tels que ClickOnce ne sera pas en mesure d’appeler des assemblys ASP.NET.

Pour plus d’informations sur le nouveau modèle de sécurité de l’accès de code ASP.NET 4, consultez [sécurité d’accès du Code à l’aide dans les Applications ASP.NET](https://msdn.microsoft.com/library/dd984947%28VS.100%29.aspx) sur le site Web MSDN.

<a id="0.1__Toc256770156"></a><a id="0.1__Toc245724863"></a><a id="0.1__Toc252995496"></a><a id="0.1__Toc255587645"></a><a id="0.1__Toc245724864"></a>

## <a name="membershipuser-and-other-types-in-the-systemwebsecurity-namespace-have-been-moved"></a>MembershipUser et autres Types dans le Namespace System.Web.Security ont été déplacés.

Certains types qui sont utilisés dans l’appartenance ASP.NET ont été déplacés de `System.Web.dll` au nouvel assembly System.Web.ApplicationServices.dll. Les types ont été déplacés pour résoudre des dépendances de couches architecturales entre des types dans le client et dans des références SKU .NET Framework étendues.

Les projets de site Web n’ont pas de problèmes à la suite de ces types, car System.Web.ApplicationServices.dll a été ajouté à la liste des assemblys référencés qui est utilisée par défaut par le système de compilation ASP.NET. Si vous mettez à niveau un projet de site Web qui a été créé à l’aide d’une version antérieure de ASP.NET vers ASP.NET 4 en l’ouvrant dans Visual Studio 2010, le projet sera compilé sans erreur.

De même, si vous mettez à niveau un projet d’application Web qui a été créé dans une version antérieure de ASP.NET vers ASP.NET 4 en l’ouvrant dans Visual Studio 2010, le processus de mise à niveau ajoute une référence à System.Web.ApplicationServices.dll au projet. Par conséquent, mis à niveau de Web, les projets d’application seront également compilée sans erreurs.

Fichiers compilés (binaires) qui ont été créés à l’aide de versions antérieures d’ASP.NET seront exécuteront également sans erreur sur ASP.NET 4, même si les types d’appartenance ont été déplacés vers un autre assembly. Transfert des informations de type a été ajouté à la version d’ASP.NET 4 de `System.Web.dll` qui achemine automatiquement les références d’exécution pour ces types vers le nouvel emplacement pour les types.

Toutefois, les bibliothèques de classes qui utilisent des types d’appartenance spécifique et qui ont été mis à niveau à partir de versions antérieures d’ASP.NET ne parvient pas à compiler lorsqu’il est utilisé dans un projet ASP.NET 4. Par exemple, un projet de bibliothèque de classes peut échouer compiler et signalent une erreur telle que la suivante :

- `The type 'System.Web.Security.MembershipUser' is defined in an assembly that is not referenced. You must add a reference to assembly 'System.Web.ApplicationServices, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'.`
  

- `The type name 'MembershipUser' could not be found. This type has been forwarded to assembly 'System.Web.ApplicationServices, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'. Consider adding a reference to that assembly.`

Vous pouvez contourner ce problème en ajoutant une référence dans votre projet de bibliothèque de classes à System.Web.ApplicationServices.dll.

La liste suivante indique les *System.Web.Security* les types qui ont été déplacés depuis `System.Web.dll` à System.Web.ApplicationServices.dll :

- *System.Web.Security.MembershipCreateStatus*
- *System.Web.Security.Membership.CreateUserException*
- *System.Web.Security.MembershipPasswordException*
- *System.Web.Security.MembershipPasswordFormat*
- *System.Web.Security.MembershipProvider*
- *System.Web.Security.MembershipProviderCollection*
- *System.Web.Security.MembershipUser*
- *System.Web.Security.MembershipUserCollection*
- *System.Web.Security.MembershipValidatePasswordEventHandler*
- *System.Web.Security.ValidatePasswordEventArgs*
- *System.Web.Security.RoleProvider*
- <a id="0.1_a"></a>*System.Web.Configuration.MembershipPasswordCompatibilityMode*

<a id="0.1__Toc256770157"></a>

## <a name="output-caching-changes-to-vary--http-header"></a>Sortie mise en cache des modifications pour faire varier \* en-tête HTTP

Dans ASP.NET 1.0, un bogue mis en cache de pages spécifié `Location="ServerAndClient"` comme un paramètre de cache de sortie pour émettre un `Vary:*` en-tête HTTP dans la réponse. Les navigateurs clients ne mettaient donc jamais en cache la page localement.

Dans la version 1.1 d’ASP.NET, le **System.Web.HttpCachePolicy.SetOmitVaryStar** méthode a été ajoutée, vous pouvez appeler pour supprimer la `Vary:*` en-tête. Cette méthode a été choisie parce que la modification de l’en-tête HTTP émis a été considérée comme une nouveauté modification à la fois. Toutefois, les développeurs ont été vous ne comprenez pas le comportement dans ASP.NET et les rapports de bogues indiquent que les développeurs ignorent existants **SetOmitVaryStar** comportement.

Dans ASP.NET 4, la décision a été effectuée pour résoudre le problème. Le `Vary:*` en-tête HTTP n’est plus émis à partir des réponses qui spécifient la directive suivante :

`<%@OutputCache Location="ServerAndClient" %>`

Par conséquent, **SetOmitVaryStar** n’est plus nécessaire afin de supprimer le `Vary:*` en-tête.

Dans les applications qui spécifient `Location="ServerAndClient"` dans les **@ OutputCache** directive sur une page, vous voyez maintenant le comportement impliqué par le nom de la **emplacement** valeur de l’attribut –, pages seront mises en cache dans le navigateur sans que vous appelez le **SetOmitVaryStar** (méthode).

Si les pages de votre application doivent émettre `Vary:*`, appelez le **AppendHeader** méthode, comme dans l’exemple suivant :

`HttpResponse.AppendHeader("Vary","*");`

Vous pouvez également modifier la valeur de la mise en cache de sortie **emplacement** l’attribut « Server ».

<a id="0.1__Toc255587646"></a><a id="0.1__Toc256770158"></a>

## <a name="systemwebsecurity-types-for-passport-are-obsolete"></a>Types System.Web.Security pour Passport sont obsolètes

La prise en charge de Passport intégrée à ASP.NET 2.0 a été obsolète et non pris en charge pour les années en raison de modifications apportées à Passport (désormais LiveID). Par conséquent, les cinq types liés à Passport dans **System.Web.Security** sont désormais marqués avec le **ObsoleteAttribute** attribut.

<a id="0.1__The_MenuItem.PopOutImageUrl_Propert"></a><a id="0.1__Toc256770159"></a>

## <a name="the-menuitempopoutimageurl-property-fails-to-render-an-image-in-aspnet-4"></a>La propriété MenuItem.PopOutImageUrl ne fournit pas une Image dans ASP.NET 4

Dans ASP.NET 3.5, le *MenuItem.PopOutImageUrl* propriété vous permet de spécifier l’URL d’une image qui est affichée dans un élément de menu pour indiquer que l’élément de menu a un sous-menu dynamique. L’exemple suivant montre comment spécifier cette propriété dans le balisage dans ASP.NET 3.5.

[!code-aspx[Main](breaking-changes/samples/sample11.aspx)]

À la suite d’une modification de conception dans ASP.NET 4, aucune sortie n’est restitué pour le *PopOutImageUrl* si la propriété est définie pour le *MenuItem* classe. Au lieu de cela, vous devez spécifier une URL d’image directement dans le *Menu* contrôler à l’aide soit le *StaticPopOutImageUrl* propriété ou le *DynamicPopOutImageUrl* propriété. Lorsque vous travaillez avec un menu statique, le *Menu.StaticPopOutImageUrl* propriété spécifie l’URL d’une image qui s’affiche pour indiquer que l’élément de menu statique a un sous-menu, comme indiqué dans l’exemple suivant :

[!code-aspx[Main](breaking-changes/samples/sample12.aspx)]

Si vous travaillez avec un menu dynamique, vous utilisez la *Menu.DynamicPopOutImageUrl* propriété pour spécifier l’URL d’une image qui indique qu’un élément de menu dynamique a un sous-menu. L’exemple suivant est semblable au précédent, mais il montre comment définir la *DynamicPopOutImageUrl* propriété pour un menu dynamique.

[!code-aspx[Main](breaking-changes/samples/sample13.aspx)]

Si le *Menu.DynamicPopOutImageUrl* propriété n’est pas définie et la *Menu.DynamicEnableDefaultPopOutImage* est définie sur *false*, aucune image n’est affichée. De même, si le *StaticPopOutImageUrl* propriété n’est pas définie et la *StaticEnableDefaultPopOutImage* est définie sur *false*, aucune image n’est affichée.

Lorsque vous définissez les chemins d’accès de ces propriétés, utilisez une barre oblique (/) au lieu d’une barre oblique inverse (\). Pour plus d’informations, consultez [Menu.StaticPopOutImageUrl et Menu.DynamicPopOutImageUrl Impossible de restituer les Images lors de chemins d’accès contiennent des barres obliques inverses](#0.1__Menu.StaticPopOutImageUrl_and_Menu. "_Menu.StaticPopOutImageUrl_and_Menu.") ailleurs dans ce document.

<a id="0.1__Menu.StaticPopOutImageUrl_and_Menu."></a><a id="0.1__Toc256770160"></a>

## <a name="menustaticpopoutimageurl-and-menudynamicpopoutimageurl-fail-to-render-images-when-paths-contain-backslashes"></a>Menu.StaticPopOutImageUrl et Menu.DynamicPopOutImageUrl échouent restituer des Images lorsque les chemins d’accès contiennent des barres obliques inverses

Dans ASP.NET 4, les images que vous spécifiez à l’aide de la *Menu.StaticPopOutImageUrl* et *Menu.DynamicPopOutImageUrl* propriétés ne sont pas rendus si le chemin d’accès contient backlashes (\). Il s’agit d’une modification à partir de versions antérieures d’ASP.NET.

L’exemple suivant de *Menu* contrôle de balise montre la *StaticPopOutImageUrl* propriété définie à l’aide d’un chemin d’accès qui contient une barre oblique inverse. Dans ASP.NET 4, l’image spécifiée dans la propriété n’est pas rendus.

[!code-aspx[Main](breaking-changes/samples/sample14.aspx)]

Pour corriger ce problème, modifiez les valeurs de chemin d’accès qui sont spécifiés dans le *StaticPopOutImageUrl* et *DynamicPopOutImageUrl* propriétés à utiliser des barres obliques (/). L’exemple suivant illustre cette modification :

[!code-aspx[Main](breaking-changes/samples/sample15.aspx)]

Notez que les applications qui ont été migrées à partir de versions antérieures d’ASP.NET vers ASP.NET 4 peuvent également être affecté, car le *MenuItem.PopOutImageUrl* propriété a été modifiée. Pour plus d’informations, consultez [MenuItem.PopOutImageUrl la propriété échoue pour restituer une Image dans ASP.NET 4](#0.1__The_MenuItem.PopOutImageUrl_Propert "_The_MenuItem.PopOutImageUrl_Propert") ailleurs dans ce document.

<a id="0.1__Toc224729061"></a><a id="0.1__Toc255587647"></a><a id="0.1__Toc256770161"></a>

## <a name="disclaimer"></a>Exclusion de responsabilité

Ce document est une version préliminaire et peut être modifié substantiellement avant le lancement de la mise en production commerciale finale du logiciel qu’il décrit.

Les informations contenues dans ce document correspondent à la connaissance que Microsoft Corporation possède des problèmes abordés à la date de la publication. Microsoft devant répondre à des conditions de marché qui évoluent, ce document ne doit pas être considéré comme un engagement de sa part, et Microsoft ne peut pas garantir l’exactitude des informations présentées à la date de la publication.

Ce livre blanc est fourni à titre d'information uniquement. MICROSOFT NE FOURNIT AUCUNE GARANTIE, EXPRESSE, IMPLICITE OU LÉGALE, QUANT AUX INFORMATIONS CONTENUES DANS CE DOCUMENT.

L'utilisateur est tenu d'observer la réglementation relative aux droits d'auteur applicable dans son pays. Aucune partie de ce document ne peut être reproduite, stockée ou introduite dans un système de restitution, ou transmise à quelque fin ou par quelque moyen que ce soit (électronique, mécanique, photocopie, enregistrement ou autre) sans la permission expresse et écrite de Microsoft Corporation.

Microsoft peut détenir des brevets, avoir déposé des demandes d’enregistrement de brevets ou être titulaire de marques, droits d’auteur ou autres droits de propriété intellectuelle portant sur tout ou partie des éléments qui font l’objet du présent document. Sauf stipulation expresse contraire d’un contrat de licence écrit de Microsoft, la fourniture de ce document n’a pas pour effet de vous concéder une licence sur ces brevets, marques, droits d’auteur ou autres droits de propriété intellectuelle.

Sauf mention contraire, les noms de sociétés, d'organisations, de produits et de domaines, les adresses de messagerie, les logos, et les noms de personnes et de lieux, ou les événements utilisés dans les exemples, sont fictifs et toute ressemblance avec des noms ou des événements réels est purement fortuite et involontaire.

© 2010 Microsoft Corporation. Tous droits réservés.

Microsoft et Windows sont soit des marques déposées de Microsoft Corporation, soit des marques de Microsoft Corporation aux États-Unis et/ou dans d'autres pays.

Les noms des sociétés et des produits mentionnés dans le présent document peuvent être des marques de leurs propriétaires respectifs.
