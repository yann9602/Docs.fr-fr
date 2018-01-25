---
uid: web-forms/overview/older-versions-security/introduction/forms-authentication-configuration-and-advanced-topics-cs
title: "Rubriques avancées (c#) et Configuration de l’authentification Forms | Documents Microsoft"
author: rick-anderson
description: "Dans ce didacticiel, nous examiner les différents paramètres de l’authentification de formulaires et voir comment les modifier dans l’élément de formulaire. Cela entraîne un détaillées..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/14/2008
ms.topic: article
ms.assetid: b9c29865-a34e-48bb-92c0-c443a72cb860
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-security/introduction/forms-authentication-configuration-and-advanced-topics-cs
msc.type: authoredcontent
ms.openlocfilehash: c57722965b510ac4f5cf0c06c7c01c8cea26384f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="forms-authentication-configuration-and-advanced-topics-c"></a>Configuration de l’authentification de formulaires et les rubriques avancées (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/2/F/7/2F705A34-F9DE-4112-BBDE-60098089645E/ASPNET_Security_Tutorial_03_CS.zip) ou [télécharger le PDF](http://download.microsoft.com/download/2/F/7/2F705A34-F9DE-4112-BBDE-60098089645E/aspnet_tutorial03_AuthAdvanced_cs.pdf)

> Dans ce didacticiel, nous examiner les différents paramètres de l’authentification de formulaires et voir comment les modifier dans l’élément de formulaire. Cela entraîne une présentation détaillée de la personnalisation de valeur de délai d’expiration du ticket d’authentification de formulaires, à l’aide d’une page de connexion avec une URL personnalisée (par exemple, SignIn.aspx au lieu de Login.aspx) et les tickets d’authentification par formulaire sans cookies.


## <a name="introduction"></a>Introduction

Dans le [didacticiel précédent](an-overview-of-forms-authentication-cs.md) , nous avons étudié les étapes nécessaires pour implémenter l’authentification par formulaire dans une application ASP.NET, à partir de la spécification des paramètres de configuration dans le fichier Web.config à la création d’un journal dans la page à l’affichage de différents contenu pour les utilisateurs authentifiés et anonymes. Rappelez-vous que nous avons configuré le site Web pour utiliser l’authentification par formulaire en définissant l’attribut mode de le &lt;authentification&gt; élément aux formulaires. Le &lt;authentification&gt; élément peut éventuellement inclure un &lt;forms&gt; élément enfant, par le biais duquel un large éventail de paramètres d’authentification forms peut être spécifié.

Dans ce didacticiel nous examiner les différents paramètres de l’authentification de formulaires et voir comment les modifier via le &lt;forms&gt; élément. Cela entraîne une présentation détaillée de la personnalisation de valeur de délai d’expiration du ticket d’authentification de formulaires, à l’aide d’une page de connexion avec une URL personnalisée (par exemple, SignIn.aspx au lieu de Login.aspx) et les tickets d’authentification par formulaire sans cookies. Nous seront également examiner plus en détail de la composition du ticket d’authentification forms et consultez les précautions QU'ASP.NET emprunté pour vous assurer que les données du ticket sont sécurisées à partir d’inspection et de falsification. Enfin, nous allons étudier comment stocker des données d’utilisateur supplémentaires dans le ticket d’authentification de formulaires et aux données via un objet principal personnalisé.

## <a name="step-1-examining-the-ltformsgt-configuration-settings"></a>Étape 1 : Examen du &lt;formulaires&gt; paramètres de Configuration

Le système d’authentification de formulaires dans ASP.NET offre un nombre de paramètres de configuration qui peuvent être personnalisés sur une base de l’application par application. Cela inclut les paramètres tels que : la durée de vie de l’authentification de formulaires de ticket ; quel type de protection est appliquée au ticket ; l’authentification sans cookie quelles conditions les tickets sont utilisés ; le chemin d’accès à la page de connexion ; et d’autres informations. Pour modifier les valeurs par défaut, ajoutez un [ &lt;forms&gt; élément](https://msdn.microsoft.com/library/1d3t3c61.aspx) en tant qu’enfant de la [ &lt;authentification&gt; élément](https://msdn.microsoft.com/library/532aee0e.aspx), en spécifiant les propriétés les valeurs que vous souhaitez personnaliser en tant qu’attributs XML comme suit :

[!code-xml[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample1.xml)]

Le tableau 1 résume les propriétés qui peuvent être personnalisées par le biais du &lt;forms&gt; élément. Étant donné que le fichier Web.config est un fichier XML, les noms d’attribut dans la colonne gauche respectent la casse.

| **Attribut** | **Description** |
| --- | --- |
| sans cookie | Cet attribut spécifie les conditions dans lesquelles le ticket d’authentification est stocké dans un cookie et qui est incorporé dans l’URL. Les valeurs autorisées sont : UseCookies ; UseUri ; Détection automatique ; et UseDeviceProfile (la valeur par défaut). Étape 2 examine ce paramètre plus en détail. |
| defaultUrl | Indique l’URL que les utilisateurs sont redirigés vers après s’être connecté à partir de la page de connexion s’il n’existe aucune valeur RedirectUrl spécifié dans la chaîne de requête. La valeur par défaut est default.aspx. |
| de domaine | Lors de l’utilisation de tickets d’authentification basée sur les cookies, ce paramètre spécifie la valeur du cookie domaine. La valeur par défaut est une chaîne vide, ce qui entraîne le navigateur à utiliser le domaine à partir duquel il a été délivré (par exemple, www.yourdomain.com). Dans ce cas, le cookie sera **pas** envoyer lors de la fabrication des demandes à sous-domaines, telles que admin.yourdomain.com. Si vous souhaitez que le cookie doit être passé à tous les sous-domaines, que vous devez personnaliser l’attribut de domaine la valeur votredomaine.com. |
| enableCrossAppRedirects | Valeur booléenne qui indique si les utilisateurs authentifiés sont mémorisés lorsque redirigés vers des URL dans d’autres applications web sur le même serveur. La valeur par défaut est false. |
| loginUrl | L’URL de la page de connexion. La valeur par défaut est login.aspx. |
| name | Lors de l’utilisation de tickets d’authentification basée sur un cookie, le nom du cookie. La valeur par défaut est. ASPXAUTH. |
| path | Lors de l’utilisation de tickets d’authentification basée sur les cookies, ce paramètre spécifie attribut de chemin d’accès du cookie. L’attribut de chemin d’accès permet au développeur limiter l’étendue d’un cookie pour une hiérarchie de répertoires particulière. La valeur par défaut est /, qui informe le navigateur à envoyer le cookie de ticket d’authentification pour toute demande adressée au domaine. |
| protection | Indique quelles techniques sont utilisées pour protéger le ticket d’authentification de formulaires. Les valeurs autorisées sont : toutes les (la valeur par défaut) ; Chiffrement ; None ; et la Validation. Ces paramètres sont décrits en détail à l’étape 3. |
| requireSSL | Valeur booléenne qui indique si une connexion SSL est requise pour transmettre le cookie d’authentification. La valeur par défaut est false. |
| SlidingExpiration | Valeur booléenne qui indique si le délai d’expiration du cookie d’authentification est réinitialisé chaque fois que l’utilisateur visite le site pendant une session unique. La valeur par défaut est true. La stratégie de délai d’expiration du ticket d’authentification est décrite plus en détail dans la spécification section sur les valeurs de délai d’expiration du Ticket. |
| délai d'expiration | Spécifie la durée, en minutes, après laquelle le cookie de ticket d’authentification expire. La valeur par défaut est 30. La stratégie de délai d’expiration du ticket d’authentification est décrite plus en détail dans la spécification section sur les valeurs de délai d’expiration du Ticket. |

**Tableau 1**: un récapitulatif de le &lt;forms&gt; attributs de l’élément

Dans ASP.NET 2.0 et versions ultérieures, la valeur par défaut des valeurs d’authentification forms sont codés en dur dans la classe FormsAuthenticationConfiguration dans le .NET Framework. Toutes les modifications doivent être appliquées sur une base de l’application en application dans le fichier Web.config. Cela diffère de ASP.NET 1.x, où les valeurs de l’authentification de formulaires par défaut ont été stockées dans le fichier machine.config (et par conséquent peut être modifiés via la modification de machine.config). Sur la rubrique d’ASP.NET 1.x, il est judicieux de mentionner que plusieurs les paramètres du système d’authentification forms ont des valeurs par défaut différentes dans ASP.NET 2.0 et autres fonctionnalités que dans ASP.NET 1.x. Si vous migrez votre application à partir d’un environnement de 1.x ASP.NET, il est important de connaître ces différences. Consultez [le &lt;forms&gt; documentation technique d’élément](https://msdn.microsoft.com/library/1d3t3c61.aspx) pour obtenir la liste des différences.

> [!NOTE]
> Plusieurs paramètres d’authentification de formulaires, tels que le délai d’attente, le domaine et le chemin d’accès, spécifient les détails pour le cookie de ticket d’authentification forms résultant. Pour plus d’informations sur les cookies, leur fonctionnement et leurs différentes propriétés, consultez [ce didacticiel Cookies](http://www.quirksmode.org/js/cookies.html).


### <a name="specifying-the-tickets-timeout-value"></a>En spécifiant la valeur de délai d’attente du Ticket

Le ticket d’authentification par formulaires est un jeton qui représente une identité. Avec les tickets d’authentification basée sur les cookies, ce jeton est conservé sous la forme d’un cookie et envoyé au serveur à chaque demande web. Possession du jeton, par essence, déclare, je *nom d’utilisateur*, je ne se sont déjà connectés et est utilisée pour que l’identité d’un utilisateur peut être mémorisée une visite de la page.

Le ticket d’authentification forms comprend l’identité de l’utilisateur mais contient également des informations pour aider à garantir l’intégrité et la sécurité du jeton. Après tout, nous ne voulons pas un utilisateur malveillant pour pouvoir créer un jeton de contrefaçon, ou pour modifier un jeton légitimes d’une certaine façon underhanded.

Un bit de ce type d’informations inclus dans le ticket est un *expiration*, qui est la date et l’heure que le ticket n’est plus valide. Chaque fois que le FormsAuthenticationModule inspecte un ticket d’authentification, elle garantit que les expiration du ticket n’a pas encore réussi. S’il a, il ignore le ticket et identifie l’utilisateur comme étant anonyme. Cette précaution vous protège contre les attaques par relecture. Sans un délai d’expiration, si un pirate a été en mesure d’obtenir ses mains sur un ticket d’authentification valide d’un utilisateur - peut-être par l’accès physique à leur ordinateur et racine via leurs cookies - ils pourrait envoyer une demande au serveur avec ce ticket d’authentification volé et peut accéder. Alors que l’expiration n’empêche pas ce scénario, il ne limite la fenêtre au cours de laquelle une telle attaque peut réussir.

> [!NOTE]
> Étape 3 détails techniques supplémentaires utilisées par le système d’authentification de formulaires pour protéger le ticket d’authentification.


Lorsque vous créez le ticket d’authentification, le système d’authentification forms détermine son expiration en consultant le paramètre de délai d’attente. Comme indiqué dans le tableau 1, le délai d’attente définissant les valeurs par défaut à 30 minutes, ce qui signifie que lors de la création du ticket d’authentification forms son expiration est définie à une date et heure, 30 minutes dans le futur.

L’expiration définit une heure absolue à l’avenir lorsque le ticket d’authentification forms expire. Toutefois, généralement aux développeurs d’implémenter une expiration décalée, qui est réinitialisé chaque fois que l’utilisateur visite le site. Ce comportement est déterminé par les paramètres de slidingExpiration. Si la valeur true (valeur par défaut), chaque fois que le FormsAuthenticationModule authentifie un utilisateur, il met à jour les expiration du ticket. Si la valeur est false, l’expiration n’est pas mis à jour à chaque demande, ce qui provoquera l’expiration du délai d’attente exactement les nombre de minutes après le ticket lors de la première du ticket est créé.

> [!NOTE]
> L’expiration stockée dans le ticket d’authentification est une date absolue et la valeur d’heure, tel que 2 août 2008 11:34:00. En outre, la date et l’heure sont par rapport au temps de local du serveur web. Cette décision peut avoir des effets intéressants autour de l’heure d’été (DST), c'est-à-dire lorsque les horloges aux États-Unis sont déplacés en avance d’une heure (en supposant que le serveur web est hébergé dans les paramètres régionaux où l’heure d’été est prise en charge). Envisagez les conséquences d’un site Web d’ASP.NET avec une expiration de 30 minutes près de l’heure de début de l’heure d’été (qui correspond à 2 h 00). Imaginez qu'un visiteur se connecte sur le site du 11 mars 2008 à 1 h 55. Cela génère un ticket d’authentification de formulaires qui expire à 11 mars 2008 à 2 h 25 (30 minutes dans le futur). Toutefois, une fois que la prochaine 2 h 00, l’horloge passe à 3:00 AM en raison de l’heure d’été. Lorsque l’utilisateur charge une nouvelle page six minutes après l’ouverture de session (à 3 h 01), FormsAuthenticationModule note que le ticket a expiré et qu’il redirige l’utilisateur vers la page de connexion. Pour une discussion plus approfondie sur cela et autres singularités de délai d’expiration de ticket d’authentification, ainsi que solutions de contournement, ils récupèrent une copie de Stefan Schackow *Professional ASP.NET 2.0 sécurité, l’appartenance et la gestion des rôles* (ISBN : 978-0-7645-9698-8).


La figure 1 illustre le flux de travail lorsque slidingExpiration est définie sur false et le délai d’attente est défini sur 30. Notez que le ticket d’authentification généré lors de la connexion contient la date d’expiration, et cette valeur n’est pas mis à jour sur les demandes suivantes. Si le FormsAuthenticationModule détecte que le ticket a expiré, il supprime et traite la requête comme anonyme.


[![Une représentation graphique de slidingExpiration d’expiration lors du Ticket d’authentification de formulaires a la valeur false](forms-authentication-configuration-and-advanced-topics-cs/_static/image2.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image1.png)

**Figure 01**: représentation graphique de slidingExpiration d’expiration lors du Ticket d’authentification de formulaires est false ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image3.png))


La figure 2 illustre le flux de travail lorsque slidingExpiration est définie sur true et le délai d’attente est défini sur 30. Lorsqu’une demande authentifiée est reçue (avec un ticket non expirés) son expiration est mis à jour au nombre de délai d’attente de minutes dans le futur.


[![Une représentation graphique du Ticket d’authentification de formulaires lorsque slidingExpiration est true](forms-authentication-configuration-and-advanced-topics-cs/_static/image5.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image4.png)

**Figure 02**: représentation graphique du Ticket d’authentification de formulaires lorsque slidingExpiration est true ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image6.png))


Lors de l’utilisation de tickets d’authentification basée sur les cookies (la valeur par défaut), cette discussion devient un peu plus compliquée, car les cookies peuvent également avoir leurs propre expirations dans le spécifié. Expiration d’un cookie (ou d’absence de ce) indique au navigateur lorsque le cookie doit être détruit. Si le cookie ne dispose pas d’un délai d’expiration, il est détruit lorsque le navigateur s’arrête. Si un délai d’expiration est présent, toutefois, le cookie reste stocké sur l’ordinateur de l’utilisateur jusqu'à la date et heure spécifiée dans l’expiration est passée. Lorsqu’un cookie est détruit par le navigateur, il n’est plus envoyé au serveur web. Par conséquent, la destruction d’un cookie est analogue à l’utilisateur de journalisation du site.

> [!NOTE]
> Bien entendu, un utilisateur peut supprimer proactive de tous les cookies stockés sur leur ordinateur. Dans Internet Explorer 7, vous accédez à outils, Options et cliquez sur le bouton Supprimer dans la section de l’historique de navigation. À partir de là, cliquez sur Supprimer les cookies.


Le système d’authentification de formulaires crée des cookies de session ou d’expiration en fonction de la valeur transmise à la *persistCookie* paramètre. Souvenez-vous que la classe FormsAuthentication GetAuthCookie, SetAuthCookie et RedirectFromLoginPage acceptent deux paramètres d’entrée : *nom d’utilisateur* et *persistCookie*. La page de connexion que nous avons créés dans le didacticiel précédent inclus une Mémoriser mes informations case à cocher, de déterminer si un cookie persistant a été créé. Les cookies persistants sont en fonction d’expiration ; cookies non persistants sont basés sur une session.

Les concepts de délai d’attente et slidingExpiration présentés précédemment appliquent la même pour les deux cookies basées sur session et d’expiration. Il existe uniquement une petite différence au niveau de l’exécution : lors de l’utilisation de cookies d’expiration avec slidingTimeout la valeur true, les expiration du cookie sont uniquement mis à jour lorsque plus de la moitié du temps spécifié écoulé.

Nous allons mettre à jour les stratégies de délai d’expiration de ticket d’authentification de notre site Web afin que des tickets de délai d’attente après une heure (60 minutes), à l’aide d’une expiration décalée. Pour effectuer cette modification, mettre à jour le fichier Web.config, ajout d’un &lt;forms&gt; élément à la &lt;l’authentification&gt; élément par le balisage suivant :

[!code-xml[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample2.xml)]

### <a name="using-an-login-page-url-other-than-loginaspx"></a>À l’aide d’une URL de Page de connexion autres que Login.aspx

Étant donné que FormsAuthenticationModule redirige automatiquement les utilisateurs non autorisés vers la page de connexion, il a besoin de connaître l’URL de la page de connexion. Cette URL est spécifiée par l’attribut loginUrl dans le &lt;forms&gt; élément et la valeur par défaut est login.aspx. Si vous déplacez sur un site Web existant, vous avez peut-être déjà une page de connexion avec une autre URL, ce qui a déjà été contenant un signet et indexé par les moteurs de recherche. Au lieu de renommer votre page de connexion existante vers login.aspx et avec rupture des liens et les signets d’utilisateurs, vous pouvez à la place modifier l’attribut loginUrl pour pointer vers votre page de connexion.

Par exemple, si votre page de connexion a été nommé SignIn.aspx et se trouvait dans l’annuaire d’utilisateurs, vous pouvez pointer le paramètre de configuration loginUrl à ~/Users/SignIn.aspx comme suit :

[!code-xml[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample3.xml)]

Étant donné que notre application a déjà en cours a une page de connexion nommée Login.aspx, il est inutile de spécifier une valeur personnalisée dans le &lt;forms&gt; élément.

## <a name="step-2-using-cookieless-forms-authentication-tickets"></a>Étape 2 : À l’aide de Tickets d’authentification par formulaire sans cookie

Par défaut, le système d’authentification forms détermine s’il faut stocker ses tickets d’authentification dans la collection de cookies ou de les incorporer dans l’URL en fonction de l’agent utilisateur visitant le site. Tous les navigateurs de bureau standard comme Internet Explorer, Firefox, Opera et Safari, les cookies de prise en charge, mais pas tous les appareils mobiles ne.

La stratégie utilisée par le système d’authentification forms varie selon le paramètre cookieless dans le &lt;forms&gt; élément, ce qui peut être assigné à une des quatre valeurs :

- UseCookies - Spécifie que les tickets d’authentification basée sur le cookie seront toujours utilisés.
- UseUri - indique que les tickets de l’authentification par cookie ne seront jamais utilisées.
- Détection automatique - si le profil de périphérique ne prend pas en charge les cookies, les tickets de l’authentification basée sur les cookies ne sont pas utilisés. Si le profil du périphérique prend en charge les cookies, un mécanisme de détection permet de déterminer si les cookies sont activés.
- UseDeviceProfile - la valeur par défaut ; utilise des tickets de l’authentification par cookie uniquement si le profil du périphérique prend en charge les cookies. Aucun mécanisme de détection n’est utilisé.

Les paramètres de détection automatique et UseDeviceProfile s’appuient sur une *profil de périphérique* de déterminer s’il faut utiliser des tickets d’authentification basé sur cookie ou sans cookies. ASP.NET gère une base de données de différents appareils et de leurs fonctionnalités, telles que s’ils prennent en charge les cookies, quelle version de JavaScript, ils prennent en charge et ainsi de suite. Chaque fois qu’un périphérique demande une page web à partir d’un serveur web qu’il envoie le long d’un *agent utilisateur* en-tête HTTP qui identifie le type de périphérique. ASP.NET met automatiquement en correspondance la chaîne user-agent fourni avec le profil correspondant spécifié dans sa base de données.

> [!NOTE]
> Cette base de données de fonctionnalités de l’appareil est stocké dans un nombre de fichiers XML qui respectent le [schéma de fichier de définition de navigateur](https://msdn.microsoft.com/library/ms228122.aspx). Les fichiers de profil de périphérique par défaut se trouvent dans % WINDIR%\Microsoft.Net\Framework\v2.0.50727\CONFIG\Browsers. Vous pouvez également ajouter des fichiers personnalisés à application votre application\_dossier des navigateurs. Pour plus d’informations, consultez [Comment : détecter les Types de navigateurs dans les Pages Web ASP.NET](https://msdn.microsoft.com/library/3yekbd5b.aspx).


Étant donné que le paramètre par défaut est UseDeviceProfile, tickets d’authentification par formulaire sans cookies seront utilisés lorsque le site est visité par un périphérique dont le profil signale qu’il ne prend pas en charge les cookies.

### <a name="encoding-the-authentication-ticket-in-the-url"></a>Le Ticket d’authentification dans l’URL de l’encodage

Les cookies sont un support naturel des informations à partir du navigateur dans chaque demande d’un site Web particulier, c’est pourquoi les paramètres d’authentification de formulaires par défaut utilisent des cookies si l’appareil visite les prend en charge. Si les cookies ne sont pas pris en charge, d’autres moyens pour transmettre le ticket d’authentification du client au serveur doit être utilisée. Une solution courante utilisée dans des environnements sans cookie consiste à encoder les données de cookie dans l’URL.

La meilleure façon de voir comment ces informations peuvent être incorporées dans l’URL est pour forcer le site utilise des tickets d’authentification sans cookies. Cela peut être accompli en définissant le paramètre de configuration sans cookie pour UseUri :

[!code-xml[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample4.xml)]

Une fois que vous avez apporté cette modification, visitez le site via un navigateur. Lors de la visite en tant qu’utilisateur anonyme, l’URL ressemble exactement comme auparavant. Par exemple, lorsque vous visitez la page Default.aspx barre d’adresses de mon navigateur affiche l’URL suivante :

`http://localhost:2448/ASPNET\_Security\_Tutorial\_03\_CS/default.aspx`

Toutefois, lors de la connexion, le ticket d’authentification par formulaires est incorporé dans l’URL. Par exemple, après la visite de la page de connexion et d’ouverture de session en tant que Sam, je reviens à la page Default.aspx, mais cette fois de l’URL est :

`http://localhost:2448/ASPNET\_Security\_Tutorial\_03\_CS/(F(jaIOIDTJxIr12xYS-VVgkqKCVAuIoW30Bu0diWi6flQC-FyMaLXJfow\_Vd9GZkB2Cv-rfezq0gKadKX0YPZCkA2))/default.aspx`

Le ticket d’authentification forms a été intégré dans l’URL. La chaîne (F (jaIOIDTJxIr12xYS-VVgkqKCVAuIoW30Bu0diWi6flQC-FyMaLXJfow\_Vd9GZkB2Cv-rfezq0gKadKX0YPZCkA2) représente les informations de ticket d’authentification de codé en hexadécimal, et sont les mêmes données sont généralement stockées dans un cookie.

Dans l’ordre cookieless tickets d’authentification à utiliser, le système doit encoder toutes les URL dans la page pour inclure les données de ticket d’authentification, sinon le ticket d’authentification seront perdu lorsque l’utilisateur clique sur un lien. Heureusement, cette logique d’incorporation est effectuée automatiquement. Pour illustrer cette fonctionnalité, ouvrez la page Default.aspx et ajouter un contrôle de lien hypertexte, en définissant ses propriétés Text et NavigateUrl pour tester le lien et SomePage.aspx, respectivement. Peu importe qu’il n’est pas vraiment une page dans notre projet nommé SomePage.aspx.

Enregistrez les modifications dans Default.aspx, puis vous accédez il via un navigateur. Ouvrez une session le site afin que le ticket d’authentification par formulaires est incorporé dans l’URL. Ensuite, à partir de Default.aspx, cliquez sur le lien de lien de Test. Que s'est-il passé ? Si aucune page nommé SomePage.aspx existe, puis une erreur 404 s’est produite, mais qui n’est pas ce qui est important ici. Concentrez-vous plutôt sur la barre d’adresses dans votre navigateur. Notez qu’il inclut le ticket d’authentification de formulaires dans l’URL !

`http://localhost:2448/ASPNET\_Security\_Tutorial\_03\_CS/(F(jaIOIDTJxIr12xYS-VVgkqKCVAuIoW30Bu0diWi6flQC-FyMaLXJfow\_Vd9GZkB2Cv-rfezq0gKadKX0YPZCkA2))/SomePage.aspx`

L’URL SomePage.aspx dans le lien a été automatiquement convertie en une URL incluant le ticket d’authentification - nous n’aviez pas à écrire la moindre ligne de code ! Le ticket d’authentification par formulaire est incorporé automatiquement dans l’URL pour tous les liens hypertexte qui ne commencent pas par http:// ou /. Peu importe si le lien hypertexte s’affiche dans un appel de Response.Redirect, dans un contrôle de lien hypertexte ou dans un élément d’ancrage HTML (par exemple, &lt;un href = «... »&gt;... &lt;/a&gt;). Tant que l’URL n’est pas quelque chose comme http://www.someserver.com/SomePage.aspx ou /SomePage.aspx, le ticket d’authentification forms est incorporé pour nous.

> [!NOTE]
> Tickets d’authentification par formulaire sans cookies respectent les mêmes stratégies de délai d’attente que les tickets d’authentification basée sur le cookie. Toutefois, les tickets d’authentification sans cookies sont plus sujets aux attaques par relecture, car le ticket d’authentification est directement incorporé dans l’URL. Imaginez un utilisateur visite un site Web, se connecte, puis colle l’URL dans un message électronique à un collègue. Si la collègue clique sur ce lien avant l’expiration est atteint, elles seront enregistrées en tant que l’utilisateur qui a envoyé le message électronique !


## <a name="step-3-securing-the-authentication-ticket"></a>Étape 3 : Sécurisation le Ticket d’authentification

Le ticket d’authentification par formulaires est transmis via le réseau soit dans un cookie ou incorporée directement dans l’URL. En plus des informations d’identité, le ticket d’authentification peut également inclure des données utilisateur (comme nous allons le voir à l’étape 4). Par conséquent, il est important que les données du ticket sont chiffrées des regards et (surtout) que le système d’authentification forms peut garantir que le ticket a été pas falsifié.

Pour garantir la confidentialité des données du ticket, le système d’authentification forms peut chiffrer les données de ticket. Échec de chiffrer les données de ticket envoie des informations potentiellement sensibles sur le réseau en texte brut.

Pour garantir l’authenticité d’un ticket, le système d’authentification forms doit *valider* le ticket. La validation est le fait de s’assurer qu’une portion de données n’a pas été modifiée et s’effectue un  *[le code d’authentification (MAC) du message](http://en.wikipedia.org/wiki/Message_authentication_code)*. En bref, le MAC est une petite partie des informations qui identifient les données qui devant être validée (dans ce cas, le ticket). Si les données représentées par le MAC sont modifiées, puis le MAC et les données ne correspondront pas. En outre, il est par le calcul difficile pour un pirate à modifier les données et générer son propre MAC pour correspondre avec les données modifiées.

Lors de la création (ou modification) un ticket, le système d’authentification forms crée un MAC et l’attache aux données du ticket. Lorsqu’une demande suivante arrive, le système d’authentification de formulaires compare les données de ticket et le MAC pour valider l’authenticité des données de ticket. La figure 3 illustre ce flux de travail sous forme graphique.


[![L’authenticité du Ticket est assurée via un MAC](forms-authentication-configuration-and-advanced-topics-cs/_static/image8.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image7.png)

**Figure 03**: authenticité du Ticket est assurée via un MAC ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image9.png))


Les mesures de sécurité sont appliquées pour le ticket d’authentification varie selon le paramètre de protection dans le &lt;forms&gt; élément. Le paramètre de protection peut-être être affecté à une des trois valeurs suivantes :

- Tout - le ticket est chiffré et signé numériquement (la valeur par défaut).
- Chiffrement : uniquement le cryptage est appliqué : aucun MAC n’est généré.
- Aucun - le ticket n’est ni chiffré ni signé numériquement.
- Validation - un MAC est générée, mais les données de ticket sont envoyées sur le réseau en texte brut.

Microsoft recommande l’utilisation du paramètre toutes les.

### <a name="setting-the-validation-and-decryption-keys"></a>Définition de la Validation et les clés de déchiffrement

Le chiffrement et les algorithmes de hachage utilisés par le système d’authentification de formulaires pour chiffrer et valider le ticket d’authentification sont personnalisables par le biais du [ &lt;machineKey&gt; élément](https://msdn.microsoft.com/library/w8h3skw9.aspx) dans le fichier Web.config. Le tableau 2 décrit les &lt;machineKey&gt; attributs de l’élément et leurs valeurs possibles.

| **Attribut** | **Description** |
| --- | --- |
| déchiffrement | Indique l’algorithme utilisé pour le chiffrement. Cet attribut peut avoir une des quatre valeurs suivantes : - Auto - la valeur par défaut ; Détermine l’algorithme en fonction de la longueur de l’attribut validationKey. -AES - utilise le [Standard (AES)](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) algorithme. -DES - utilise le [Standard DES (Data Encryption)](http://en.wikipedia.org/wiki/Data_Encryption_Standard) cet algorithme est considérée comme faible de nombreuses ressources de calcul et ne doit pas être utilisé. -3DES - utilise le [Triple DES](http://en.wikipedia.org/wiki/Triple_DES) algorithme, qui fonctionne en appliquant l’algorithme DES trois fois. |
| validationKey | La clé secrète utilisée par l’algorithme de chiffrement. Cette valeur doit être une chaîne hexadécimale de longueur appropriée (selon la valeur de déchiffrement), AutoGenerate ou une valeur ajoutée, IsolateApps. Ajout de IsolateApps indique à ASP.NET d’utiliser une valeur unique pour chaque application. La valeur par défaut est AutoGenerate, IsolateApps. |
| validation | Indique l’algorithme utilisé pour la validation. Cet attribut peut avoir une des quatre valeurs suivantes : - AES - utilise l’algorithme Advanced Encryption Standard (AES). -MD5 - utilise le [Message Digest 5 (MD5)](http://en.wikipedia.org/wiki/MD5) algorithme. -SHA1 - utilise le [SHA1](http://en.wikipedia.org/wiki/Sha1) algorithme (la valeur par défaut). -3DES - utilise l’algorithme Triple DES. |
| validationKey | La clé secrète utilisée par l’algorithme de validation. Cette valeur doit être une chaîne hexadécimale de la longueur appropriée (basé sur la valeur de validation), AutoGenerate ou une valeur ajoutée, IsolateApps. Ajout de IsolateApps indique à ASP.NET d’utiliser une valeur unique pour chaque application. La valeur par défaut est AutoGenerate, IsolateApps. |

**Tableau 2**: le &lt;machineKey&gt; des attributs d’élément

Une discussion détaillée de ces options de chiffrement et la validation et les professionnels de l’et les inconvénients des différents algorithmes, sont dépasse le cadre de ce didacticiel. Pour en profondeur examiner ces problèmes, notamment des conseils sur les algorithmes de chiffrement et de validation à utiliser, les longueurs de clé à utiliser et comment mieux générer ces clés, reportez-vous à *Professional ASP.NET 2.0 sécurité, l’appartenance et la gestion des rôles* .

Par défaut, les clés utilisées pour le chiffrement et la validation sont générés automatiquement pour chaque application, et ces clés sont stockées dans l’autorité de sécurité locale (LSA). En bref, les paramètres par défaut garantit que les clés uniques sur un serveur web-par-serveur web et l’application par application. Par conséquent, ce comportement par défaut ne fonctionne pas pour les deux scénarios suivants :

- **Batteries de serveurs Web** : dans un [batterie de serveurs web](http://en.wikipedia.org/wiki/Web_farm) scénario, une seule application web est hébergé sur plusieurs serveurs web à des fins d’évolutivité et de redondance. Chaque demande entrante est distribué à un serveur dans la batterie de serveurs, ce qui signifie que la durée de vie d’une session utilisateur, des serveurs différents peuvent utilisés pour gérer ses différentes requêtes. Par conséquent, chaque serveur doit utiliser les mêmes clés de chiffrement et de validation afin que le ticket d’authentification forms créés, chiffré et validé sur un serveur peut être déchiffré et validé sur un autre serveur dans la batterie de serveurs.
- **Cross-partage d’Application Ticket** -un serveur web peut héberger plusieurs applications ASP.NET. Si vous avez besoin pour ces différentes applications partager un ticket d’authentification unique de formulaires, il est impératif que leurs clés de chiffrement et validation correspondent.

Lorsque vous travaillez dans une batterie de serveurs web, paramètre ou le partage de tickets d’authentification entre les applications sur le même serveur, vous devez configurer le &lt;machineKey&gt; élément dans les applications affectées afin que leur validationKey et les valeurs validationKey correspondent.

Alors qu’aucun des scénarios ci-dessus s’applique à notre exemple d’application, nous pouvons toujours spécifier validationKey explicite et les valeurs validationKey et définir les algorithmes à utiliser. Ajouter un &lt;machineKey&gt; paramètre au fichier Web.config :

[!code-xml[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample5.xml)]

Pour plus d’informations consultez [comment faire : configurer MachineKey dans ASP.NET 2.0](https://msdn.microsoft.com/library/ms998288.aspx).

> [!NOTE]
> Les valeurs ValidationKey et validationKey ont été effectuées à partir de [Steve Gibson](http://www.grc.com/stevegibson.htm)de [parfait des mots de passe web page](https://www.grc.com/passwords.htm), ce qui génère de 64 caractères hexadécimaux aléatoires sur chaque visite de la page. Pour réduire la probabilité de ces clés atteindre dans vos applications de production, il est conseillé de remplacer par ceux généré de manière aléatoire à partir de la page des mots de passe parfait clés ci-dessus.


## <a name="step-4-storing-additional-user-data-in-the-ticket"></a>Étape 4 : Stockage des données utilisateur supplémentaires dans le Ticket

De nombreuses applications web, affichent des informations sur ou affichage de la page de base sur l’utilisateur actuellement connecté. Par exemple, une page web peut afficher le nom d’utilisateur et la date qu'elle dernière session dans l’angle supérieur de chaque page. Le ticket d’authentification forms stocke le nom d’utilisateur de l’utilisateur actuellement connecté, mais toute autre information est nécessaire, la page doit se trouver dans le magasin de l’utilisateur - en général, une base de données - pour rechercher les informations non stockées dans le ticket d’authentification.

Avec un peu de code, nous pouvons stocker des informations utilisateur supplémentaires dans le ticket d’authentification de formulaires. Ces données peuvent être exprimées par les [classe FormsAuthenticationTicket](https://msdn.microsoft.com/library/system.web.security.formsauthenticationticket.aspx)de [propriété UserData](https://msdn.microsoft.com/library/system.web.security.formsauthenticationticket.userdata.aspx). Cela est utile pour placer de petites quantités d’informations sur l’utilisateur qui est souvent nécessaire. La valeur spécifiée dans le UserData propriété inclus dans le cadre du cookie de ticket d’authentification et, comme les autres champs de ticket, est chiffrée et validée basée sur la configuration du système de l’authentification de formulaires. Par défaut, UserData est une chaîne vide.

Pour stocker les données utilisateur dans le ticket d’authentification, nous devons écrire du code dans la page de connexion qui extrait les informations spécifiques à l’utilisateur et le stocke dans le ticket. UserData étant une propriété de type chaîne, les données qu’elle contient doivent être correctement sérialisées sous forme de chaîne. Par exemple, imaginez que notre magasin de l’utilisateur inclus la date de naissance de chaque utilisateur et le nom de l’employeur et nous voulions stocker les valeurs de ces deux propriétés dans le ticket d’authentification. Nous avons sérialiser ces valeurs dans une chaîne en concaténant date de l’utilisateur de la chaîne de naissance avec une barre verticale (|), suivi du nom de l’employeur. Pour un utilisateur né le 15 août 1974 fonctionne pour Northwind Traders, nous allons affecter la chaîne à la propriété UserData : 1974-08-15 | Northwind Traders.

Chaque fois que nous avons besoin d’accéder aux données stockées dans le ticket, nous pouvons faire, en saisissant FormsAuthenticationTicket de la demande actuelle et de la désérialisation de la propriété UserData. Dans le cas de la date de naissance et employeur exemple de nom, nous fractionne la chaîne UserData en deux sous-chaînes en fonction du délimiteur (|).


[![Informations utilisateur supplémentaires peuvent être stockées dans le Ticket d’authentification](forms-authentication-configuration-and-advanced-topics-cs/_static/image11.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image10.png)

**Figure 04**: supplémentaires utilisateur informations peuvent être stockées dans le Ticket d’authentification ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image12.png))


### <a name="writing-information-to-userdata"></a>Écriture d’informations dans UserData

Malheureusement, l’ajout d’informations spécifiques à l’utilisateur à un ticket d’authentification de formulaires n’est pas aussi simple que celle attendue. La propriété UserData de la classe FormsAuthenticationTicket est en lecture seule et ne peut être spécifiée via le constructeur de classe FormsAuthenticationTicket. Lorsque vous spécifiez la propriété UserData dans le constructeur, nous devons également fournir le ticket d’autres valeurs : le nom d’utilisateur, la date d’émission, l’expiration et ainsi de suite. Lorsque nous avons créé la page de connexion dans le didacticiel précédent, cela a été toutes prises en charge par la classe FormsAuthentication. Lorsque vous ajoutez UserData à FormsAuthenticationTicket, vous devrez écrire du code pour la plupart des fonctionnalités fournies par la classe FormsAuthentication répliquer.

Examinons le code nécessaire pour l’utilisation des UserData en mettant à jour de la page Login.aspx pour enregistrer des informations supplémentaires sur l’utilisateur pour le ticket d’authentification. Supposons que notre magasin de l’utilisateur contient des informations sur la société de l’utilisateur travaille pour et leur titre et que vous voulez capturer ces informations dans le ticket d’authentification. Mise à jour LoginButton Gestionnaire d’événements de la page Login.aspx afin que le code ressemble à ceci :

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample6.cs)]

Examinons ce code ligne par ligne à la fois. La méthode commence par définir quatre tableaux de chaînes : les utilisateurs, les mots de passe, companyName et titleAtCompany. Ces tableaux contiennent les noms d’utilisateur, les mots de passe, noms de société et des titres pour les comptes d’utilisateur dans le système, de laquelle il existe trois : Scott Jisun et Sam. Dans une application réelle, ces valeurs seraient être interrogées à partir du magasin de l’utilisateur, ne pas codé en dur dans le code source de la page.

Dans le didacticiel précédent, si les informations d’identification valides. nous vous avons appelé simplement FormsAuthentication.RedirectFromLoginPage (UserName.Text, RememberMe.Checked), qui a effectué les étapes suivantes :

1. Créé les formulaires ticket d’authentification
2. Écriture du ticket dans le magasin approprié. Pour les tickets d’authentification basée sur les cookies, de la collection de cookies du navigateur est utilisée ; cookieless tickets d’authentification, les données de ticket sont sérialisées dans l’URL
3. Rediriger l’utilisateur à la page appropriée

Ces étapes sont répliqués dans le code ci-dessus. Tout d’abord, la chaîne que nous allons finalement stocker dans la propriété UserData est formée en combinant le nom de la société et le titre, délimitant les deux valeurs avec une barre verticale (|).

string userDataString = string.Concat(companyName[i], "|", titleAtCompany[i]);

Ensuite, le FormsAuthentication.GetAuthCookie méthode est appelée, ce qui crée le ticket d’authentification, chiffre valide selon les paramètres de configuration et le place dans un objet HttpCookie.

HttpCookie authCookie = FormsAuthentication.GetAuthCookie (UserName.Text, RememberMe.Checked) ;

Afin de travailler avec le FormAuthenticationTicket incorporé dans le cookie, il faut appeler la classe FormAuthentication [déchiffrer méthode](https://msdn.microsoft.com/library/system.web.security.formsauthentication.decrypt.aspx), en passant la valeur du cookie.

FormsAuthenticationTicket ticket = FormsAuthentication.Decrypt(authCookie.Value);

Ensuite, nous créons un *nouvelle* FormsAuthenticationTicket instance selon les valeurs de FormsAuthenticationTicket existant. Toutefois, ce nouveau ticket inclut les informations spécifiques à l’utilisateur (userDataString).

FormsAuthenticationTicket newTicket = new FormsAuthenticationTicket(ticket.Version, ticket.Name, ticket.IssueDate, ticket.Expiration, ticket.IsPersistent, userDataString);

Nous puis chiffrer (et valider) la nouvelle instance de FormsAuthenticationTicket en appelant le [chiffrer méthode](https://msdn.microsoft.com/library/system.web.security.formsauthentication.encrypt.aspx)et remettre ces données chiffrées (et validées) dans authCookie.

authCookie.Value = FormsAuthentication.Encrypt(newTicket);

Enfin, authCookie est ajouté à la collection Response.Cookies et la méthode GetRedirectUrl est appelée pour déterminer la page appropriée à envoyer à l’utilisateur.

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample7.cs)]

Tout ce code est nécessaire, car la propriété UserData est en lecture seule et de la classe FormsAuthentication ne fournit pas de méthode pour spécifier les informations UserData dans ses méthodes GetAuthCookie, SetAuthCookie ou RedirectFromLoginPage.

> [!NOTE]
> Le code que nous avons examiné simplement stocke les informations spécifiques à l’utilisateur dans un ticket d’authentification basée sur les cookies. Les classes doit sérialiser le ticket d’authentification de formulaires à l’URL sont internes au .NET Framework. Résumer, vous ne pouvez pas stocker les données utilisateur dans un ticket d’authentification de formulaires sans cookie.


### <a name="accessing-the-userdata-information"></a>Accéder aux informations UserData

À ce stade nom de la société et le titre de chaque utilisateur sont stockées dans la propriété UserData du ticket d’authentification de formulaires lors de la connexion. Cette information est accessible à partir du ticket d’authentification sur n’importe quelle page sans nécessiter un aller-retour vers le magasin de l’utilisateur. Pour illustrer la façon dont ces informations peuvent être récupérées à partir de la propriété UserData, nous allons mettre à jour Default.aspx afin que son message d’accueil inclut non seulement le nom d’utilisateur, mais également l’entreprise qu’ils fonctionnent pour et leur titre.

Actuellement, Default.aspx contient un panneau AuthenticatedMessagePanel avec un contrôle Label nommé WelcomeBackMessage. Ce volet s’affiche uniquement pour les utilisateurs authentifiés. Mettre à jour le code dans la Page de Default.aspx\_charger le Gestionnaire d’événements pour qu’il ressemble à ce qui suit :

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample8.cs)]

Si Request.IsAuthenticated est true, alors du WelcomeBackMessage texte est tout d’abord définie sur Bienvenue, *nom d’utilisateur*. Ensuite, la propriété User.Identity est effectuée à un objet FormsIdentity afin que nous pouvons accéder à FormsAuthenticationTicket sous-jacent. Une fois que nous avons FormsAuthenticationTicket, nous désérialiser la propriété UserData dans le nom de la société et le titre. Pour cela, vous devez fractionner la chaîne sur la barre verticale. Le nom de la société et le titre sont ensuite affichés dans l’étiquette WelcomeBackMessage.

La figure 5 illustre une capture d’écran de cet affichage en action. Se connecter en tant que Scott affiche un message d’accueil précédent qui inclut la société et le titre de Scott.


[![Société actuellement connecté sur l’utilisateur et le titre sont affichent.](forms-authentication-configuration-and-advanced-topics-cs/_static/image14.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image13.png)

**Figure 05**: société et le titre actuellement connecté sur l’utilisateur sont affichés ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image15.png))


> [!NOTE]
> Propriété de UserData du ticket d’authentification sert un cache pour le magasin de l’utilisateur. Comme tous les caches, il doit être mis à jour lorsque les données sous-jacentes sont modifiées. Par exemple, s’il existe une page web à partir de laquelle les utilisateurs peuvent mettre à jour leur profil, les champs mis en cache dans la propriété UserData doivent être actualisés pour refléter les modifications apportées par l’utilisateur.


## <a name="step-5-using-a-custom-principal"></a>Étape 5 : À l’aide d’un principal de sécurité personnalisée

Sur chaque demande entrante FormsAuthenticationModule tente d’authentifier l’utilisateur. Si un ticket d’authentification de non expiré est présent, FormsAuthenticationModule assigne la propriété HttpContext.User vers un nouvel objet GenericPrincipal. Cet objet GenericPrincipal a une identité de type FormsIdentity, qui inclut une référence pour le ticket d’authentification de formulaires. La classe GenericPrincipal contient les fonctionnalités minimales système requises par une classe qui implémente IPrincipal : elle doit simplement une propriété d’identité et à une méthode IsInRole.

L’objet principal a deux tâches : pour indiquer quels rôles appartient l’utilisateur et pour fournir des informations d’identité. Cela s’effectue via IsInRole l’interface IPrincipal (*roleName*) méthode et la propriété Identity, respectivement. La classe GenericPrincipal permet un tableau de chaînes des noms de rôles à être spécifié via son constructeur. son IsInRole (*roleName*) vérifie simplement pour voir si passé dans *roleName* existe dans le tableau de chaînes. Lorsque FormsAuthenticationModule crée l’objet GenericPrincipal, il passe au constructeur de la classe de GenericPrincipal dans un tableau de chaînes vide. Par conséquent, un appel à IsInRole renvoie toujours false.

La classe GenericPrincipal répond aux besoins de la plupart des scénarios de l’authentification basée sur des formulaires où les rôles ne sont pas utilisés. Pour les cas où la gestion de rôle par défaut est insuffisante ou lorsque vous devez associer un objet IIdentity personnalisé à l’utilisateur, vous pouvez créer un objet IPrincipal personnalisé pendant le flux de travail de l’authentification et l’affecter à la propriété HttpContext.User.

> [!NOTE]
> Comme nous le verrons dans les futures didacticiels, lorsque ASP. Infrastructure de rôles du réseau est activée qu’il crée un objet principal personnalisé de type [RolePrincipal](https://msdn.microsoft.com/library/system.web.security.roleprincipal.aspx) et remplace l’objet GenericPrincipal créés par l’authentification de formulaires. Il procède ainsi afin de personnaliser la méthode IsInRole du principal pour interagir avec les API de l’infrastructure de rôles.


Étant donné que nous n'avons pas concerné nous-mêmes avec des rôles encore, la seule raison pour laquelle que nous aurait pour la création d’un objet principal personnalisé à partir de là serait pour associer un objet IIdentity personnalisé à l’entité de sécurité. À l’étape 4, nous avons étudié le stockage des informations utilisateur supplémentaires dans la propriété UserData du ticket d’authentification, en particulier, leur titre et le nom de la société de l’utilisateur. Toutefois, les informations UserData sont uniquement accessible via le ticket d’authentification et puis uniquement sous forme de chaîne sérialisée, ce qui signifie que chaque fois que vous souhaitez afficher les informations d’utilisateur stockées dans le ticket nous devons analyser la propriété UserData.

Nous pouvons améliorer l’expérience du développeur en créant une classe qui implémente IIdentity et inclut des propriétés CompanyName et titre. De cette façon, un développeur peut accéder au nom de la société de l’utilisateur actuellement connecté et titre directement via les propriétés du titre et CompanyName sans nécessaire de savoir comment analyser la propriété UserData.

### <a name="creating-the-custom-identity-and-principal-classes"></a>Création de l’identité personnalisée et les Classes principales

Pour ce didacticiel, nous allons créer les objets principal et identity personnalisés dans l’application\_dossier de Code. Commencez par ajouter une application\_dossier à votre projet de Code - avec le bouton droit sur le nom du projet dans l’Explorateur de solutions, sélectionnez l’option Ajouter le dossier ASP.NET et choisissez application\_Code. L’application\_dossier de Code est un dossier spécial ASP.NET qui contient les fichiers de classes spécifiques au site Web.

> [!NOTE]
> L’application\_dossier de Code ne doit être utilisé lors de la gestion de votre projet via le modèle de projet de site Web. Si vous utilisez la [le modèle de projet d’Application Web](https://msdn.microsoft.com/asp.net/Aa336618.aspx), créez un dossier standard et qui renforcent les classes. Par exemple, vous pourriez ajouter un nouveau dossier nommé Classes et placer votre code il.


Ensuite, ajoutez deux nouveaux fichiers de classe à l’application\_dossier de Code, un CustomIdentity.cs nommée et l’autre nommé CustomPrincipal.cs.


[![Ajoutez les CustomIdentity et les Classes de CustomPrincipal à votre projet](forms-authentication-configuration-and-advanced-topics-cs/_static/image17.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image16.png)

**Figure 06**: ajouter les CustomIdentity et les Classes de CustomPrincipal à votre projet ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image18.png))


La classe CustomIdentity est responsable de l’implémentation de l’interface IIdentity, qui définit les propriétés nom AuthenticationType et IsAuthenticated. En plus de ces propriétés requises, nous sommes intéressés par exposer le sous-jacent ticket d’authentification forms ainsi que des propriétés pour le nom de la société et le titre de l’utilisateur. Entrez le code suivant dans la classe CustomIdentity.

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample9.cs)]

Notez que la classe contient une variable de membre FormsAuthenticationTicket (\_ticket) et que ces informations de ticket doivent être fournies par le constructeur. Les données de ce ticket sont utilisées pour renvoyer le nom de l’identité ; Pour renvoyer les valeurs des propriétés CompanyName et titre, sa propriété UserData est analysée.

Ensuite, créez la classe CustomPrincipal. Étant donné que nous ne sont pas concernés par les rôles à partir de là, constructeur de la classe CustomPrincipal accepte uniquement un objet CustomIdentity ; sa méthode IsInRole renvoie toujours false.

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample10.cs)]

### <a name="assigning-a-customprincipal-object-to-the-incoming-requests-security-context"></a>Affectation d’un objet CustomPrincipal au contexte de sécurité de la demande entrante

Nous disposons désormais d’une classe qui étend la spécification d’identité par défaut pour inclure des propriétés CompanyName et titre, ainsi que d’une classe d’entité personnalisée qui utilise l’identité personnalisée. Nous prêts à l’étape dans le pipeline ASP.NET et que vous affectez de notre objet principal personnalisé au contexte de sécurité de la demande entrante.

Le pipeline ASP.NET accepte une demande entrante et traite par le biais d’un nombre d’étapes. À chaque étape, un événement particulier soit déclenché, ce qui permet aux développeurs d’exploiter le pipeline ASP.NET et modifiez la requête à certains points du cycle de vie. FormsAuthenticationModule, par exemple, attend que ASP.NET déclencher le [événement AuthenticateRequest](https://msdn.microsoft.com/library/system.web.httpapplication.authenticaterequest.aspx), à quel point il examine la demande entrante d’un ticket d’authentification. Si un ticket d’authentification est trouvé, un objet GenericPrincipal est créé et affecté à la propriété HttpContext.User.

Après l’événement AuthenticateRequest, le pipeline ASP.NET déclenche le [PostAuthenticateRequest événement](https://msdn.microsoft.com/library/system.web.httpapplication.postauthenticaterequest.aspx), qui est où nous pouvons remplacer l’objet GenericPrincipal créé par FormsAuthenticationModule avec une instance de notre Objet CustomPrincipal. La figure 7 illustre ce flux de travail.


[![La classe GenericPrincipal est remplacé par un CustomPrincipal dans l’événement PostAuthenticationRequest](forms-authentication-configuration-and-advanced-topics-cs/_static/image20.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image19.png)

**Figure 07**: le GenericPrincipal est remplacé par un CustomPrincipal dans l’événement PostAuthenticationRequest ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image21.png))


Pour exécuter le code en réponse à un événement du pipeline ASP.NET, nous pouvons créer le Gestionnaire d’événements appropriée dans Global.asax ou créer votre propre HTTP Module. Pour ce didacticiel nous allons créer le Gestionnaire d’événements dans Global.asax. Commencez par ajouter Global.asax à votre site Web. Avec le bouton droit sur le nom du projet dans l’Explorateur de solutions et ajouter un élément de type de classe d’Application globale nommé Global.asax.


[![Ajouter un fichier Global.asax à votre site Web](forms-authentication-configuration-and-advanced-topics-cs/_static/image23.png)](forms-authentication-configuration-and-advanced-topics-cs/_static/image22.png)

**Figure 08**: ajouter un fichier Global.asax pour votre site Web ([cliquez pour afficher l’image en taille réelle](forms-authentication-configuration-and-advanced-topics-cs/_static/image24.png))


Le modèle de Global.asax par défaut inclut des gestionnaires d’événements pour un nombre d’événements de pipeline ASP.NET, y compris le début, fin et [événement d’erreur](https://msdn.microsoft.com/library/system.web.httpapplication.error.aspx), entre autres. N’hésitez pas à supprimer ces gestionnaires d’événements, comme nous ne les requièrent pas de cette application. L’événement que nous sommes intéressés est PostAuthenticateRequest. Mettre à jour le fichier Global.asax afin de son balisage ressemble à ce qui suit :

[!code-aspx[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample11.aspx)]

L’Application\_OnPostAuthenticateRequest méthode s’exécute chaque fois que le runtime ASP.NET déclenche l’événement PostAuthenticateRequest, ce qui se produit une fois sur chaque demande entrante de la page. Le Gestionnaire d’événements commence par vérifier si l’utilisateur est authentifié et a été authentifié via l’authentification par formulaire. Dans ce cas, un nouvel objet CustomIdentity est créé et passé le ticket d’authentification de la requête actuelle dans son constructeur. Un objet CustomPrincipal est créé et passé l’objet CustomIdentity nouvellement créé dans son constructeur. Enfin, contexte de sécurité de la demande en cours est affecté à l’objet CustomPrincipal nouvellement créé.

Notez que la dernière étape - associant l’objet CustomPrincipal du contexte de sécurité de la demande - assigne l’entité de sécurité à deux propriétés : HttpContext.User et Thread.CurrentPrincipal. Ces deux affectations sont nécessaires en raison de la façon de contextes de sécurité sont gérées dans ASP.NET. Le .NET Framework associe un contexte de sécurité à chaque thread en cours d’exécution ; Ces informations sont disponibles en tant qu’objet IPrincipal via la [objet Thread](https://msdn.microsoft.com/library/system.threading.thread.aspx)de [propriété CurrentPrincipal](https://msdn.microsoft.com/library/system.threading.thread.currentcontext.aspx). Une confusion est que ASP.NET possède ses propres informations de contexte de sécurité (HttpContext.User).

Dans certains scénarios, la propriété Thread.CurrentPrincipal est examinée pour déterminer le contexte de sécurité ; dans d’autres scénarios, HttpContext.User est utilisé. Par exemple, il existe des fonctionnalités de sécurité dans .NET qui permettent aux développeurs de façon déclarative indiquer ce que les utilisateurs ou rôles peuvent instancier une classe ou d’appeler des méthodes spécifiques (voir [Ajout de règles d’autorisation à l’entreprise et les données à l’aide de couches PrincipalPermissionAttributes](https://weblogs.asp.net/scottgu/archive/2006/10/04/Tip_2F00_Trick_3A00_-Adding-Authorization-Rules-to-Business-and-Data-Layers-using-PrincipalPermissionAttributes.aspx)). Dans les coulisses, ces techniques déclaratives déterminent le contexte de sécurité via la propriété Thread.CurrentPrincipal.

Dans d’autres scénarios, la propriété HttpContext.User est utilisée. Par exemple, dans le didacticiel précédent, nous avons utilisé cette propriété pour afficher le nom d’utilisateur de l’utilisateur actuellement connecté. Clairement, puis, il est impératif que les informations de contexte de sécurité dans les propriétés Thread.CurrentPrincipal et HttpContext.User correspondent.

Le runtime ASP.NET synchronise automatiquement ces valeurs de propriété pour nous. Toutefois, cette synchronisation se produit après l’événement AuthenticateRequest, mais *avant* l’événement PostAuthenticateRequest. Par conséquent, lors de l’ajout d’un objet principal personnalisé dans l’événement PostAuthenticateRequest nous devons être certains attribuer manuellement le Thread.CurrentPrincipal sinon Thread.CurrentPrincipal et HttpContext.User sera synchronisée. Consultez [Context.User vs. Thread.CurrentPrincipal](http://leastprivilege.com/2005/11/23/context-user-vs-thread-currentprincipal/) pour plus d’informations sur ce problème.

### <a name="accessing-the-companyname-and-title-properties"></a>Accédez à le CompanyName et les propriétés du titre

Chaque fois qu’une demande arrive et qu’il soit distribuée au moteur ASP.NET, l’Application\_Gestionnaire d’événements OnPostAuthenticateRequest dans global.asax et se déclenche. Si la demande a été correctement authentifiée par FormsAuthenticationModule, le Gestionnaire d’événements créera un nouvel objet CustomPrincipal avec un objet CustomIdentity selon le ticket d’authentification de formulaires. Avec cette logique en place, l’accès aux informations sur le nom de la société et le titre de l’utilisateur actuellement connecté est extrêmement simple.

Revenir à la Page\_Gestionnaire d’événements de chargement dans Default.aspx, où à l’étape 4, nous avons rédigé code pour récupérer le ticket d’authentification par formulaire et analyser la propriété UserData afin d’afficher le nom de la société et le titre de l’utilisateur. Avec les objets CustomPrincipal et CustomIdentity en cours d’utilisation maintenant, il est inutile pour analyser les valeurs de propriété de UserData du ticket. Au lieu de cela, obtenir une référence à l’objet CustomIdentity simplement et utiliser ses propriétés CompanyName et titre :

[!code-csharp[Main](forms-authentication-configuration-and-advanced-topics-cs/samples/sample12.cs)]

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, nous avons examiné comment personnaliser les paramètres du système d’authentification forms via le fichier Web.config. Nous avons examiné comment les expiration du ticket d’authentification est gérée et comment les mécanismes de protection de chiffrement et la validation sont utilisés pour protéger le ticket d’inspection et de modification. Enfin, nous avons abordé à l’aide de la propriété UserData du ticket d’authentification pour stocker des informations utilisateur supplémentaires dans le ticket lui-même et comment utiliser des objets principal et identity personnalisés d’exposer ces informations de manière plus conviviales et.

Ce didacticiel conclut notre examen de l’authentification par formulaire dans ASP.NET. Le didacticiel suivant démarre notre voyage dans le cadre de l’appartenance.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Ayant été disséqué de l’authentification par formulaire](http://aspnet.4guysfromrolla.com/articles/072005-1.aspx)
- [Expliqué : L’authentification par formulaire dans ASP.NET 2.0](https://msdn.microsoft.com/library/aa480476.aspx)
- [Comment : Protéger l’authentification par formulaire dans ASP.NET 2.0](https://msdn.microsoft.com/library/ms998310.aspx)
- [Professionnel ASP.NET 2.0 sécurité, l’appartenance et la gestion des rôles](http://www.wrox.com/WileyCDA/WroxTitle/productCd-0764596985.html) (ISBN : 978-0-7645-9698 MSSQLServer-8)
- [Sécurisation des contrôles de connexion](https://msdn.microsoft.com/library/ms178346.aspx)
- [Le &lt;authentification&gt; élément](https://msdn.microsoft.com/library/532aee0e.aspx)
- [Le &lt;forms&gt; , élément pour &lt;l’authentification&gt;](https://msdn.microsoft.com/library/1d3t3c61.aspx)
- [Le &lt;machineKey&gt; élément](https://msdn.microsoft.com/library/w8h3skw9.aspx)
- [Comprendre le Ticket d’authentification de formulaires et de cookies](https://support.microsoft.com/kb/910443)

### <a name="video-training-on-topics-contained-in-this-tutorial"></a>Formations vidéo sur les rubriques contenues dans ce didacticiel

- [Comment modifier les propriétés d’authentification de formulaires](../../../videos/authentication/how-to-change-the-forms-authentication-properties.md)
- [Comment le programme d’installation et utilisez l’authentification sans Cookie dans une Application ASP.NET](../../../videos/authentication/how-to-setup-and-use-cookie-less-authentication-in-an-aspnet-application.md)
- [Réadressage de connexion par formulaires ASP](../../../videos/authentication/asp-forms-login-relocation.md)
- [Configuration de clé personnalisée de la connexion par formulaires](../../../videos/authentication/forms-login-custom-key-configuration.md)
- [Ajouter des données personnalisées à la méthode d’authentification](../../../videos/authentication/add-custom-data-to-the-authentication-method.md)
- [Utiliser des objets de principal personnalisés](../../../videos/authentication/use-custom-principal-objects.md)

### <a name="about-the-author"></a>À propos de l’auteur

Scott Mitchell, auteur de plusieurs livres sur ASP/ASP.NET et créateur de 4GuysFromRolla.com, travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est  *[SAM animer vous-même ASP.NET 2.0 des dernières 24 heures](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco)*. Scott peut être atteint à [ mitchell@4guysfromrolla.com ](mailto:mitchell@4guysfromrolla.com) ou via son blog à [http://ScottOnWriting.NET](http://scottonwriting.net/).

### <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Entraîner un réviseur pour ce didacticiel a été Alicja Maziarz. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com ](mailto:mitchell@4guysfromrolla.com).

>[!div class="step-by-step"]
[Précédent](an-overview-of-forms-authentication-cs.md)
[Suivant](security-basics-and-asp-net-support-vb.md)
