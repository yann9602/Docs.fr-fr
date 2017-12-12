---
uid: web-forms/overview/data-access/advanced-data-access-scenarios/protecting-connection-strings-and-other-configuration-information-cs
title: "Protection des chaînes de connexion et d’autres informations de Configuration (c#) | Documents Microsoft"
author: rick-anderson
description: "En général, une application ASP.NET stocke les informations de configuration dans un fichier Web.config. Certaines de ces informations est sensible et garantit la protection. Par def...."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/03/2007
ms.topic: article
ms.assetid: ad8dd396-30f7-4abe-ac02-a0b84422e5be
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/data-access/advanced-data-access-scenarios/protecting-connection-strings-and-other-configuration-information-cs
msc.type: authoredcontent
ms.openlocfilehash: e57886250fa98af95b61103d67481f747f44c390
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="protecting-connection-strings-and-other-configuration-information-c"></a>Protection des chaînes de connexion et d’autres informations de Configuration (c#)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/3/9/f/39f92b37-e92e-4ab3-909e-b4ef23d01aa3/ASPNET_Data_Tutorial_73_CS.zip) ou [télécharger le PDF](protecting-connection-strings-and-other-configuration-information-cs/_static/datatutorial73cs1.pdf)

> En général, une application ASP.NET stocke les informations de configuration dans un fichier Web.config. Certaines de ces informations est sensible et garantit la protection. Par défaut ce fichier ne sera pas utilisé pour le visiteur du site Web, mais un administrateur ou un pirate peut accéder au système de fichiers du serveur Web et afficher le contenu du fichier. Dans ce didacticiel, nous apprendre qu’ASP.NET 2.0 permet de protéger les informations sensibles en chiffrant les sections du fichier Web.config.


## <a name="introduction"></a>Introduction

Informations de configuration pour les applications ASP.NET sont généralement stockées dans un fichier XML nommé `Web.config`. Au cours de ces didacticiels nous avons mis à jour le `Web.config` un certain nombre de fois. Lors de la création du `Northwind` DataSet typé dans les [premier didacticiel](../introduction/creating-a-data-access-layer-cs.md), par exemple, les informations de chaîne de connexion a été ajoutées automatiquement à `Web.config` dans la `<connectionStrings>` section. Plus tard, dans le [les Pages maîtres et la Navigation sur le Site](../introduction/master-pages-and-site-navigation-cs.md) didacticiel, nous avons mis à jour manuellement `Web.config`, ajout un `<pages>` élément indiquant que toutes les pages ASP.NET dans notre projet doivent utiliser le `DataWebControls` thème.

Étant donné que `Web.config` peut contenir des données sensibles telles que des chaînes de connexion, il est important que le contenu de `Web.config` être conservées en toute sécurité masqués des personnes non autorisées. Par défaut, n’importe quel HTTP demande à un fichier avec le `.config` extension est gérée par le moteur ASP.NET, qui retourne le *ce type de page n’est pas pris en charge* message indiqué dans la Figure 1. Cela signifie que les visiteurs ne peut pas afficher votre `Web.config` s le contenu du fichier en entrant simplement l’http://www.YourServer.com/Web.config dans la barre d’adresse de leur navigateur s.


[![Visite Web.config via un navigateur retourne ce type de page non pris en charge Message](protecting-connection-strings-and-other-configuration-information-cs/_static/image2.png)](protecting-connection-strings-and-other-configuration-information-cs/_static/image1.png)

**Figure 1**: visite `Web.config` via un navigateur retourne ce type de page non pris en charge Message ([cliquez pour afficher l’image en taille réelle](protecting-connection-strings-and-other-configuration-information-cs/_static/image3.png))


Mais que se passe-t-il si un intrus est en mesure de trouver certaines autres exploiter qui lui permet d’afficher votre `Web.config` s le contenu du fichier ? Ce qui pourrait faire un attaquant avec ces informations et connaître les étapes peuvent être prises afin de mieux protéger les informations sensibles `Web.config`? Heureusement, la plupart des sections dans `Web.config` ne contiennent pas d’informations sensibles. Lesquels dommages une personne malveillante peut perpétrer s’ils ne connaissent le nom du thème utilisé par vos pages ASP.NET par défaut ?

Certaines `Web.config` sections, toutefois, contient des informations sensibles qui peuvent inclure des chaînes de connexion, les noms d’utilisateur, les mots de passe, noms de serveur, des clés de chiffrement et ainsi de suite. Ces informations se trouvent généralement dans l’exemple suivant `Web.config` sections :

- `<appSettings>`
- `<connectionStrings>`
- `<identity>`
- `<sessionState>`

Dans ce didacticiel, nous allons examiner les techniques de protection de ces informations de configuration sensibles. Comme nous allons le voir, le .NET Framework version 2.0 inclut un système de configuration protégée qui rend les sections de configuration sélectionnée de chiffrement et déchiffrement par programmation est très simple.

> [!NOTE]
> Ce didacticiel se termine par un examen des recommandations de s de Microsoft pour la connexion à une base de données à partir d’une application ASP.NET. En plus du chiffrement de vos chaînes de connexion, vous pouvez aider à renforcer votre système en vous assurant que vous vous connectez à la base de données de façon sécurisée.


## <a name="step-1-exploring-aspnet-20-s-protected-configuration-options"></a>Étape 1 : Exploration ASP.NET 2.0 s protégés des Options de Configuration

ASP.NET 2.0 comprend un système de configuration protégée pour chiffrer et déchiffrer les informations de configuration. Cela inclut des méthodes dans le .NET Framework qui peut être utilisé pour chiffrer ou déchiffrer les informations de configuration par programme. Le système de configuration protégée utilise le [modèle de fournisseur](http://aspnet.4guysfromrolla.com/articles/101905-1.aspx), ce qui permet aux développeurs de choisir quelle implémentation de services de chiffrement est utilisée.

Le .NET Framework est fourni avec deux fournisseurs de configuration protégée :

- [`RSAProtectedConfigurationProvider`](https://msdn.microsoft.com/en-us/library/system.configuration.rsaprotectedconfigurationprovider.aspx)-utilise l’asymétrique [algorithme RSA](http://en.wikipedia.org/wiki/Rsa) pour le chiffrement et le déchiffrement.
- [`DPAPIProtectedConfigurationProvider`](https://msdn.microsoft.com/en-us/system.configuration.dpapiprotectedconfigurationprovider.aspx)-utilise les fenêtres [API de Protection des données (DPAPI)](https://msdn.microsoft.com/en-us/library/ms995355.aspx) pour le chiffrement et le déchiffrement.

Étant donné que le système de configuration protégée implémente le modèle de conception du fournisseur, il est possible de créer votre propre fournisseur de configuration protégée et connectez-le à votre application. Consultez [implémentation d’un fournisseur de Configuration protégée](https://msdn.microsoft.com/en-us/library/wfc2t3az(VS.80).aspx) pour plus d’informations sur ce processus.

Les fournisseurs de DPAPI et RSA utilisent des clés pour leurs routines de chiffrement et le déchiffrement, et ces clés peuvent être stockées sur l’ordinateur - ou au niveau utilisateur. Clés au niveau de l’ordinateur sont idéales pour les scénarios où l’application web s’exécute sur son propre serveur ou s’il existe plusieurs applications sur un serveur qui doivent partager des informations cryptées. Clés au niveau de l’utilisateur sont une option plus sécurisée dans les environnements d’hébergement partagés où autres applications sur le même serveur ne doivent pas être en mesure de déchiffrer des sections de configuration s protégé par votre application.

Dans ce didacticiel nos exemples utilise le fournisseur DPAPI et les clés au niveau de l’ordinateur. Plus précisément, nous allons nous intéresser à chiffrer la `<connectionStrings>` section `Web.config`, bien que le système de configuration protégée peut être utilisé pour chiffrer la plupart des `Web.config` section. Pour plus d’informations sur l’utilisation de clés de niveau de l’utilisateur ou à l’aide du fournisseur RSA, consultez les ressources dans la section Documentation supplémentaire à la fin de ce didacticiel.

> [!NOTE]
> Le `RSAProtectedConfigurationProvider` et `DPAPIProtectedConfigurationProvider` fournisseurs sont enregistrés dans le `machine.config` fichier avec les noms de fournisseur `RsaProtectedConfigurationProvider` et `DataProtectionConfigurationProvider`, respectivement. Pour chiffrer ou déchiffrer les informations de configuration que vous devrez fournir le nom du fournisseur approprié (`RsaProtectedConfigurationProvider` ou `DataProtectionConfigurationProvider`) au lieu du nom de type réel (`RSAProtectedConfigurationProvider` et `DPAPIProtectedConfigurationProvider`). Vous pouvez trouver la `machine.config` de fichiers dans le `$WINDOWS$\Microsoft.NET\Framework\version\CONFIG` dossier.


## <a name="step-2-programmatically-encrypting-and-decrypting-configuration-sections"></a>Étape 2 : Chiffrement par programme et le déchiffrement des Sections de Configuration

Avec quelques lignes de code, nous pouvons chiffrer ou déchiffrer une section de configuration particulier à l’aide d’un fournisseur spécifié. Le code, comme nous le verrons dans quelques instants, simplement doit référencer par programme la section de configuration approprié, appelez sa `ProtectSection` ou `UnprotectSection` (méthode), puis appelez le `Save` méthode pour conserver les modifications. En outre, le .NET Framework inclut un utilitaire de ligne de commande utile qui peut chiffrer et déchiffrer les informations de configuration. Nous allons examiner cet utilitaire de ligne de commande à l’étape 3.

Pour illustrer par programme de protection des informations de configuration, s permettent de créer une page ASP.NET qui comporte des boutons pour chiffrer et déchiffrer le `<connectionStrings>` section `Web.config`.

Commencez par ouvrir le `EncryptingConfigSections.aspx` page dans le `AdvancedDAL` dossier. Faites glisser un contrôle de zone de texte à partir de la boîte à outils vers le concepteur, en définissant ses `ID` propriété `WebConfigContents`, ses `TextMode` propriété `MultiLine`et son `Width` et `Rows` propriétés à 95 % et 15, respectivement. Ce contrôle de zone de texte affiche le contenu de `Web.config` permet de voir rapidement si le contenu est chiffré ou non. Bien entendu, dans une application réelle serait jamais souhaité afficher le contenu de `Web.config`.

Sous la zone de texte, ajoutez deux contrôles bouton nommés `EncryptConnStrings` et `DecryptConnStrings`. Définir leurs propriétés de texte pour les chaînes de connexion chiffrer et déchiffrer les chaînes de connexion.

À ce stade votre écran doit ressembler à la Figure 2.


[![Ajoutez une zone de texte et deux contrôles bouton à la Page](protecting-connection-strings-and-other-configuration-information-cs/_static/image5.png)](protecting-connection-strings-and-other-configuration-information-cs/_static/image4.png)

**Figure 2**: ajouter une zone de texte et les deux contrôles bouton à la Page ([cliquez pour afficher l’image en taille réelle](protecting-connection-strings-and-other-configuration-information-cs/_static/image6.png))


Ensuite, nous avons besoin d’écrire du code qui charge et affiche le contenu de `Web.config` dans le `WebConfigContents` chargé de la zone de texte lors de la page. Ajoutez le code suivant à la classe code-behind de s page. Ce code ajoute une méthode nommée `DisplayWebConfig` et appelle à partir de la `Page_Load` Gestionnaire d’événements lorsque `Page.IsPostBack` est `false`:


[!code-csharp[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample1.cs)]

Le `DisplayWebConfig` utilise le [ `File` classe](https://msdn.microsoft.com/en-us/library/system.io.file.aspx) pour ouvrir l’application s `Web.config` fichier, le [ `StreamReader` classe](https://msdn.microsoft.com/en-us/library/system.io.streamreader.aspx) pour lire son contenu dans une chaîne et la [ `Path` classe](https://msdn.microsoft.com/en-us/library/system.io.path.aspx) pour générer le chemin d’accès physique à le `Web.config` fichier. Ces trois classes sont trouvent dans le [ `System.IO` espace de noms](https://msdn.microsoft.com/en-us/library/system.io.aspx). Par conséquent, vous devez ajouter un `using` `System.IO` instruction vers le haut de la classe code-behind, vous pouvez également préfixe ou ces noms de classe `System.IO.` .

Ensuite, nous devons ajouter des gestionnaires d’événements pour les deux contrôles bouton `Click` événements et ajoutez le code nécessaire pour chiffrer et déchiffrer les `<connectionStrings>` section à l’aide d’une clé au niveau de l’ordinateur avec le fournisseur DPAPI. Dans le concepteur, double-cliquez sur chaque bouton pour ajouter un `Click` Gestionnaire d’événements dans le code-behind de classe, puis ajoutez le code suivant :


[!code-csharp[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample2.cs)]

Le code utilisé dans les deux gestionnaires d’événements est presque identique. Les deux commence par obtenir des informations sur le s d’application en cours `Web.config` de fichiers le [ `WebConfigurationManager` classe](https://msdn.microsoft.com/en-us/library/system.web.configuration.webconfigurationmanager.aspx) s [ `OpenWebConfiguration` méthode](https://msdn.microsoft.com/en-us/library/system.web.configuration.webconfigurationmanager.openwebconfiguration.aspx). Cette méthode retourne le fichier de configuration web pour le chemin d’accès virtuel spécifié. Ensuite, le `Web.config` s de fichier `<connectionStrings>` section est accessible la [ `Configuration` classe](https://msdn.microsoft.com/en-us/library/system.configuration.configuration.aspx) s [ `GetSection(sectionName)` méthode](https://msdn.microsoft.com/en-us/library/system.configuration.configuration.getsection.aspx), qui retourne un [ `ConfigurationSection` ](https://msdn.microsoft.com/en-us/library/system.configuration.configurationsection.aspx) objet.

Le `ConfigurationSection` objet comprend un [ `SectionInformation` propriété](https://msdn.microsoft.com/en-us/library/system.configuration.configurationsection.sectioninformation.aspx) qui fournit des informations supplémentaires et des fonctionnalités relatives à la section de configuration. Comme le code ci-dessus, nous pouvons déterminer si la section de configuration est chiffrée en vérifiant la `SectionInformation` propriété s `IsProtected` propriété. En outre, la section peut être chiffrée ou déchiffrée la `SectionInformation` propriété s `ProtectSection(provider)` et `UnprotectSection` méthodes.

Le `ProtectSection(provider)` méthode accepte comme entrée une chaîne qui spécifie le nom du fournisseur de configuration protégée à utiliser lors du chiffrement. Dans le `EncryptConnString` Gestionnaire d’événements de bouton s nous passons DataProtectionConfigurationProvider dans les `ProtectSection(provider)` méthode afin que le fournisseur DPAPI est utilisé. Le `UnprotectSection` méthode peut déterminer le fournisseur qui a été utilisé pour chiffrer la section de configuration et par conséquent ne nécessite pas les paramètres d’entrée.

Après avoir appelé la `ProtectSection(provider)` ou `UnprotectSection` (méthode), vous devez appeler la `Configuration` objet s [ `Save` méthode](https://msdn.microsoft.com/en-us/library/system.configuration.configuration.save.aspx) pour conserver les modifications. Une fois que les informations de configuration ont été chiffrées ou déchiffrées et les modifications enregistrées, nous appelons `DisplayWebConfig` pour charger la mise à jour `Web.config` contenu dans le contrôle de zone de texte.

Une fois que vous avez entré le code ci-dessus, testez-le en vous rendant sur le `EncryptingConfigSections.aspx` page via un navigateur. Vous devez voir initialement une page qui affiche le contenu du `Web.config` avec la `<connectionStrings>` section affichée au format texte brut (voir Figure 3).


[![Ajoutez une zone de texte et deux contrôles bouton à la Page](protecting-connection-strings-and-other-configuration-information-cs/_static/image8.png)](protecting-connection-strings-and-other-configuration-information-cs/_static/image7.png)

**Figure 3**: ajouter une zone de texte et les deux contrôles bouton à la Page ([cliquez pour afficher l’image en taille réelle](protecting-connection-strings-and-other-configuration-information-cs/_static/image9.png))


Maintenant, cliquez sur le bouton de chiffrer des chaînes de connexion. Si la validation de la demande est activée, le balisage est publiée par le `WebConfigContents` TextBox produira une `HttpRequestValidationException`, qui affiche le message, potentiellement dangereux `Request.Form` valeur a été détectée à partir du client. Validation de la demande, qui est activée par défaut dans ASP.NET 2.0, interdit les publications (postback) qui incluent non encodée HTML et est conçue pour aider à empêcher les attaques par injection de script. Cette vérification peut être désactivée au niveau page - ou -niveau de l’application. Pour désactiver cette option pour cette page, définissez la `ValidateRequest` à `false` dans la `@Page` directive. Le `@Page` directive se trouve en haut du balisage déclaratif de page s.


[!code-aspx[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample3.aspx)]

Pour plus d’informations sur la validation de la demande, son objectif, désactivez-le au niveau page - et -niveau de l’application, comme comment encoder balisage HTML, voir [Validation de la demande - empêche les attaques de Script](../../../../whitepapers/request-validation.md).

Après avoir désactivé la validation de la demande pour la page, essayez de cliquer de nouveau sur le bouton de chiffrer des chaînes de connexion. Lors de la publication, le fichier de configuration est accessible et que son `<connectionStrings>` section chiffrée à l’aide du fournisseur DPAPI. La zone de texte est ensuite mis à jour pour afficher la nouvelle `Web.config` contenu. Comme le montre la Figure 4, le `<connectionStrings>` informations sont maintenant chiffrées.


[![Cliquez sur le chiffrer connexion chaînes bouton chiffre le &lt;connectionString&gt; Section](protecting-connection-strings-and-other-configuration-information-cs/_static/image11.png)](protecting-connection-strings-and-other-configuration-information-cs/_static/image10.png)

**Figure 4**: cliquez sur le chiffrer connexion chaînes bouton chiffre le `<connectionString>` Section ([cliquez pour afficher l’image en taille réelle](protecting-connection-strings-and-other-configuration-information-cs/_static/image12.png))


Le texte chiffré `<connectionStrings>` section générée sur mon ordinateur suivant, bien que le contenu dans le `<CipherData>` élément a été supprimé par souci de concision :


[!code-xml[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample4.xml)]

> [!NOTE]
> Le `<connectionStrings>` élément spécifie le fournisseur utilisé pour effectuer le chiffrement (`DataProtectionConfigurationProvider`). Ces informations sont utilisées par le `UnprotectSection` méthode clic sur le bouton de déchiffrer les chaînes de connexion.


Lorsque les informations de chaîne de connexion sont accessible à partir de `Web.config` - soit par le code à écrire, à partir d’un contrôle SqlDataSource, ou le code généré automatiquement et les TableAdapters dans notre typés - il est automatiquement déchiffrée. En résumé, nous n’avez pas besoin ajouter du code supplémentaire ou logique pour déchiffrer les données chiffrées `<connectionString>` section. Pour illustrer cela, reportez-vous à l’un des didacticiels antérieures à ce stade, telles que le didacticiel complet Simple à partir de la section rapports de base (`~/BasicReporting/SimpleDisplay.aspx`). Comme le montre la Figure 5, le didacticiel fonctionne exactement comme nous l’attend, indiquant que les informations de chaîne de connexion chiffrée sont déchiffrées automatiquement par la page ASP.NET.


[![La couche d’accès aux données déchiffre automatiquement les informations de chaîne de connexion](protecting-connection-strings-and-other-configuration-information-cs/_static/image14.png)](protecting-connection-strings-and-other-configuration-information-cs/_static/image13.png)

**Figure 5**: la couche d’accès aux données déchiffre automatiquement les informations de chaîne de connexion ([cliquez pour afficher l’image en taille réelle](protecting-connection-strings-and-other-configuration-information-cs/_static/image15.png))


Pour rétablir le `<connectionStrings>` en sa représentation sous forme de texte brut, cliquez sur le bouton de déchiffrer les chaînes de connexion. Lors de la publication, vous devez voir les chaînes de connexion dans `Web.config` au format texte brut. À ce stade, votre écran doit ressembler comme lors de la première visite cette page (voir Figure 3).

## <a name="step-3-encrypting-configuration-sections-usingaspnetregiisexe"></a>Étape 3 : Chiffrer des Sections de Configuration à l’aide de`aspnet_regiis.exe`

Le .NET Framework inclut une variété d’outils de ligne de commande dans le `$WINDOWS$\Microsoft.NET\Framework\version\` dossier. Dans le [à l’aide des dépendances de Cache SQL](../caching-data/using-sql-cache-dependencies-cs.md) (didacticiel), par exemple, nous avons examiné à l’aide de la `aspnet_regsql.exe` outil de ligne de commande pour ajouter l’infrastructure nécessaire pour les dépendances de cache SQL. Un autre outil de ligne de commande utile dans ce dossier est le [outil ASP.NET IIS Registration (`aspnet_regiis.exe`)](https://msdn.microsoft.com/en-us/library/k6h9cz8h(VS.80).aspx). Comme son nom l’indique, l’outil ASP.NET IIS Registration est principalement utilisé pour inscrire une application ASP.NET 2.0 avec Microsoft s professionnelles Web server, IIS. Outre ses fonctionnalités liées à IIS, l’outil ASP.NET IIS Registration peut également être utilisé pour chiffrer ou déchiffrer des sections de configuration spécifié dans `Web.config`.

L’instruction suivante montre la syntaxe générale utilisée pour chiffrer une section de configuration avec la `aspnet_regiis.exe` outil de ligne de commande :


[!code-console[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample5.cmd)]

*section* est la section de configuration pour chiffrer (comme connectionStrings), le *physique\_active* est le chemin physique complet vers le répertoire racine de l’application s web, et *fournisseur*  est le nom du fournisseur de configuration protégée à utiliser (par exemple, DataProtectionConfigurationProvider). Vous pouvez également, si l’application web est enregistrée dans IIS, vous pouvez entrer le chemin d’accès virtuel au lieu du chemin d’accès physique à l’aide de la syntaxe suivante :


[!code-console[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample6.cmd)]

Les éléments suivants `aspnet_regiis.exe` exemple chiffre le `<connectionStrings>` section à l’aide du fournisseur DPAPI avec une clé au niveau de l’ordinateur :


[!code-console[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample7.cmd)]

De même, le `aspnet_regiis.exe` outil de ligne de commande peut être utilisé pour déchiffrer les sections de configuration. Au lieu d’utiliser le `-pef` , les `-pdf` (ou à la place de `-pe`, utilisez `-pd`). En outre, notez que le nom du fournisseur n’est pas nécessaire lors du déchiffrement.


[!code-console[Main](protecting-connection-strings-and-other-configuration-information-cs/samples/sample8.cmd)]

> [!NOTE]
> Étant donné que nous utilisons le fournisseur DPAPI, qui utilise des clés spécifiques à l’ordinateur, vous devez exécuter `aspnet_regiis.exe` à partir de la même machine à partir de laquelle les pages web sont pris en charge. Par exemple, si vous exécutez ce programme de ligne de commande à partir de votre ordinateur de développement local, puis téléchargez le fichier Web.config chiffré sur le serveur de production, le serveur de production pas sera en mesure de déchiffrer les informations de chaîne de connexion, car il a été chiffré à l’aide de clés spécifiques à votre ordinateur de développement. Le fournisseur RSA n’a pas cette limitation car il est possible d’exporter les clés RSA vers un autre ordinateur.


## <a name="understanding-database-authentication-options"></a>Présentation des Options d’authentification de base de données

Avant de n’importe quelle application peut émettre `SELECT`, `INSERT`, `UPDATE`, ou `DELETE` requêtes à une base de données Microsoft SQL Server, la base de données doivent tout d’abord identifier le demandeur. Ce processus est appelé *authentification* et SQL Server fournit deux méthodes d’authentification :

- **L’authentification Windows** -le processus sous lequel s’exécute l’application est utilisé pour communiquer avec la base de données. Lorsque vous exécutez une application ASP.NET dans Visual Studio 2005 s serveur de développement ASP.NET, l’application ASP.NET utilise l’identité de l’utilisateur actuellement connecté. Pour les applications ASP.NET sur Microsoft Internet Information Server (IIS), les applications ASP.NET supposent généralement l’identité de `domainName``\MachineName` ou `domainName``\NETWORK SERVICE`, bien que cela peut être personnalisé.
- **L’authentification SQL** -valeurs ID et mot de passe d’un utilisateur sont fournis en tant qu’informations d’identification pour l’authentification. Avec l’authentification SQL, l’ID utilisateur et un mot de passe sont fournies dans la chaîne de connexion.

L’authentification Windows est préférable l’authentification SQL, car il est plus sécurisé. Avec l’authentification Windows, la chaîne de connexion est disponible à partir d’un nom d’utilisateur et un mot de passe et si le serveur web et le serveur de base de données se trouvent sur deux ordinateurs différents, les informations d’identification ne sont pas envoyées sur le réseau en texte brut. Avec l’authentification SQL, toutefois, les informations d’identification d’authentification sont codés en dur dans la chaîne de connexion et sont transmises à partir du serveur web sur le serveur de base de données au format texte brut.

Ces didacticiels ont utilisé l’authentification Windows. Vous pouvez déterminer quel mode d’authentification est utilisé en examinant la chaîne de connexion. La chaîne de connexion dans `Web.config` pour nos didacticiels a été :

`Data Source=.\SQLEXPRESS; AttachDbFilename=|DataDirectory|\NORTHWND.MDF; Integrated Security=True; User Instance=True`

La sécurité intégrée = True et l’absence d’un nom d’utilisateur et un mot de passe indiquent que l’authentification Windows est utilisée. Le terme connexion approuvée des chaînes dans une connexion = Oui ou la sécurité intégrée = SSPI est utilisé au lieu de la sécurité intégrée = True, mais les trois indiquent l’utilisation de l’authentification Windows.

L’exemple suivant montre une chaîne de connexion qui utilise l’authentification SQL. Notez que les informations d’identification incorporées dans la chaîne de connexion :

`Server=serverName; Database=Northwind; uid=userID; pwd=password`

Imaginez qu’une personne malveillante est en mesure d’afficher votre application s `Web.config` fichier. Si vous utilisez l’authentification SQL pour se connecter à une base de données est accessible sur Internet, l’attaquant peut utiliser cette chaîne de connexion pour se connecter à votre base de données via SQL Management Studio ou à partir des pages ASP.NET sur leur propre site Web. Pour aider à atténuer cette menace, chiffrer les informations de chaîne de connexion `Web.config` à l’aide du système de configuration protégée.

> [!NOTE]
> Pour plus d’informations sur les différents types d’authentification disponible dans SQL Server, consultez [création d’Applications ASP.NET sécurisées : authentification, autorisation et Communication sécurisée](https://msdn.microsoft.com/en-us/library/aa302392.aspx). Pour obtenir les exemples de chaîne connexion illustrant les différences entre la syntaxe de l’authentification Windows et SQL, reportez-vous à [ConnectionStrings.com](http://www.connectionstrings.com/).


## <a name="summary"></a>Résumé

Par défaut, les fichiers avec un `.config` extension dans une application ASP.NET ne sont pas accessibles via un navigateur. Ces types de fichiers ne sont pas retournées, car ils ne peuvent contenir des informations sensibles, telles que les chaînes de connexion de base de données, les noms d’utilisateur et mots de passe et ainsi de suite. Le système de configuration protégée dans .NET 2.0 permet de renforcer la protection des informations sensibles en permettant à des sections de configuration spécifié à chiffrer. Il existe deux fournisseurs de configuration protégée intégrés : une qui utilise l’algorithme RSA et qui utilise l’API de Protection des données (DPAPI) Windows.

Dans ce didacticiel, nous avons étudié comment chiffrer et déchiffrer les paramètres de configuration à l’aide du fournisseur DPAPI. Cela peut être accompli à la fois par programme, comme nous l’avons vu à l’étape 2, ainsi que via les `aspnet_regiis.exe` outil de ligne de commande, qui a été couverte à l’étape 3. Pour plus d’informations à l’aide de clés au niveau de l’utilisateur ou le fournisseur RSA au lieu de cela, consultez les ressources dans la section obtenir des informations supplémentaires.

Bonne programmation !

## <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Génération de l’Application ASP.NET sécurisées : Authentification, l’autorisation et Communication sécurisée](https://msdn.microsoft.com/en-us/library/aa302392.aspx)
- [Chiffrement des informations de Configuration dans ASP.NET 2.0 Applications](http://aspnet.4guysfromrolla.com/articles/021506-1.aspx)
- [Chiffrement `Web.config` valeurs dans ASP.NET 2.0](https://weblogs.asp.net/scottgu/archive/2006/01/09/434893.aspx)
- [Comment : Chiffrer des Sections de Configuration dans ASP.NET 2.0 à l’aide de DPAPI](https://msdn.microsoft.com/en-us/library/ms998280.aspx)
- [Comment : Chiffrer des Sections de Configuration dans ASP.NET 2.0 à l’aide de RSA](https://msdn.microsoft.com/en-us/library/ms998283.aspx)
- [L’API de Configuration dans le .NET 2.0](http://www.odetocode.com/Articles/418.aspx)
- [Protection des données Windows](https://msdn.microsoft.com/en-us/library/ms995355.aspx)

## <a name="about-the-author"></a>À propos de l’auteur

[Scott Mitchell](http://www.4guysfromrolla.com/ScottMitchell.shtml), auteur de sept manuels ASP/ASP.NET et créateur de [4GuysFromRolla.com](http://www.4guysfromrolla.com), travaille avec les technologies Web Microsoft depuis 1998. Scott fonctionne comme un consultant indépendant, formateur et writer. Son dernier ouvrage est [ *SAM animer vous-même ASP.NET 2.0 des dernières 24 heures*](https://www.amazon.com/exec/obidos/ASIN/0672327384/4guysfromrollaco). Il peut être atteint à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com) ou via son blog, qui se trouvent à [http://ScottOnWriting.NET](http://ScottOnWriting.NET).

## <a name="special-thanks-to"></a>Remerciements

Cette série de didacticiels a été révisée par plusieurs réviseurs utiles. Les réviseurs tête pour ce didacticiel ont été Teresa Murphy et Randy Schmidt. Vous souhaitez consulter mes prochains articles MSDN ? Dans ce cas, me supprimer une ligne à [ mitchell@4GuysFromRolla.com.](mailto:mitchell@4GuysFromRolla.com)

>[!div class="step-by-step"]
[Précédent](configuring-the-data-access-layer-s-connection-and-command-level-settings-cs.md)
[Suivant](debugging-stored-procedures-cs.md)
