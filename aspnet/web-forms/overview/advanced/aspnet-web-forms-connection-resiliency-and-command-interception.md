---
uid: web-forms/overview/advanced/aspnet-web-forms-connection-resiliency-and-command-interception
title: "L’Interception de commande et de résilience des connexions ASP.NET Web Forms | Documents Microsoft"
author: Erikre
description: "Ce didacticiel décrit comment modifier un exemple d’application pour prendre en charge la résilience des connexions et l’interception de commande."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/31/2014
ms.topic: article
ms.assetid: 6d497001-fa80-4765-b4cc-181fe90b894e
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/advanced/aspnet-web-forms-connection-resiliency-and-command-interception
msc.type: authoredcontent
ms.openlocfilehash: e3347657fb5c7bf8c7bb4e51a2e810a1edde826a
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-web-forms-connection-resiliency-and-command-interception"></a>Résilience des connexions ASP.NET Web Forms et l’Interception de commande
====================
Par [Erik Reitan](https://github.com/Erikre)

Dans ce didacticiel, vous allez modifier l’exemple d’application Wingtip Toys pour prendre en charge la résilience des connexions et l’interception de commande. En activant la résilience des connexions, l’exemple d’application Wingtip Toys va retenter automatiquement les appels de données lorsque des erreurs temporaires classiques d’un environnement de cloud se produisent. En outre, en implémentant l’interception de commande, l’exemple d’application Wingtip Toys intercepte toutes les requêtes SQL envoyées à la base de données afin d’ouvrir une session ou de les modifier.

> [!NOTE] 
> 
> Ce didacticiel Web Forms était basé sur le didacticiel MVC de Tom Dykstra :  
> [Résilience des connexions et l’Interception de commande avec Entity Framework dans une Application ASP.NET MVC](../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)


## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment assurer la résilience de connexion.
- Comment implémenter l’interception de commande.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous d’avoir les logiciels suivants installés sur votre ordinateur :

- [Microsoft Visual Studio 2013](https://www.microsoft.com/visualstudio/11/downloads#vs) ou [Microsoft Visual Studio Express 2013 pour le Web](https://www.microsoft.com/visualstudio/11/downloads#express-web). Le .NET Framework est installé automatiquement.
- Le Wingtip Toys exemple de projet, afin que vous pouvez implémenter les fonctionnalités mentionnées dans ce didacticiel dans le projet Wingtip Toys. Le lien suivant fournit des détails du téléchargement :

    - [Mise en route avec ASP.NET 4.5.1 Web Forms - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&amp;clcid=0x409) (c#)
- Avant la fin de ce didacticiel, pour passer en revue la série de didacticiels associée, [prise en main de Web Forms ASP.NET 4.5 et Visual Studio 2013](../getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview.md). La série de didacticiels vous aidera à vous familiariser avec les **WingtipToys** projet et le code.

## <a name="connection-resiliency"></a>Résilience de la connexion

Lorsque vous envisagez de déployer une application dans Windows Azure, une option à envisager déploie la base de données **Windows** **base de données SQL Azure**, un service de base de données du cloud. Erreurs temporaires de connexion sont généralement plus fréquents lorsque vous vous connectez à un service cloud de base de données lorsque votre serveur web et votre serveur de base de données sont directement interconnectés dans le même centre de données. Même si un serveur web du cloud et un service de base de données du cloud sont hébergés dans le même centre de données, il existe plusieurs connexions réseau entre eux qui peuvent avoir des problèmes, tels que les équilibrages de charge.

Un service cloud est également généralement partagé par d’autres utilisateurs, ce qui signifie que sa réactivité peut être affectée par ces derniers. Et l’accès à la base de données peut être soumis à une limitation. La limitation signifie que le service de base de données lève des exceptions lorsque vous essayez d’y accéder plus fréquemment que n’est autorisé dans votre *contrat de niveau de Service* (SLA).

Nombre ou la plupart des problèmes de connexion qui se produisent lorsque vous accédez à un service cloud sont transitoires, autrement dit, elles se résoudre eux-mêmes dans une courte période de temps. Par conséquent, lorsque vous tentez une opération de base de données et obtenez un type d’erreur est généralement transitoire, vous pouvez essayer à nouveau l’opération après qu’une attente courte et l’opération peuvent aboutir. Vous pouvez fournir une bien meilleure expérience pour vos utilisateurs, si vous gérez des erreurs temporaires en réessayant d’automatiquement, ce qui rend la plupart d'entre elles invisible au client. La fonctionnalité de résilience de connexion dans Entity Framework 6 automatise qu’Échec du processus de nouvelle tentative de requêtes SQL.

La fonctionnalité de résilience de connexion doit être configurée de manière appropriée pour un service de base de données particulière :

1. Il doit connaître les exceptions qui sont susceptibles d’être temporaire. Vous souhaitez réessayer les erreurs provoquées par une perte temporaire de connectivité réseau, pas dû à des bogues de programme, par exemple.
2. Il doit attendre une quantité appropriée de temps entre les tentatives d’une opération ayant échoué. Vous pouvez attendre de plus entre les nouvelles tentatives pour un traitement par lots que vous pouvez le faire pour une page web en ligne dans laquelle un utilisateur est en attente d’une réponse.
3. Il doit réessayer un nombre approprié de fois avant d’abandonner. Vous souhaiterez peut-être recommencer plusieurs fois dans un traitement par lots que vous le feriez dans une application en ligne.

Vous pouvez configurer ces paramètres manuellement à n’importe quel environnement de base de données pris en charge par un fournisseur Entity Framework.

Il vous suffit pour permettre la résilience des connexions est de créer une classe dans votre assembly qui dérive de la `DbConfiguration` classe et, dans cette classe, définissez la stratégie d’exécution de base de données SQL, qui est un autre terme de la stratégie de nouvelle tentative dans Entity Framework.

### <a name="implementing-connection-resiliency"></a>Implémentation de résilience des connexions

1. Téléchargez et ouvrez le [WingtipToys](https://go.microsoft.com/fwlink/?LinkID=389434&amp;clcid=0x409) exemple d’application Web Forms dans Visual Studio.
2. Dans le *logique* dossier de la **WingtipToys** application, ajoutez un fichier de classe nommé *WingtipToysConfiguration.cs*.
3. Remplacez le code existant par celui-ci :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample1.cs)]

Entity Framework exécute automatiquement le code qu’il se trouve dans une classe qui dérive de `DbConfiguration`. Vous pouvez utiliser la `DbConfiguration` classe pour effectuer des tâches de configuration dans le code que vous le feriez dans le cas contraire dans le *Web.config* fichier. Pour plus d’informations, consultez [EntityFramework Configuration basée sur le Code](https://msdn.microsoft.com/data/jj680699).

1. Dans le *logique* dossier, ouvrez le *AddProducts.cs* fichier.
2. Ajouter un `using` instruction pour `System.Data.Entity.Infrastructure` comme indiqué en surbrillance en jaune :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample2.cs?highlight=6)]
3. Ajouter un `catch` bloquer à la `AddProduct` méthode afin que le `RetryLimitExceededException` est enregistré comme mis en surbrillance en jaune :   

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample3.cs?highlight=14-15,17-22)]

