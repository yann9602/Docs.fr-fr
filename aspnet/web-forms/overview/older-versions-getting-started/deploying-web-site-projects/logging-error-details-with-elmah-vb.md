---
uid: web-forms/overview/older-versions-getting-started/deploying-web-site-projects/logging-error-details-with-elmah-vb
title: "Journalisation des détails de l’erreur avec ELMAH (VB) | Documents Microsoft"
author: rick-anderson
description: "Erreur de journalisation des Modules et gestionnaires (ELMAH) offre une autre approche pour la journalisation des erreurs d’exécution dans un environnement de production. ELMAH une erreur s’est libre et open source..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/09/2009
ms.topic: article
ms.assetid: a5f0439f-18b2-4c89-96ab-75b02c616f46
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/deploying-web-site-projects/logging-error-details-with-elmah-vb
msc.type: authoredcontent
ms.openlocfilehash: d7082808d4aeb21767524c1e687dd688324d4d46
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="logging-error-details-with-elmah-vb"></a>Journalisation des détails de l’erreur avec ELMAH (VB)
====================
par [Scott Mitchell](https://twitter.com/ScottOnWriting)

[Télécharger le Code](http://download.microsoft.com/download/1/0/C/10CC829F-A808-4302-97D3-59989B8F9C01/ASPNET_Hosting_Tutorial_14_VB.zip) ou [télécharger le PDF](http://download.microsoft.com/download/5/C/5/5C57DB8C-5DEA-4B3A-92CA-4405544D313B/aspnet_tutorial14_ELMAH_vb.pdf)

> Erreur de journalisation des Modules et gestionnaires (ELMAH) offre une autre approche pour la journalisation des erreurs d’exécution dans un environnement de production. ELMAH est une bibliothèque de journalisation erreur libre et open source qui inclut des fonctionnalités telles que filtrer les erreurs et d’afficher le journal des erreurs à partir d’une page web, sous la forme d’un flux RSS, ni à le télécharger sous forme de fichier délimité par des virgules. Ce didacticiel vous guide tout au téléchargement et de configuration ELMAH.


## <a name="introduction"></a>Introduction

Le [didacticiel précédent](logging-error-details-with-asp-net-health-monitoring-vb.md) examiné ASP. Contrôle d’intégrité de NET système, qui offre un hors de la bibliothèque pour l’enregistrement d’un large éventail d’événements Web de surveillance. De nombreux développeurs utilisent pour se connecter et envoyer par courrier électronique les détails d’exceptions non gérées d’analyse du fonctionnement. Toutefois, il existe quelques points faibles avec ce système. Tout d’abord est l’absence de tout type d’interface utilisateur pour afficher des informations sur les événements enregistrés. Si vous souhaitez afficher un récapitulatif des 10 dernières erreurs ou afficher les détails d’une erreur s’est produite la semaine dernière, vous devez soit réserver la base de données, une recherche dans votre boîte de réception ou créer une page web qui affiche des informations à partir de la `aspnet_WebEvent_Events` table.

Un autre point de faibles tourne autour de la complexité de l’analyse de l’intégrité. Étant donné que le contrôle d’intégrité peut servir à enregistrer une multitude de différents événements, et comme il existe diverses options pour indiquer quand et comment les événements sont enregistrés, le configurer correctement le contrôle d’intégrité système de surveillance peut être une tâche onéreuse. Enfin, il existe des problèmes de compatibilité. Étant donné que le contrôle d’intégrité a été ajouté tout d’abord le .NET Framework version 2.0, il n’est pas disponible pour les applications web plus anciens créées à l’aide d’ASP.NET version 1.x. En outre, le `SqlWebEventProvider` (classe), que nous avons utilisée dans les détails des erreurs de journaux à une base de données du didacticiel précédent, ne fonctionne qu’avec les bases de données Microsoft SQL Server. Vous devez créer une classe de fournisseur de journal personnalisé si vous avez besoin consigner les erreurs dans un magasin de données différent, tel qu’un fichier XML ou une base de données Oracle.

Une alternative à l’intégrité de système de surveillance est erreur journalisation des Modules et gestionnaires (ELMAH), un système de journalisation des erreurs libre et open source créé par [Atif Aziz](http://www.raboof.com/). La différence la plus notable entre les deux systèmes est la possibilité de ELAMH pour afficher la liste des erreurs et les détails d’une erreur spécifique à partir d’une page web et comme un flux RSS. ELMAH est plus facile à configurer que le contrôle d’intégrité, car elle enregistre uniquement les erreurs. En outre, ELMAH prend en charge ASP.NET 1.x, les applications ASP.NET 2.0 et ASP.NET 3.5 et est fourni avec une gamme de fournisseurs de source de journal.

Ce didacticiel décrit les étapes d’ajout d’ELMAH à une application ASP.NET. C’est parti !

> [!NOTE]
> Le contrôle d’intégrité système de surveillance et ELMAH ont leurs propres jeux de leurs avantages et inconvénients. Je vous encourage à essayer les deux systèmes et de décider quelles une la mieux adaptée à vos besoins.


## <a name="adding-elmah-to-an-aspnet-web-application"></a>ELMAH ajout à une Application Web ASP.NET

Intégration ELMAH dans une application ASP.NET nouvelle ou existante est un processus simple et direct qui prend moins de cinq minutes. En bref, il implique quatre étapes simples :

1. Téléchargez ELMAH et ajoutez le `Elmah.dll` assembly à votre application web,
2. Inscrire de ELMAH Modules HTTP et le Gestionnaire de `Web.config`,
3. Spécifiez les options de configuration de ELMAH, et
4. Créer l’infrastructure de source de journal des erreurs, si nécessaire.

Examinons chacune de ces quatre étapes, à la fois.

### <a name="step-1-downloading-the-elmah-project-files-and-addingelmahdllto-your-web-application"></a>Étape 1 : Télécharger les fichiers de projet ELMAH et l’ajout de`Elmah.dll`à votre Application Web

ELMAH 1.0 bêta 3 (Build 10617), la version la plus récente au moment de l’écriture, est inclus dans le téléchargement disponible avec ce didacticiel. Vous pouvez également contacter le [site Web ELMAH](https://code.google.com/p/elmah/) pour obtenir la version la plus récente ou pour télécharger le code source. Extraire le téléchargement ELMAH dans un dossier sur votre bureau et recherchez le fichier d’assembly ELMAH (`Elmah.dll`).

> [!NOTE]
> Le `Elmah.dll` fichier se trouve dans le téléchargement `Bin` dossier qui contient des sous-dossiers pour les différentes versions du .NET Framework et pour les versions Release et Debug. Utilisez la version Release pour la version appropriée de .NET framework. Par exemple, si vous générez une application de web ASP.NET 3.5, copiez la `Elmah.dll` de fichiers à partir de la `Bin\net-3.5\Release` dossier.


Ensuite, ouvrez Visual Studio et ajoutez l’assembly à votre projet en cliquant sur le nom du site Web dans l’Explorateur de solutions et en choisissant l’option Ajouter une référence dans le menu contextuel. Cela affiche la boîte de dialogue Ajouter une référence. Accédez à l’onglet Parcourir, sélectionnez le `Elmah.dll` fichier. Cette action ajoute le `Elmah.dll` fichier à l’application web `Bin` dossier.

> [!NOTE]
> Le type de projet d’Application Web (WAP) n’affiche pas les `Bin` dossier dans l’Explorateur de solutions. Au lieu de cela, il répertorie ces éléments dans le dossier Références.


Le `Elmah.dll` assembly inclut les classes utilisées par le système ELMAH. Ces classes se répartissent en trois catégories :

- **Les Modules HTTP** -un HTTP Module est une classe qui définit des gestionnaires d’événements pour `HttpApplication` événements, tels que le `Error` événement. ELMAH comprend plusieurs Modules HTTP, les plus pertinentes trois en cours : 

    - `ErrorLogModule`-enregistre les exceptions non gérées dans une source de journal.
    - `ErrorMailModule`-envoie les détails d’une exception non gérée dans un message électronique.
    - `ErrorFilterModule`-applique spécifié par le développeur de filtres pour déterminer quelles exceptions sont enregistrées et que celles sont ignorés.
- **Les gestionnaires HTTP** -un gestionnaire HTTP est une classe qui est chargée de générer le balisage pour un type particulier de demande. ELMAH inclut des gestionnaires HTTP qui restituent les détails de l’erreur comme une page web, un flux RSS ou un fichier délimité par des virgules (CSV).
- **Sources de journaux d’erreur** - emploi ELMAH peut enregistrer les erreurs dans la mémoire, à une base de données Microsoft SQL Server pour une base de données Microsoft Access, à une base de données Oracle dans un fichier XML, à une base de données SQLite ou à une base de données de la base de données de Vista. Comme l’intégrité du système de surveillance, architecture de ELMAH a été généré à l’aide du modèle de fournisseur, ce qui signifie que vous pouvez créer et intégrer en toute transparence vos propres fournisseurs de source de journal personnalisé, si nécessaire.

### <a name="step-2-registering-elmahs-http-module-and-handler"></a>Étape 2 : Inscription Module HTTP et le Gestionnaire de ELMAH

Alors que le `Elmah.dll` fichier contient les Modules HTTP et Gestionnaire nécessaires pour connecter automatiquement les exceptions non gérées et pour afficher les détails de l’erreur à partir d’une page web, ceux-ci doivent être inscrits explicitement dans la configuration de l’application web. Le `ErrorLogModule` HTTP Module, une fois inscrit, s’abonne à la `HttpApplication`de `Error` événements. Cet événement est déclenché chaque fois que le `ErrorLogModule` enregistre les détails de l’exception à une source de journal spécifié. Nous verrons comment définir le fournisseur de source de journal dans la section suivante, « Configuration ELMAH ». Le `ErrorLogPageFactory` fabrique de gestionnaire HTTP est chargé de générer le balisage lorsque vous affichez le journal des erreurs à partir d’une page web.

La syntaxe spécifique pour l’enregistrement des Modules HTTP et des gestionnaires varie selon le serveur web qui est en cours du site. Pour le serveur de développement ASP.NET et de Microsoft IIS version 6.0 et versions antérieure, les Modules HTTP et les gestionnaires sont enregistrés dans le `<httpModules>` et `<httpHandlers>` sections qui s’affichent dans le `<system.web>` élément. Si vous utilisez IIS 7.0, ils doivent être inscrits dans le `<system.webServer>` l’élément `<modules>` et `<handlers>` sections. Heureusement, vous pouvez définir les Modules HTTP et les gestionnaires dans *les deux* place quel que soit le serveur web utilisé. Cette option est celle qui est plus portable car elle permet de la même configuration à utiliser dans les environnements de développement et de production, quelle que soit le serveur web utilisé.

Commencez par inscrire la `ErrorLogModule` HTTP Module et le `ErrorLogPageFactory` gestionnaire HTTP dans le `<httpModules>` et `<httpHandlers>` section dans `<system.web>`. Si votre configuration déjà définit ces deux éléments la puis simplement inclure le `<add>` , élément pour le HTTP Module et le Gestionnaire de ELMAH.

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample1.xml)]

Ensuite, enregistrez le HTTP Module et le gestionnaire dans ELMAH la `<system.webServer>` élément. Comme précédemment, si cet élément n’est pas déjà présent dans votre configuration puis l’ajouter.

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample2.xml)]

