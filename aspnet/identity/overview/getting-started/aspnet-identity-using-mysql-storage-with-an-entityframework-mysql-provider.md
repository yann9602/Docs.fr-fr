---
uid: identity/overview/getting-started/aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider
title: "Identité ASP.NET : L’utilisation du stockage de MySQL avec un fournisseur de MySQL EntityFramework (c#) | Documents Microsoft"
author: maumar
description: "Ce didacticiel vous montre comment remplacer le mécanisme de stockage de données par défaut pour ASP.NET Identity EntityFramework (fournisseur SQL client) avec un fournir MySQL..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/10/2013
ms.topic: article
ms.assetid: 15253312-a92c-43ba-908e-b5dacd3d08b8
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /identity/overview/getting-started/aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider
msc.type: authoredcontent
ms.openlocfilehash: ac254abcb756d048d159a9b67967a581f35ac871
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider-c"></a>Identité ASP.NET : L’utilisation du stockage de MySQL avec un fournisseur EntityFramework MySQL (c#)
====================
par [Maurycy Markowski](https://github.com/maumar), [Raquel Soares De Almeida](https://github.com/raquelsa), [Robert McMurray](https://github.com/rmcmurray)

> Ce didacticiel vous montre comment remplacer le mécanisme de stockage de données par défaut pour [ **ASP.NET Identity** ](introduction-to-aspnet-identity.md) avec EntityFramework (fournisseur SQL client) avec un fournisseur MySQL.


Les rubriques suivantes sont traitées dans ce didacticiel :

- Création d’une base de données MySQL sur Azure
- Création d’une application MVC à l’aide du modèle MVC de 2013 Visual Studio
- Configuration EntityFramework pour travailler avec un fournisseur de base de données MySQL
- Exécution de l’application pour vérifier les résultats

À la fin de ce didacticiel, vous disposerez une application MVC avec l’identité de ASP.NET stocker qui utilise une base de données MySQL qui est hébergé dans Azure.

## <a name="creating-a-mysql-database-instance-on-azure"></a>Création d’une instance de la base de données MySQL sur Azure

1. Se connecter à la [portail Azure](https://go.microsoft.com/fwlink/?linkid=529715&amp;clcid=0x409).
2. Cliquez sur **nouveau** au bas de la page, puis sélectionnez **magasin**:  
  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image2.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image1.png)
3. Dans le **choisir et des modules complémentaires** Assistant, sélectionnez **base de données MySQL de ClearDB**, puis cliquez sur le **suivant** flèche en bas de l’image :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image4.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image3.png)
4. Conservez la valeur par défaut **libre** planifier, de modifier le **nom** à **IdentityMySQLDatabase**, sélectionnez la région qui est plus proche de vous, puis cliquez sur le **suivant** flèche en bas de l’image :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image6.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image5.png)
5. Cliquez sur le **achat** coche pour terminer la création de la base de données.  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image8.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image7.png)
6. Une fois que votre base de données a été créé, vous pouvez le gérer à partir de la **modules complémentaires** onglet dans le portail de gestion. Pour récupérer les informations de connexion pour votre base de données, cliquez sur **informations de connexion** au bas de la page :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image10.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image9.png)
7. Copier la chaîne de connexion en cliquant sur le bouton Copier par le **CONNECTIONSTRING** champ et l’enregistrer, vous allez utiliser ces informations ultérieurement dans ce didacticiel pour votre application MVC :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image12.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image11.png)

## <a name="creating-an-mvc-application-project"></a>Création d’un projet d’application MVC

Pour effectuer les étapes décrites dans cette section du didacticiel, vous devez d’abord installer [Visual Studio Express 2013 pour le Web](https://go.microsoft.com/fwlink/?LinkId=299058) ou [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Une fois que Visual Studio a été installé, procédez comme suit pour créer un nouveau projet d’application MVC :

1. Ouvrez Visual Studio 2103.
2. Cliquez sur **nouveau projet** à partir de la **Démarrer** page, vous pouvez également cliquer sur le **fichier** menu, puis **nouveau projet**:  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image2.jpg)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image1.jpg)
3. Lorsque le **nouveau projet** boîte de dialogue, développez **Visual C#** dans la liste des modèles, puis cliquez sur **Web**, puis sélectionnez **ASP.NET Web Application**. Nommez votre projet **IdentityMySQLDemo** puis cliquez sur **OK**:  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image14.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image13.png)
4. Dans le **nouveau projet ASP.NET** boîte de dialogue, sélectionnez le **MVC** templatewith les options par défaut ; cette va configurer **comptes d’utilisateur individuels** comme méthode d’authentification. Cliquez sur **OK**:  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image16.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image15.png)