En ajoutant le `RetryLimitExceededException` exception, vous pouvez fournir une meilleure journalisation ou afficher un message d’erreur à l’utilisateur où ils peuvent choisir pour recommencer le processus. En interceptant le `RetryLimitExceededException` exception, seules les erreurs susceptibles d’être temporaire sera déjà avoir été en vain plusieurs fois. L’exception réelle retournée à encapsuler dans le `RetryLimitExceededException` exception. En outre, vous avez également ajouté un bloc catch général. Pour plus d’informations sur la `RetryLimitExceededException` exception, consultez [résilience des connexions Entity Framework / logique de nouvelle tentative](https://msdn.microsoft.com/data/dn456835).

## <a name="command-interception"></a>Interception de commande

Maintenant que vous avez activé la stratégie de nouvelle tentative, comment tester pour vérifier qu’elle fonctionne comme prévu ? Il n’est pas facile à forcer une erreur temporaire se produire, en particulier lorsque vous exécutez localement, et il serait particulièrement difficile à intégrer des erreurs temporaires réels dans un test unitaire automatisé. Pour tester la fonctionnalité de résilience de connexion, vous avez besoin d’un moyen pour intercepter les requêtes Entity Framework envoie à SQL Server et remplacez la réponse de SQL Server avec un type d’exception qui est généralement temporaire.

Vous pouvez également utiliser l’interception de requête pour implémenter la méthode recommandée pour les applications cloud : la latence et la réussite ou l’échec de tous les appels externe au journal des services tels que les services de base de données.

Dans cette section du didacticiel, vous allez utiliser Entity Framework [ *fonctionnalité d’interception* ](https://msdn.microsoft.com/data/dn469464) à la fois pour la journalisation et de simulation d’erreurs temporaires.

### <a name="create-a-logging-interface-and-class"></a>Créer une interface de journalisation et de la classe

Une meilleure pratique pour la journalisation consiste à le faire en utilisant un [ `interface` ](https://msdn.microsoft.com/library/ms173156.aspx) au lieu de coder en dur les appels à `System.Diagnostics.Trace` ou une classe de journalisation. Qui rend plus facile de modifier votre mécanisme de journalisation ultérieurement si vous avez besoin de le faire. Par conséquent, dans cette section, vous allez créer l’interface de journalisation et d’une classe pour l’implémenter.

Selon la procédure ci-dessus, vous avez téléchargé et ouvert le **WingtipToys** exemple d’application dans Visual Studio.

1. Créer un dossier dans le **WingtipToys** de projet et nommez-le *journalisation*.
2. Dans le *journalisation* dossier, créez un fichier de classe nommé *ILogger.cs* et remplacez le code par défaut par le code suivant :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample4.cs)]

 L’interface fournit trois niveaux de suivi pour indiquer l’importance relative des journaux et l’autre conçue pour fournir des informations de latence pour les appels de service externe telles que les requêtes de base de données. Les méthodes de journalisation ont des surcharges qui vous permettent de passer d’une exception. Il s’agit afin que les informations sur les exceptions, y compris la pile trace et les exceptions internes sont fiable enregistrées par la classe qui implémente l’interface, au lieu de compter sur une fois l’opération effectuée dans chaque appel de méthode de journalisation dans toute l’application.  
  
 Le `TraceApi` méthodes permettent d’effectuer le suivi de la latence de chaque appel à un service externe telles que de la base de données SQL.
3. Dans le *journalisation* dossier, créez un fichier de classe nommé *Logger.cs* et remplacez le code par défaut par le code suivant :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample5.cs)]