Par défaut, IIS 7 se plaint si les Modules HTTP et les gestionnaires sont enregistrés dans le `<system.web>` section. Le `validateIntegratedModeConfiguration` d’attribut dans le `<validation>` IIS 7 pour supprimer ces messages d’erreur indique à l’élément.

Notez que la syntaxe pour l’inscription du `ErrorLogPageFactory` gestionnaire HTTP inclut un `path` attribut, qui est définie sur `elmah.axd`. Cet attribut informe l’application web que s’une demande arrive pour une page nommée `elmah.axd` ensuite la demande doit être traitée par le `ErrorLogPageFactory` gestionnaire HTTP. Nous verrons le `ErrorLogPageFactory` gestionnaire HTTP en action, plus loin dans ce didacticiel.

### <a name="step-3-configuring-elmah"></a>Étape 3 : Configuration ELMAH

ELMAH recherche ses options de configuration dans du site Web `Web.config` fichier dans une section de configuration personnalisée nommée `<elmah>`. Pour pouvoir utiliser une section personnalisée dans `Web.config` il doit d’abord être définie dans le `<configSections>` élément. Ouvrez le `Web.config` et ajoutez le balisage suivant à la `<configSections>`:

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample3.xml)]

> [!NOTE]
> Si vous configurez ELMAH pour une application ASP.NET 1.x, supprimez le `requirePermission="false"` attribut à partir de la `<section>` éléments ci-dessus.


