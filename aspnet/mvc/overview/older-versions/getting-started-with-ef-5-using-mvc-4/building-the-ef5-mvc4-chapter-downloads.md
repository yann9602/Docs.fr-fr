---
uid: mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/building-the-ef5-mvc4-chapter-downloads
title: "Génération du chapitre télécharge pour le MVC EF 5 4 didacticiels | Documents Microsoft"
author: Rick-Anderson
description: "L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/30/2013
ms.topic: article
ms.assetid: d0a89089-eed8-4f61-a478-c5ffa30186f5
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/building-the-ef5-mvc4-chapter-downloads
msc.type: authoredcontent
ms.openlocfilehash: 912a1383ed170b49782657372abc1801140df8dd
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="building-the-chapter-downloads-for-the-ef-5-mvc-4-tutorials"></a>Génération du chapitre télécharge pour le MVC EF 5 4 didacticiels
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

[Télécharger le projet terminé](http://code.msdn.microsoft.com/Getting-Started-with-dd0e2ed8)

> L’exemple d’application web Contoso University montre comment créer des applications ASP.NET MVC 4 à l’aide de l’Entity Framework 5 Code First et Visual Studio 2012. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md).


## <a name="building-the-chapter-downloads"></a>Générer les téléchargements de chapitre

1. Téléchargez et décompressez le fichier zip d’exemple projet. Dans le package de téléchargement décompressé, vous trouverez les fichiers zip supplémentaires, une pour la fin de chaque chapitre.
2. Cliquez avec le bouton droit sur le fichier zip de votre choix, cliquez sur **propriétés**, puis cliquez sur le **Unblock** bouton.  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image1.png)
3. Décompressez le fichier.
4. Double-cliquez sur le *CUx.sln* fichier pour lancer Visual Studio.
5. À partir de la **outils** menu, cliquez sur **Gestionnaire de Package de bibliothèque**, puis **Package Manager Console**.  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image2.png)
6. Dans Package Manager Console (PMC), cliquez sur **restaurer**.  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image3.png)
7. Quittez Visual Studio.
8. Redémarrez Visual Studio, en ouvrant le fichier de solution que vous avez fermé à l’étape précédente.
9. Dans Package Manager Console (PMC), entrez le `Update-Database` commande :  
  
    ![](building-the-ef5-mvc4-chapter-downloads/_static/image4.png)  

    > [!NOTE]
    > Si vous obtenez l’erreur suivante :  
    >   
    >  *Le terme 'Mise à jour de base de données' n’est pas reconnu en tant que le nom de l’applet de commande, fonction, fichier de script ou programme exécutable. Vérifiez l’orthographe du nom, ou si un chemin d’accès existe, vérifiez que le chemin d’accès est correct et réessayez.*  
    > Quittez et redémarrez Visual Studio.

    Chaque migration s’exécute, puis la méthode de valeur initiale s’exécute. Vous pouvez maintenant exécuter l’application.

    ![](building-the-ef5-mvc4-chapter-downloads/_static/image5.png)

>[!div class="step-by-step"]
[Précédent](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)
