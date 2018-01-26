---
uid: web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-8
title: "Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 d’ASP.NET Web Forms - partie 8 | Documents Microsoft"
author: tdykstra
description: "L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide d’Entity Framework. L’exemple d’application est en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/03/2010
ms.topic: article
ms.assetid: aaadd9bb-5508-448c-ad57-5497dff90e13
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/older-versions-getting-started/getting-started-with-ef/the-entity-framework-and-aspnet-getting-started-part-8
msc.type: authoredcontent
ms.openlocfilehash: 323ee44f43f6d4081bd9ba50791755696bc9128f
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="getting-started-with-entity-framework-40-database-first-and-aspnet-4-web-forms---part-8"></a>Mise en route avec base de données Entity Framework 4.0 tout d’abord et 4 les Web Forms ASP.NET - partie 8
====================
Par [Tom Dykstra](https://github.com/tdykstra)

> L’exemple d’application web Contoso University montre comment créer des applications Web Forms ASP.NET à l’aide de l’Entity Framework 4.0 et Visual Studio 2010. Pour plus d’informations sur la série de didacticiels, consultez [le premier didacticiel de la série](the-entity-framework-and-aspnet-getting-started-part-1.md)


## <a name="using-dynamic-data-functionality-to-format-and-validate-data"></a>À l’aide des fonctionnalités Dynamic Data à mettre en forme et de valider les données

Dans le didacticiel précédent vous implémenté les procédures stockées. Ce didacticiel vous explique comment les fonctionnalités Dynamic Data offre les avantages suivants :

- Les champs sont automatiquement mis en forme pour l’affichage en fonction de leur type de données.
- Les champs sont automatiquement validées en fonction de leur type de données.
- Vous pouvez ajouter des métadonnées au modèle de données pour personnaliser le comportement de mise en forme et de validation. Lorsque ce faire, vous pouvez ajouter les règles de validation et de mise en forme dans un seul endroit, et elles sont appliquées automatiquement partout où vous accéder aux champs à l’aide des contrôles Dynamic Data.

Pour voir comment cela fonctionne, vous allez modifier les contrôles que vous utilisez pour afficher et modifier des champs existants *Students.aspx* page et que vous ajouterez les métadonnées de mise en forme et de validation pour les champs nom et la date de la `Student` type d’entité.

[![Image01](the-entity-framework-and-aspnet-getting-started-part-8/_static/image2.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image1.png)

## <a name="using-dynamicfield-and-dynamiccontrol-controls"></a>À l’aide de DynamicField et contrôles de DynamicControl

Ouvrez le *Students.aspx* page et dans le `StudentsGridView` contrôle remplacer le **nom** et **Date d’inscription** `TemplateField` éléments par le balisage suivant :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample1.aspx)]

Ce balisage utilise `DynamicControl` contrôle à la place de `TextBox` et `Label` champ de modèle de nom de contrôles dans les étudiants, et il utilise un `DynamicField` contrôle pour la date d’inscription. Aucuns chaînes de format ne sont spécifiés.

Ajouter un `ValidationSummary` de contrôle après le `StudentsGridView` contrôle.

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample2.aspx)]

Dans le `SearchGridView` contrôle remplacer le balisage de la **nom** et **Date d’inscription** colonnes comme vous l’avez fait dans le `StudentsGridView` contrôler, à ceci près omettre la `EditItemTemplate` élément. Le `Columns` élément de la `SearchGridView` contrôle contient maintenant le balisage suivant :

[!code-aspx[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample3.aspx)]

Ouvrez *Students.aspx.cs* et ajoutez le code suivant `using` instruction :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample4.cs)]

Ajoutez un gestionnaire pour la page `Init` événement :

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample5.cs)]

Ce code spécifie que les données dynamiques va fournir mise en forme et validation dans ces contrôles liés aux données pour les champs de la `Student` entité. Si vous obtenez un message d’erreur à l’exemple suivant lorsque vous exécutez la page, cela signifie généralement que vous avez oublié d’appeler le `EnableDynamicData` méthode dans `Page_Init`:

`Could not determine a MetaTable. A MetaTable could not be determined for the data source 'StudentsEntityDataSource' and one could not be inferred from the request URL.`

Exécutez la page.