La syntaxe ci-dessus inscrit personnalisé `<elmah>` section et ses sous-sections : `<security>`, `<errorLog>`, `<errorMail>`, et `<errorFilter>`.

Ensuite, ajoutez le `<elmah>` section `Web.config`. Cette section doit apparaître au même niveau que le `<system.web>` élément. À l’intérieur de la `<elmah>` section Ajouter la `<security>` et `<errorLog>` sections comme suit :

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample4.xml)]

Le `<security>` section `allowRemoteAccess` attribut indique si l’accès à distance est autorisé. Si cette valeur est définie sur 0, puis la page web du journal des erreurs ne peut être affichée localement. Si cet attribut est défini sur 1 la page web du journal des erreurs est activé pour les visiteurs locales et distantes. Pour l’instant, nous allons désactiver la page web du journal des erreurs pour les visiteurs à distance. Nous allons autoriser l’accès distant ultérieurement une fois que nous avons la possibilité d’évoquer les problèmes de sécurité de cette opération.

Le `<errorLog>` section définit la source de journal d’erreur, qui détermine où les détails de l’erreur sont enregistrés ; elle est similaire à la `<providers>` section dans le contrôle d’intégrité système de surveillance. La syntaxe ci-dessus spécifie la `SqlErrorLog` classe en tant que source de journal l’erreur, les journaux d’erreurs à une base de données Microsoft SQL Server spécifiée par la `connectionStringName` valeur d’attribut.

