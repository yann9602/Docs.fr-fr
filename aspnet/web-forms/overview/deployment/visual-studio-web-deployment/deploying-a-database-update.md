---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-database-update
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement d’une mise à jour de la base de données | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 9cad0833-486a-4474-a7f3-7715542ec4ce
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-a-database-update
msc.type: authoredcontent
ms.openlocfilehash: 3fd29a5b26c564d88e4128d1904fab00b57a3b7c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-a-database-update"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement d’une mise à jour de la base de données
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous apportez une modification de la base de données et les modifications de code associé, les modifications de test dans Visual Studio, puis déployer la mise à jour pour les environnements de test, intermédiaire et de production.

Ce didacticiel illustre d’abord la mettre à jour une base de données qui est géré par les Migrations Code First et ultérieurement il montre ensuite comment mettre à jour une base de données à l’aide du fournisseur dbDacFx.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter le [page Résolution des problèmes](troubleshooting.md).

## <a name="deploy-a-database-update-by-using-code-first-migrations"></a>Déployer une mise à jour de la base de données à l’aide de Migrations Code First

Dans cette section, vous ajoutez une colonne de date de naissance à la `Person` classe de base pour le `Student` et `Instructor` entités. Puis vous mettez à jour la page qui affiche les données de formateur afin qu’il affiche la nouvelle colonne. Enfin, vous déployez les modifications apportées au test, intermédiaire et de production.

### <a name="add-a-column-to-a-table-in-the-application-database"></a>Ajouter une colonne à une table dans la base de données d’application

1. Dans le *ContosoUniversity.DAL* projet, ouvrez *Person.cs* et ajoutez la propriété suivante à la fin de la `Person` classe (vous devez voir deux accolades suit) :

    [!code-csharp[Main](deploying-a-database-update/samples/sample1.cs)]

    Ensuite, mettez à jour le `Seed` méthode afin qu’elle fournit une valeur pour la nouvelle colonne. Ouvrez *Migrations\Configuration.cs* et remplacez le bloc de code qui commence `var instructors = new List<Instructor>` avec le bloc de code suivant, qui inclut des informations de date de naissance :

    [!code-csharp[Main](deploying-a-database-update/samples/sample2.cs)]
2. Générez la solution, puis ouvrez le **Package Manager Console** fenêtre. Assurez-vous que ContosoUniversity.DAL est toujours sélectionné en tant que le **projet par défaut**.
3. Dans le **Package Manager Console** fenêtre, sélectionnez **ContosoUniversity.DAL** comme le **projet par défaut**, puis entrez la commande suivante :

    [!code-powershell[Main](deploying-a-database-update/samples/sample3.ps1)]

    Une fois cette commande, Visual Studio ouvre le fichier de classe qui définit la nouvelle `DbMIgration` (classe), puis, dans le `Up` (méthode), vous pouvez voir le code qui crée la nouvelle colonne. Le `Up` méthode crée la colonne lorsque vous implémentez la modification et le `Down` méthode supprime la colonne lorsque vous restaurez la modification.

    ![AddBirthDate_migration_code](deploying-a-database-update/_static/image1.png)
4. Générez la solution et entrez la commande suivante dans le **Package Manager Console** fenêtre (Assurez-vous que le projet ContosoUniversity.DAL est toujours sélectionné) :

    [!code-powershell[Main](deploying-a-database-update/samples/sample4.ps1)]

    Entity Framework exécute le `Up` (méthode), puis exécute le `Seed` (méthode).

### <a name="display-the-new-column-in-the-instructors-page"></a>Afficher la nouvelle colonne dans la page des instructeurs

1. Dans le projet ContosoUniversity, ouvrez *Instructors.aspx* et ajouter un nouveau champ de modèle pour afficher la date de naissance. Ajoutez-le entre celles pour l’attribution d’embauche de date et d’office :

    [!code-aspx[Main](deploying-a-database-update/samples/sample5.aspx?highlight=9-17)]

    (Si la mise en retrait du code est désynchronisé, vous pouvez appuyer sur CTRL-K, puis sur CTRL + D pour automatiquement remettre le fichier.)
2. Exécutez l’application et cliquez sur le **instructeurs** lien.

    Lors du chargement de la page, vous constatez qu’il comporte le nouveau champ de date de naissance.

    ![Page instructeurs avec la date de naissance](deploying-a-database-update/_static/image2.png)
3. Fermez le navigateur.

