---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: "Base de données EF tout d’abord avec ASP.NET MVC : amélioration de la Validation des données | Documents Microsoft"
author: tfitzmac
description: "À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Ce didacticiel seri..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/29/2014
ms.topic: article
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 465be4b51ab280d0b3e2f4b33362fa771480d5e1
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="ef-database-first-with-aspnet-mvc-enhancing-data-validation"></a>Base de données EF tout d’abord avec ASP.NET MVC : amélioration de la Validation des données
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> À l’aide de la structure d’ASP.NET MVC et Entity Framework, vous pouvez créer une application web qui fournit une interface à une base de données existante. Cette série de didacticiels vous montre comment automatiquement générer du code qui permet aux utilisateurs d’afficher, modifier, créer et supprimer des données qui résident dans une table de base de données. Le code généré correspond aux colonnes dans la table de base de données.
> 
> Cette partie de la série se concentre sur l’ajout d’annotations de données au modèle de données pour spécifier les exigences de validation et afficher la mise en forme. Il a été améliorée en fonction des commentaires des utilisateurs dans la section commentaires.


## <a name="add-data-annotations"></a>Ajouter des annotations de données

Comme vous l’avez vu dans une rubrique précédente, certaines règles de validation de données sont automatiquement appliquées à l’entrée d’utilisateur. Par exemple, vous pouvez fournir uniquement un nombre pour la propriété de classe. Pour spécifier plusieurs règles de validation de données, vous pouvez ajouter des annotations de données à votre classe de modèle. Ces annotations sont appliquées dans l’ensemble de votre application web pour la propriété spécifiée. Vous pouvez également appliquer des attributs de mise en forme qui modifient la façon dont les propriétés sont affichées ; comme la modification de la valeur utilisée pour les étiquettes de texte.

Dans ce didacticiel, vous allez ajouter des annotations de données pour limiter la longueur des valeurs fournies pour les propriétés FirstName, LastName et MiddleName. Dans la base de données, ces valeurs sont limités à 50 caractères. Toutefois, dans votre application web ce caractère est actuellement pas appliqué. Si un utilisateur fournit plus de 50 caractères pour l’une de ces valeurs, la page se bloque quand vous tentez d’enregistrer la valeur dans la base de données. Vous allez également restreindre le niveau pour les valeurs comprises entre 0 et 4.

Ouvrez le **Student.cs** de fichiers dans le **modèles** dossier. Ajoutez le code en surbrillance suivant à la classe.

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

Dans Enrollment.cs, ajoutez le code en surbrillance suivant.

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

Générez la solution.

Accédez à une page de modification ou de création d’un étudiant. Si vous essayez d’entrer plus de 50 caractères, un message d’erreur s’affiche.

![afficher le message d’erreur](enhancing-data-validation/_static/image1.png)

Accédez à la page de modification des inscriptions et tentent de fournir un niveau au-dessus de 4.

![Erreur de plage de niveau](enhancing-data-validation/_static/image2.png)

Pour obtenir une liste complète des annotations de validation de données que vous pouvez appliquer aux classes et propriétés, consultez [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/en-us/library/system.componentmodel.dataannotations.aspx).

## <a name="add-metadata-classes"></a>Ajouter des classes de métadonnées

Ajout d’attributs de validation directement à la classe de modèle fonctionne lorsque vous ne prévoyez pas de la base de données pour modifier ; Toutefois, si vos modifications de la base de données et que vous devez régénérer la classe de modèle, vous perdrez tous les attributs que vous aviez appliqué à la classe de modèle. Cette approche peut être très inefficace et sujette à la perte des règles de validation importantes.

Pour éviter ce problème, vous pouvez ajouter une classe de métadonnées qui contient les attributs. Lorsque vous associez la classe de modèle pour la classe de métadonnées, ces attributs sont appliqués au modèle. Dans cette approche, la classe de modèle peut être régénérée sans perte de tous les attributs qui ont été appliqués à la classe de métadonnées.

Dans le **modèles** dossier, ajoutez une classe nommée **Metadata.cs**.

![ajouter la classe de métadonnées](enhancing-data-validation/_static/image3.png)

Remplacez le code dans Metadata.cs par le code suivant.

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

Ces classes de métadonnées contiennent tous les attributs de validation que vous aviez précédemment appliqué aux classes de modèle. Le **affichage** attribut est utilisé pour modifier la valeur utilisée pour les étiquettes de texte.

Maintenant, vous devez associer les classes de modèle avec les classes de métadonnées.

Dans le **modèles** dossier, ajoutez une classe nommée **PartialClasses.cs**.

Remplacez le contenu du fichier par le code suivant.

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

Notez que chaque classe est marquée comme un `partial` classe et chacun correspond au nom et l’espace de noms que la classe qui est générée automatiquement. En appliquant l’attribut de métadonnées à la classe partielle, vous assurez que les attributs de validation de données seront être appliquées à la classe générée automatiquement. Ces attributs ne seront pas perdues lorsque vous régénérez les classes du modèle, car l’attribut de métadonnées est appliqué dans des classes partielles qui ne sont pas régénérées.

Pour régénérer les classes générées automatiquement, ouvrez le fichier ContosoModel.edmx. Une fois encore, avec le bouton droit sur l’aire de conception et sélectionnez **modèle de mise à jour à partir de la base de données**. Même si vous n’avez pas modifié la base de données, ce processus sera régénérer les classes. Dans le **Actualiser** onglet, sélectionnez **Tables** et **Terminer**.

![Actualiser les tables](enhancing-data-validation/_static/image4.png)

Enregistrez le fichier ContosoModel.edmx pour appliquer les modifications.

Ouvrez le fichier Student.cs ou le fichier Enrollment.cs et notez que les attributs de validation de données que vous appliqué précédemment ne sont plus dans le fichier. Toutefois, exécutez l’application et notez que les règles de validation sont toujours appliquées lorsque vous entrez des données.

>[!div class="step-by-step"]
[Précédent](customizing-a-view.md)
[Suivant](publish-to-azure.md)