> [!NOTE]
> ELMAH est fourni avec les modules fournisseurs d’informations d’erreur qui peuvent être utilisés pour enregistrer les erreurs dans un fichier XML, une base de données Microsoft Access, une base de données Oracle et autres banques de données. Consultez l’exemple `Web.config` fichier qui est inclus avec le téléchargement ELMAH pour plus d’informations sur l’utilisation de ces fournisseurs de journaux d’erreur secondaire.


### <a name="step-4-creating-the-error-log-source-infrastructure"></a>Étape 4 : Création de l’Infrastructure de Source de journal des erreurs

De ELMAH `SqlErrorLog` fournisseur enregistre les détails de l’erreur dans une base de données Microsoft SQL Server spécifié. Le `SqlErrorLog` fournisseur s’attend à cette base de données d’une table nommée `ELMAH_Error` et trois procédures stockées : `ELMAH_GetErrorsXml`, `ELMAH_GetErrorXml`, et `ELMAH_LogError`. Le téléchargement ELMAH inclut un fichier nommé `SQLServer.sql` dans le `db` dossier qui contient le T-SQL pour la création de cette table et ces procédures. Vous devez exécuter ces instructions dans votre base de données à utiliser le `SqlErrorLog` fournisseur.

**Les figures 1** et **2** afficher l’Explorateur de base de données dans Visual Studio après les objets de base de données requis par le `SqlErrorLog` fournisseur ont été ajoutés.

[![](logging-error-details-with-elmah-vb/_static/image2.png)](logging-error-details-with-elmah-vb/_static/image1.png)

**Figure 1**: le `SqlErrorLog` fournisseur consigne les erreurs pour le `ELMAH_Error` Table