[![Image03](the-entity-framework-and-aspnet-getting-started-part-8/_static/image4.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image3.png)

Dans le **Date d’inscription** colonne, l’heure s’affiche, avec la date, car le type de propriété est `DateTime`. Vous allez résoudre cela plus tard.

Pour l’instant, notez que dynamique des données assure automatiquement la validation de la base de données. Par exemple, cliquez sur **modifier**, effacez le champ de date, cliquez sur **mise à jour**, et vous voyez que Dynamic Data effectue automatiquement cette un champ obligatoire, car la valeur n’est pas nullable dans le modèle de données. La page affiche un astérisque après le champ et un message d’erreur dans le `ValidationSummary` contrôle :

[![Image05](the-entity-framework-and-aspnet-getting-started-part-8/_static/image6.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image5.png)

Vous pourriez omettre le `ValidationSummary` contrôler, étant donné que vous pouvez également maintenir le pointeur de la souris sur l’astérisque pour afficher le message d’erreur :

[![Image06](the-entity-framework-and-aspnet-getting-started-part-8/_static/image8.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image7.png)

Données dynamiques valide également que les données entrées dans le **Date d’inscription** champ est une date valide :

[![Image04](the-entity-framework-and-aspnet-getting-started-part-8/_static/image10.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image9.png)

Comme vous pouvez le voir, il s’agit d’un message d’erreur générique. Dans la section suivante, vous allez apprendre à personnaliser les messages, ainsi que la validation et les règles de mise en forme.

## <a name="adding-metadata-to-the-data-model"></a>Ajout de métadonnées pour le modèle de données

En règle générale, vous souhaitez personnaliser les fonctionnalités fournies par les données dynamiques. Par exemple, vous pouvez modifier l’affichage des données et le contenu des messages d’erreur. Vous en général également personnalisez des règles de validation pour fournir plus de fonctionnalités que celui des données dynamiques automatiquement selon les types de données. Pour ce faire, vous créez des classes partielles qui correspondent aux types d’entités.

Dans **l’Explorateur de solutions**, avec le bouton droit le **ContosoUniversity** projet, sélectionnez **ajouter une référence**et ajoutez une référence à `System.ComponentModel.DataAnnotations`.

[![Image11](the-entity-framework-and-aspnet-getting-started-part-8/_static/image12.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image11.png)

Dans le *DAL* dossier, créez un nouveau fichier de classe, nommez-le *Student.cs*et remplacez le code du modèle qu’il contient le code suivant.

[!code-csharp[Main](the-entity-framework-and-aspnet-getting-started-part-8/samples/sample6.cs)]

Ce code crée une classe partielle pour la `Student` entité. Le `MetadataType` attribut appliqué à cette classe partielle identifie la classe que vous utilisez pour spécifier les métadonnées. La classe de métadonnées peut avoir n’importe quel nom, mais à l’aide du nom de l’entité plus « Métadonnées » est une pratique courante.

Les attributs appliqués aux propriétés dans la classe de métadonnées spécifient la mise en forme, les messages de validation, de règles et d’erreur. Les attributs affichés ici possède les résultats suivants :

- `EnrollmentDate`affiche une date (sans heure).
- Les deux champs de noms doivent être de 25 caractères ou moins, et un message d’erreur personnalisé est fourni.
- Les deux champs de noms sont nécessaires, et un message d’erreur personnalisé est fourni.

Exécutez le *Students.aspx* nouveau d’une page, et vous voyez que les dates sont maintenant affichés sans heures :

[![Image08](the-entity-framework-and-aspnet-getting-started-part-8/_static/image14.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image13.png)

Modifier une ligne et essayez d’effacer les valeurs dans les champs de nom. Les astérisques indiquant des erreurs de champ apparaissent dès que vous laissez un champ, avant de cliquer sur **mise à jour**. Lorsque vous cliquez sur **mise à jour**, la page affiche le texte de message d’erreur spécifié.

[![Image10](the-entity-framework-and-aspnet-getting-started-part-8/_static/image16.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image15.png)

Essayez d’entrer des noms de plus de 25 caractères, cliquez sur **mise à jour**, et la page affiche le texte de message d’erreur spécifié.

[![Image09](the-entity-framework-and-aspnet-getting-started-part-8/_static/image18.png)](the-entity-framework-and-aspnet-getting-started-part-8/_static/image17.png)

Maintenant que vous avez configuré ces règles de mise en forme et de validation dans les métadonnées de modèle de données, les règles sont automatiquement appliquées sur chaque page qui affiche ou permet de modifier ces champs, tant que vous utilisez `DynamicControl` ou `DynamicField` contrôles. Cela réduit la quantité de code redondant, que vous devez écrire, ce qui rend la programmation et le test, et il garantit que la mise en forme des données et la validation sont cohérentes dans une application.

## <a name="more-information"></a>Informations complémentaires

Ceci conclut cette série de didacticiels sur la mise en route avec Entity Framework. Pour plus de ressources pour vous aider à apprendre à utiliser Entity Framework, poursuivre [le premier didacticiel de la série de didacticiels Entity Framework suivant](../continuing-with-ef/using-the-entity-framework-and-the-objectdatasource-control-part-1-getting-started.md) ou visitez les sites suivants :

- [Forum aux questions de Entity Framework](http://www.ef-faq.org/introduction.html)
- [Blog de l’équipe Entity Framework](https://blogs.msdn.com/b/adonet/)
- [Entity Framework dans la bibliothèque MSDN](https://msdn.microsoft.com/library/bb399572.aspx)
- [Entity Framework dans le centre de développement de données MSDN](https://msdn.microsoft.com/data/ef.aspx)
- [Vue d’ensemble du contrôle serveur Web EntityDataSource dans MSDN Library](https://msdn.microsoft.com/library/cc488502.aspx)
- [Contrôle EntityDataSource référence d’API dans la bibliothèque MSDN](https://msdn.microsoft.com/library/system.web.ui.webcontrols.entitydatasource.aspx)
- [Entity Framework Forums MSDN](https://social.msdn.microsoft.com/forums/adodotnetentityframework/)
- [Blog de Julie Lerman](http://thedatafarm.com/blog/)

>[!div class="step-by-step"]
[Précédent](the-entity-framework-and-aspnet-getting-started-part-7.md)
