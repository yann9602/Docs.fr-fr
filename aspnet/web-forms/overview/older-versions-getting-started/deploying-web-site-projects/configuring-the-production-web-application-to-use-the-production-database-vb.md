---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-the-production-web-application-to-use-the-production-database-vb
title: "Configuration de l’Application Web de Production à utiliser la base de données de Production (VB) | Documents Microsoft"
author: rick-anderson
description: "Comme indiqué dans les didacticiels antérieures, il n’est pas rare que les informations de configuration à diffèrent entre les environnements de développement et de production. Il s’agit d’es..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/23/2009
ms.topic: article
ms.assetid: a64a7aa0-6608-449e-83bf-1ef8cceee504
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/configuring-the-production-web-application-to-use-the-production-database-vb
msc.type: authoredcontent
ms.openlocfilehash: 5b193fa3256e5886481c7b36d88aa09c1fa7017c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="configuring-the-production-web-application-to-use-the-production-database-vb"></a>Configuration de l’Application Web de Production à utiliser la base de données de Production (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/E/6/F/E6FE3A1F-EE3A-4119-989A-33D1A9F6F6DD/ASPNET_Hosting_Tutorial_08_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/C/3/9/C391A649-B357-4A7B-BAA4-48C96871FEA6/aspnet_tutorial08_DBConfig_vb.pdf)

> Comme indiqué dans les didacticiels antérieures, il n’est pas rare que les informations de configuration à diffèrent entre les environnements de développement et de production. Cela est particulièrement vrai pour les applications web pilotées par les données, comme les chaînes de connexion de base de données diffèrent entre les environnements de développement et de production. Ce didacticiel explore les façons de configurer l’environnement de production afin d’inclure la chaîne de connexion appropriée plus en détail.


## <a name="introduction"></a>Introduction

Les applications web pilotées par les données utilisent généralement une base de données en cas de développement que lorsque en production. Pour les applications hébergées par un fournisseur d’hébergement web et développés localement, la base de données de développement réside généralement sur l’ordinateur du développeur s tandis que la base de données de production est hébergée sur un serveur de base de données d’hébergement de s entreprise le web. Déploiement d’une application web pilotées par des données implique la copie de la base de données de développement sur le serveur de base de données de production. Dans le didacticiel précédent, nous avons des méthodes pour effectuer cette étape.

L’application web utilise les informations contenues dans un *chaîne de connexion* pour établir une connexion avec la base de données. La chaîne de connexion, qui est généralement stockée dans `Web.config`, spécifie le nom du serveur de base de données, le nom de la base de données, le contexte de sécurité et d’autres informations. Étant donné que la base de données utilisé par l’application web dépend de si l’application web est en cours d’exécution dans les environnements de développement ou de production, les chaînes de connexion doivent différer entre les deux environnements.

Il n’est pas rare que les informations de configuration à diffèrent entre les environnements de développement et de production. Le *les différences entre développement Configuration commune et Production* didacticiel décrit des techniques pour gérer les informations de configuration distincts entre ces deux environnements, ainsi qu’une brève description de chaînes de connexion de base de données. Ce didacticiel explore les façons de configurer l’environnement de production afin d’inclure la chaîne de connexion appropriée plus en détail.

## <a name="examining-the-connection-string-information"></a>Examiner les informations de chaîne de connexion

