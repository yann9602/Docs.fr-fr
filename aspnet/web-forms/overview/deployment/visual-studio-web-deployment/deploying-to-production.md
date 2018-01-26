---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-production
title: "Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement de Production | Documents Microsoft"
author: tdykstra
description: "Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, en utilisant des éléments..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/15/2013
ms.topic: article
ms.assetid: 416438a1-3b2f-4d27-bf53-6b76223c33bf
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-production
msc.type: authoredcontent
ms.openlocfilehash: abd3f3f78dd9a9e6394e2f61aa9bd692810ca875
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-production"></a>Déploiement de Web ASP.NET à l’aide de Visual Studio : déploiement en Production
====================
Par [Tom Dykstra](https://github.com/tdykstra)

[Télécharger le projet de démarrage](http://go.microsoft.com/fwlink/p/?LinkId=282627)

> Cette série de didacticiels vous montre comment déployer (publier) ASP.NET web application Azure App Service Web Apps ou un fournisseur d’hébergement tiers, à l’aide de Visual Studio 2012 ou Visual Studio 2010. Pour plus d’informations sur la série, consultez [le premier didacticiel de la série](introduction.md).


## <a name="overview"></a>Vue d'ensemble

Dans ce didacticiel, vous configurez un compte Microsoft Azure, créez des environnements intermédiaire et de production et déployez votre application de web ASP.NET pour la mise en transit et les environnements de production à l’aide de Visual Studio en un clic publier la fonctionnalité.

Si vous préférez, vous pouvez déployer sur un fournisseur d’hébergement tiers. La plupart des procédures décrites dans ce didacticiel est les mêmes pour un fournisseur d’hébergement ou Azure, sauf que chaque fournisseur possède sa propre interface utilisateur pour la gestion de compte et le site web. Vous pouvez trouver un fournisseur d’hébergement dans le [la galerie de fournisseurs](https://www.microsoft.com/web/hosting) sur le site web Microsoft.com.

Rappel : Si vous obtenez un message d’erreur, ou quelque chose ne fonctionne pas lorsque vous parcourez le didacticiel, veillez à consulter la page de résolution des problèmes dans cette série de didacticiels.

## <a name="get-a-microsoft-azure-account"></a>Obtenir un compte Microsoft Azure

Si vous n’avez pas déjà un compte Azure, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez [d’évaluation gratuite Azure](https://azure.microsoft.com/free/?WT.mc_id=A443DD604).

## <a name="create-a-staging-environment"></a>Créer un environnement intermédiaire

> [!NOTE]
> Depuis la rédaction de ce didacticiel, Azure App Service ajouté une nouvelle fonctionnalité pour automatiser une partie des processus de création d’environnements intermédiaire et de production. Consultez [configurer d’environnements intermédiaires pour applications web dans Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/).


Comme expliqué dans la [déployer le didacticiel de l’environnement de Test](deploying-to-iis.md), le meilleur environnement de test fiable est un site web au fournisseur d’hébergement qui a la même façon que le site web de production. Plusieurs fournisseurs d’hébergement, vous devez peser les avantages de ce par rapport à un coût, mais dans Azure, vous pouvez créer une application web libre supplémentaire que votre application intermédiaire. Vous avez également besoin d’une base de données, et les coûts supplémentaires pour que sur les dépenses de votre base de données de production sera aucun ou minimale. Dans Azure, vous payez pour la quantité de stockage de base de données que vous utilisez plutôt que pour chaque base de données, et la quantité de stockage supplémentaire, que vous allez utiliser intermédiaire sera minime.

Comme expliqué dans la [déployer le didacticiel de l’environnement de Test](deploying-to-iis.md), intermédiaire et de production que vous allez déployer les deux bases de données dans une base de données. Si vous souhaitez les conserver séparément, le processus serait identiques, sauf que vous devez créer une base de données supplémentaire pour chaque environnement et que vous sélectionnez la chaîne de destination correct pour chaque base de données lorsque vous créez le profil de publication.

Dans cette section du didacticiel, vous allez créer une application web et la base de données à utiliser pour l’environnement intermédiaire, et vous allez déployer dans l’environnement intermédiaire et tester il avant de créer et de déploiement dans l’environnement de production.

> [!NOTE]
> Les étapes suivantes montrent comment créer une application web dans Azure App Service à l’aide du portail de gestion Azure. Dans la dernière version du SDK Azure, vous pouvez également le faire sans quitter Visual Studio, à l’aide de l’Explorateur de serveurs. Dans Visual Studio 2013, vous pouvez également créer une application web directement à partir de la boîte de dialogue Publier. Pour plus d’informations, consultez [créer une application web ASP.NET dans Azure App Service.](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)


1. Dans le [portail de gestion Azure](https://manage.windowsazure.com/), cliquez sur **sites Web**, puis cliquez sur **nouveau**.
2. Cliquez sur **site Web**, puis cliquez sur **création personnalisée**.

    Le **nouveau site Web - création personnalisée** Assistant s’ouvre. Le **création personnalisée** Assistant vous permet de créer un site web et une base de données en même temps.
3. Dans le **créer un site Web** étape de l’Assistant, entrez une chaîne dans le **URL** zone à utiliser comme URL unique pour votre application de l’environnement de test. Par exemple, entrez ContosoUniversity-staging123 (y compris des nombres aléatoires à la fin pour le rendre unique dans le cas où ContosoUniversity-intermédiaire est effectuée).

    L’URL complète consiste à ce que vous entrez ici ainsi que le suffixe que vous voyez en regard de la zone de texte.
4. Dans le **région** liste déroulante, sélectionnez la région est le plus proche de vous.

    Ce paramètre spécifie quel centre de données votre application web s’exécutera dans.
5. Dans le **base de données** liste déroulante, sélectionnez **créer une base de données SQL**.
6. Dans le **nom de chaîne de connexion de base de données** zone, laissez la valeur par défaut, *DefaultConnection*.
7. Cliquez sur la flèche pointant vers la droite en bas de la zone.

    L’illustration suivante montre le **créer un site Web** boîte de dialogue avec des exemples de valeurs qu’il contient. L’URL et la région que vous avez entrées seront différentes.

    ![Créer l’étape de site Web](deploying-to-production/_static/image1.png)

    L’Assistant passe à le **spécifier les paramètres de la base de données** étape.
8. Dans le **nom** , entrez *ContosoUniversity* et un nombre aléatoire pour le rendre unique, par exemple *ContosoUniversity123*.
9. Dans le **Server** boîte, sélectionnez **nouveau serveur de base de données SQL Server**.
10. Entrez un nom d’administrateur et le mot de passe.

    Vous n’êtes pas entrer un nom existant et un mot de passe ici. Vous entrez un nouveau nom et mot de passe que vous définissez maintenant pour l’utiliser ultérieurement lorsque vous accédez à la base de données.
11. Dans le **région** , choisissez la même région que vous avez choisi pour l’application web.

    Le serveur web et le serveur de base de données dans la même région vous offre de meilleures performances et réduit les dépenses.
12. Cliquez sur la coche en bas de la case pour indiquer que vous avez terminé.

    L’illustration suivante montre le **spécifier les paramètres de la base de données** boîte de dialogue avec des exemples de valeurs qu’il contient. Les valeurs que vous avez entré peuvent être différents.

    ![Étape des paramètres de base de données du site Web de New - créer avec l’Assistant de base de données](deploying-to-production/_static/image2.png)

    Le portail de gestion renvoie à la page de sites Web et le **état** colonne indique que l’application web est créée. Après un certain temps (en général moins d’une minute), la **état** colonne indique que l’application web a été créée. Dans la barre de navigation à gauche, le nombre d’applications web que vous avez dans votre compte s’affiche en regard du **sites Web** icône et le nombre de bases de données s’affiche en regard du **bases de données SQL** icône.

    ![Page de Sites Web du portail de gestion, site web créé](deploying-to-production/_static/image3.png)

    Le nom de votre application web sera différent de l’exemple d’application dans l’illustration.

## <a name="deploy-the-application-to-staging"></a>Déployer l’application dans l’environnement intermédiaire

Maintenant que vous avez créé une application web et la base de données pour l’environnement intermédiaire, vous pouvez déployer le projet à ce dernier.

> [!NOTE]
> Ces instructions indiquent comment créer un profil de publication en téléchargeant un *.publishsettings* fichier, qui fonctionne non seulement pour Azure, mais également pour les fournisseurs d’hébergement tiers. La dernière version du SDK Azure vous permet également pour vous connecter directement à Azure à partir de Visual Studio, choisissez dans la liste des applications web que vous avez dans votre compte Azure. Dans Visual Studio 2013, vous connecter à Azure à partir de la **Web Publish** boîte de dialogue ou à partir de la **l’Explorateur de serveurs** fenêtre. Pour plus d’informations, consultez [créer une application web ASP.NET dans Azure App Service](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).


### <a name="download-the-publishsettings-file"></a>Téléchargez le fichier .publishsettings

1. Cliquez sur le nom de l’application web que vous venez de créer.

    ![Cliquez sur le site pour accéder au tableau de bord](deploying-to-production/_static/image4.png)
2. Sous **coup de œil rapide** dans les **tableau de bord** , cliquez sur **télécharger le profil de publication**.

    ![Profil de publication lien de téléchargement](deploying-to-production/_static/image5.png)

    Cette étape télécharge un fichier qui contient tous les paramètres dont vous avez besoin pour déployer une application à votre application web. Vous allez importer ce fichier dans Visual Studio, vous n’avez pas à entrer ces informations manuellement.
3. Enregistrer le *.publishsettings* fichier dans un dossier auquel vous pouvez accéder à partir de Visual Studio.

    ![l’enregistrement du fichier .publishsettings](deploying-to-production/_static/image6.png)

    > [!WARNING]
    > Sécurité - le *.publishsettings* fichier contient vos informations d’identification (non codées) utilisées pour gérer vos abonnements Azure et les services. La meilleure pratique de sécurité pour ce fichier est pour stocker temporairement en dehors de vos répertoires sources (par exemple dans le dossier bibliothèques\documents), puis de le supprimer une fois l’importation terminée. Un utilisateur malveillant qui accède à la *.publishsettings* fichier peut modifier, créer et supprimer vos services Azure.

### <a name="create-a-publish-profile"></a>Créer un profil de publication

1. Dans Visual Studio, cliquez sur le projet ContosoUniversity dans **l’Explorateur de solutions** et sélectionnez **publier** dans le menu contextuel.

    Le **publier le site Web** Assistant s’ouvre.
2. Cliquez sur le **profil** onglet.
3. Cliquez sur **Importer**.
4. Accédez à la *.publishsettings* fichier que vous avez téléchargé précédemment, puis cliquez sur **ouvrir**.

    ![Boîte de dialogue Importer les paramètres de publication](deploying-to-production/_static/image7.png)
5. Dans le **connexion** , cliquez sur **valider la connexion** pour vous assurer que les paramètres sont corrects.

    Lorsque la connexion a été validée, une coche verte s’affiche en regard du **valider la connexion** bouton.

    Certains fournisseurs d’hébergement, lorsque vous cliquez sur **valider la connexion**, vous pouvez voir un **erreur de certificat** boîte de dialogue. Si vous le faites, vérifiez que le nom du serveur est ce que vous attendez. Si le nom du serveur est correct, sélectionnez **enregistrer ce certificat pour les futures sessions de Visual Studio** et cliquez sur **accepter**. (Cette erreur signifie que le fournisseur d’hébergement a choisi pour éviter de devoir acheter un certificat SSL pour l’URL que vous effectuez le déploiement. Si vous souhaitez établir une connexion sécurisée à l’aide d’un certificat valide, contactez votre fournisseur d’hébergement.)
6. Cliquez sur **Suivant**.

    ![icône de connexion réussie et le bouton suivant dans l’onglet Connexion](deploying-to-production/_static/image8.png)
7. Dans le **paramètres** onglet, développez **Options de publication du fichier**, puis sélectionnez **exclure des fichiers de l’application\_dossier de données**.

    Pour plus d’informations sur les autres options sous **Options de publication du fichier**, consultez la [déploiement vers IIS](deploying-to-iis.md) didacticiel. La capture d’écran qui s’affiche le résultat de cette étape et les étapes de configuration de base de données suivante est à la fin des étapes de configuration de base de données.
8. Sous **DefaultConnection** dans les **bases de données** section, configurer le déploiement de base de données de la base de données d’appartenance.
9. 1. Sélectionnez **base de données de mise à jour**.

        Le **chaîne de connexion à distance** zone immédiatement sous **DefaultConnection** est remplie avec la chaîne de connexion à partir du fichier .publishsettings. La chaîne de connexion inclut des informations d’identification SQL Server, qui sont stockées en texte brut dans le *.pubxml* fichier. Si vous préférez ne pas définitivement y stocker, vous pouvez les supprimer à partir du profil de publication après avoir déployé la base de données et les stocker dans Azure. Pour plus d’informations, consultez [comment protéger votre base de données ASP.NET des chaînes de connexion lors du déploiement vers Azure à partir de la Source](http://www.hanselman.com/blog/HowToKeepYourASPNETDatabaseConnectionStringsSecureWhenDeployingToAzureFromSource.aspx) sur le blog de Scott Hanselman.
    2. Cliquez sur **configurer des mises à jour de la base de données**.
    3. Dans le **configurer les mises à jour de base de données** boîte de dialogue, cliquez sur **ajouter un Script SQL**.
    4. Dans le **ajouter un Script SQL** , accédez à la *aspnet-données-prod.sql* script que vous avez enregistré précédemment dans le dossier de solution, puis cliquez sur **ouvrir**.
    5. Fermer le **configurer les mises à jour de base de données** boîte de dialogue.
10. Sous **SchoolContext** dans les **bases de données** section, sélectionnez **exécuter fonctionnalité Migrations Code First (s’exécute sur le démarrage de l’application)**.

    Visual Studio affiche **exécuter des Migrations Code First** au lieu de **mise à jour de la base de données** pour `DbContext` classes. Si vous souhaitez utiliser le fournisseur dbDacFx au lieu de Migrations pour déployer une base de données auxquels vous accédez à l’aide un `DbContext` de classe, consultez [comment déployer une base de données Code First sans Migrations ?](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) dans le Forum aux questions de déploiement Web pour Visual Studio et ASP.NET sur MSDN.

    Le **paramètres** onglet ressemble maintenant à l’exemple suivant :

    ![Onglet Paramètres de mise en lots](deploying-to-production/_static/image9.png)
11. Procédez comme suit pour enregistrer le profil et le renommer *intermédiaire*:

    1. Cliquez sur le **profil** onglet, puis cliquez sur **gérer les profils**.
    2. L’importation créé deux nouveaux profils, un pour FTP et un pour Web Deploy. Vous avez configuré le profil de déploiement Web : renommer ce profil pour *intermédiaire*.

        ![Renommer un profil dans l’environnement intermédiaire](deploying-to-production/_static/image10.png)
    3. Fermer le **modifier les profils de publication Web** boîte de dialogue.
    4. Fermer le **publier le site Web** Assistant.

### <a name="configure-a-publish-profile-transform-for-the-environment-indicator"></a>Configurer une transformation de profil de publication pour l’indicateur d’environnement

> [!NOTE]
> Cette section montre comment configurer une transformation Web.config pour l’indicateur d’environnement. Étant donné que l’indicateur est dans le `<appSettings>` élément, que vous avez une solution de rechange pour la spécification de la transformation lorsque vous effectuez un déploiement vers Azure App Service. Pour plus d’informations, consultez [paramètres spécifiant le fichier Web.config dans Azure](web-config-transformations.md#watransforms).


1. Dans **l’Explorateur de solutions**, développez **propriétés**, puis développez **PublishProfiles**.
2. Avec le bouton droit *Staging.pubxml*, puis cliquez sur **ajouter une transformation de Config**.

    ![Ajouter une transformation de la configuration pour l’environnement intermédiaire](deploying-to-production/_static/image11.png)

    Visual Studio crée le *Web.Staging.config* fichier de transformation et l’ouvre.
3. Dans le *Web.Staging.config* de transformer le fichier, insérez le code suivant immédiatement après l’ouverture `configuration` balise.

    [!code-xml[Main](deploying-to-production/samples/sample1.xml)]

    Lorsque vous utilisez le profil de publication mise en lots, cette transformation définit l’indicateur d’environnement pour « Production ». Dans l’application web déployée, vous ne voyez aucun suffixe tel que « (Dev) » ou « (Test) » après le titre H1 de l’université « Contoso ».
4. Avec le bouton droit le *Web.Staging.config* de fichier et cliquez sur **aperçu transformer** pour vous assurer que la transformation que vous avez codé génère les modifications attendues.

    Le **Web.config aperçu** fenêtre affiche le résultat de l’application à la fois le *Web.Release.config* transforme et *Web.Staging.config* transforme.

### <a name="prevent-public-use-of-the-test-app"></a>Empêcher une utilisation publique de l’application de test

Une considération importante pour l’application intermédiaire est qu’est publiée sur Internet, mais vous ne souhaitez pas que le public à utiliser. Pour réduire la probabilité de trouver et l’utiliser de personnes, vous pouvez utiliser un ou plusieurs des méthodes suivantes :

- Définir des règles de pare-feu qui autorisent l’accès à l’application intermédiaire uniquement à partir d’adresses IP que vous utilisez pour tester la mise en lots.
- Utiliser une URL masquée qui serait impossible à deviner.
- Créer un *robots.txt* fichier pour vous assurer que les moteurs de recherche pas analysera les liens de rapport et d’application de test à ce dernier dans les résultats de la recherche.

La première de ces méthodes est la plus efficace, mais n’est pas couverte dans ce didacticiel, car cela nécessiterait que vous déployez sur un Service de Cloud Azure au lieu du Service d’applications Azure. Pour plus d’informations sur les Services de cloud computing et les restrictions IP dans Azure, consultez [de calcul hébergeant Options fournies par Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) et [bloc des adresses IP spécifiques d’accéder à un rôle Web](https://msdn.microsoft.com/library/windowsazure/jj154098.aspx). Si vous déployez sur un fournisseur d’hébergement tiers, contactez le fournisseur pour savoir comment implémenter des restrictions d’adresse IP.

Pour ce didacticiel, vous allez créer un *robots.txt* fichier.

1. Dans **l’Explorateur de solutions**, cliquez sur le projet ContosoUniversity et cliquez sur **ajouter un nouvel élément**.
2. Créer un nouveau **fichier texte** nommé *robots.txt*et placer le texte suivant :

    [!code-console[Main](deploying-to-production/samples/sample2.cmd)]

    Le `User-agent` ligne indique les règles dans le fichier s’appliquent à tous les web robots d’indexation (automates), les moteurs de recherche et la `Disallow` ligne spécifie qu’aucune page sur le site ne doit être analysé.

    Vous ne souhaitez pas que les moteurs de recherche pour le catalogue de votre application de production, vous devez exclure ce fichier de déploiement de production. Pour faire, vous allez configurer un paramètre dans la Production de profil de publication lors de sa création.

### <a name="deploy-to-staging"></a>Déployer dans l’environnement intermédiaire

1. Ouvrez le **publier le site Web** Assistant en cliquant sur le projet de Contoso University en cliquant sur **publier**.
2. Assurez-vous que le **intermédiaire** profil est sélectionné.
3. Cliquez sur **Publier**.

    Le **sortie** fenêtre affiche les actions de déploiement ont été effectuées et signale la réussite du déploiement. Le navigateur par défaut s’ouvre automatiquement à l’URL de l’application web déployée.

## <a name="test-in-the-staging-environment"></a>Tester dans l’environnement intermédiaire

Notez que l’indicateur d’environnement est absent (il n’existe aucune « (Test) » ou « (Dev) » après le titre H1, ce qui indique que le *Web.config* réussie de la transformation de l’indicateur d’environnement.

![Mise en attente de la page d’accueil](deploying-to-production/_static/image12.png)

Exécutez le **étudiants** page pour vérifier ne qu’aucun élève la base de données déployée.

Exécutez le **instructeurs** page pour vérifier que le premier Code amorcée la base de données de formateur :

Sélectionnez **ajouter des étudiants** à partir de la **étudiants** menu, ajouter un étudiant et afficher le nouvel étudiant dans la **étudiants** page pour vérifier que vous pouvez écrire avec succès à la base de données .

À partir de la **cours** , cliquez sur **mise à jour des crédits**. Le **crédits de mise à jour** page nécessite des autorisations d’administrateur, afin que la **connexion** page s’affiche. Entrez les informations d’identification du compte administrateur que vous avez créé plus tôt (« admin » et « prodpwd »). Le **mise à jour des crédits** page s’affiche, qui vérifie que le compte d’administrateur que vous avez créé dans le didacticiel précédent a été correctement déployé sur l’environnement de test.

Demander une URL non valide pour provoquer une erreur que ELMAH suit et ensuite demander le rapport d’erreurs ELMAH. Si vous déployez sur un fournisseur d’hébergement tiers, vous constaterez probablement que le rapport est vide pour la même raison elle était vide dans le didacticiel précédent. Vous devez utiliser des outils de gestion de compte du fournisseur d’hébergement pour configurer les autorisations de dossier pour activer ELMAH à écrire dans le dossier du journal.

L’application que vous avez créé est en cours d’exécution dans le cloud dans une application web qui s’apparente à ce que vous allez utiliser pour la production. Étant donné que tout fonctionne correctement, l’étape suivante consiste à déployer en production.

## <a name="deploy-to-production"></a>Déployer en production

Le processus de création d’une application web de production et de déploiement en production est le même que pour la mise en lots, à ceci près que vous devez exclure le *robots.txt* du déploiement. Pour ce faire, vous allez modifier le fichier de profil de publication.

### <a name="create-the-production-environment-and-the-production-publish-profile"></a>Créer l’environnement de production et de profil de publication de la production

1. Créer l’application web de production et de la base de données dans Azure, en suivant la même procédure que celle utilisée pour la mise en lots.

    Lorsque vous créez la base de données, vous pouvez choisir de placer sur le même serveur que vous avez créé précédemment, ou créer un nouveau serveur.
2. Téléchargez le *.publishsettings* fichier.
3. Créer le profil de publication en important la production *.publishsettings* fichier, en suivant la même procédure que celle utilisée pour la mise en lots.

    N’oubliez pas de configurer le script de déploiement de données sous **DefaultConnection** dans les **bases de données** section de la **paramètres** onglet.
4. Renommer le profil de publication pour *Production*.
5. Configurer une transformation de profil de publication pour l’indicateur d’environnement, en suivant la même procédure que vous avez utilisé pour l’environnement intermédiaire...

### <a name="edit-the-pubxml-file-to-exclude-robotstxt"></a>Modifiez le fichier .pubxml pour exclure robots.txt

Les fichiers sont nommés de profil de publication &lt;profilename&gt;*.pubxml* et se trouvent dans le *PublishProfiles* dossier. Le *PublishProfiles* dossier se trouve sous le *propriétés* dossier dans une application web c# de projet, sous la *mon projet* dossier dans un projet d’application web Visual Basic, ou sous le *Application\_données* dossier dans un projet d’application web. Chaque *.pubxml* fichier contient des paramètres qui s’appliquent à un profil de publication. Les valeurs que vous entrez dans l’Assistant de publication Web sont stockés dans ces fichiers, et vous pouvez les modifier pour créer ou modifier les paramètres qui ne sont pas rendus disponibles dans l’interface utilisateur de Visual Studio.

Par défaut, *.pubxml* fichiers sont inclus dans le projet lorsque vous créez un profil de publication, mais vous pouvez les exclure du projet et Visual Studio sera toujours les utiliser. Visual Studio recherche les *PublishProfiles* dossier *.pubxml* fichiers, quelle que soit la si elles sont incluses dans le projet.

Pour chaque *.pubxml* fichier est un *. pubxml.user* fichier. Le *. pubxml.user* fichier contient le mot de passe chiffré si vous avez sélectionné le **enregistrer le mot de passe** option et, par défaut, il est exclu du projet.

A *.pubxml* fichier contient les paramètres qui se rapportent à un profil de publication spécifique. Si vous souhaitez configurer les paramètres qui s’appliquent à tous les profils, vous pouvez créer un *. wpp.targets* fichier. Le processus de génération importe ces fichiers dans le *.csproj* ou *.vbproj* fichier projet, pour que la plupart des paramètres que vous pouvez configurer dans le fichier projet peut être configuré dans ces fichiers. Pour plus d’informations sur *.pubxml* fichiers et *. wpp.targets* de fichiers, consultez [Comment : modifier les paramètres de déploiement dans les fichiers de profil de publication (.pubxml) et le. wpp.targets fichier dans Visual Studio Projets Web](https://msdn.microsoft.com/library/ff398069.aspx).

1. Dans **l’Explorateur de solutions**, développez **propriétés** et développez **PublishProfiles**.
2. Avec le bouton droit *Production.pubxml* et cliquez sur **ouvrir**.

    ![Ouvrez le fichier .pubxml](deploying-to-production/_static/image13.png)
3. Avec le bouton droit *Production.pubxml* et cliquez sur **ouvrir**.
4. Ajoutez les lignes suivantes juste avant la fermeture `PropertyGroup` élément :

    [!code-xml[Main](deploying-to-production/samples/sample3.xml)]

    Le fichier .pubxml ressemble maintenant à l’exemple suivant :

    [!code-xml[Main](deploying-to-production/samples/sample4.xml?highlight=18-20)]

    Pour plus d’informations sur la façon d’exclure des fichiers et dossiers, consultez [exclure certains fichiers ou dossiers de déploiement ?](https://msdn.microsoft.com/library/ee942158.aspx#can_i_exclude_specific_files_or_folders_from_deployment) dans les **Forum aux questions de déploiement Web pour Visual Studio et ASP.NET** sur MSDN.

### <a name="deploy-to-production"></a>Déployer en production

1. Ouvrir le **publier le site Web** Assistant vous assurer que le **Production** le profil de publication est sélectionnée, puis cliquez sur **démarrer la version préliminaire** sur la **aperçu**onglet pour vérifier que le *robots.txt* fichier ne sera pas copié vers l’application de production.

    ![Aperçu des fichiers à être publié en production](deploying-to-production/_static/image14.png)

    Passez en revue la liste des fichiers à copier. Vous verrez que tous les *.cs* , y compris *. aspx.cs*, *. aspx.designer.cs*, *Master.cs*, et  *Master.Designer.cs* fichiers sont omis. Tout ce code a été compilée dans le *ContosoUniversity.dll* et *ContosUniversity.pdb* les fichiers que vous trouverez dans le *bin* dossier. Étant donné qu’uniquement le *.dll* est nécessaire pour exécuter l’application et vous avez spécifié précédemment que seuls les fichiers nécessaires à l’exécution de l’application doivent être déployées, non *.cs* fichiers ont été copiés vers la destination environnement. Le *obj* dossier et le *ContosoUniversity.csproj* et *. csproj.user* fichiers sont omis par la même raison.

    Cliquez sur **publier** à déployer dans l’environnement de production.
2. Test en production, en suivant la même procédure que vous avez utilisé pour l’environnement intermédiaire.

    Tout est identique à la mise en lots, à l’exception de l’URL et l’absence de la *robots.txt* fichier.

## <a name="summary"></a>Récapitulatif

Vous avez désormais déployé et testé votre application web et il est publiquement disponible sur Internet.

![Production de la page d’accueil](deploying-to-production/_static/image15.png)

Dans l’étape suivante du didacticiel, vous allez mettre à jour le code de l’application et déploiement de la modification pour les environnements de test, intermédiaire et de production.

> [!NOTE]
> Pendant que votre application est en cours d’utilisation dans l’environnement de production vous être devez implémenter un plan de récupération. Autrement dit, vous devez être sauvegarder régulièrement vos bases de données à partir de l’application de production vers un emplacement de stockage sécurisé, et vous devez conserver plusieurs générations de ces sauvegardes. Lorsque vous mettez à jour la base de données, vous devez vous une copie de sauvegarde à partir d’immédiatement avant la modification. Ensuite, si vous commettez une erreur et ne Découvrez qu’une fois que vous l’avez déployée en production, vous serez toujours en mesure de récupérer la base de données à l’état, qu'il se trouvait avant qu’il a été endommagé. Pour plus d’informations, consultez [sauvegarde de base de données SQL Azure et de restauration](https://msdn.microsoft.com/library/windowsazure/jj650016.aspx).


> [!NOTE]
> Dans ce didacticiel, le serveur SQL Server Édition que vous déployez est la base de données SQL Azure. Pendant le processus de déploiement est similaire aux autres éditions de SQL Server, une application de production réel peut nécessiter un code spécial pour la base de données SQL Azure, dans certains scénarios. Pour plus d’informations, consultez [utilisation de base de données SQL Azure](../../../../whitepapers/aspnet-data-access-content-map.md#ssdb) et [choix entre SQL Server et la base de données SQL Azure](../../../../whitepapers/aspnet-data-access-content-map.md#ssdbchoosing).

>[!div class="step-by-step"]
[Précédent](setting-folder-permissions.md)
[Suivant](deploying-a-code-update.md)
