---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/aspnet-error-handling
title: Gestion des erreurs de ASP.NET | Documents Microsoft
author: Erikre
description: "Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 09/08/2014
ms.topic: article
ms.assetid: 423498f7-1a4b-44a1-b342-5f39d0bcf94f
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/aspnet-error-handling
msc.type: authoredcontent
ms.openlocfilehash: c5ec43ac78be4a9452ebaa6495a6883506ac162f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-error-handling"></a>Gestion des erreurs de ASP.NET
====================
Par [Erik Reitan](https://github.com/Erikre)

[Télécharger Wingtip Toys exemple de projet (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) ou [télécharger des livres (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

> Cette série de didacticiels, vous allez apprendre les principes fondamentaux de la création d’une application Web Forms ASP.NET à l’aide de ASP.NET 4.5 et Microsoft Visual Studio Express 2013 pour le Web. Un Visual Studio 2013 [projet avec le code source c#](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) est disponible pour accompagner cette série de didacticiels.


Dans ce didacticiel, vous allez modifier l’exemple d’application Wingtip Toys pour inclure la gestion des erreurs et journalisation des erreurs. Gestion des erreurs permet à l’application gérer les erreurs normalement et d’afficher les messages d’erreur en conséquence. Journalisation des erreurs permettent de rechercher et corriger les erreurs qui se sont produites. Ce didacticiel s’appuie sur le didacticiel précédent, « URL de routage » et fait partie de la série de didacticiels Wingtip Toys.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre :

- Comment ajouter global Gestion des erreurs à la configuration de l’application.
- Comment ajouter à l’application, page, les niveaux de code de gestion des erreurs.
- Comment enregistrer les erreurs pour consultation ultérieure.
- Comment afficher les messages d’erreur ne compromettent pas la sécurité.
- Comment implémenter la journalisation des erreurs de Modules de journalisation de l’erreur et les gestionnaires (ELMAH).

## <a name="overview"></a>Vue d'ensemble

Les applications ASP.NET doivent être en mesure de gérer les erreurs qui se produisent pendant l’exécution de façon cohérente. ASP.NET utilise le common language runtime (CLR), qui offre un moyen d’avertir les applications des erreurs de façon uniforme. Lorsqu’une erreur se produit, une exception est levée. Une exception est toute erreur, condition ou une application rencontre un comportement inattendu.

Dans le .NET Framework, une exception est un objet qui hérite de la classe `System.Exception`. Une exception est levée à partir d'une partie du code où un problème s'est produit. L’exception remonte la pile des appels vers un emplacement où l’application fournit du code pour gérer l’exception. Si l’application ne gère pas l’exception, le navigateur est forcé pour afficher les détails de l’erreur.

Comme meilleure pratique, gérer les erreurs dans le niveau de code dans `Try` / `Catch` / `Finally` blocs au sein de votre code. Essayez de placer ces blocs afin que l’utilisateur peut résoudre des problèmes dans le contexte dans lequel ils se produisent. Si les blocs de gestion des erreurs sont trop éloigné de l’endroit où l’erreur s’est produite, il devient plus difficile de fournir aux utilisateurs les informations nécessaires pour résoudre le problème.

### <a name="exception-class"></a>Exception (classe)

La classe d’Exception est la classe de base à partir de laquelle hériteront des exceptions. La plupart des objets d’exception sont des instances d’une classe dérivée de la classe d’Exception, comme le `SystemException` (classe), le `IndexOutOfRangeException` (classe), ou la `ArgumentNullException` classe. La classe Exception comporte des propriétés, telles que la `StackTrace` propriété, le `InnerException` propriété et le `Message` propriété, qui fournissent des informations spécifiques sur l’erreur qui s’est produite.

### <a name="exception-inheritance-hierarchy"></a>Hiérarchie d’héritage (exception)

Le runtime dispose d’un ensemble de base d’exceptions dérivant de la `SystemException` classe le runtime lève lorsqu’une exception est détectée. La plupart des classes qui héritent de la classe d’Exception, telles que la `IndexOutOfRangeException` classe et le `ArgumentNullException` de classe, n’implémentez pas de membres supplémentaires. Par conséquent, vous peuvent trouver les informations les plus importantes pour une exception dans la hiérarchie des exceptions, le nom de l’exception et les informations contenues dans l’exception.

### <a name="exception-handling-hierarchy"></a>Hiérarchie de la gestion des exceptions

Dans une application ASP.NET Web Forms, les exceptions peuvent être traitées selon une hiérarchie de gestion spécifique. Une exception peut être gérée aux niveaux suivants :

- Niveau de l’application
- Niveau de la page
- Au niveau du code

Lorsqu’une application gère les exceptions, des informations supplémentaires sur l’exception qui est héritée de la classe d’Exception peuvent souvent être récupérées et affichées à l’utilisateur. Outre l’application, page et niveau de code, vous pouvez également gérer des exceptions au niveau du module HTTP et à l’aide d’un gestionnaire personnalisé d’IIS.

### <a name="application-level-error-handling"></a>Gestion des erreurs de niveau application

Vous pouvez gérer des erreurs par défaut au niveau de l’application en modifiant la configuration de votre application ou en ajoutant un `Application_Error` gestionnaire dans le *Global.asax* fichier de votre application.

Vous pouvez gérer des erreurs par défaut et les erreurs HTTP en ajoutant un `customErrors` section à la *Web.config* fichier. Le `customErrors` section vous permet de spécifier une page par défaut vers laquelle les utilisateurs seront redirigés lorsqu’une erreur se produit. Il vous permet également de spécifier des pages individuelles pour les erreurs de code d’état spécifique.

[!code-xml[Main](aspnet-error-handling/samples/sample1.xml?highlight=3-5)]

Malheureusement, lorsque vous utilisez la configuration pour rediriger l’utilisateur vers une autre page, vous n’avez pas les détails de l’erreur qui s’est produite.

Toutefois, vous pouvez intercepter les erreurs qui se produisent n’importe où dans votre application en ajoutant du code à la `Application_Error` gestionnaire dans le *Global.asax* fichier.

[!code-csharp[Main](aspnet-error-handling/samples/sample2.cs)]

### <a name="page-level-error-event-handling"></a>Gestion des événements de niveau page erreur

Un gestionnaire de niveau page retourne l’utilisateur à la page où l’erreur s’est produite, mais étant donné que les instances de contrôles ne sont pas conservées, il cesse de faire quoi que ce soit sur la page. Pour fournir des détails de l’erreur à l’utilisateur de l’application, vous devez spécifiquement écrire les détails de l’erreur à la page.

Vous utiliserez généralement un gestionnaire d’erreurs de niveau page pour consigner les erreurs non gérées ou à l’utilisateur vers une page qui peut afficher des informations utiles.

Cet exemple de code montre un gestionnaire pour l’événement d’erreur dans une page Web ASP.NET. Ce gestionnaire intercepte toutes les exceptions qui ne sont pas déjà gérées dans `try` / `catch` bloque dans la page.

[!code-csharp[Main](aspnet-error-handling/samples/sample3.cs)]

Une fois que vous gérez une erreur, vous devez le désactiver en appelant le `ClearError` méthode de l’objet serveur (`HttpServerUtility` classe), dans le cas contraire, vous verrez une erreur s’est produite précédemment.

### <a name="code-level-error-handling"></a>Gestion des erreurs au niveau du code

L’instruction try-catch se compose d’un bloc try suivi d’un ou plusieurs clauses catch, qui spécifient des gestionnaires pour différentes exceptions. Lorsqu’une exception est levée, le common language runtime (CLR) recherche l’instruction catch qui gère cette exception. Si la méthode en cours d’exécution ne contient pas un bloc catch, le CLR examine la méthode qui a appelé la méthode actuelle et ainsi de suite, la pile des appels. Si aucun bloc catch n’est trouvé, le CLR affiche un message d’exception non gérée à l’utilisateur et arrête l’exécution du programme.

L’exemple de code suivant illustre une manière courante de l’utilisation de `try` / `catch` / `finally` pour gérer les erreurs.

[!code-csharp[Main](aspnet-error-handling/samples/sample4.cs)]

Dans le code ci-dessus, le bloc try contient le code qui doit être protégée contre une possible exception. Le bloc est exécuté jusqu'à ce que le bloc est terminé avec succès ou une exception est levée. Si un `FileNotFoundException` exception ou une `IOException` une exception se produit, l’exécution est transférée vers une autre page. Ensuite, le code contenu dans le bloc finally est exécuté, si une erreur s’est produite ou non.

## <a name="adding-error-logging-support"></a>Ajout de prise en charge de la journalisation des erreurs

Avant d’ajouter la gestion des erreurs à l’exemple d’application Wingtip Toys, vous allez ajouter la prise en charge de la journalisation des erreurs en ajoutant une `ExceptionUtility` classe le *logique* dossier. Ainsi, chaque fois que l’application gère une erreur, les détails de l’erreur figurera dans le fichier journal des erreurs.

1. Avec le bouton droit le *logique* dossier, puis sélectionnez **ajouter**  - &gt; **un nouvel élément**.   
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Sélectionnez le **Visual C#**  - &gt; **Code** groupe de modèles sur la gauche. Ensuite, sélectionnez **classe**à partir du milieu de liste et nommez-le **ExceptionUtility.cs**.
3. Sélectionnez **Ajouter**. Le nouveau fichier de classe s’affiche.
4. Remplacez le code existant par le code ci-dessous :  

    [!code-csharp[Main](aspnet-error-handling/samples/sample5.cs)]

Lorsqu’une exception se produit, l’exception peut être écrites dans un fichier de journal d’exception en appelant le `LogException` (méthode). Cette méthode prend deux paramètres, l’objet exception et une chaîne contenant les détails de la source de l’exception. Le journal d’exception est écrit dans le *ErrorLog.txt* de fichiers dans le *application\_données* dossier.

### <a name="adding-an-error-page"></a>Ajout d’une Page d’erreur

Dans l’exemple d’application Wingtip Toys, une page servira pour afficher les erreurs. La page d’erreurs est conçue pour afficher un message d’erreur sécurisé aux utilisateurs du site. Toutefois, si l’utilisateur est un développeur qui apporte une requête HTTP qui est prise en charge localement sur l’ordinateur où se trouve le code, les détails d’erreur supplémentaires seront affichera sur la page d’erreur.

1. Cliquez sur le nom du projet (**Wingtip Toys**) dans **l’Explorateur de solutions** et sélectionnez **ajouter**  - &gt; **unnouvelélément**.   
 La boîte de dialogue **Ajouter un nouvel élément** s’affiche.
2. Sélectionnez le **Visual C#**  - &gt; **Web** groupe de modèles sur la gauche. Dans la liste du milieu, sélectionnez **Web Form avec Page maître**et nommez-le **Pageerreur.aspx**.
3. Cliquez sur **Ajouter**.
4. Sélectionnez le *Site.Master* de fichiers en tant que la page maître, puis choisissez **OK**.
5. Remplacez la balise existante avec les éléments suivants :   

    [!code-aspx[Main](aspnet-error-handling/samples/sample6.aspx)]
6. Remplacez le code existant du code-behind (*ErrorPage.aspx.cs*) afin qu’il apparaisse comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample7.cs)]

Lorsque la page d’erreur est affichée, le `Page_Load` Gestionnaire d’événements est exécuté. Dans la `Page_Load` gestionnaire, l’emplacement d’où l’erreur a été gérée pour la première est déterminé. Ensuite, la dernière erreur qui s’est produite est déterminée par l’appel de la `GetLastError` méthode de l’objet serveur. Si l’exception n’existe plus, une exception générique est créée. Ensuite, si la requête HTTP a été effectuée localement, tous les détails de l’erreur sont affichés. Dans ce cas, seul l’ordinateur local qui exécute l’application web s’affiche les détails de l’erreur. Après avoir affiché les informations d’erreur, l’erreur est ajouté au fichier journal et l’erreur est désactivée à partir du serveur.

### <a name="displaying-unhandled-error-messages-for-the-application"></a>Affichage des Messages d’erreur non gérée de l’Application

En ajoutant un `customErrors` section à la *Web.config* , vous pouvez rapidement descripteur de fichier simples erreurs qui se produisent dans l’ensemble de l’application. Vous pouvez également spécifier comment gérer les erreurs en fonction de leur valeur de code d’état, tels que 404 - Fichier introuvable.

#### <a name="update-the-configuration"></a>Mettre à jour la Configuration

Mettre à jour la configuration en ajoutant un `customErrors` section à la *Web.config* fichier.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *Web.config* fichier à la racine de l’exemple d’application Wingtip Toys.
2. Ajouter le `customErrors` section à la *Web.config* fichier inclus dans le `<system.web>` nœud comme suit :   

    [!code-xml[Main](aspnet-error-handling/samples/sample8.xml?highlight=3-5)]
3. Enregistrer le *Web.config* fichier.

Le `customErrors` section spécifie le mode, qui est défini sur « Sur ». Il spécifie également le `defaultRedirect`, ce qui indique quelle page à atteindre lors d’une erreur se produit à l’application. En outre, vous avez ajouté un élément de l’erreur spécifique qui spécifie comment gérer une erreur 404 lorsqu’une page est introuvable. Plus loin dans ce didacticiel, vous allez ajouter supplémentaires gestion des erreurs qui capture les détails d’une erreur au niveau de l’application.

#### <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application maintenant pour voir les itinéraires de mise à jour.

1. Appuyez sur **F5** pour exécuter l’exemple d’application Wingtip Toys.  
 Le navigateur s’ouvre et affiche le *Default.aspx* page.
2. Entrez l’URL suivante dans le navigateur (veillez à utiliser **votre** numéro de port) :  
    `https://localhost:44300/NoPage.aspx`
3. Examinez le *Pageerreur.aspx* affiché dans le navigateur. 

    ![Gestion des erreurs de ASP.NET – erreur Page introuvable](aspnet-error-handling/_static/image1.png)

Lorsque vous demandez la *NoPage.aspx* page qui n’existe pas, la page d’erreur affiche le message d’erreur et les informations d’erreur détaillées si des détails supplémentaires sont disponibles. Toutefois, si l’utilisateur a demandé une page inexistante à partir d’un emplacement distant, la page d’erreur uniquement affiche le message d’erreur en rouge.

### <a name="including-an-exception-for-testing-purposes"></a>Y compris une Exception pour les tests

Pour vérifier le fonctionne de votre application lorsqu’une erreur se produit, vous pouvez créer délibérément des conditions d’erreur dans ASP.NET. L’exemple d’application Wingtip Toys, lève une exception de test lors de la charge de la page par défaut pour voir ce qui se passe.

1. Ouvrez le fichier code-behind de la *Default.aspx* page dans Visual Studio.   
 Le *Default.aspx.cs* page code-behind s’affichera.
2. Dans la `Page_Load` gestionnaire, ajoutez le code afin que le gestionnaire s’affiche comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample9.cs?highlight=3-4)]

Il est possible de créer différents types d’exceptions. Dans le code ci-dessus, vous créez un `InvalidOperationException` lors de la *Default.aspx* page est chargée.

#### <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application pour voir comment l’application gère l’exception.

1. Appuyez sur **CTRL + F5** pour exécuter l’exemple d’application Wingtip Toys.  
 L’application lève l’exception InvalidOperationException. 

    > [!NOTE] 
    > 
    > Vous devez appuyer sur **CTRL + F5** pour afficher la page sans rupture dans le code pour afficher la source de l’erreur dans Visual Studio.
2. Examinez le *Pageerreur.aspx* affiché dans le navigateur. 

    ![Gestion des erreurs ASP.NET - Page d’erreur](aspnet-error-handling/_static/image2.png)

Comme vous pouvez le voir dans les détails de l’erreur, l’exception a été interceptée par le `customError` section dans le *Web.config* fichier.

### <a name="adding-application-level-error-handling"></a>Ajout de la gestion des erreurs de niveau Application

Au lieu de l’interruption de l’exception à l’aide de la `customErrors` section dans le *Web.config* file, où vous bénéficiez peu d’informations sur l’exception, vous pouvez intercepter l’erreur au niveau de l’application et récupérer les détails de l’erreur.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *Global.asax.cs* fichier.
2. Ajouter un **Application\_erreur** gestionnaire afin qu’il apparaisse comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample10.cs)]