La chaîne de connexion utilisée par l’application web critiques est stockée dans le fichier de configuration s application `Web.config`. `Web.config`inclut une section spéciale pour le stockage des chaînes de connexion, nommés invoque [ &lt;connectionStrings&gt;](https://msdn.microsoft.com/en-us/library/bf7sd233.aspx). Le `Web.config` fichier pour le site Web critiques comporte une chaîne de connexion définie dans cette section nommée `ReviewsConnectionString`:

[!code-xml[Main](configuring-the-production-web-application-to-use-the-production-database-vb/samples/sample1.xml)]

La chaîne de connexion - Source de données =. \SQLEXPRESS ; AttachDbFilename = | Sécurité de DataDirectory|\Reviews.mdf;Integrated = True ; User Instance = True - est composé d’un nombre d’options et des valeurs avec des paires de valeur d’option séparées par un point-virgule et chaque option et valeur séparées par un signe égal. Les quatre options utilisées dans cette chaîne de connexion sont :

- `Data Source`-Spécifie l’emplacement du serveur de base de données et le nom d’instance de serveur de base de données (le cas échéant). La valeur, `.\SQLEXPRESS`, est un exemple dans lequel il existe un serveur de base de données et un nom d’instance. La période de Spécifie que le serveur de base de données est sur le même ordinateur que l’application ; le nom d’instance est `SQLEXPRESS`.
- `AttachDbFilename`-Spécifie l’emplacement du fichier de base de données. La valeur contient l’espace réservé `|DataDirectory|`, qui est résolu sur le chemin d’accès complet de l’application s `App_Data` dossier lors de l’exécution.
- `Integrated Security`-une valeur booléenne qui indique s’il faut utiliser un nom d’utilisateur/mot de passe spécifié lors de la connexion à la base de données (false) ou Windows actuel des informations d’identification de compte (true).
- `User Instance`-une option de configuration spécifique pour les éditions SQL Server Express qui indique s’il faut autoriser les utilisateurs non-administrateurs sur l’ordinateur local, attachent et se connecter à une base de données SQL Server Express Edition. Consultez [Instances utilisateur SQL Server Express](https://msdn.microsoft.com/en-us/library/ms254504.aspx) pour plus d’informations sur ce paramètre.
  

Les options de chaîne de connexion autorisés dépendent de la base de données que vous êtes connecté et le [ADO.NET](http://ADO.NET) fournisseur de base de données utilisé. Par exemple, la chaîne de connexion pour la connexion à un Microsoft SQL Server diffère de la base de données utilisé pour se connecter à une base de données Oracle. De même, la connexion à une base de données Microsoft SQL Server à l’aide du fournisseur SqlClient utilise une chaîne de connexion différents que lorsque vous utilisez le fournisseur OLE DB.

Vous pouvez générer la chaîne de connexion de base de données manuellement à l’aide d’un tel site [ConnectionStrings.com](http://www.connectionstrings.com/) en tant que ressource pour quelles options sont disponibles. Toutefois, une approche plus simple consiste à ajouter la base de données à l’Explorateur de serveurs dans Visual Studio, puis de saisir la chaîne de connexion à partir de la fenêtre Propriétés. Laisser les s à utiliser cette technique pour construire la chaîne de connexion au serveur de base de données de production.

Ouvrez Visual Studio et accédez à la fenêtre de l’Explorateur de serveurs (dans Visual Web Developer, cette fenêtre est appelée l’Explorateur de base de données). Avec le bouton droit sur l’option des connexions de données et choisissez l’option Ajouter une connexion dans le menu contextuel. De l’Assistant illustré dans la Figure 1 s’affiche. Choisissez la source de données approprié, puis cliquez sur Continuer.


[![Choisir d’ajouter une nouvelle base de données à l’Explorateur de serveurs](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image2.jpg)](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image1.jpg) 

**Figure 1**: choisir d’ajouter une nouvelle base de données à l’Explorateur de serveurs ([cliquez pour afficher l’image en taille réelle](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image3.jpg))


Ensuite, spécifiez les différentes informations de connexion de la base de données (voir Figure 2). Lorsque vous vous abonnez à votre société d’hébergement web qu’ils doivent fourni d’informations sur la façon de se connecter à la base de données - le nom du serveur de base de données, le nom de la base de données, le nom d’utilisateur et le mot de passe à utiliser pour se connecter à la base de données et ainsi de suite. Après avoir entré ces informations, cliquez sur OK pour terminer cet Assistant et d’ajouter la base de données à l’Explorateur de serveurs.


[![Spécifiez les informations de connexion de base de données](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image5.jpg)](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image4.jpg) 

**Figure 2**: spécifiez les informations de connexion de base de données ([cliquez pour afficher l’image en taille réelle](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image6.jpg))


La base de données des environnement de production doit figurer dans l’Explorateur de serveurs. Sélectionnez la base de données à partir de l’Explorateur de serveurs et accédez à la fenêtre Propriétés. Vous y trouverez une propriété de chaîne de connexion avec la chaîne de connexion de base de données s. Si que vous utilisez une base de données Microsoft SQL Server sur la production et le fournisseur SqlClient votre chaîne de connexion doit ressembler à ce qui suit :

**Source de données =*nom_serveur*; Catalogue initial =*databaseName*; Persist Security Info = True ; ID d’utilisateur =*nom d’utilisateur*; Mot de passe =*mot de passe***

Où *nom_serveur*, *databaseName*, *nom d’utilisateur*, et *mot de passe* sont avec les valeurs pour le nom du serveur de base de données, la base de données nom et le nom d’utilisateur et mot de passe fournie par votre société d’hôte web.

## <a name="deploying-the-book-reviews-web-application"></a>Déploiement de l’Application Web de révisions du livre

Le didacticiel précédent présenté à la copie de la base de données de développement dans l’environnement de production, mais ne pas Explorer déploiement de l’application contrôlée par les données. À ce stade, l’environnement de production contient la base de données mais est à l’aide de la version de l’application passe en revue des livres avec révisions statiques. Vous devez déployer l’application nouvelle, pilotés par les données sur le serveur de production, ainsi que les informations de configuration mis à jour.

Prenez un moment pour déployer l’application contrôlée par les données à partir de l’environnement de développement à la production. Ce processus a été couvert en détail dans les didacticiels précédents. Si vous avez besoin d’un rappel, consultez la *déploiement de votre site Web à l’aide d’un FTP Client* ou *déploiement de votre site Web à l’aide de Visual Studio* didacticiels. Vous devez vous assurer que la chaîne de connexion de base de données de production est celui utilisé dans l’environnement de production, ce qui signifie qu’un autre `Web.config` fichier doit être déployé. Plus précisément, cette option modifiée `Web.config` fichier s `<connectionStrings>` élément doit contenir la chaîne de connexion de base de données de production et doit ressembler à ce qui suit :

[!code-xml[Main](configuring-the-production-web-application-to-use-the-production-database-vb/samples/sample2.xml)]

Notez que la chaîne de connexion dans le `<connectionStrings>` élément porte le même (`ReviewsConnectionString`), mais contient désormais la chaîne de connexion de base de données de production au lieu de la chaîne de connexion de base de données de développement.

Sauf si vous avez un flux de travail de déploiement plus formelle, vous devez soit modifier manuellement la `Web.config` fichier à utiliser la chaîne de connexion de base de données de production avant de déployer (n’oubliez pas de revenir en arrière à l’aide de la chaîne de connexion de base de données de développement par la suite) ou maintenir un distinct `Web.config` fichier avec les informations de configuration des environnement de production qui sont chargées sur l’environnement de production dans le cadre du processus de déploiement.

> [!NOTE]
> Si vous déployez accidentellement un `Web.config` fichier qui contient la chaîne de connexion de base de données de développement, puis il y aura une erreur lors de l’application de production tente de se connecter à la base de données. Cette erreur se manifeste en tant qu’un `SqlException` avec un message indiquant que le serveur est introuvable ou n’est pas accessible.


Une fois que le site a été déployé en production, visitez le site de production via votre navigateur. Vous devez voir et profitez de la même expérience utilisateur en tant que lors de l’exécution de l’application contrôlée par les données localement. Bien entendu lorsque vous visitez le site Web de production le site est rendue possible par le serveur de base de données de production, alors que la visite le site Web dans l’environnement de développement utilise la base de données en cours de développement. La figure 3 illustre les *apprendre vous-même ASP.NET 3.5 des dernières 24 heures* passez en revue la page depuis le site Web dans l’environnement de production (Notez l’URL dans la barre d’adresses navigateur s).


[![L’Application Data-Driven est maintenant disponible sur Production !](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image8.jpg)](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image7.jpg) 

**Figure 3**: The Data-Driven Application est maintenant disponible sur Production ! ([Cliquez pour afficher l’image en taille réelle](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image9.jpg))


### <a name="storing-connection-strings-in-a-separate-configuration-file"></a>Le stockage des chaînes de connexion dans un fichier de Configuration distinct

Une technique courante pour gérer les informations de configuration distinct dans les environnements de développement et de production consiste à avoir deux versions de la `Web.config`: un pour l’environnement de développement et un environnement de production. Moment de la déployer approprié `Web.config` version peut être copiée dans l’environnement de production. Dans l’idéal, ce processus est automatisé dans le cadre du flux de travail de déploiement.

Au lieu de la maintenance de deux `Web.config` fichiers si vous le souhaitez, vous pouvez fournissent des différences plus granulaires. Les éléments qui composent le `Web.config` fichier peut être défini dans un fichier de configuration externes qui est ensuite référencés dans les `Web.config` fichier. En résumé, vous pouvez avoir un `Web.config` fichier pour les deux environnements qui fait référence à un fichier databaseConnectionStrings.config, qui contiendrait les chaînes de connexion utilisées par l’application et qu’il seraient uniques pour chaque environnement. Je trouve que séparer les différentes informations de configuration dans des fichiers distincts fournit un utilisons et plus simple `Web.config` fichier et bien plus encore clairement présente les différences de configuration entre les environnements de développement et de production.

Pour utiliser cette technique, commencez par créer un nouveau dossier dans l’application web appelée `ConfigSections`. Ensuite, ajoutez deux fichiers à ce nouveau dossier nommé databaseConnectionStrings.dev.config et databaseConnectionStrings.production.config. Ensuite, copiez la `<connectionStrings>` élément à partir de `Web.config` dans les fichiers databaseConnectionStrings.dev.config et databaseConnectionStrings.production.config, puis modifiez la chaîne de connexion dans le databaseConnectionStrings.production.config fichier afin qu’elle spécifie la chaîne de connexion de base de données de production. Par exemple, le fichier databaseConnectionStrings.dev.config doit contenir uniquement le `<connectionStrings>` élément avec une chaîne de connexion qui fait référence à la base de données de développement :

[!code-xml[Main](configuring-the-production-web-application-to-use-the-production-database-vb/samples/sample3.xml)]

De même, le fichier databaseConnectionStrings.production.config doit contenir uniquement un `<connectionStrings>` élément, mais que la chaîne de connexion de base de données de production.

Faites une copie du fichier databaseConnectionStrings.dev.config et nommez-le databaseConnectionStrings.config.

> [!NOTE]
> Vous pouvez nommer le fichier de configuration autre chose que databaseConnectionStrings.config, si d vous le souhaitez, tel que `connectionStrings.config` ou `dbInfo.config`. Toutefois, veillez à nommer le fichier avec un `.config` extension comme `.config` fichiers sont, par défaut, non pris en charge par le moteur ASP.NET. Si vous nommez le fichier autre chose, tel que `connectionStrings.txt`, un utilisateur peut pointer son navigateur pour [www.yoursite.com/ConfigSettings/connectionStrings.txt](http://www.yoursite.com/ConfigSettings/connectionStrings.txt) et afficher le contenu du fichier !


À ce stade le `ConfigSections` dossier doit contenir les trois fichiers (voir Figure 4). Les fichiers databaseConnectionStrings.dev.config et databaseConnectionStrings.production.config contiennent les chaînes de connexion pour les environnements de développement et de production, respectivement. Le fichier databaseConnectionStrings.config contient les informations de chaîne de connexion qui seront utilisées par l’application web lors de l’exécution. Par conséquent, le fichier databaseConnectionStrings.config doit être identique au fichier databaseConnectionStrings.dev.config dans l’environnement de développement, tandis que sur la production, le fichier databaseConnectionStrings.config doit être identique à databaseConnectionStrings.production.config.


[![ConfigSections](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image11.jpg)](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image10.jpg) 

**Figure 4**: ConfigSections ([cliquez pour afficher l’image en taille réelle](configuring-the-production-web-application-to-use-the-production-database-vb/_static/image12.jpg))


Nous devons maintenant demander à `Web.config` à utiliser le fichier databaseConnectionStrings.config pour son magasin de chaînes de connexion. Ouvrez `Web.config` et remplacer la `<connectionStrings>` élément avec les éléments suivants :

[!code-xml[Main](configuring-the-production-web-application-to-use-the-production-database-vb/samples/sample4.xml)]

Le `configSource` attribut spécifie un chemin d’accès physique relatif à la `Web.config` fichier. Si l’externe `.config` fichier se trouve dans le même répertoire que `Web.config` puis définissez cet attribut sur le nom de fichier de le `.config` fichier. Si elle s dans un sous-répertoire, comme c’est le cas avec databaseConnectionStrings.config, spécifiez le sous-dossier à l’aide d’une barre oblique inverse pour délimiter les noms de fichiers et de dossiers, tels que ConfigSections\databaseConnectionStrings.config.

Avec cette modification, les environnements de développement et de production contiennent les mêmes `Web.config` fichier. Désormais, la seule différence est le fichier databaseConnectionStrings.config. Copiez le fichier databaseConnectionStrings.production.config en production et renommez-le databaseConnectionStrings.config. Si, à l’avenir, il existe des modifications apportées à la chaîne de connexion de base de données de production vous devez rendre le fichier databaseConnectionStrings.production.config et puis télécharger ce fichier à la production, en le renommant databaseConnectionStrings.config.

> [!NOTE]
> Vous pouvez spécifier les informations pour toute `Web.config` élément dans un fichier distinct et l’utilisation la `configSource` attribut faire référence à ce fichier depuis `Web.config`.


## <a name="summary"></a>Résumé

Applications pilotées par les données utilisent généralement des bases de données différentes dans les environnements de développement et de production. Par conséquent, les chaînes de connexion de base de données stockées dans la configuration de s d’application web doivent être uniques pour chaque environnement. Dans ce didacticiel, nous avons étudié comment déterminer la chaîne de connexion de base de données de production et les façons de mettre à jour les informations de chaîne de connexion unique dans les deux environnements.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [Chaînes de connexion et les fichiers de Configuration](https://msdn.microsoft.com/en-us/library/ms254494.aspx)
- [Configuration de la base de données de chaînes d’informations @ ConnectionStrings.com](http://www.connectionstrings.com/)
- [Déplacer les paramètres du fichier Web.config](http://www.asp101.com/tips/index.asp?id=154)
- [Documentation technique pour le &lt;connectionStrings&gt; élément](https://msdn.microsoft.com/en-us/library/bf7sd233.aspx)

>[!div class="step-by-step"]
[Précédent](deploying-a-database-vb.md)
[Suivant](configuring-a-website-that-uses-application-services-vb.md)