L’implémentation utilise `System.Diagnostics` pour effectuer le suivi. Il s’agit d’une fonctionnalité intégrée de .NET qui permet de facilement générer et utiliser les informations de traçage. Il existe de nombreux &quot;écouteurs&quot; que vous pouvez utiliser avec `System.Diagnostics` le suivi, d’écrire des journaux dans des fichiers, par exemple, ou de les écrire dans le stockage d’objets blob dans Windows Azure. Certaines des options et des liens vers d’autres ressources pour plus d’informations, consultez dans [dépannage de Sites Web Azure Windows dans Visual Studio](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio). Pour ce didacticiel, vous allez uniquement consulter le journal dans Visual Studio **sortie** fenêtre.

Dans une application de production, vous souhaiterez envisager d’utiliser des infrastructures de suivi autre que `System.Diagnostics`et le `ILogger` interface rend relativement facile à basculer vers un mécanisme de suivi différent si vous décidez de le faire.

### <a name="create-interceptor-classes"></a>Créer des classes de l’intercepteur

Ensuite, vous allez créer les classes Entity Framework appelle chaque fois qu’il va envoyer une requête à la base de données, un pour simuler des erreurs temporaires et faire de la journalisation. Ces classes de l’intercepteur doivent dériver de la `DbCommandInterceptor` classe. Dans, vous écrivez des substitutions de méthode qui sont appelées automatiquement lorsque la requête est prête à être exécutée. Dans ces méthodes, vous pouvez examiner ou se connecter à la requête qui est envoyée à la base de données, et vous pouvez modifier la requête avant qu’il est envoyé à la base de données ou retourner un élément à Entity Framework vous-même sans même passer la requête à la base de données.

1. Pour créer la classe de l’intercepteur qui enregistre toutes les requêtes SQL avant d’être envoyée à la base de données, créez un fichier de classe nommé *InterceptorLogging.cs* dans les *logique* dossier et remplacer la valeur par défaut de code avec le code suivant :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample6.cs)]

 Pour les requêtes ayant réussi ou des commandes, ce code écrit un journal d’informations avec les informations de latence. Pour les exceptions, il crée un journal des erreurs.