## <a name="configure-entityframework-to-work-with-a-mysql-database"></a>Configurer EntityFramework pour travailler avec une base de données MySQL

### <a name="update-the-entity-framework-assembly-for-your-project"></a>Mise à jour de l’assembly d’Entity Framework pour votre projet

L’application MVC qui a été créée à partir du modèle Visual Studio 2013 contienne une référence à la [EntityFramework 6.0.0](http://www.nuget.org/packages/EntityFramework) du package, mais il ont été mises à jour à cet assembly depuis sa version qui contiennent améliorations des performances. Pour utiliser ces dernières mises à jour dans votre application, procédez comme suit.

1. Ouvrez votre projet MVC dans Visual Studio 2013.
2. Cliquez sur **outils**, puis cliquez sur **Gestionnaire de Package de bibliothèque**, puis cliquez sur **Package Manager Console**:  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image18.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image17.png)
3. Le **Package Manager Console** apparaîtra dans la section inférieure de Visual Studio. Type &quot; **mise à jour-Package EntityFramework** &quot; et appuyez sur ENTRÉE :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image20.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image19.png)

### <a name="install-the-mysql-provider-for-entityframework"></a>Installez le fournisseur MySQL pour EntityFramework

Dans l’ordre pour EntityFramework pour se connecter à la base de données MySQL, vous devez installer un fournisseur MySQL. Pour ce faire, ouvrez le **Package Manager Console** et type &quot; **Pre - Install-Package MySql.Data.Entity**&quot;, puis appuyez sur ENTRÉE.

> [!NOTE]
> Il s’agit d’une version préliminaire de l’assembly, et par conséquent il peut contenir des bogues. Vous ne devez pas utiliser une version préliminaire du fournisseur en production.


[Cliquez sur l’image suivante pour la développer.]  
  
