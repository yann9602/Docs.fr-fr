---
uid: identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure
title: "Meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Service d’applications Azure | Documents Microsoft"
author: Rick-Anderson
description: "Ce didacticiel montre comment votre code peut stocker en toute sécurité et accéder aux informations sécurisées. Le point le plus important est que vous ne devez jamais stocker les mots de passe ou autres voyer..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/21/2015
ms.topic: article
ms.assetid: 97902c66-cb61-4d11-be52-73f962f2db0a
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure
msc.type: authoredcontent
ms.openlocfilehash: 465c9cf6f452c268e7e23509e7a29547df5d3e83
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure-app-service"></a>Meilleures pratiques pour le déploiement des mots de passe et autres données sensibles sur ASP.NET et Service d’applications Azure
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Ce didacticiel montre comment votre code peut stocker en toute sécurité et accéder aux informations sécurisées. Le point le plus important est de vous ne devez jamais stocker les mots de passe ou d’autres données sensibles dans le code source, et vous ne devez pas utiliser les clés secrètes de production en mode de développement et de test.
> 
> L’exemple de code est une application de console de la tâche Web simple et une application ASP.NET MVC qui doit accéder à une base de données chaîne mot de passe, Twilio, Google et SendGrid sécurisée clés de connexion.
> 
> Sur le site paramètres et PHP est également mentionnée.