Lorsqu’une erreur se produit dans l’application, le `Application_Error` gestionnaire est appelé. Dans ce gestionnaire, la dernière exception est récupérée et passé en revue. Si l’exception a été non prise en charge et l’exception contient les détails de l’exception interne (autrement dit, `InnerException` n’est pas null), l’application transfère l’exécution à la page d’erreur où sont affichés les détails de l’exception.

#### <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application pour afficher les détails d’erreur supplémentaires fournies par la gestion de l’exception au niveau de l’application.

1. Appuyez sur **CTRL + F5** pour exécuter l’exemple d’application Wingtip Toys.  
 L’application lève le `InvalidOperationException` .
2. Examinez le *Pageerreur.aspx* affiché dans le navigateur. 

    ![Gestion des erreurs ASP.NET - erreur de niveau Application](aspnet-error-handling/_static/image3.png)

### <a name="adding-page-level-error-handling"></a>Ajout de la gestion des erreurs de niveau Page :

Vous pouvez ajouter la gestion des erreurs de niveau de la page à une page à l’aide d’ajout d’un `ErrorPage` attribut le `@Page` directive de la page, ou en ajoutant un `Page_Error` Gestionnaire d’événements pour le code-behind d’une page. Dans cette section, vous allez ajouter un `Page_Error` Gestionnaire d’événements qui transfère l’exécution à la *Pageerreur.aspx* page.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *Default.aspx.cs* fichier.
2. Ajouter un `Page_Error` gestionnaire afin que le code-behind apparaît comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample11.cs?highlight=18-30)]