[![](logging-error-details-with-elmah-vb/_static/image4.png)](logging-error-details-with-elmah-vb/_static/image3.png)

**Figure 2**: le `SqlErrorLog` fournisseur utilise trois procédures stockées

## <a name="elmah-in-action"></a>ELMAH en Action

À ce stade, nous avons ajouté ELMAH à l’application web, inscrit le `ErrorLogModule` HTTP Module et le `ErrorLogPageFactory` gestionnaire HTTP spécifié les options de configuration de ELMAH dans `Web.config`et ajouté les objets de base de données requis pour la `SqlErrorLog` module fournisseur d’erreur. Nous sommes prêts à voir ELMAH en action ! Visitez le site Web critiques, vous accédez à une page qui génère une erreur d’exécution, telles que `Genre.aspx?ID=foo`, ou une page inexistante, tel que `NoSuchPage.aspx`. Ce que vous voyez lors de la visite de ces pages varie selon le `<customErrors>` configuration et si vous vous visitez localement ou à distance. (Faire référence à la [ *affichage d’une Page d’erreur personnalisée* didacticiel](displaying-a-custom-error-page-vb.md) pour un petit rappel sur cette rubrique.)

ELMAH n’affecte pas le contenu est affiché à l’utilisateur lorsqu’une exception non gérée se produit ; Il enregistre uniquement les détails. Ce journal des erreurs est accessible à partir de la page web `elmah.axd` à partir de la racine de votre site Web, tel que `http://localhost/BookReviews/elmah.axd`. (Ce fichier n’existe pas physiquement dans votre projet, mais lorsqu’une demande arrive pour `elmah.axd` le runtime le distribue à la `ErrorLogPageFactory` gestionnaire HTTP, qui génère le balisage envoyé au navigateur.)

> [!NOTE]
> Vous pouvez également utiliser le `elmah.axd` page pour indiquer à ELMAH pour générer une erreur de test. Visite `elmah.axd/test` (comme dans `http://localhost/BookReviews/elmah.axd/test`) provoque ELMAH lever une exception de type `Elmah.TestException`, qui a le message d’erreur : « Il s’agit d’une exception de test qui peut être ignorée en toute sécurité. »


**Figure 3** affiche le journal des erreurs lors de la visite `elmah.axd` à partir de l’environnement de développement.

[![](logging-error-details-with-elmah-vb/_static/image6.png)](logging-error-details-with-elmah-vb/_static/image5.png)

**Figure 3**: `Elmah.axd` affiche le journal des erreurs à partir d’une Page Web  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image7.png))

Le journal des erreurs **Figure 3** contient six entrées d’erreur. Chaque entrée inclut le code d’état HTTP (404 ou 500, ces erreurs), le type, la description, le nom de l’utilisateur connecté lorsque l’erreur s’est produite et la date et l’heure. En cliquant sur le lien Détails affiche une page qui comporte le même message d’erreur indiqué dans l’erreur détails jaune écran de décès (consultez **Figure 4**), ainsi que les valeurs des variables de serveur au moment de l’erreur (voir  **Figure 5**). Vous pouvez également afficher le code XML brut dans lequel les détails de l’erreur sont enregistrés, qui inclut des informations supplémentaires telles que les valeurs dans l’en-tête HTTP POST.

[![](logging-error-details-with-elmah-vb/_static/image9.png)](logging-error-details-with-elmah-vb/_static/image8.png)

**Figure 4**: afficher les détails de l’erreur YSOD  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image10.png))

[![](logging-error-details-with-elmah-vb/_static/image12.png)](logging-error-details-with-elmah-vb/_static/image11.png)

**Figure 5**: Explorer les valeurs de la Collection de Variables de serveur au moment de l’erreur  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image13.png))

Déploiement ELMAH vers le site Web de production implique :

- Copie le `Elmah.dll` le fichier le `Bin` dossier sur la production,
- Copie les paramètres de configuration spécifiques à ELMAH le `Web.config` fichier utilisé sur la production, et
- Ajout de l’infrastructure de source de journal des erreurs à la base de données de production.