### <a name="deploy-the-database-update"></a>Déployer la mise à jour de la base de données

1. Dans **l’Explorateur de solutions** sélectionnez le projet ContosoUniversity.
2. Dans le **publication Web en un clic** barre d’outils, cliquez sur le **Test** le profil de publication, puis cliquez sur **publier le site Web**. (Si la barre d’outils est désactivé, sélectionnez le projet ContosoUniversity dans **l’Explorateur de solutions**.)

    Visual Studio déploie l’application mis à jour, et le navigateur s’ouvre à la page d’accueil.
3. Exécutez le **instructeurs** page pour vérifier que la mise à jour a été déployé.

    Lorsque l’application tente d’accéder à la base de données pour cette page, le premier Code met à jour le schéma de base de données et exécute le `Seed` (méthode). Lorsque la page s’affiche, vous voyez attendu **Date de naissance** colonne de dates qu’il contient.
4. Dans le **publication Web en un clic** barre d’outils, cliquez sur le **intermédiaire** le profil de publication, puis cliquez sur **publier le site Web**.
5. Exécutez le **instructeurs** page de la zone de transit pour vérifier que la mise à jour a été déployé.
6. Dans le **publication Web en un clic** barre d’outils, cliquez sur le **Production** le profil de publication, puis cliquez sur **publier le site Web**.
7. Exécutez le **instructeurs** page en production pour vérifier que la mise à jour a été déployé.

    Pour une une mise à jour d’application réelles de production qui inclut une modification de la base de données en général également suivre l’application en mode hors connexion durant le déploiement à l’aide de *application\_offline.htm*, comme vous l’avez vu dans le didacticiel précédent.

## <a name="deploy-a-database-update-by-using-the-dbdacfx-provider"></a>Déployer une mise à jour de la base de données en utilisant le fournisseur dbDacFx

Dans cette section, vous ajoutez un *commentaires* colonne à la *utilisateur* de table dans la base de données d’appartenance et de créer une page qui vous permet d’afficher et modifier les commentaires pour chaque utilisateur. Vous déployez les modifications sur le test, intermédiaire et de production.

### <a name="add-a-column-to-a-table-in-the-membership-database"></a>Ajouter une colonne à une table dans la base de données d’appartenance

1. Dans Visual Studio, ouvrez **l’Explorateur d’objets SQL Server**.
2. Développez **(localdb) \v11.0**, développez **bases de données**, développez **aspnet-ContosoUniversity** (pas **aspnet-ContosoUniversity-Prod**) puis développez **Tables**.

    Si vous ne voyez pas **(localdb) \v11.0** sous le **SQL Server** nœud, avec le bouton droit le **SQL Server** nœud et cliquez sur **ajouter SQL Server**. Dans le **se connecter au serveur** boîte de dialogue Entrez *(localdb) \v11.0* comme le **nom du serveur**, puis cliquez sur **connexion**.

    Si vous ne voyez pas **aspnet-ContosoUniversity**, exécutez le projet et connectez-vous à l’aide de la *admin* informations d’identification (mot de passe est *devpwd*), puis actualisez le  **L’Explorateur d’objets SQL Server** fenêtre.
3. Avec le bouton droit le **utilisateurs** de table, puis cliquez sur **Concepteur de vue**.

    ![Concepteur de vue SSOX](deploying-a-database-update/_static/image3.png)
4. Dans le concepteur, ajoutez un *commentaires* colonne et le rendre *nvarchar (128)* et accepte les valeurs NULL, puis cliquez sur **mise à jour**.

    ![Ajout de la colonne commentaires](deploying-a-database-update/_static/image4.png)
5. Dans le **mises à jour de la base de données aperçu** , cliquez sur **mise à jour de la base de données**.

    ![Aperçu des mises à jour de base de données](deploying-a-database-update/_static/image5.png)

### <a name="create-a-page-to-display-and-edit-the-new-column"></a>Créer une page pour afficher et modifier la nouvelle colonne

1. Dans **l’Explorateur de solutions**, avec le bouton droit le **compte** cliquez sur le dossier dans le projet ContosoUniversity, **ajouter**, puis cliquez sur **un nouvel élément** .
2. Créer un nouveau **Page maître à l’aide de formulaire Web** et nommez-le *UserInfo.aspx*. Acceptez la valeur par défaut *Site.Master* fichier en tant que la page maître.
3. Copiez le balisage suivant dans le `MainContent` `Content` élément (la dernière de 3 `Content` éléments) :

    [!code-aspx[Main](deploying-a-database-update/samples/sample6.aspx)]