Lorsqu’une erreur se produit dans la page, le `Page_Error` Gestionnaire d’événements est appelé. Dans ce gestionnaire, la dernière exception est récupérée et passé en revue. Si un `InvalidOperationException` se produit, le `Page_Error` Gestionnaire d’événements transfère l’exécution à la page d’erreur où sont affichés les détails de l’exception.

#### <a name="running-the-application"></a>Exécution de l'application

Vous pouvez exécuter l’application maintenant pour voir les itinéraires de mise à jour.

1. Appuyez sur **CTRL + F5** pour exécuter l’exemple d’application Wingtip Toys.  
 L’application lève le `InvalidOperationException` .
2. Examinez le *Pageerreur.aspx* affiché dans le navigateur. 

    ![Gestion des erreurs ASP.NET - erreur de niveau Page](aspnet-error-handling/_static/image4.png)
3. Fermez la fenêtre du navigateur.

### <a name="removing-the-exception-used-for-testing"></a>Suppression de l’Exception utilisée pour le test

Pour permettre l’exemple d’application Wingtip Toys à fonctionner sans lever d’exception que vous avez ajouté précédemment dans ce didacticiel, supprimez l’exception.

1. Ouvrez le fichier code-behind de la *Default.aspx* page.
2. Dans la `Page_Load` gestionnaire, supprimez le code qui lève l’exception afin que le gestionnaire s’affiche comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample12.cs)]

