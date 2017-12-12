---
uid: mvc/overview/getting-started/database-first-development/publish-to-azure
title: Publier un site MVC Database First sur Azure | Documents Microsoft
author: tfitzmac
description: "À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Ce didacticiel seri..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/22/2014
ms.topic: article
ms.assetid: 7131f1c1-cef3-4396-ab44-ed4519676546
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/database-first-development/publish-to-azure
msc.type: authoredcontent
ms.openlocfilehash: f75b7192b4d97c88fcbcb4ad7fef88c83157c902
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="publish-mvc-database-first-site-to-azure"></a>Publier un site MVC Database First dans Windows Azure
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Cette série de didacticiels vous montre comment automatiquement générer du code qui permet aux utilisateurs d’afficher, modifier, créer et supprimer des données qui résident dans une table de base de données. Le code généré correspond aux colonnes dans la table de base de données.
> 
> Cette partie de la série se concentre sur l’application web et la base de données de publication vers Azure. Vous pouvez lire cette rubrique pour en savoir plus sur la publication d’une application web et la base de données, mais pour effectuer les étapes que vous devez commencer au début du didacticiel. Consultez [mise en route](setting-up-database.md).


## <a name="deploy-your-web-app-on-azure"></a>Déployer votre application web sur Azure

Vous avez besoin d’un compte Azure pour effectuer ce didacticiel :

- Vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=A261C142F) -vous obtenez des crédits que vous pouvez utiliser pour tester les services Azure payants et même après qu’ils soient utilisés jusqu'à, vous pouvez conserver le compte et libérer de l’utilisation des services Azure.
- Vous pouvez [activer les abonnés MSDN](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) -votre abonnement MSDN vous donne des crédits chaque mois que vous pouvez utiliser pour les services Azure payants.

Pour publier votre application web, cliquez sur le projet et sélectionnez **publier**.

![publication du site](publish-to-azure/_static/image1.png)

Sélectionnez les sites Web Microsoft Azure.

![Sélectionnez Azure](publish-to-azure/_static/image2.png)

Si vous n’êtes pas connecté Azure, fournissez vos informations d’identification de compte Azure. Ensuite, sélectionnez Nouveau pour créer une application web.

![nouveau site](publish-to-azure/_static/image3.png)

Créer un nom unique pour votre application web. Vous savez que le nom est unique si vous voyez une coche verte à droite du champ name. Sélectionnez une région pour votre application web. Sélectionnez **créer un nouveau serveur** pour la base de données et fournir un nom d’utilisateur et un mot de passe pour ce nouveau serveur de base de données. Lorsque vous avez terminé, cliquez sur **créer**.

![créer un site](publish-to-azure/_static/image4.png)

Les valeurs de connexion sont maintenant tous définis. Vous pouvez laisser ces valeurs inchangées.

![connection](publish-to-azure/_static/image5.png)

Cliquez sur **Suivant**.

Pour les paramètres, notez que deux connexions de base de données sont spécifiés - ApplicationDBContext et ContosoUniversityDataEntities. ApplicationDBContext correspond à la connexion pour les tables de compte d’utilisateur. Ces valeurs affichent uniquement les chaînes de connexion pour les bases de données. Cela ne signifie pas que ces bases de données seront publiées lorsque vous publiez votre site. Vous allez publier votre projet de base de données après avoir fini de l’application web de publication.

![](publish-to-azure/_static/image6.png)

Les points de suspension (...) en regard de la connexion de base de données affiche les détails de la chaîne de connexion. Cliquez sur le bouton de sélection en regard de ContosoUniversityDataEntities.

![](publish-to-azure/_static/image7.png)

Notez le nom du serveur de base de données et la base de données. Le nom du serveur est généré aléatoirement. Le nom de la base de données est simple le nom de votre site avec  **\_db** ajouté. Vous aurez besoin des noms ultérieurement lorsque vous publiez votre base de données.

Cliquez sur **OK** pour fermer la fenêtre de chaîne de connexion de base de données.

Dans la fenêtre de publier le site Web, cliquez sur **suivant** permet d’afficher l’aperçu.

![](publish-to-azure/_static/image8.png)

Vous pouvez cliquer sur Démarrer l’aperçu pour afficher la liste des fichiers à publier. Dans la mesure où il s’agit de la première fois que vous avez publié ce site, la liste est tous les fichiers appropriés dans le projet.

Cliquez sur **Publier**.

Le volet de résultats affiche les résultats de votre publication.

![](publish-to-azure/_static/image9.png)

Après la publication, le site est immedialely lancée dans un navigateur web. Votre site a été déployée et vous pouvez inscrire un nouvel utilisateur sur le site ; Toutefois, les tables dans le projet ContosoUniversityData n’ont pas encore été publiées. Si vous cliquez sur la liste des étudiants lien, vous recevrez une erreur.

## <a name="publish-database-to-sql-azure"></a>Publier la base de données vers SQL Azure

Avant de publier votre base de données, il se peut que vous devez vous assurer que votre ordinateur local peut se connecter au serveur de base de données. Le pare-feu pour votre serveur de base de données restreint qui machines peuvent se connecter à la base de données. Vous devez ajouter l’adresse IP de votre ordinateur aux adresses IP autorisées pour le pare-feu.

Connectez-vous à votre compte Azure via le portail Azure.

Sélectionnez votre nouvelle base de données et sélectionnez **gérer**.

![Gérer](publish-to-azure/_static/image10.png)