4. Cliquez sur le *UserInfo.aspx* page, puis cliquez sur **afficher dans le navigateur**.
5. Ouvrez une session avec votre *admin* informations d’identification de l’utilisateur (mot de passe est *devpwd*) et ajouter des commentaires à un utilisateur pour vérifier que la page fonctionne correctement.

    ![Page UserInfo](deploying-a-database-update/_static/image6.png)
6. Fermez le navigateur.

## <a name="deploy-the-database-update"></a>Déployer la mise à jour de la base de données

Pour déployer à l’aide du fournisseur dbDacFx, il vous suffit de sélectionner le **base de données de mise à jour** option dans le profil de publication. Toutefois, pour le déploiement initial lorsque vous avez utilisé cette option vous avez également configuré certains scripts SQL supplémentaires pour exécuter : ceux sont toujours dans le profil et vous devrez les empêcher de s’exécuter à nouveau.

1. Ouvrez le **publier le site Web** Assistant en cliquant sur le projet ContosoUniversity en cliquant sur **publier**.
2. Sélectionnez le **Test** profil.
3. Cliquez sur le **paramètres** onglet.
4. Sous **DefaultConnection**, sélectionnez **base de données de mise à jour**.
5. Désactiver les scripts supplémentaires que vous avez configuré pour s’exécuter pour le déploiement initial :

    1. Cliquez sur **configurer des mises à jour de la base de données**.
    2. Dans le **configurer les mises à jour de base de données** boîte de dialogue, désactivez les cases à cocher à côté *Grant.sql* et *aspnet-données-dev.sql*.
    3. Cliquez sur **Fermer**.
6. Cliquez sur le **aperçu** onglet.
7. Sous **bases de données** et à droite de **DefaultConnection**, cliquez sur le **base de données préliminaire** lien.

    ![Aperçu de la base de données](deploying-a-database-update/_static/image7.png)

    La fenêtre d’aperçu affiche le script qui sera exécuté dans la base de données de destination pour rendre ce schéma de base de données correspond au schéma de la base de données source. Le script inclut une commande ALTER TABLE qui ajoute la nouvelle colonne.
8. Fermer le **aperçu de la base de données** boîte de dialogue, puis cliquez sur **publier**.

    Visual Studio déploie l’application mis à jour, et le navigateur s’ouvre à la page d’accueil.
9. Exécutez la page UserInfo (ajouter *Account/UserInfo.aspx* à l’URL de la page d’accueil) pour vérifier que la mise à jour a été déployé. Vous devrez vous connecter en entrant *admin* et *devpwd*.

    Les données dans les tables ne sont pas déployées par défaut, et vous n’a pas configuré un script de déploiement de données à exécuter, donc vous ne trouverez pas les commentaires que vous avez ajouté dans le développement. Vous pouvez ajouter un nouveau commentaire maintenant la zone de transit pour vérifier que la modification a été déployée sur la base de données et que la page fonctionne correctement.
10. Suivez la même procédure pour déployer sur intermédiaire et de production.

    N’oubliez pas de désactiver les scripts supplémentaires. La seule différence par rapport au profil de Test est que vous allez désactiver un seul script dans la mise en transit et des profils de Production, car ils ont été configurés pour s’exécuter uniquement *aspnet-op-data.sql*.

    Les informations d’identification intermédiaire et de production sont admin et prodpwd.

    Pour une mise à jour d’application réelles de production qui inclut une modification de la base de données en général également suivre l’application en mode hors connexion durant le déploiement en téléchargeant *application\_offline.htm* avant la publication et en la supprimant Ensuite, comme vous l’avez vu dans [du didacticiel précédent](deploying-a-code-update.md).

## <a name="summary"></a>Résumé

Vous avez maintenant déployé une mise à jour d’application qui comportait une modification de la base de données à l’aide de Migrations Code First et le fournisseur dbDacFx.

![Page instructeurs avec la date de naissance](deploying-a-database-update/_static/image8.png)

![Page UserInfo](deploying-a-database-update/_static/image9.png)

Le didacticiel suivant vous montre comment exécuter les déploiements à l’aide de la ligne de commande.

>[!div class="step-by-step"]
[Précédent](deploying-a-code-update.md)
[Suivant](command-line-deployment.md)