### <a name="adding-code-level-error-logging"></a>Ajout de la journalisation des erreurs au niveau du Code

Comme mentionné précédemment dans ce didacticiel, vous pouvez ajouter des instructions try/catch pour tenter d’exécuter une section de code et de gérer la première erreur se produit. Dans cet exemple, vous allez écrire uniquement les détails de l’erreur dans le fichier de journal des erreurs afin que l’erreur peut être consulté ultérieurement.

1. Dans **l’Explorateur de solutions**, dans le *logique* dossier, recherchez et ouvrez le *PayPalFunctions.cs* fichier.
2. Mise à jour le `HttpCall` méthode afin que le code s’affiche comme suit :   

    [!code-csharp[Main](aspnet-error-handling/samples/sample13.cs?highlight=20,22-23)]

Le code ci-dessus appelle le `LogException` méthode contenue dans la `ExceptionUtility` classe. Vous avez ajouté le *ExceptionUtility.cs* fichier de classe pour le *logique* dossier précédemment dans ce didacticiel. Le `LogException` méthode accepte deux paramètres. Le premier paramètre est l’objet exception. Le deuxième paramètre est une chaîne utilisée pour identifier la source de l’erreur.

### <a name="inspecting-the-error-logging-information"></a>Examiner les informations de journalisation des erreurs