Vous devez configurer votre serveur de base de données pour autoriser les connexions à partir de votre ordinateur. Lorsque vous sélectionnez Gérer, vous êtes invité à ajouter l’adresse IP actuelle comme autorisées sur le serveur de base de données. Sélectionnez Oui.

![Ajouter une adresse ip](publish-to-azure/_static/image11.png)

Il est probable que l’adresse IP que vous avez ajouté à l’étape précédente n’est pas la seule adresse IP, que vous devez configurer pour les connexions. Vous pouvez tenter de se connecter à la base de données afin de déterminer si les connexions ont été correctement configurées. Fournir l’utilisateur et le mot de passe que vous avez créé précédemment.

![Connexion](publish-to-azure/_static/image12.png)

Si vous recevez un message d’erreur, vous devez ajouter une autre adresse IP. Cliquez sur le message d’erreur pour plus d’informations sur l’erreur. Dans les détails, vous verrez l’adresse IP que vous souhaitez ajouter. Notez cette adresse IP.

![non autorisé](publish-to-azure/_static/image13.png)

Fermez cette fenêtre de connexion et retourner au portail Azure.

Accédez au tableau de bord pour votre base de données. Cliquez sur **gérer les adresses IP autorisée**.

![gérer les adresses ip](publish-to-azure/_static/image14.png)

Vous devez à présent ajouter l’adresse IP à partir du message d’erreur. Modifier la plage d’adresses IP autorisées à inclure de celui du message d’erreur, ou ajouter cette adresse IP en tant qu’une entrée distincte.

![Ajouter une nouvelle adresse](publish-to-azure/_static/image15.png)

Enregistrez la modification des adresses IP autorisées.

Cliquez sur Gérer et essayez de vous connecter à nouveau à la base de données. Vous devrez peut-être attendre quelques minutes avant que les adresses IP autorisées sont correctement configurés pour le pare-feu. Lorsque vous pouvez vous connecter avec succès dans la base de données, vous avez défini votre connexion à la base de données.

Vous pouvez laisser cette fenêtre d’administration ouvert, car vous allez vérifier le résultat de votre déploiement de base de données peu de temps.

Revenir à votre projet de base de données. Cliquez sur le projet et sélectionnez **publier**.

![publier la base de données](publish-to-azure/_static/image16.png)

Dans la fenêtre de la base de données de publication, sélectionnez **modifier**.

![modifier](publish-to-azure/_static/image17.png)

Indiquez le nom du serveur de base de données et vos informations d’identification d’authentification pour le serveur. Après avoir entré les informations d’identification, sélectionnez la base de données que vous avez créé à partir de la liste des bases de données disponibles. Par défaut, Visual Studio définit le nom du champ de base de données sur le nom de votre projet qui ne peut pas être identique à la base de données que vous avez créé.

![](publish-to-azure/_static/image18.png)

Cliquez sur OK.

Vous souhaiterez probablement enregistrer ce profil, vous pouvez publier des mises à jour à l’avenir sans entrer à nouveau toutes les informations de connexion. Sélectionnez **créer profil**.

![enregistrer le profil](publish-to-azure/_static/image19.png)

Il crée un fichier dans votre projet nommé **ContosoUniversityData.publish.xml**. La prochaine fois que vous souhaitez publier la base de données vers Azure, il suffit de recharger ce profil.

Maintenant, cliquez sur **publier** pour créer la base de données sur Azure.

![publish](publish-to-azure/_static/image20.png)

Après l’exécution d’un certain temps, les publication des résultats sont affichés.

![résultats](publish-to-azure/_static/image21.png)

Maintenant, vous pouvez revenir au portail de gestion pour votre base de données. Actualiser la vue de conception et notez que les 3 tables avec données préremplies ont été déployés.

![nouvelles tables](publish-to-azure/_static/image22.png)

Vous êtes maintenant prêt à tester l’application web qui est déployée sur Azure. Accédez à l’application web sur Azure (par exemple, http://contosositeexample.azurewebsites.net/). Cliquez sur le lien pour la liste d’étudiants et vous devez voir l’affichage de l’index pour les étudiants.

![vue](publish-to-azure/_static/image23.png)

Parfois, la base de données et la connexion doivent un peu de temps pour être correctement configurés. Si vous recevez une erreur, attendez quelques minutes et réessayez.

## <a name="conclusion"></a>Conclusion

Cette série fourni un exemple simple de génération de code à partir d’une base de données existante qui permet aux utilisateurs de modifier, mettre à jour, créer et supprimer des données. Il utilisé ASP.NET MVC 5, Entity Framework et génération de modèles automatique ASP.NET pour créer le projet.

Pour obtenir un exemple d’introduction du développement Code First, consultez [mise en route avec ASP.NET MVC 5](../introduction/getting-started.md).

Pour obtenir un exemple plus avancé, consultez [création d’un modèle de données Entity Framework pour une application ASP.NET MVC est 4](../getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md). Notez que l’API DbContext que vous utilisez pour l’utilisation des données dans la première base de données est identique à l’API que vous utilisez pour travailler avec des données dans le Code First. Même si vous envisagez d’utiliser la première base de données, vous pouvez apprendre à gérer des scénarios plus complexes telles que la lecture et mise à jour des données associées, la gestion des conflits d’accès concurrentiel, et ainsi de suite à partir d’un didacticiel de Code First. La seule différence est dans la façon dont la base de données, classe de contexte et des classes d’entité sont créés.

>[!div class="step-by-step"]
[Précédent](enhancing-data-validation.md)