Nous avons exploré les techniques permettant de copier les fichiers de développement pour la production dans les didacticiels précédents. Le moyen le plus simple pour obtenir de l’infrastructure de source de journal des erreurs sur la base de données de production est peut-être d’utiliser SQL Server Management Studio pour vous connecter à la base de données de production, puis exécutez le `SqlServer.sql` fichier de script qui crée la table nécessaire et stockées procédures.

### <a name="viewing-the-error-details-page-on-production"></a>Affichage de la Page de détails de l’erreur sur la Production

Après avoir déployé votre site de production, visitez le site Web de production et génère une exception non gérée. Comme dans l’environnement de développement ELMAH n’a aucun effet sur la page d’erreur affichée lorsqu’une exception non gérée se produit ; au lieu de cela, il consigne simplement l’erreur. Si vous essayez de visiter la page journal d’erreurs (`elmah.axd`) à partir de l’environnement de production, vous êtes accueilli avec la page interdit illustrée **Figure 6**.

[![](logging-error-details-with-elmah-vb/_static/image15.png)](logging-error-details-with-elmah-vb/_static/image14.png)

**Figure 6**: par défaut, les visiteurs distants ne peuvent pas afficher la Page Web du journal des erreurs  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image16.png))

N’oubliez pas que dans la configuration de ELMAH `<security>` section, nous avons défini le `allowRemoteAccess` attribut 0, ce qui interdit aux utilisateurs distants d’afficher le journal des erreurs. Il est important d’empêcher les visiteurs anonymes d’afficher le journal des erreurs, les détails de l’erreur peuvent révéler des failles de sécurité ou d’autres informations sensibles. Si vous décidez de définir cet attribut sur 1 et activer l’accès à distance dans le journal des erreurs, il est important de verrouiller le `elmah.axd` chemin d’accès afin que seuls les visiteurs autorisés peut y accéder. Cela peut être obtenue en ajoutant un `<location>` élément à la `Web.config` fichier.

La configuration suivante autorise uniquement les utilisateurs dans le rôle d’administrateur pour accéder à la page web du journal des erreurs :

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample5.xml)]

> [!NOTE]
> Le rôle d’administrateur et les trois utilisateurs dans le système - Scott Jisun et Alice - ont été ajoutés dans le [ *configuration d’un site Web qu’utilise les Services d’Application* didacticiel](configuring-a-website-that-uses-application-services-vb.md). Les utilisateurs Scott et Jisun sont membres du rôle d’administrateur. Pour plus d’informations sur l’authentification et d’autorisation, reportez-vous à mon [didacticiels de sécurité du site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md).


Le journal des erreurs sur l’environnement de production peut désormais être affiché par les utilisateurs distants ; faire référence à **Figures 3**, **4**, et **5** pour les captures d’écran de la page web du journal des erreurs. Toutefois, si un utilisateur anonyme ou non administrateur tente d’afficher la page journal d’erreurs qu’ils sont automatiquement redirigés vers la page de connexion (`Login.aspx`), en tant que **Figure 7** montre.

[![](logging-error-details-with-elmah-vb/_static/image18.png)](logging-error-details-with-elmah-vb/_static/image17.png)

**Figure 7**: les utilisateurs non autorisés sont automatiquement redirigé vers la Page de connexion  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image19.png))

### <a name="programmatically-logging-errors"></a>Journalisation des erreurs par programmation

De ELMAH `ErrorLogModule` HTTP Module exceptions non gérées se connecte automatiquement à la source de journal spécifié. Vous pouvez également enregistrer une erreur sans avoir à déclencher une exception non gérée à l’aide de la `ErrorSignal` classe et ses `Raise` (méthode). Le `Raise` méthode est passée un `Exception` de l’objet et l’enregistre comme si cette exception a été levée et a atteint le runtime ASP.NET sans être géré. Toutefois, la différence est que la demande continue de s’exécuter normalement après le `Raise` méthode a été appelée, tandis qu’une exception non gérée levée interrompt l’exécution normale de la demande et, le runtime ASP.NET afficher la configuration page d’erreur.