Comme mentionné précédemment, vous pouvez utiliser le journal des erreurs pour déterminer quelles erreurs dans votre application doivent être corrigés en premier. Bien entendu, seules les erreurs qui ont été interceptées et écrites dans le journal des erreurs seront enregistrés.

1. Dans **l’Explorateur de solutions**, recherchez et ouvrez le *ErrorLog.txt* de fichiers dans le *application\_données* dossier.   
 Vous devrez peut-être sélectionner la »**afficher tous les fichiers**» option ou la «**Actualiser**» option à partir du haut de **l’Explorateur de solutions** pour voir le *ErrorLog.txt*fichier.
2. Passez en revue le journal des erreurs affiché dans Visual Studio : 

    ![Gestion des erreurs ASP.NET - ErrorLog.txt](aspnet-error-handling/_static/image5.png)

### <a name="safe-error-messages"></a>Messages d’erreur sécurisés

Il s’agit de **important de noter** que lorsque votre application affiche des messages d’erreur, il ne doit pas donner d’informations un utilisateur malveillant peut s’avérer utile attaquer votre application. Par exemple, si votre application ne parvient pas à écrire une base de données, il ne doit pas afficher un message d’erreur qui inclut le nom d’utilisateur qu’il utilise. Pour cette raison, un message d’erreur générique en rouge s’affiche à l’utilisateur. Tous les détails d’erreur supplémentaires sont affichées uniquement pour le développeur sur l’ordinateur local.