2. Pour créer la classe de l’intercepteur qui génère des erreurs temporaires factices lorsque vous entrez &quot;lever&quot; dans les **nom** zone de texte dans la page appelée *AdminPage.aspx*, créez une classe fichier nommé *InterceptorTransientErrors.cs* dans les *logique* dossier et remplacer la valeur par défaut de code avec le code suivant :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample7.cs)]

    Ce code substitue uniquement le `ReaderExecuting` méthode, qui est appelée pour les requêtes qui peuvent retourner plusieurs lignes de données. Si vous souhaitez vérifier la résilience des connexions pour les autres types de requêtes, vous pouvez également substituer la `NonQueryExecuting` et `ScalarExecuting` méthodes, comme l’intercepteur de journalisation le fait.  
  
 Ultérieurement, vous allez ouvrir une session en tant que le « Admin » et sélectionnez le **Admin** lien dans la barre de navigation supérieure. Puis, dans le *AdminPage.aspx* page que vous allez ajouter un produit nommé &quot;lever&quot;. Le code crée une exception de base de données SQL factice pour le numéro d’erreur 20, un type connu pour être généralement transitoires. Autres numéros d’erreur actuellement reconnus comme transitoire sont 64 233, 10053, 10054, 10060, 10928, 10929, 40197, 40501 et 40613, mais ceux-ci sont susceptibles de changer dans les nouvelles versions de base de données SQL. Le produit sera renommé en « TransientErrorExample », vous pouvez suivre dans le code de la *InterceptorTransientErrors.cs* fichier.  
  
 Le code retourne l’exception à Entity Framework au lieu de la requête en cours d’exécution et en passant les résultats. L’exception temporaire est retournée *quatre* fois, et reprend ensuite le code à la procédure normale de transmission de la requête à la base de données.

    Étant donné que tout est connecté, vous serez en mesure de voir qu’Entity Framework essaie d’exécuter la requête à quatre reprises avant de réussir et la seule différence dans l’application est qu’il est plus long pour restituer une page de résultats de la requête.  
  
 Le nombre de tentatives de Entity Framework est configurable ; le code spécifie quatre fois car il s’agit de la valeur par défaut pour la stratégie d’exécution de base de données SQL. Si vous modifiez la stratégie d’exécution, vous pouviez également modifier le code qui spécifie le nombre d’erreurs temporaires sont générés. Vous pouvez également modifier le code pour générer des exceptions plus afin que Entity Framework lèvera le `RetryLimitExceededException` exception.
3. Dans *Global.asax*, ajoutez le code suivant à l’aide des instructions :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample8.cs)]
4. Ensuite, ajoutez les lignes en surbrillance à le `Application_Start` méthode :  

    [!code-csharp[Main](aspnet-web-forms-connection-resiliency-and-command-interception/samples/sample9.cs?highlight=17-20)]

Ces lignes de code sont la cause de votre code de l’intercepteur être exécutée à Entity Framework envoie des requêtes à la base de données. Notez qu’étant donné que vous avez créé des classes de l’intercepteur distinct pour la simulation d’une erreur temporaire et la journalisation, vous pouvez indépendamment les activer et les désactiver.   
  
 Vous pouvez ajouter des intercepteurs d’à l’aide de la `DbInterception.Add` méthode n’importe où dans votre code ; ne doit pas nécessairement être dans le `Application_Start` (méthode). Une autre option, si vous n’avez pas ajouter des intercepteurs dans le `Application_Start` méthode consisterait à mettre à jour ou ajouter la classe nommée *WingtipToysConfiguration.cs* et placez le code ci-dessus à la fin du constructeur de la `WingtipToysbConfiguration` classe.

Partout où vous placez ce code, veillez à ne pas être exécuter `DbInterception.Add` de l’intercepteur même plusieurs fois, ou vous obtiendrez les instances supplémentaires de l’intercepteur. Par exemple, si vous ajoutez deux fois l’intercepteur de journalisation, vous verrez deux journaux pour chaque requête SQL.

Intercepteurs sont exécutées dans l’ordre d’enregistrement (l’ordre dans lequel le `DbInterception.Add` méthode est appelée). L’ordre peut concerner selon ce que vous faites de l’intercepteur. Par exemple, un intercepteur peut changer la commande SQL qu’il obtient dans le `CommandText` propriété. Si elle ne modifie pas la commande SQL, l’intercepteur suivant obtient la commande SQL modifiée, pas la commande SQL d’origine.