La `ErrorSignal` classe est utile dans les situations où il existe des actions qui risquent d’échouer, mais son échec n’est pas grave à l’opération globale est en cours d’exécution. Par exemple, un site Web peut contenir un formulaire qui accepte l’entrée d’utilisateur, il stocke dans une base de données et envoie ensuite l’utilisateur un message électronique les informant qu’ils a été traitée. Ce qui doit se produire si les informations sont enregistrées avec succès à la base de données, mais il existe une erreur lors de l’envoi du message électronique ? Une option peut consister à lever une exception et l’envoyer à l’utilisateur à la page d’erreur. Toutefois, cela peut perturber l’utilisateur considèrent que les informations qu’ils entrées n’ont pas été enregistrées. Une autre approche consisterait à enregistrer l’erreur liés au courrier électronique, mais ne modifie pas l’expérience utilisateur en aucune façon. C’est là la `ErrorSignal` classe est utile.

[!code-vb[Main](logging-error-details-with-elmah-vb/samples/sample6.vb)]

## <a name="error-notification-via-e-mail"></a>Notification d’erreur par courrier électronique

En même temps que la journalisation des erreurs à une base de données, ELMAH peut également être configuré pour envoyer par courrier électronique des détails de l’erreur à un destinataire spécifié. Cette fonctionnalité est fournie par le `ErrorMailModule` HTTP Module ; par conséquent, vous devez inscrire ce HTTP Module dans `Web.config` afin d’envoyer les détails de l’erreur par courrier électronique.

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample7.xml)]

Ensuite, spécifiez les informations sur le message d’erreur dans le `<elmah>` l’élément `<errorMail>` section, qui indique l’expéditeur du message électronique et le destinataire, l’objet, et si le message électronique est envoyé de manière asynchrone.

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample8.xml)]

Avec les paramètres ci-dessus en place, chaque fois qu’une erreur d’exécution se produit ELMAH envoie un message électronique à support@example.com avec les détails de l’erreur. Par courrier électronique des erreurs de ELMAH inclut les mêmes informations que celui indiquées dans la page de web de détails de l’erreur, à savoir le message d’erreur, la trace de pile et les variables de serveur (font référence aux **Figures 4** et **5**). Le message d’erreur inclut également le contenu de l’Exception détails de l’écran jaune de décès en tant que pièce jointe (`YSOD.html`).

**Figure 8** présente la messagerie d’erreur de ELMAH généré en vous rendant sur `Genre.aspx?ID=foo`. Alors que **Figure 8** affiche uniquement le message et la pile de trace d’erreur, les variables de serveur sont incluses plus bas dans les corps du message électronique.

[![](logging-error-details-with-elmah-vb/_static/image21.png)](logging-error-details-with-elmah-vb/_static/image20.png)

**Figure 8**: vous pouvez configurer ELMAH pour envoyer les détails de l’erreur par courrier électronique  
([Cliquez pour afficher l’image en taille réelle](logging-error-details-with-elmah-vb/_static/image22.png))

## <a name="only-logging-errors-of-interest"></a>Enregistrement uniquement des erreurs d’intérêt

Par défaut, ELMAH enregistre les détails de chaque exception non gérée, y compris 404 et autres erreurs HTTP. Vous pouvez demander à ELMAH pour ignorer ces ou autres types d’erreurs à l’aide du filtrage de l’erreur. La logique de filtrage est effectuée par du ELMAH `ErrorFilterModule` HTTP Module, vous devez inscrire dans `Web.config` afin d’utiliser la logique de filtrage. Les règles de filtrage sont spécifiés dans la `<errorFilter>` section.

Le balisage suivant fait en sorte que ELMAH ne pas consigner les erreurs de 404.

[!code-xml[Main](logging-error-details-with-elmah-vb/samples/sample9.xml)]

> [!NOTE]
> N’oubliez pas que pour pouvoir utiliser le filtrage des erreurs que vous devez inscrire le `ErrorFilterModule` HTTP Module.