## <a name="using-elmah"></a>À l’aide de ELMAH

ELMAH (gestionnaires et les Modules de journalisation de l’erreur) est une fonctionnalité de journalisation des erreurs que vous connecter à votre application ASP.NET sous forme de package NuGet. ELMAH offre les fonctionnalités suivantes :

- Journalisation des exceptions non gérées.
- Une page web pour afficher la totalité du journal d’exceptions non gérées recodées.
- Une page web pour afficher les détails de chaque ouvert une exception.
- Un message de notification de chaque erreur au moment où qu'il se produit.
- Un flux RSS des 15 dernières erreurs à partir du journal.

Avant de pouvoir travailler avec ELMAH, vous devez l’installer. Il est facile à l’aide de la *NuGet* programme d’installation du package. Comme mentionné plus haut dans cette série de didacticiels, NuGet est une extension Visual Studio qui facilite l’installation et mettre à jour les bibliothèques open source et les outils de Visual Studio.

1. Dans Visual Studio, à partir de la **outils** menu, sélectionnez **Gestionnaire de Package de bibliothèque**  - &gt; **gérer les Packages NuGet pour la Solution**. 

    ![Gestion d’erreurs ASP.NET - gérer les Packages NuGet pour la Solution](aspnet-error-handling/_static/image6.png)