Vous avez écrit le code d’erreur temporaire simulation d’une manière qui vous permet de provoquer des erreurs temporaires en entrant une valeur différente dans l’interface utilisateur. En guise d’alternative, vous pouvez écrire le code de l’intercepteur pour toujours générer la séquence d’exceptions transitoires sans recherche d’une valeur de paramètre particulier. Vous pouvez ensuite ajouter l’intercepteur uniquement lorsque vous souhaitez générer des erreurs temporaires. Si vous faites cela, toutefois, n’ajoutez pas l’intercepteur jusqu'à ce que la fin de l’initialisation de la base de données. En d’autres termes, effectuez l’opération au moins une base de données comme une requête sur l’un de vos jeux d’entités avant de commencer la génération d’erreurs temporaires. Entity Framework exécute plusieurs requêtes lors de l’initialisation de base de données, et elles ne sont pas exécutées dans une transaction, erreurs lors de l’initialisation risquerait de provoquer le contexte obtenir un état incohérent.

## <a name="test-logging-and-connection-resiliency"></a>Résilience de journalisation et de la connexion de test

1. Dans Visual Studio, appuyez sur **F5** pour exécuter l’application en mode débogage, puis connectez-vous en tant que « Admin », à l’aide de « Pa$ $word » comme mot de passe.
2. Sélectionnez **Admin** à partir de la barre de navigation en haut.
3. Entrez un nouveau produit nommé « Throw » avec le fichier de description, prix et image approprié.
4. Appuyez sur la **ajouter un produit** bouton.  
 Vous remarquerez que le navigateur semble se bloquer pendant quelques secondes lors de l’Entity Framework retente la requête plusieurs fois. La première nouvelle tentative se produit très rapidement, puis l’attente augmente avant chaque nouvelle tentative supplémentaire. Ce processus d’attente plus avant chaque nouvelle tentative est appelée *interruption exponentielle* .
5. Attendez que la page n’est plus atttempting à charger.
6. Arrêtez le projet et examinez le Visual Studio **sortie** fenêtre pour afficher la sortie de traçage. Vous pouvez trouver la **sortie** en sélectionnant **déboguer**  - &gt; **Windows**  - &gt;  **Sortie**. Vous devrez peut-être défiler plusieurs autres journaux générés par votre journal.  
  
 Notez que vous pouvez voir les requêtes SQL réelles envoyées à la base de données. Vous consultez certaines requêtes initiales et les commandes qu’Entity Framework effectue pour la prise en main, la vérification de la table d’historique de version et de migration de base de données.   
    ![Sortie (fenêtre)](aspnet-web-forms-connection-resiliency-and-command-interception/_static/image1.png)   
 Notez que vous ne répétez ce test, sauf si vous arrêtez l’application et le redémarrez. Si vous souhaitez être en mesure de tester résilience des connexions à plusieurs reprises en une seule exécution de l’application, vous pouvez écrire du code pour réinitialiser le compteur d’erreurs dans `InterceptorTransientErrors` .
7. Pour voir la différence la stratégie d’exécution (stratégie de nouvelle tentative) transforme, commentaire le `SetExecutionStrategy` dans la ligne *WingtipToysConfiguration.cs* de fichiers dans le *logique* dossier, exécutez la **Admin**  à nouveau la page en mode débogage et ajouter le produit nommé &quot;lever&quot; à nouveau.  
  
 Cette fois le débogueur s’arrête sur la première exception générée immédiatement lorsqu’il tente d’exécuter la requête de la première fois.  
    ![Débogage - afficher les détails](aspnet-web-forms-connection-resiliency-and-command-interception/_static/image2.png)
8. Ne pas commenter le `SetExecutionStrategy` de ligne dans le *WingtipToysConfiguration.cs* fichier.

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez vu comment modifier un exemple d’application Web Forms pour prendre en charge la résilience des connexions et l’interception de commande.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez consulté la résilience des connexions et l’interception de commande dans ASP.NET Web Forms, consultez la rubrique de Web Forms ASP.NET [des méthodes asynchrones dans ASP.NET 4.5](../performance-and-caching/using-asynchronous-methods-in-aspnet-45.md). La rubrique, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET asynchrone à l’aide de Visual Studio.
