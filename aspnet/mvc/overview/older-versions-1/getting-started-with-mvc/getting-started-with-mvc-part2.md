---
uid: mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part2
title: "Ajout d’un contrôleur | Documents Microsoft"
author: shanselman
description: "Une version mise à jour si ce didacticiel est disponible ici à l’aide de Visual Studio 2013. Le nouveau didacticiel utilise ASP.NET MVC 5, qui fournit de nombreuses améliorations de t..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 08/14/2010
ms.topic: article
ms.assetid: ff03dcc0-da97-458d-838f-0823e7482642
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/getting-started-with-mvc/getting-started-with-mvc-part2
msc.type: authoredcontent
ms.openlocfilehash: 93a362cf83d39b29fcba3f2dee0c28257805a89e
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="adding-a-controller"></a>Ajour d’un contrôleur
====================
par [Scott Hanselman](https://github.com/shanselman)

> > [!NOTE]
> > Une version mise à jour si ce didacticiel est disponible [ici](../../getting-started/introduction/getting-started.md) à l’aide de [Visual Studio 2013](https://www.microsoft.com/visualstudio/eng/2013-downloads). Le nouveau didacticiel utilise ASP.NET MVC 5, qui fournit de nombreuses améliorations de ce didacticiel.
> 
> 
> Il s’agit d’un didacticiel débutant qui présente les notions de base d’ASP.NET MVC. Vous allez créer une application web simple qui lit et écrit à partir d’une base de données. Visitez le [centre d’apprentissage ASP.NET MVC](../../../index.md) pour rechercher d’autres ASP.NET MVC didacticiels et exemples.


MVC représente le modèle, vue, du contrôleur. MVC est un modèle pour le développement d’applications de telle sorte que chaque partie possède une responsabilité est différente d’un autre.

- Modèle : Les données de votre application
- Vues : Les fichiers de modèle de votre application utilisera pour générer dynamiquement des réponses HTML.
- Contrôleurs : Classes qui gèrent les demandes d’URL entrantes à l’application, récupérer des données de modèle, puis spécifiez les modèles d’affichage qui restituent une réponse au client

Nous allons être couvrant tous ces concepts dans ce didacticiel et vous montrent comment les utiliser pour générer une application.

Nous allons créer un nouveau contrôleur en double-cliquant sur le dossier contrôleurs dans l’Explorateur de solutions et en sélectionnant Ajouter un contrôleur.

[![AddControllerRightClick](getting-started-with-mvc-part2/_static/image2.png)](getting-started-with-mvc-part2/_static/image1.png)

Nom de votre nouveau contrôleur « HelloWorldController » et cliquez sur Ajouter.

[![Boîte de dialogue contrôleur ajouter](getting-started-with-mvc-part2/_static/image4.png)](getting-started-with-mvc-part2/_static/image3.png)

Notez que dans l’Explorateur de solutions sur la droite un nouveau fichier a été créé pour vous appelé HelloWorldController.cs et que ce fichier est maintenant ouvert dans le **IDE**.

[![HelloWorldControllerCode](getting-started-with-mvc-part2/_static/image6.png)](getting-started-with-mvc-part2/_static/image5.png)

Créez deux nouvelles méthodes qui ressemblent à ceci à l’intérieur de votre classe publique HelloWorldController. Nous allons retourne une chaîne HTML directement à partir de notre contrôleur comme exemple.

[!code-csharp[Main](getting-started-with-mvc-part2/samples/sample1.cs)]

Votre contrôleur est nommé HelloWorldController et votre nouvelle méthode est appelée l’Index. Exécuter votre application, exactement comme précédemment (cliquez sur le bouton lecture ou appuyez sur F5 pour cela). Une fois que votre navigateur ait démarré, modifiez le chemin d’accès dans la barre d’adresse pour `http://localhost:xx/HelloWorld` où xx est le numéro de votre ordinateur, tout ce qui a choisi. Maintenant, votre navigateur doit ressembler à la capture d’écran ci-dessous. Dans notre méthode ci-dessus nous retourné une chaîne passée à une méthode appelée « Contenu ». Nous dit que le système ne renvoie que du code HTML, et il l’a fait !

ASP.NET MVC appelle différentes classes de contrôleur (et les différentes méthodes d’Action au sein de celles-ci) en fonction de l’URL entrante. La logique de mappage par défaut utilisée par ASP.NET MVC utilise un format comme suit pour contrôler le code est exécuté :

/ [Controller] / [ActionName] / [paramètres]

La première partie de l’URL détermine la classe de contrôleur à exécuter. Par conséquent, /HelloWorld mappe à la classe HelloWorldController. La deuxième partie de l’URL détermine la méthode d’Action sur la classe à exécuter. Par conséquent, /HelloWorld/Index risque d’entraîner la méthode Index() de la classe HelloWorldcontroller à exécuter. Notez que nous avons dû à visiter /HelloWorld ci-dessus et la méthode de Qu'index a été impliquée. Il s’agit d’une méthode nommée « Index » étant la méthode par défaut qui sera appelée sur un contrôleur s’il n’est pas explicitement spécifié.

[![Mon action par défaut](getting-started-with-mvc-part2/_static/image8.png)](getting-started-with-mvc-part2/_static/image7.png)

Maintenant, nous allons visitez `http://localhost:xx/HelloWorld/Welcome.` maintenant notre méthode de bienvenue a exécuté et retourné de sa chaîne au format HTML.

Là encore, / [Controller] / [ActionName] / [paramètres] contrôleur est donc HelloWorld et bienvenue est la méthode dans ce cas. Nous n’avons pas encore terminé les paramètres.

[![C’est la méthode d’action de bienvenue](getting-started-with-mvc-part2/_static/image10.png)](getting-started-with-mvc-part2/_static/image9.png)

Nous allons légèrement modifier notre exemple afin que nous pouvons transmettre des informations dans à partir de l’URL vers notre contrôleur, par exemple comme suit : / HelloWorld/Bienvenue ? nom = Scott&amp;numtimes = 4. Modifier votre méthode de bienvenue pour inclure les deux paramètres et mise à jour il comme ci-dessous. Notez que nous avons utilisé la fonctionnalité de paramètre facultatif C# pour indiquer que le paramètre numTimes doit utiliser par défaut 1 si elle n’est pas passé.

[!code-csharp[Main](getting-started-with-mvc-part2/samples/sample2.cs)]

Exécutez votre application, vous accédez à `http://localhost:xx/HelloWorld/Welcome?name=Scott&numtimes=4` modification de la valeur du nom et numtimes comme vous le souhaitez. Le système mappées automatiquement les paramètres nommés dans votre chaîne de requête dans la barre d’adresses à des paramètres dans votre méthode.

Dans ces deux exemples le contrôleur a été effectuer tout le travail et a été renvoyant du HTML directement. En général nous ne voulons pas nos contrôleurs renvoyant du HTML directement - étant donné que qui finit par être très fastidieuse au code. Au lieu de cela, nous allons utiliser généralement un fichier de modèle de vue séparé afin de générer la réponse HTML. Voyons comment nous pouvons faire cela. Fermez votre navigateur et revenir à l’IDE.

>[!div class="step-by-step"]
[Précédent](getting-started-with-mvc-part1.md)
[Suivant](getting-started-with-mvc-part3.md)