Le `<equal>` élément à l’intérieur du `<test>` section est appelée une assertion. Si l’assertion a la valeur true, puis l’erreur est filtré à partir du journal de ELMAH. Il existe d’autres assertions, y compris : `<greater>`, `<greater-or-equal>`, `<not-equal>`, `<lesser>`, `<lesser-or-equal>`, et ainsi de suite. Vous pouvez également combiner des assertions à l’aide de la `<and>` et `<or>` opérateurs booléens. De plus, vous pouvez même inclure une expression JavaScript simple comme une assertion, ou écrire vos propres assertions dans c# ou Visual Basic.

Pour plus d’informations sur l’erreur de ELMAH fonctionnalités de filtrage, reportez-vous à la [section erreur filtrage](https://code.google.com/p/elmah/wiki/ErrorFiltering) dans les [ELMAH wiki](https://code.google.com/p/elmah/w/list).

## <a name="summary"></a>Résumé

ELMAH fournit un mécanisme simple et puissant pour la journalisation des erreurs dans une application web ASP.NET. Comme le système de surveillance de l’intégrité de Microsoft, ELMAH peut enregistrer les erreurs dans une base de données et envoyer des détails de l’erreur à un développeur par courrier électronique. Contrairement à l’intégrité du système de surveillance, ELMAH inclut la prise en charge pour un large éventail de magasins de données de journal erreur, y compris : Microsoft SQL Server, Microsoft Access, Oracle, les fichiers XML et plusieurs autres. En outre, ELMAH offre un mécanisme intégré pour afficher le journal des erreurs et les détails sur une erreur spécifique à partir d’une page web, `elmah.axd`. Le `elmah.axd` page peut également rendre les informations d’erreur comme un flux RSS ou comme un fichier de valeurs séparées par des virgules (CSV), que vous pouvez consulter à l’aide de Microsoft Excel. Vous pouvez également demander à ELMAH pour filtrer les erreurs dans le journal à l’aide d’assertions déclaratives ou par programme. Et ELMAH peut être utilisé avec les applications ASP.NET version 1.x.

Chaque application déployée doit avoir un mécanisme de journalisation automatiquement les exceptions non gérées et l’envoi de notification à l’équipe de développement. Si cela s’effectue à l’aide du contrôle d’intégrité ou ELMAH est secondaire. En d’autres termes, peu beaucoup l’utilisation de contrôle d’intégrité ou ELMAH ; évaluer les deux systèmes, puis choisissez celle qui répond le mieux à vos besoins. Ce qui est fondamentalement important est qu’un mécanisme d’être mis en place pour enregistrer les exceptions non gérées dans l’environnement de production.

Bonne programmation !

### <a name="further-reading"></a>informations supplémentaires

Pour plus d’informations sur les sujets abordés dans ce didacticiel, consultez les ressources suivantes :

- [ELMAH - gestionnaires et les Modules de journalisation d’erreur](http://dotnetslackers.com/articles/aspnet/ErrorLoggingModulesAndHandlers.aspx)
- [Page de projet ELMAH](https://code.google.com/p/elmah/) (source de code, des exemples, wiki)
- [Brancher ELMAH dans une Application Web pour intercepter les Exceptions non gérées](http://screencastaday.com/ScreenCasts/43_Plugging_Elmah_into_Web_Application_to_Catch_Unhandled_Exceptions.aspx) (vidéo)
- [Pages du journal de sécurité erreur](https://code.google.com/p/elmah/wiki/SecuringErrorLogPages)
- [À l’aide des Modules HTTP et des gestionnaires pour créer des composants enfichables ASP.NET](https://msdn.microsoft.com/en-us/library/aa479332.aspx)
- [Didacticiels de sécurité de site Web](../../older-versions-security/introduction/security-basics-and-asp-net-support-cs.md)

>[!div class="step-by-step"]
[Précédent](logging-error-details-with-asp-net-health-monitoring-vb.md)
[Suivant](precompiling-your-website-vb.md)
