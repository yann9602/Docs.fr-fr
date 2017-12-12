---
uid: aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/the-fix-it-sample-application
title: "Annexe : Le correctif il exemple d’Application (création d’applications de Cloud du monde réel avec Azure) | Documents Microsoft"
author: MikeWasson
description: "Les applications du Cloud monde réel construction avec Azure livres est basée sur une présentation développée par Scott Guthrie. Il explique 13 des modèles et des meilleures pratiques qui peuvent il..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/12/2014
ms.topic: article
ms.assetid: 1bc333c5-f096-4ea7-b170-779accc21c1a
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/the-fix-it-sample-application
msc.type: authoredcontent
ms.openlocfilehash: 470b8a5f4a004c85f603c9c5d0766e5826c96e38
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="appendix-the-fix-it-sample-application-building-real-world-cloud-apps-with-azure"></a>Annexe : Le correctif il exemple d’Application (création d’applications de Cloud du monde réel avec Azure)
====================
par [Mike Wasson](https://github.com/MikeWasson), [Rick Anderson](https://github.com/Rick-Anderson), [Tom Dykstra](https://github.com/tdykstra)

[Téléchargez le correctif il de projet](http://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4)

> Le **construction réelle applications Cloud du monde avec Azure** livres est basé sur une présentation développée par Scott Guthrie. Il explique 13 modèles et pratiques qui peuvent vous aider à réussir le développement d’applications web pour le cloud. Pour plus d’informations sur le livre électronique, consultez [le premier chapitre](introduction.md).


Cette annexe pour les applications du Cloud monde réel construction avec livres Azure contient les sections suivantes fournissent des informations supplémentaires sur la réparation exemple d’application que vous pouvez télécharger :

- [Problèmes connus](#knownissues)
- [Meilleures pratiques](#bestpractices)
- [Comment exécuter l’application à partir de Visual Studio sur votre ordinateur local](#run-in-vs)
- [Comment déployer l’application de base pour les applications Web de Azure App Service à l’aide de scripts Windows PowerShell](#deploybase)
- [Résolution des problèmes de scripts Windows PowerShell](#troubleshooting)
- [Comment déployer l’application avec la file d’attente de traitement pour Azure App Service Web Apps et un Service Cloud Azure](#deployqueues)

<a id="knownissues"></a>
## <a name="known-issues"></a>Problèmes connus

L’application de réparation a été développée pour illustrer la façon la plus simple que possible parmi les modèles présentés dans ce livre électronique. Toutefois, étant donné que le livre électronique concerne la création d’applications du monde réel, nous soumis le code corriger à un examen et le processus de test similaire à ce que nous le faisons des logiciels existants. Nous avons trouvé un nombre de problèmes et comme avec n’importe quelle application réelle, certains d'entre eux que nous avons résolu et certaines d'entre elles nous différée vers une version ultérieure.

La liste suivante inclut des problèmes qui doivent être traités dans une application de production, mais pour une raison ou une autre que nous avons décidées de ne pas l’adresse dans la version initiale de l’application exemple corriger.

### <a name="security"></a>Sécurité

- Assurez-vous que vous ne pouvez attribuer une tâche à un propriétaire n’existe pas.
- Vérifiez que vous pouvez uniquement afficher et modifier les tâches que vous avez créé, ou vous sont assignés.
- Utiliser HTTPS pour les pages de connexion et les cookies d’authentification.
- Spécifiez une limite de temps pour les cookies d’authentification.

### <a name="input-validation"></a>Validation d’entrée

En règle générale, une application de production feriez plus d’entrées de validation à l’application corriger. Par exemple, la taille des images ou d’image de taille de fichier autorisée pour le téléchargement doit être limité.

### <a name="administrator-functionality"></a>Fonctionnalité de l’administrateur

Un administrateur doit être en mesure de modifier la propriété sur les tâches existantes. Par exemple, le créateur d’une tâche peut laisser la société, en laissant aucune habilité à mettre à jour de la tâche, sauf si l’accès d’administration est activé.

### <a name="queue-message-processing"></a>Traitement des messages de file d’attente

Message de file d’attente de traitement dans l’application corriger a été conçu pour être simple afin d’illustrer le modèle centré sur la file d’attente de travail avec une quantité minimale de code. Ce code simple n’est pas adapté à une application de production réel.

- Le code ne garantit pas que chaque message de la file d’attente soient traitée au maximum une fois. Lorsque vous recevez un message à partir de la file d’attente, il existe une période de délai d’attente, au cours de laquelle le message est invisible dans d’autres écouteurs de file d’attente. Si le délai d’attente expire avant que le message est supprimé, le message est à nouveau visible. Par conséquent, si une instance de rôle de travail passe à un certain temps de traitement d’un message, il est théoriquement possible pour le même message soient traitées à deux reprises, ce qui entraîne une tâche en double dans la base de données. Pour plus d’informations sur ce problème, consultez [files d’attente de stockage Azure à l’aide de](https://msdn.microsoft.com/en-us/library/ff803365.aspx#sec7).
- La logique d’interrogation de la file d’attente peut être plus rentable, en traitement par lots de la récupération du message. Chaque fois que vous appelez [CloudQueue.GetMessageAsync](https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.queue.cloudqueue.getmessageasync.aspx), il existe un coût de la transaction. Au lieu de cela, vous pouvez appeler [CloudQueue.GetMessagesAsync](https://msdn.microsoft.com/en-us/library/microsoft.windowsazure.storage.queue.cloudqueue.getmessagesasync.aspx) (Notez le pluriel '), qui obtient plusieurs messages dans une transaction unique. Les coûts de transaction pour les files d’attente de stockage Azure sont très faibles, l’impact sur les coûts n’est pas importante dans la plupart des scénarios.
- La boucle serrée dans le code de traitement des messages de file d’attente entraîne l’affinité du processeur, ce qui n’utilise pas efficacement les ordinateurs virtuels de plusieurs cœurs. Une meilleure conception utiliseriez le parallélisme des tâches à exécuter plusieurs tâches Asynch en parallèle.
- Traitement du message de file d’attente a la gestion des exceptions rudimentaire uniquement. Par exemple, le code ne gère pas [messages incohérents](https://msdn.microsoft.com/en-us/library/ms789028.aspx). (Lorsque le traitement des messages provoque une exception, vous devez enregistrer l’erreur et de supprimer le message, ou le rôle de travail va tenter de le traiter à nouveau, et la boucle continue indéfiniment).

### <a name="sql-queries-are-unbounded"></a>Les requêtes SQL sont non liées

Code actuel corriger n’impose aucune limite sur les requêtes pour les pages d’Index peuvent retourner le nombre de lignes. Si un grand nombre de tâches est entré dans la base de données, la taille de la liste reçu peut entraîner des problèmes de performances. La solution consiste à implémenter la pagination. Pour obtenir un exemple, consultez [tri, filtrage et la pagination avec Entity Framework dans une Application ASP.NET MVC](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md).

### <a name="view-models-recommended"></a>Afficher les modèles recommandés

L’application Fix It utilise la classe d’entité FixItTask pour passer des informations entre le contrôleur et la vue. Une meilleure pratique consiste à utiliser des modèles d’affichage. Le modèle de domaine (par exemple, la classe d’entité FixItTask) est conçu autour de ce qui est nécessaire pour la persistance des données, tandis que le modèle d’affichage peut être conçu pour la présentation des données. Pour plus d’informations, consultez [12 ASP.NET MVC meilleures pratiques](http://codeclimber.net.nz/archive/2009/10/27/12-asp.net-mvc-best-practices.aspx).

### <a name="secure-image-blob-recommended"></a>Objet blob d’image sécurisée recommandée

Les magasins d’applications corriger téléchargement des images comme publiques, ce qui signifie que que toute personne qui recherche l’URL peut accéder aux images. Les images pouvaient être sécurisées au lieu de public.

### <a name="no-powershell-automation-scripts-for-queues"></a>Aucun scripts d’automatisation de PowerShell pour les files d’attente

Exemples de scripts PowerShell automation ont été écrites uniquement pour la version de base de corriger ce qui s’exécute entièrement dans Azure App Service Web Apps. Nous n’avons pas fourni les scripts de configuration et de déploiement de l’application web plus l’environnement de Service Cloud pour le traitement de la file d’attente.

### <a name="special-handling-for-html-codes-in-user-input"></a>Traitement spécial pour les codes HTML dans l’entrée d’utilisateur

ASP.NET empêche automatiquement les manières dont les utilisateurs malveillants peuvent tenter des attaques de script entre sites en entrant le script dans les zones de texte d’entrée utilisateur. Et le MVC `DisplayFor` application d’assistance utilisée pour afficher la tâche de titres et notes automatiquement les valeurs de code HTML qu’elle envoie au navigateur. Toutefois, dans une application de production vous pouvez prendre des mesures supplémentaires. Pour plus d’informations, consultez [demande de Validation dans ASP.NET](https://msdn.microsoft.com/en-us/library/hh882339.aspx).

<a id="bestpractices"></a>
## <a name="best-practices"></a>meilleures pratiques recommandées.

Voici certains problèmes qui ont été résolus après avoir été découverts au cours de la révision du code et de test de la version d’origine de l’application corriger. Certains ont été causées par l’auteur du code d’origine n’est ne pas conscient de meilleure pratique particulière, certaines simplement parce que le code a été écrit rapidement et n’a pas été prévu pour les logiciels. Nous mettons répertoriant les problèmes ici en cas de quelque chose que nous avons appris à partir de cette révision et de test qui peut être utile pour d’autres personnes sont également développement d’applications web.

### <a name="dispose-the-database-repository"></a>Supprimer le référentiel de base de données

Le `FixItTaskRepository` classe doit disposer de l’Entity Framework `DbContext` instance. Nous avons fait cela en implémentant `IDisposable` dans la `FixItTaskRepository` classe :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample1.cs)]

Notez que AutoFac supprimera automatiquement le `FixItTaskRepository` de l’instance, nous n’avez pas besoin de manière explicite de la suppression.

Une autre option consiste à supprimer la `DbContext` variable membre à partir de `FixItTaskRepository`et à la place créer une variable locale `DbContext` variable dans chaque méthode de référentiel, à l’intérieur d’un `using` instruction. Exemple :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample2.cs)]

### <a name="register-singletons-as-such-with-di"></a>Enregistrer en tant que tel des singletons avec DI

Depuis qu’une seule instance de la `PhotoService` classe et `Logger` classe est nécessaire, ces classes doivent être [inscrit en tant qu’instances uniques pour l’injection de dépendance](https://code.google.com/p/autofac/wiki/InstanceScope) dans *DependenciesConfig.cs*:

[!code-csharp[Main](the-fix-it-sample-application/samples/sample3.cs?highlight=1,3)]

### <a name="security-dont-show-error-details-to-users"></a>Sécurité : Ne pas afficher les détails de l’erreur pour les utilisateurs

L’application d’origine de réparation n’a pas avoir une page d’erreur générique et permettent uniquement de toutes les bulles d’exceptions à l’interface utilisateur, afin de certaines exceptions telles que les erreurs de connexion de base de données peut entraîner une trace de pile complet qui est affichée dans le navigateur. Informations d’erreur détaillées peuvent faciliter parfois les attaques par des utilisateurs malveillants. La solution consiste à consigner les détails de l’exception et afficher une page d’erreur à l’utilisateur qui n’inclut pas détails de l’erreur. L’application de réparation a été déjà journalisation, et pour afficher une page d’erreur, nous avons ajouté `<customErrors mode=On>` dans le fichier Web.config.

[!code-xml[Main](the-fix-it-sample-application/samples/sample4.xml?highlight=2)]

Par défaut, cela provoque *Views\Shared\Error.cshtml* à afficher pour les erreurs. Vous pouvez personnaliser *Error.cshtml* ou créez votre propre vue de page d’erreur et ajoutez un `defaultRedirect` attribut. Vous pouvez également spécifier des pages d’erreur différentes pour des erreurs spécifiques.

### <a name="security-only-allow-a-task-to-be-edited-by-its-creator"></a>Sécurité : autoriser uniquement être modifié par son créateur d’une tâche

La page d’Index de tableau de bord affiche les tâches créées par l’utilisateur connecté, mais un utilisateur malveillant peut créer une URL avec un ID pour les tâches d’un autre utilisateur. Nous avons ajouté du code dans *DashboardController.cs* pour retourner une erreur 404 dans ce cas :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample5.cs?highlight=9-14,24-29)]

### <a name="dont-swallow-exceptions"></a>Ne pas absorber des exceptions

L’application d’origine de corriger vient d’être retourné null après l’ouverture d’une exception qui est le résultat d’une requête SQL :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample6.cs?highlight=4)]

Cela rend présenté à l’utilisateur comme si la requête a réussi, mais simplement la n’a pas retourné des lignes. Solution consiste à lever à nouveau l’exception après l’interception et la journalisation :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample7.cs)]

### <a name="catch-all-exceptions-in-worker-roles"></a>Intercepter toutes les exceptions dans les rôles de travail

Les exceptions non gérées dans un rôle de travail entraînera la machine virtuelle à recycler, donc vous encapsulez tout dans un bloc try-catch et de gérer toutes les exceptions.

### <a name="specify-length-for-string-properties-in-entity-classes"></a>Spécifier une longueur pour les propriétés de chaîne dans les classes d’entité

Pour afficher le code simple, la version d’origine de l’application Fix It ne spécifiait longueurs des champs de l’entité FixItTask et ainsi qu’ils ont été définies comme varchar (max) dans la base de données. Par conséquent, l’interface utilisateur acceptaient presque n’importe quel volume de l’entrée. La spécification définit les longueurs limites qui s’appliquent à l’utilisateur d’entrée dans la page web et la taille de la colonne dans la base de données :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample8.cs?highlight=4,7,10,12,14)]

### <a name="mark-private-members-as-readonly-when-they-arent-expected-to-change"></a>Marquez les membres privés en lecture seule lorsqu’ils ne sont pas supposées changer

Par exemple, dans le `DashboardController` classe une instance de `FixItTaskRepository` est créé et n’est pas supposé changer, pour que nous avons défini en tant que [readonly](https://msdn.microsoft.com/en-us/library/acdd6hb7.aspx).

[!code-csharp[Main](the-fix-it-sample-application/samples/sample9.cs?highlight=3)]

### <a name="use-listany-instead-of-listcount-gt-0"></a>Utilisez la liste. Any() au lieu de la liste. La fonction Count() &gt; 0

Si vous vous souciez est si un ou plusieurs éléments dans une liste répondent aux critères spécifiés, utilisez le [n’importe quel](https://msdn.microsoft.com/en-us/library/bb534972.aspx) (méthode), car elle retourne dès qu’un élément qui respecte les critères est trouvé, alors que le `Count` méthode dispose toujours effectuer une itération au à chaque élément. Le tableau de bord *Index.cshtml* du fichier sont à l’origine de ce code :

[!code-cshtml[Main](the-fix-it-sample-application/samples/sample10.cshtml)]

Nous le remplacé par :

[!code-cshtml[Main](the-fix-it-sample-application/samples/sample11.cshtml?highlight=1)]

### <a name="generate-urls-in-mvc-views-using-mvc-helpers"></a>Générer des URL dans les vues MVC à l’aide de programmes d’assistance MVC

Pour le **créer un corriger** bouton sur la page d’accueil, l’application corriger dur codé de l’élément d’ancrage :

[!code-cshtml[Main](the-fix-it-sample-application/samples/sample12.cshtml)]

Pour obtenir des liens d’Action d’affichage comme suit, il est préférable d’utiliser le [Url.Action](https://msdn.microsoft.com/en-us/library/system.web.mvc.urlhelper.action.aspx) programme d’assistance HTML, par exemple :

[!code-cshtml[Main](the-fix-it-sample-application/samples/sample13.cshtml)]

### <a name="use-taskdelay-instead-of-threadsleep-in-worker-role"></a>Utilisez Task.Delay au lieu de Thread.Sleep dans le rôle de travail

Le modèle nouveau projet place `Thread.Sleep` dans l’exemple de code pour un rôle de travail, mais à l’origine de la mise en veille du thread peut entraîner le pool de threads générer dynamiquement des threads supplémentaires inutiles. Vous pouvez éviter cela à l’aide de [Task.Delay](https://msdn.microsoft.com/en-us/library/hh139096.aspx) à la place.

[!code-csharp[Main](the-fix-it-sample-application/samples/sample14.cs?highlight=11)]

### <a name="avoid-async-void"></a>Éviter async void

Si une méthode async n’a pas besoin de retourner une valeur, retournent un `Task` type plutôt que `void`.

Cet exemple est issu du `FixItQueueManager` classe : 

[!code-csharp[Main](the-fix-it-sample-application/samples/sample15.cs)]

Vous devez utiliser `async void` uniquement pour les gestionnaires d’événements de niveau supérieur. Si vous définissez une méthode en tant que `async void`, l’appelant ne peut pas **await** la méthode ou d’intercepter des exceptions lève de la méthode. Pour plus d’informations, consultez [meilleures pratiques de programmation asynchrone](https://msdn.microsoft.com/en-us/magazine/jj991977.aspx). 

### <a name="use-a-cancellation-token-to-break-from-worker-role-loop"></a>Utiliser un jeton d’annulation pour arrêter l’exécution à partir de la boucle de rôle de travail

En règle générale, les **exécuter** méthode sur un rôle de travail contient une boucle infinie. Lorsque le rôle de travail est arrêté, le [RoleEntryPoint.OnStop](https://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) méthode est appelée. Vous devez utiliser cette méthode pour annuler le travail est effectué à l’intérieur de la **exécuter** (méthode) et sortie en douceur. Sinon, le processus peut être arrêté au milieu d’une opération.

### <a name="opt-out-of-automatic-mime-sniffing-procedure"></a>Refuser la procédure de détection automatique de MIME

Dans certains cas, Internet Explorer signale un type MIME différent de celui du type spécifié par le serveur web. Par exemple, si Internet Explorer trouve HTML contenu dans un fichier remis avec l’en-tête de réponse HTTP Content-Type : texte/brut, Internet Explorer détermine que le contenu doit être rendu au format HTML. Malheureusement, cette « détection MIME sur » peut également entraîner des problèmes de sécurité pour les serveurs hébergeant le contenu non fiable. Pour résoudre ce problème, Internet Explorer 8 a effectué un nombre de modifications au code de détermination du type MIME et permet aux développeurs d’applications [refuser la détection MIME sur](https://blogs.msdn.com/b/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx). Le code suivant a été ajouté à la *Web.config* fichier.

[!code-xml[Main](the-fix-it-sample-application/samples/sample16.xml?highlight=2-7)]

### <a name="enable-bundling-and-minification"></a>Activer le groupement et la minimisation

Lorsque Visual Studio crée un nouveau projet web, groupement et minimisation des fichiers JavaScript n’est pas activé par défaut. Nous avons ajouté une ligne de code dans BundleConfig.cs :

[!code-csharp[Main](the-fix-it-sample-application/samples/sample17.cs?highlight=9)]

### <a name="set-an-expiration-time-out-for-authentication-cookies"></a>Définir un délai d’expiration pour les cookies d’authentification

Par défaut, les cookies d’authentification expirent après deux semaines. Une durée plus courte est plus sûre. Vous pouvez modifier ce paramètre dans *StartupAuth.cs*:

[!code-csharp[Main](the-fix-it-sample-application/samples/sample18.cs?highlight=4-5)]

<a id="run-in-vs"></a>
## <a name="how-to-run-the-app-from-visual-studio-on-your-local-computer"></a>Comment exécuter l’application à partir de Visual Studio sur votre ordinateur local

Il existe deux façons d’exécuter l’application corriger :

- Exécutez l’application de base qui écrit des nouvelles tâches directement dans la base de données SQL.
- Exécutez l’application à l’aide d’une file d’attente et un service principal pour créer des tâches. Le modèle de file d’attente est décrit dans le chapitre [modèle centré sur la file d’attente de travail](queue-centric-work-pattern.md).

<a id="runbase"></a>
### <a name="run-the-base-application"></a>Exécutez l’application de base

1. Installer [Visual Studio 2013 ou Visual Studio 2013 Express pour le Web](https://www.visualstudio.com/en-us/downloads).
2. Installer le [Azure SDK pour .NET de Visual Studio 2013.](https://go.microsoft.com/fwlink/p/?linkid=323510&amp;clcid=0x409)
3. Téléchargez le fichier .zip à partir de la [MSDN Code Gallery](https://code.msdn.microsoft.com/Fix-It-app-for-Building-cdd80df4).
4. Dans l’Explorateur de fichiers, cliquez sur le fichier .zip, puis cliquez sur Propriétés, dans la fenêtre Propriétés, cliquez sur Débloquer.
5. Décompressez le fichier.
6. Double-cliquez sur le fichier .sln pour lancer Visual Studio.
7. Dans le menu Outils, cliquez sur Gestionnaire de Package de bibliothèque, puis Console du Gestionnaire de Package.
8. Dans Package Manager Console (PMC), cliquez sur Restaurer.
9. Quittez Visual Studio.
10. Démarrer le [émulateur de stockage Azure](https://msdn.microsoft.com/en-us/library/windowsazure/hh403989.aspx).
11. Redémarrez Visual Studio, en ouvrant le fichier de solution que vous avez fermé à l’étape précédente.
12. Assurez-vous que le projet de correctif est défini comme projet de démarrage, puis appuyez sur CTRL + F5 pour exécuter le projet.

<a id="queueslocal"></a>
### <a name="run-the-application-with-queue-processing"></a>Exécutez l’application avec le traitement de la file d’attente

1. Suivez les instructions pour [exécuter l’application de base](#runbase), puis fermez le navigateur et fermez Visual Studio.
2. Démarrez Visual Studio avec des privilèges d’administrateur. (Vous allez utiliser l’émulateur de calcul Azure, et qui requiert des privilèges d’administrateur).
3. Dans l’application *Web.config* de fichiers dans le *MyFixIt* project (projet web), modifiez la valeur de `appSettings/UseQueues` sur « true » : 

    [!code-console[Main](the-fix-it-sample-application/samples/sample19.cmd?highlight=3)]
4. Si le [émulateur de stockage Azure](https://msdn.microsoft.com/en-us/library/windowsazure/hh403989.aspx) n’est en cours d’exécution, démarrez-le à nouveau.
5. Exécutez le projet web correctif et le projet MyFixItCloudService simultanément.

    À l’aide de Visual Studio 2013 :

    1. Appuyez sur F5 pour exécuter le projet de correctif.
    2. Dans **l’Explorateur de solutions**, cliquez sur le projet MyFixItCloudService, puis cliquez sur **déboguer** -- **démarrer une nouvelle Instance**.

    À l’aide de Visual Studio 2013 Express pour le Web :

    1. Dans l’Explorateur de solutions, cliquez sur la solution de réparation et sélectionnez **propriétés**.
    2. Sélectionnez **plusieurs projets de démarrage**...
    3. Dans le **Action** liste déroulante sous MyFixIt et MyFixItCloudService, sélectionnez **Démarrer**.
    4. Cliquez sur **OK**.
    5. Appuyez sur F5 pour exécuter les deux projets.

    Lorsque vous exécutez le projet MyFixItCloudService, Visual Studio démarre l’émulateur de calcul Azure. Selon votre configuration de pare-feu, vous devrez peut-être autoriser l’émulateur à travers le pare-feu.

<a id="deploybase"></a>
## <a name="how-to-deploy-the-base-app-to-azure-app-service-web-apps-by-using-the-windows-powershell-scripts"></a>Comment déployer l’application de base pour les applications Web de Azure App Service à l’aide de scripts Windows PowerShell

Pour illustrer la [automatiser tout](automate-everything.md) modèle, l’application corriger est fourni avec des scripts pour configurer un environnement dans Azure et de déploiement le projet vers le nouvel environnement. Les instructions suivantes expliquent comment utiliser les scripts.

Si vous souhaitez exécuter dans Azure sans utiliser les files d’attente et les modifications pour exécuter localement avec les files d’attente, vérifiez que la valeur UseQueues appSetting sur false avant de poursuivre les instructions suivantes.

Ces instructions supposent que vous avez déjà téléchargé et exécutez la solution résoudre localement, et que vous disposez d’un Azure compte ou ont un abonnement Azure que vous êtes autorisé à gérer.

1. Installer le **Azure PowerShell** console. Pour obtenir des instructions, consultez [comment installer et configurer Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-4.3.1).

    Cette console personnalisée est configurée pour fonctionner avec votre abonnement Azure. Le module Azure est installé dans le *Program Files* active et est automatiquement importé à chaque utilisation de la console Azure PowerShell.

    Si vous préférez travailler dans un programme hôte différent, tel que Windows PowerShell ISE, veillez à utiliser le [Import-Module](https://go.microsoft.com/fwlink/?LinkID=141553) applet de commande pour importer le module Azure ou utilisez une commande dans le module Azure pour déclencher l’importation automatique du module.
2. Démarrez PowerShell Azure avec la **exécuter en tant qu’administrateur** option.
3. Exécutez le [Set-ExecutionPolicy](https://go.microsoft.com/fwlink/p/?linkid=293941) applet de commande pour définir la stratégie d’exécution PowerShell de Azure `RemoteSigned`. Entrez **Y** (pour Oui) pour terminer la modification de la stratégie.

    [!code-console[Main](the-fix-it-sample-application/samples/sample20.cmd)]

    Ce paramètre vous permet d’exécuter les scripts locaux qui ne sont pas signés numériquement. (Vous pouvez également définir la stratégie d’exécution `Unrestricted`, qui élimine la nécessité pour l’étape de débloquer ultérieurement, mais cela n’est pas recommandé pour des raisons de sécurité.)
4. Exécutez le `Add-AzureAccount` applet de commande pour installer PowerShell avec les informations d’identification de votre compte.

    [!code-console[Main](the-fix-it-sample-application/samples/sample21.cmd)]

    Ces informations d’identification expirent après une période de temps et vous devez exécuter à nouveau le `Add-AzureAccount` applet de commande. Comme ce livre électronique est en cours d’écriture, le délai d’attente avant l’expirent des informations d’identification est de 12 heures.
5. Si vous avez plusieurs abonnements, utilisez l’applet de commande Select-AzureSubscription pour spécifier l’abonnement que vous souhaitez créer dans l’environnement de test.
6. Importer un certificat de gestion pour le même abonnement Azure à l’aide de la `Get-AzurePublishSettingsFile` et `Import-AzurePublishSettingsFile` applets de commande. Le premier de ces applets de commande télécharge un fichier de certificat et dans l’autre, vous spécifiez l’emplacement de ce fichier pour pouvoir pour l’importer. > [!IMPORTANT]
 > Conserver le fichier téléchargé dans un emplacement sécurisé ou supprimez-le lorsque vous avez terminé, car il contient un certificat qui peut être utilisé pour gérer vos services Azure.

    [!code-console[Main](the-fix-it-sample-application/samples/sample22.cmd)]

    Le certificat est utilisé pour un appel d’API REST qui détecte l’adresse IP de l’ordinateur de développement afin de définir une règle de pare-feu sur le serveur de base de données SQL.
7. Exécutez le [Set-Location](https://go.microsoft.com/fwlink/p/?linkid=293912) applet de commande (alias sont `cd`, `chdir`, et `sl`) pour accéder au répertoire qui contient les scripts. (Ils se trouvent dans le *Automation* dans le dossier de solution Fix It.) Placez le chemin d’accès entre guillemets si un des noms de répertoire contient des espaces. Par exemple, pour accéder à la `c:\Sample Apps\FixIt\Automation` active, vous pouvez entrer la commande suivante :

    [!code-console[Main](the-fix-it-sample-application/samples/sample23.cmd)]
8. Pour autoriser Windows PowerShell exécuter ces scripts, utilisez la [Unblock-File](https://go.microsoft.com/fwlink/p/?linkid=294021) applet de commande. (Les scripts sont bloqués car ils ont été téléchargés à partir d’Internet.)

    > [!WARNING]
    > Sécurité - avant d’exécuter `Unblock-File` sur n’importe quel script ou un fichier exécutable, ouvrez le fichier dans le bloc-notes, examinez les commandes et vérifier qu’ils ne contiennent pas de code malveillant.

    Par exemple, la commande suivante exécute le `Unblock-File` applet de commande sur tous les scripts dans le répertoire actif.

    [!code-console[Main](the-fix-it-sample-application/samples/sample24.cmd)]
9. Pour créer l’application web pour la base (aucune file d’attente de traitement) application de réparation, exécutez le script de création d’environnement.

    Requis `Name` paramètre spécifie le nom de la base de données et est également utilisé pour le compte de stockage que crée le script. Le nom doit être globalement unique dans le domaine azurewebsites.net. Si vous spécifiez un nom qui n’est pas unique, comme le correctif ou de Test (ou même comme dans l’exemple, fixitdemo), le `New-AzureWebsite` applet de commande échoue avec une erreur interne qui indique un conflit. Le script convertit le nom en minuscule pour respecter les exigences de nom pour les applications web, les comptes de stockage et les bases de données.

    Requis `SqlDatabasePassword` paramètre spécifie le mot de passe du compte administrateur qui sera créé pour la base de données SQL. Ne pas inclure les caractères XML spéciaux dans le mot de passe (&amp; &lt; &gt; ;). Il s’agit d’une limitation de la façon dont les scripts ont été écrits, pas une limitation de Azure.

    Par exemple, si vous souhaitez créer une application web nommée « fixitdemo » et un mot de passe administrateur SQL Server de « Passw0rd1 », vous pouvez entrer la commande suivante :

    [!code-console[Main](the-fix-it-sample-application/samples/sample25.cmd)]

    Le nom doit être unique dans le domaine azurewebsites.net et le mot de passe doit répondre aux exigences de base de données SQL de la complexité de mot de passe. (L’exemple Passw0rd1 ne satisfait pas les exigences.)

    Notez que la commande commence par ». \". Pour éviter les malveillant d’exécution de scripts, Windows PowerShell vous devez fournir le chemin d’accès complet au fichier de script lorsque vous exécutez un script. Vous pouvez utiliser un point pour indiquer le répertoire actif («.\") ou indiquez le chemin d’accès qualifié complet, par exemple :

    [!code-console[Main](the-fix-it-sample-application/samples/sample26.cmd)]

    Pour plus d’informations sur le script, utilisez la `Get-Help` applet de commande.

    [!code-console[Main](the-fix-it-sample-application/samples/sample27.cmd)]

    Vous pouvez utiliser la `Detailed`, `Full`, `Parameters`, et `Examples` paramètres de l’applet de commande Get-Help pour filtrer l’aide qui est retourné.

    Si le script échoue ou qu’il génère des erreurs, telles que « New-AzureWebsite : appeler Set-AzureSubscription et Select-AzureSubscription tout d’abord, » vous n’avez ne peut-être pas terminé la configuration d’Azure PowerShell.

    Une fois le script terminé, vous pouvez utiliser le portail de gestion Azure pour voir les ressources qui ont été créées, comme indiqué dans le [automatiser tout](automate-everything.md) chapitre.
10. Pour déployer le projet de correctif vers le nouvel environnement Azure, utilisez le *AzureWebsite.ps1* script. Exemple :

    [!code-console[Main](the-fix-it-sample-application/samples/sample28.cmd)]

    Lorsque le déploiement est terminé, le navigateur s’ouvre avec Fix It s’exécutant dans Azure.

<a id="troubleshooting"></a>
## <a name="troubleshooting-the-windows-powershell-scripts"></a>Résolution des problèmes de scripts Windows PowerShell

Erreurs les plus courantes rencontrées lors de l’exécution de ces scripts sont liés aux autorisations. Assurez-vous que `Add-AzureAccount` et `Import-AzurePublishSettingsFile` ont réussi et que vous les avez utilisés pour le même abonnement Azure. Même si `Add-AzureAccount` a réussi vous devrez peut-être exécuter de nouveau. Les autorisations ajoutées par `Add-AzureAccount` expirer dans les 12 heures.

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>La référence d'objet n'a pas la valeur d'une instance d'un objet.

Si le script renvoie des erreurs, telle que l’exécution de la référence d’objet » non définie sur une instance d’un objet, « ce qui signifie que Windows PowerShell ne peut pas trouver un objet processus (il s’agit d’une exception de référence null), le `Add-AzureAccount` applet de commande, puis réessayez le script.

[!code-console[Main](the-fix-it-sample-application/samples/sample29.cmd)]

### <a name="internalerror-the-server-encountered-an-internal-error"></a>Erreur interne : Le serveur a rencontré une erreur interne.

Le `New-AzureWebsite` applet de commande renvoie une erreur interne lorsque le nom n’est pas unique dans le domaine azurewebsites.net. Pour résoudre l’erreur, utilisez une valeur différente pour le nom, qui se trouve dans le paramètre Name de *New-AzureWebsiteEnv.ps1*.

[!code-console[Main](the-fix-it-sample-application/samples/sample30.cmd)]

### <a name="restarting-the-script"></a>Redémarrer le script

Si vous devez redémarrer le *New-AzureWebsiteEnv.ps1* script car il a échoué avant le message « Le Script est terminé » imprimé, vous pouvez souhaiter supprimer les ressources que le script créé avant qu’il s’est arrêté. Par exemple, si le script a déjà créé l’application web ContosoFixItDemo et que vous exécutez à nouveau le script avec le même nom, le script échoue car le nom est en cours d’utilisation.

Pour déterminer quelles ressources le script créé avant de pouvoir arrêtée, utilisez les applets de commande suivantes :

- `Get-AzureWebsite`
- `Get-AzureSqlDatabaseServer`
- `Get-AzureSqlDatabase`: Pour exécuter cette applet de commande, dirigez le nom du serveur de base de données pour `Get-AzureSqlDatabase`:  
    `Get-AzureSqlDatabaseServer | Get-AzureSqlDatabase.`

Pour supprimer ces ressources, utilisez les commandes suivantes. Notez que si vous supprimez le serveur de base de données, vous supprimer automatiquement les bases de données associées au serveur.

- `Get-AzureWebsite -Name <WebsiteName> | Remove-AzureWebsite`
- `Get-AzureSqlDatabase -Name <DatabaseName> -ServerName <DatabaseServerName> | Remove-SqlAzureDatabase`
- `Get-AzureSqlDatabaseServer | Remove-AzureSqlDatabaseServer`

<a id="deployqueues"></a>
## <a name="how-to-deploy-the-app-with-queue-processing-to-azure-app-service-web-apps-and-an-azure-cloud-service"></a>Comment déployer l’application avec la file d’attente de traitement pour Azure App Service Web Apps et un Service Cloud Azure

Pour activer les files d’attente, apporter la modification suivante dans le fichier MyFixIt\Web.config. Sous `appSettings`, modifiez la valeur de `UseQueues` sur « true » : 

[!code-xml[Main](the-fix-it-sample-application/samples/sample31.xml)]

Puis déployer l’application MVC pour une application web dans Azure App Service, tel que décrit [antérieures](#deploybase).

Ensuite, créez un nouveau service cloud Azure. Les scripts inclus dans l’application Fix It ne créer ou déployer le service cloud, vous devez utiliser le portail Azure pour cela. Dans le portail, cliquez sur **nouveau** -- **de calcul** – **Service Cloud** -- **création rapide**, puis entrez une URL et un emplacement de centre de données. Utilisez le même centre de données où vous avez déployé l’application web.

![](the-fix-it-sample-application/_static/image1.png)

Avant de pouvoir déployer le service cloud, vous devez mettre à jour certains des fichiers de configuration.

Dans MyFixIt.WorkerRoler\app.config, sous `connectionStrings`, remplacez la valeur de la `appdb` chaîne de connexion avec la chaîne de connexion pour la base de données SQL. Vous pouvez obtenir la chaîne de connexion à partir du portail. Dans le portail, cliquez sur **bases de données SQL** - **appdb** - **chaînes de connexion de base de données de vue SQL pour ADO .net, ODBC, PHP et JDBC**. Copiez la chaîne de connexion ADO.NET et collez la valeur dans le fichier app.config. Remplacez « {votre\_mot de passe\_ici} » avec votre mot de passe de base de données. (En supposant que vous avez utilisé les scripts pour déployer l’application MVC, vous avez spécifié le mot de passe de base de données dans le `SqlDatabasePassword` paramètre de script.)

Le résultat doit ressembler à ce qui suit :

[!code-xml[Main](the-fix-it-sample-application/samples/sample32.xml)]

Dans le même fichier MyFixIt.WorkerRoler\app.config, sous `appSettings`, remplacez les deux valeurs d’espace réservé pour le compte de stockage Azure.

[!code-xml[Main](the-fix-it-sample-application/samples/sample33.xml?highlight=2-3)]

Vous pouvez obtenir la clé d’accès à partir du portail. Consultez [comment gérer les comptes de stockage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

Dans MyFixItCloudService\ServiceConfiguration.Cloud.cscfg, remplacez les valeurs de deux même des espaces réservés pour le compte de stockage Azure.

[!code-xml[Main](the-fix-it-sample-application/samples/sample34.xml?highlight=3)]

Vous êtes maintenant prêt à déployer le service cloud. Dans l’Explorateur de solutions, cliquez sur le projet MyFixItCloudService et sélectionnez **publier**. Pour plus d’informations, consultez «[déployer l’Application sur Azure](https://www.windowsazure.com/en-us/develop/net/tutorials/multi-tier-web-site/2-download-and-run/#deployAz)», qui est dans la partie 2 de [ce didacticiel](https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36).

>[!div class="step-by-step"]
[Précédent](more-patterns-and-guidance.md)