- [Utilisation des mots de passe dans l’environnement de développement](#pwd)
- [Utilisation des chaînes de connexion dans l’environnement de développement](#con)
- [Applications de console tâches Web](#wj)
- [Déploiement des secrets dans Azure](#da)
- [Notes pour sur site et PHP](#not)
- [Ressources supplémentaires pour MSBuild](#addRes)

<a id="pwd"></a>
## <a name="working-with-passwords-in-the-development-environment"></a>Utilisation des mots de passe dans l’environnement de développement

Didacticiels montrent souvent des données sensibles dans le code source, nous espérons qu’avec un inconvénient que vous ne devez jamais stocker des données sensibles dans le code source. Par exemple, mon [application ASP.NET MVC 5 avec SMS et messagerie 2FA](../../../mvc/overview/security/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication.md) didacticiel montre les éléments suivants dans le *web.config* fichier :

[!code-xml[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample1.xml)]

Le *web.config* fichier est le code source, ces clés secrètes ne doivent jamais être stockées dans ce fichier. Heureusement, le `<appSettings>` élément a un `file` attribut qui vous permet de spécifier un fichier externe qui contient les paramètres de configuration d’application sensibles. Tant que le fichier externe n’est pas activé dans votre arborescence source, vous pouvez déplacer tous les secrets votre vers un fichier externe. Par exemple, dans le balisage suivant, le fichier *AppSettingsSecrets.config* contient tous les secrets de l’application :

[!code-xml[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample2.xml)]

Le balisage dans le fichier externe (*AppSettingsSecrets.config* dans cet exemple), le même balisage se trouve dans le *web.config* fichier :

[!code-xml[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample3.xml)]

Le runtime ASP.NET fusionne le contenu du fichier externe avec le balisage dans &lt;appSettings&gt; élément. Le runtime ignore l’attribut de fichier si le fichier spécifié est introuvable.

> [!WARNING]
> Sécurité : n’ajoutez pas votre *secrets .config* de fichiers à votre projet ou la vérifier dans le contrôle de code source. Par défaut, Visual Studio affecte la `Build Action` à `Content`, ce qui signifie que le fichier est déployé. Pour plus d’informations, consultez [pourquoi ne pas tous les fichiers dans le dossier du projet sont déployés ?](https://msdn.microsoft.com/en-us/library/ee942158(v=vs.110).aspx#can_i_exclude_specific_files_or_folders_from_deployment) Bien que vous pouvez utiliser n’importe quelle extension pour le *secrets .config* fichier, il est préférable qu’il soit *.config*, comme les fichiers de configuration ne sont pas pris en charge par IIS. Notez également que la *AppSettingsSecrets.config* fichier est à deux niveaux de répertoire au-dessus de la *web.config* de fichiers, par conséquent, il est complètement hors du répertoire de solution. En déplaçant le fichier du répertoire de la solution, &quot;git ajouter \* &quot; ne sera pas l’ajouter à votre référentiel.


<a id="con"></a>
## <a name="working-with-connection-strings-in-the-development-environment"></a>Utilisation des chaînes de connexion dans l’environnement de développement

Visual Studio crée les nouveaux projets ASP.NET qui utilisent [LocalDB](https://blogs.msdn.com/b/sqlexpress/archive/2011/07/12/introducing-localdb-a-better-sql-express.aspx). Base de données locale a été créée spécifiquement pour l’environnement de développement. Il ne nécessite pas un mot de passe, par conséquent, vous ne devez rien à faire pour empêcher des secrets d’en cours de vérification dans votre code source. Certaines équipes de développement utilisent les versions complètes de SQL Server (ou autres SGBD) qui requièrent un mot de passe.

Vous pouvez utiliser la `configSource` pour remplacer l’ensemble de l’attribut `<connectionStrings>` balisage. Contrairement à la `<appSettings>` `file` attribut qui fusionne le balisage, le `configSource` attribut remplace le balisage. La balise suivante montre la `configSource` d’attribut dans le *web.config* fichier :

[!code-xml[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample4.xml?highlight=1)]

> [!NOTE]
> Si vous utilisez la `configSource` d’attributs comme indiqué plus haut pour déplacer vos chaînes de connexion à un fichier externe et que Visual Studio crée un nouveau site web, il ne pourra pas détecter vous utilisez une base de données, et vous n’obtiendrez pas l’option de configuration de la base de données lorsque vous pu blier vers Azure à partir de Visual Studio. Si vous utilisez la `configSource` attribut, vous pouvez utiliser PowerShell pour créer et déployer votre site web et la base de données, ou vous pouvez créer le site web et la base de données dans le portail avant de publier. Le [New-AzureWebsitewithDB.ps1](https://gallery.technet.microsoft.com/scriptcenter/Ultimate-Create-Web-SQL-DB-9e0fdfd3) script créera un nouveau site web et la base de données.


> [!WARNING]
> Sécurité - Contrairement à la *AppSettingsSecrets.config* fichier, le fichier de chaînes de connexion externe doit être dans le même répertoire que la racine *web.config* de fichiers, donc vous devez prendre des précautions pour vous assurer ne pas intégrer dans votre référentiel de code source.


> [!NOTE]
> **Avertissement de sécurité sur les fichiers de secrets :** une meilleure solution consiste à ne pas utiliser les secrets de fabrication dans le développement et de test. Fuites de ces clés secrètes à l’aide de mots de passe de production dans le test ou de développement.


<a id="wj"></a>
## <a name="webjobs-console-apps"></a>Applications de console tâches Web

Le *app.config* fichier utilisé par une application de console ne prend pas en charge les chemins d’accès relatifs, mais il ne prend pas en charge les chemins d’accès absolus. Vous pouvez utiliser un chemin d’accès absolu pour déplacer vos secrets en dehors de votre répertoire de projet. Le balisage suivant montre les secrets dans le *C:\secrets\AppSettingsSecrets.config* du fichier et les données non sensibles dans le *app.config* fichier.

[!code-xml[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample5.xml?highlight=2)]

<a id="da"></a>
## <a name="deploying-secrets-to-azure"></a>Déploiement des secrets dans Azure

Lorsque vous déployez votre application web sur Azure, le *AppSettingsSecrets.config* fichier ne sera pas déployé (c’est ce que vous souhaitez). Vous pouvez accéder à la [portail de gestion Azure](https://azure.microsoft.com/services/management-portal/) et les définir manuellement, pour ce faire :

1. Accédez à [https://portal.azure.com](https://portal.azure.com)et connectez-vous avec vos informations d’identification Azure.
2. Cliquez sur **Parcourir &gt; Web Apps**, puis cliquez sur le nom de votre application web.
3. Cliquez sur **tous les paramètres &gt; paramètres de l’Application**.

Le **paramètres de l’application** et **chaîne de connexion** valeurs remplacent les mêmes paramètres dans le *web.config* fichier. Dans notre exemple, nous n’avez pas déployé ces paramètres dans Azure, mais si ces clés se trouvaient dans le *web.config* fichier, les paramètres affichés sur le portail sont prioritaire.

Une meilleure pratique consiste à suivre un [DevOps workflow](../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything.md) et utiliser [Azure PowerShell](https://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/) (ou une autre infrastructure, tels que [Chef](http://www.opscode.com/chef/) ou [Puppet](http://puppetlabs.com/puppet/what-is-puppet)) à automatiser la définition de ces valeurs dans Azure. Le script PowerShell suivant utilise [Export-CliXml](http://www.powershellcookbook.com/recipe/PukO/securely-store-credentials-on-disk) pour exporter les secrets chiffrés sur le disque :

[!code-powershell[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample6.ps1)]

Dans le script ci-dessus, « Name » est le nom de la clé secrète, tel que «&quot;FB\_AppSecret&quot; ou « TwitterSecret ». Vous pouvez afficher le fichier « .credential » créé par le script dans votre navigateur. L’extrait de code ci-dessous teste chacun des fichiers d’informations d’identification et définit les clés secrètes de l’application web nommée :

[!code-powershell[Main](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure/samples/sample7.ps1)]

> [!WARNING]
> Sécurité - n’incluez pas les mots de passe ou autres secrets dans le script PowerShell, en effectuant ce empêche l’objectif de l’utilisation d’un script PowerShell pour déployer des données sensibles. Le [Get-Credential](https://technet.microsoft.com/en-us/library/hh849815.aspx) applet de commande fournit un mécanisme sécurisé pour obtenir un mot de passe. À l’aide d’une invite de commandes de l’interface utilisateur peut empêcher une fuite d’un mot de passe.


### <a name="deploying-db-connection-strings"></a>Déploiement des chaînes de connexion de base de données

Chaînes de connexion de base de données sont gérées de la même façon pour les paramètres de l’application. Si vous déployez votre application web à partir de Visual Studio, la chaîne de connexion est configurée pour vous. Vous pouvez le vérifier dans le portail. La méthode recommandée pour définir la chaîne de connexion est avec PowerShell. Pour obtenir un exemple de script PowerShell les crée un site Web et la base de données et définit la chaîne de connexion dans le site Web, téléchargez [New-AzureWebsitewithDB.ps1](https://gallery.technet.microsoft.com/scriptcenter/Ultimate-Create-Web-SQL-DB-9e0fdfd3) à partir de la [bibliothèque de Script Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&amp;f%5B0%5D.Value=WindowsAzure).

<a id="not"></a>
## <a name="notes-for-php"></a>Notes pour PHP

Depuis les paires clé-valeur pour les deux **paramètres de l’application** et **les chaînes de connexion** sont stockées dans les variables d’environnement sur le Service d’applications Azure, les développeurs qui utilisent des infrastructures (telles que PHP) d’une application web peut facilement extraire ces valeurs. Consultez de Stefan Schackow [Sites Web Windows Azure : manière dont les chaînes d’Application et de travail des chaînes de connexion](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) billet de blog qui présente un extrait de code PHP pour lire les paramètres de l’application et les chaînes de connexion.

## <a name="notes-for-on-premises-servers"></a>Notes pour les serveurs locaux

Si vous déployez vers les serveurs web locaux, vous pouvez aider les secrets sécurisés par [chiffrer les sections de configuration des fichiers de configuration](https://msdn.microsoft.com/en-us/library/ff647398.aspx). En guise d’alternative, vous pouvez utiliser la même approche recommandée pour les sites Web Azure : conserver les paramètres de développement dans les fichiers de configuration et utiliser des valeurs de variables d’environnement pour les paramètres de production. Dans ce cas, toutefois, vous devez écrire du code d’application pour la fonctionnalité est automatique dans les sites Web Azure : récupérer les paramètres des variables d’environnement et utiliser ces valeurs à la place du fichier de configuration ou utiliser le fichier de configuration lorsque variables d’environnement sont introuvables.

<a id="addRes"></a>
## <a name="additional-resources"></a>Ressources supplémentaires

Pour obtenir un exemple d’un PowerShell script qui crée une application web + de la base de données, définit la chaîne de connexion + de paramètres de l’application, de téléchargement [New-AzureWebsitewithDB.ps1](https://gallery.technet.microsoft.com/scriptcenter/Ultimate-Create-Web-SQL-DB-9e0fdfd3) à partir de la [bibliothèque de Script Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&amp;f%5B0%5D.Value=WindowsAzure). 

Consultez de Stefan Schackow [Sites Web Windows Azure : manière dont les chaînes d’Application et de connexion chaînes travail](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)


Un remerciement Barry Dorrans particulier ( [ @blowdart ](https://twitter.com/blowdart) ) et Carlos Farre pour la révision.