2. Le **gérer les Packages NuGet** boîte de dialogue s’affiche dans Visual Studio.
3. Dans le **gérer les Packages NuGet** boîte de dialogue, développez **Online** sur la gauche, puis sélectionnez **nuget.org**. Ensuite, rechercher et installer le **ELMAH** package à partir de la liste des packages disponibles en ligne. 

    ![Gestion des erreurs ASP.NET - Package NuGet de ELMA](aspnet-error-handling/_static/image7.png)
4. Vous devez disposer d’une connexion internet pour télécharger le package.
5. Dans le **sélectionner les projets** boîte de dialogue zone, assurez-vous que le **WingtipToys** sélection est sélectionné, puis cliquez sur **OK**. 

    ![Gestion d’erreurs ASP.NET - sélectionner les projets](aspnet-error-handling/_static/image8.png)
6. Cliquez sur **fermer** dans **gérer les Packages NuGet** boîte de dialogue si nécessaire.
7. Si Visual Studio requiert que vous rechargez les fichiers ouverts, sélectionnez «**Oui pour tout**».
8. Le package ELMAH ajoute des entrées pour lui-même dans le *Web.config* fichier à la racine de votre projet. Si Visual Studio vous demande si vous souhaitez recharger modifié *Web.config* de fichiers, cliquez sur **Oui**.

ELMAH est maintenant prêt à stocker les erreurs non gérées qui se produisent.

### <a name="viewing-the-elmah-log"></a>Afficher le journal ELMAH

Afficher le journal ELMAH est facile, mais tout d’abord, vous allez créer une exception non gérée qui sera enregistrée dans le journal ELMAH.

1. Appuyez sur **CTRL + F5** pour exécuter l’exemple d’application Wingtip Toys.
2. Pour écrire une exception non gérée dans le journal ELMAH, naviguer dans votre navigateur à l’URL suivante (à l’aide de votre numéro de port) :  
    `https://localhost:44300/NoPage.aspx`La page d’erreur s’affichera.
3. Pour afficher le journal ELMAH, naviguer dans votre navigateur à l’URL suivante (à l’aide de votre numéro de port) :  
    `https://localhost:44300/elmah.axd`

    ![Gestion des erreurs ASP.NET - journal des erreurs ELMAH](aspnet-error-handling/_static/image9.png)

## <a name="summary"></a>Récapitulatif

Dans ce didacticiel, vous avez appris sur la gestion des erreurs au niveau de l’application, le niveau de la page et le niveau de code. Vous avez également appris à consigner les erreurs gérées et non gérées pour consultation ultérieure. Vous avez ajouté l’utilitaire ELMAH pour fournir la journalisation de l’exception et la notification à votre application à l’aide de NuGet. En outre, vous avez appris sur l’importance des messages d’erreur de sécurité.

## <a name="tutorial-series-conclusion"></a>Conclusion de la série de didacticiels