[![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image22.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image21.png)

### <a name="making-project-configuration-changes-to-the-webconfig-file-for-your-application"></a>Apporter des modifications de configuration de projet dans le fichier Web.config pour votre application

Dans cette section, que vous allez configurer l’Entity Framework pour utiliser le fournisseur MySQL que vous venez d’installer, inscrire la fabrique de fournisseur MySQL et ajoutez votre chaîne de connexion à partir d’Azure.

> [!NOTE]
> Les exemples suivants contiennent une version d’assembly spécifique pour MySql.Data.dll. Si la version d’assembly change, vous devez modifier les paramètres de configuration approprié avec la version appropriée.


1. Ouvrez le fichier Web.config de votre projet dans Visual Studio 2013.
2. Recherchez les paramètres de configuration suivants, qui définissent le fournisseur de base de données par défaut et la fabrique pour Entity Framework :

    [!code-xml[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample1.xml)]
3. Remplacer les paramètres de configuration avec la commande suivante, qui configure l’Entity Framework pour utiliser le fournisseur MySQL : 

    [!code-xml[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample2.xml)]
4. Recherchez le &lt;connectionStrings&gt; section et remplacez-le par le code suivant, qui définit la chaîne de connexion pour votre base de données MySQL qui est hébergé sur Azure (Notez que valeur providerName a également été modifié à partir de la d’origine) :

    [!code-xml[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample3.xml?highlight=3-4)]

### <a name="adding-custom-migrationhistory-context"></a>Ajout de contexte MigrationHistory personnalisé

Utilise Entity Framework Code First un **MigrationHistory** table pour effectuer le suivi des modifications apportées au modèle et pour assurer la cohérence entre le schéma de base de données et le schéma conceptuel. Toutefois, cette table ne fonctionne pas les pour MySQL par défaut, car la clé primaire est trop grande. Pour remédier à cette situation, vous devez réduire la taille de clé pour cette table. Pour ce faire, procédez comme suit :

1. Les informations de schéma pour cette table sont capturées dans une **HistoryContext**, ce qui peut être modifié comme n’importe quel autre **DbContext**. Pour ce faire, ajoutez un nouveau fichier de classe nommé **MySqlHistoryContext.cs** au projet et remplacez son contenu par le code suivant :

    [!code-csharp[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample4.cs)]
2. Ensuite, vous devrez configurer Entity Framework pour utiliser modifié **HistoryContext**, plutôt que par défaut. Cela est possible en tirant parti des fonctionnalités de configuration basée sur le code. Pour ce faire, ajoutez le nouveau fichier de classe nommé **MySqlConfiguration.cs** à votre projet et remplacez son contenu avec :

    [!code-csharp[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample5.cs)]

### <a name="creating-a-custom-entityframework-initializer-for-applicationdbcontext"></a>Création d’un initialiseur EntityFramework personnalisé pour ApplicationDbContext

Le fournisseur MySQL qui est inclu dans ce didacticiel ne prend pas en charge les migrations d’Entity Framework, vous devez utiliser des initialiseurs de modèle pour vous connecter à la base de données. Étant donné que ce didacticiel utilise une instance de MySQL sur Azure, vous devrez peut-être créer un initialiseur d’Entity Framework personnalisé.

> [!NOTE]
> Cette étape n’est pas nécessaire si vous vous connectez à une instance de SQL Server sur Azure ou si vous utilisez une base de données est hébergée sur site.


Pour créer un initialiseur d’Entity Framework personnalisé pour MySQL, procédez comme suit :

1. Ajouter un nouveau fichier de classe nommé **MySqlInitializer.cs** pour le projet, puis remplacez, il est contenu par le code suivant : 

    [!code-csharp[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample6.cs?highlight=23)]
2. Ouvrez le **IdentityModels.cs** fichier de votre projet, ce qui se trouve dans le **modèles** active et remplacer le contenu avec les éléments suivants : 

    [!code-csharp[Main](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/samples/sample7.cs)]

## <a name="running-the-application-and-verifying-the-database"></a>Exécution de l’application et la vérification de la base de données

Une fois que vous avez terminé les étapes décrites dans les sections précédentes, vous devez tester votre base de données. Pour ce faire, procédez comme suit :

1. Appuyez sur **Ctrl + F5** pour générer et exécuter l’application web.
2. Cliquez sur le **inscrire** onglet en haut de la page :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image4.jpg)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image3.jpg)
3. Entrez un nom d’utilisateur et un mot de passe, puis cliquez sur **inscrire**:  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image24.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image23.png)
4. À ce stade, les tables d’identité ASP.NET sont créés sur la base de données MySQL, et l’utilisateur est enregistré et connecté à l’application :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image6.jpg)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image5.jpg)

### <a name="installing-mysql-workbench-tool-to-verify-the-data"></a>Installation de l’outil de banc d’essai MySQL pour vérifier les données

1. Installer le **banc d’essai MySQL** outil à partir de la [page Téléchargements de MySQL](http://dev.mysql.com/downloads/windows/installer/)
2. Dans l’Assistant installation : **sélection des fonctionnalités** onglet, sélectionnez **banc d’essai MySQL** sous **applications** section.
3. Lancez l’application et ajouter une nouvelle connexion en utilisant les données de chaîne de connexion à partir de la base de données MySQL de Azure que vous avez créé à la lacune de ce didacticiel.
4. Après avoir établi la connexion, vérifiez que le **ASP.NET Identity** tables créées sur le **IdentityMySQLDatabase.**
5. Vous verrez que tous les ASP.NET Identity requis tables sont créées comme indiqué dans l’image ci-dessous :  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image8.jpg)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image7.jpg)
6. Inspecter le **aspnetusers** de table pour l’instance dont vous recherchez les entrées que vous inscrivez des nouveaux utilisateurs.  
  
 [Cliquez sur l’image suivante pour la développer. ]  
    [![](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image26.png)](aspnet-identity-using-mysql-storage-with-an-entityframework-mysql-provider/_static/image25.png)