*Merci de suivre. J’espère que cet ensemble de didacticiels vous a permis de vous familiariser avec ASP.NET Web Forms. Si vous avez besoin de plus d’informations sur les fonctionnalités de Web Forms disponibles dans ASP.NET 4.5 et Visual Studio 2013, consultez* [ *ASP.NET et Web Tools pour Visual Studio 2013 Release Notes* ](../../../../visual-studio/overview/2013/release-notes.md)  *. En outre, veillez à examiner le didacticiel mentionné dans le* ***étapes *** section et defintely essaient le* [ *gratuite de Azure* ](https://azure.microsoft.com/pricing/free-trial/)*.*

![Nous vous remercions - Erik](aspnet-error-handling/_static/image10.png)  

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le déploiement de votre application web sur Microsoft Azure, consultez [déployer un Secure ASP.NET Web Forms application avec l’appartenance, OAuth et base de données SQL sur un Site Web de Azure](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/).

## <a name="free-trial"></a>Version d’évaluation gratuite

[Microsoft Azure - version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)  
 Publication de votre site Web sur Microsoft Azure enregistrer la durée, maintenance et les coûts. C’est un processus rapide au déploiement de votre application web sur Azure. Lorsque vous avez besoin gérer et surveiller votre application web, Azure offre une variété d’outils et de services. Gérer les données, le trafic, identité, des sauvegardes, la messagerie, média et les performances dans Azure. Et, tout ceci est fournie dans une approche très économique.

## <a name="additional-resources"></a>Ressources supplémentaires

[Journalisation des détails de l’erreur avec le contrôle d’état ASP.NET](../../older-versions-getting-started/deploying-web-site-projects/logging-error-details-with-asp-net-health-monitoring-cs.md)   
[ELMAH](https://code.google.com/p/elmah/)

## <a name="acknowledgements"></a>Remerciements

J’aimerais remercier les personnes suivantes qui a effectué des contributions significatives pour le contenu de cette série de didacticiels :

- [Alberto Poblacion, MVP &amp; MCT, Spain](https://mvp.microsoft.com/mvp/Alberto%20Poblacion%20Bolano-36772)
- [Alex Thissen, pays-bas](http://blog.alexthissen.nl/) (twitter : [ @alexthissen ](http://twitter.com/alexthissen))
- [Andre Tournier, États-Unis](http://andret503.wordpress.com/)
- Apurva Joshi, Microsoft
- [Bojan Vrhovnik, Slovenia](http://twitter.com/bvrhovnik)
- [Bruno Sonnino, Brésil](http://msmvps.com/blogs/bsonnino) (twitter : [ @bsonnino ](http://twitter.com/bsonnino))
- [Carlos dos Santos, Brazil](http://www.carloscds.net/)
- [Dave Campbell, États-Unis](http://www.wynapse.com/) (twitter : [ @windowsdevnews ](http://twitter.com/windowsdevnews))
- [Jon Galloway, Microsoft](https://weblogs.asp.net/jgalloway) (twitter : [ @jongalloway ](http://twitter.com/jongalloway))
- [Des dièses de Michael, États-Unis](http://www.930solutions.com/) (twitter : [ @mrsharps ](http://twitter.com/mrsharps))
- Protocole de Mike
- [Les vendeurs Mitchel, États-Unis d’Amérique](http://www.mitchelsellers.com/) (twitter : [ @MitchelSellers ](http://twitter.com/MitchelSellers))
- [Paul Cociuba, Microsoft](http://linqto.me/Links/pcociuba)
- [Paulo Morgado, Portugal](http://paulomorgado.net/)
- [Pranav Rastogi, Microsoft](https://blogs.msdn.com/b/pranav_rastogi)
- [Tim Ammann, Microsoft](https://blogs.iis.net/timamm/default.aspx)
- [Tom Dykstra, Microsoft](https://blogs.msdn.com/aspnetue)

## <a name="community-contributions"></a>Contributions de la Communauté

- Graham Mendick ([@grahammendick](http://twitter.com/grahammendick))  
 Visual Studio 2012 liés l’exemple de code sur MSDN : [Navigation Wingtip Toys](https://code.msdn.microsoft.com/Navigation-Wingtip-Toys-5f0daba2)
- James Chaney ([jchaney@agvance.net](mailto:jchaney@agvance.net))  
 Visual Studio 2012 liés l’exemple de code sur MSDN : [série de didacticiel ASP.NET 4.5 Web Forms dans Visual Basic](https://code.msdn.microsoft.com/ASPNET-45-Web-Forms-f37f0f63)
- Andrielle Azevedo - collaborateur assistance technique de Microsoft (twitter : @driazevedo)  
 Visual Studio 2012 traduction : [Iniciando com e ASP.NET Web Forms 4.5 - entendue 1 - Introdução Visão Geral](https://andrielleazevedo.wordpress.com/2013/01/24/iniciando-com-asp-net-web-forms-4-5-introducao-e-visao-geral/)

>[!div class="step-by-step"]
[Précédent](url-routing.md)
