---
title: "Pages d’aide d’API web ASP.NET Core à l’aide de Swagger"
author: spboyer
description: "Ce didacticiel montre comment ajouter Swagger pour générer des pages d’aide et de documentation à une application d’API web."
keywords: "ASP.NET Core, Swagger, Swashbuckle, pages d’aide, API web"
ms.author: spboyer
manager: wpickett
ms.date: 09/01/2017
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 647ab48fb83c5e2c79b5de371173bc644c65d831
ms.sourcegitcommit: 98ecb0f1bae4886507b090c84ecd99ff1e5c46ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2017
---
# <a name="aspnet-web-api-help-pages-using-swagger"></a>Pages d’aide d’API web ASP.NET à l’aide de Swagger

<a name=web-api-help-pages-using-swagger></a>

De [Shayne Boyer](https://twitter.com/spboyer) et [Scott Addie](https://twitter.com/Scott_Addie)

Comprendre les différentes méthodes d’une API peut être un défi pour un développeur lors de la création d’une application consommatrice.

Générer des pages d’aide et de documentation utiles pour votre API web à l’aide de [Swagger](https://swagger.io/) avec l’implémentation .NET Core de [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) est un jeu d’enfant : il vous suffit d’ajouter deux packages NuGet et de modifier le fichier *Startup.cs*.

* [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) est un projet open source pour la génération de documents Swagger pour des API web ASP.NET Core.

* [Swagger](https://swagger.io/) est une représentation lisible par la machine d’une API RESTful qui permet de bénéficier de la prise en charge de documentation interactive, de la génération de SDK client et de la fonctionnalité de découverte.

Ce didacticiel s’appuie sur l’exemple [Création de votre première API web avec ASP.NET Core MVC et Visual Studio](xref:tutorials/first-web-api). Si vous souhaitez suivre ce didacticiel, téléchargez l’exemple à l’adresse [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample).

## <a name="getting-started"></a>Commencer

Swashbuckle compte trois composants principaux :

* `Swashbuckle.AspNetCore.Swagger` : un modèle objet Swagger et un intergiciel (middleware) pour exposer des objets `SwaggerDocument` en tant que points de terminaison JSON.

* `Swashbuckle.AspNetCore.SwaggerGen` : un générateur Swagger qui crée des objets `SwaggerDocument` directement à partir de vos itinéraires, contrôleurs et modèles. Il est généralement associé à l’intergiciel de point de terminaison Swagger pour exposer automatiquement Swagger JSON.

* `Swashbuckle.AspNetCore.SwaggerUI` : une version incorporée de l’outil d’interface utilisateur Swagger qui interprète Swagger JSON afin de générer une expérience enrichie et personnalisable pour la description de la fonctionnalité de l’API web. Il inclut des ateliers de test intégrés pour les méthodes publiques.

## <a name="nuget-packages"></a>Packages NuGet

Vous pouvez ajouter Swashbuckle en adoptant l’une des approches suivantes :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* À partir de la fenêtre **Console du Gestionnaire de package** :

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* À partir de la boîte de dialogue **Gérer les packages NuGet** :

     * Cliquez avec le bouton droit sur votre projet dans **l’Explorateur de solutions** > **Gérer les packages NuGet**.
     * Affectez la valeur « nuget.org » à **Source du package**.
     * Entrez « Swashbuckle.AspNetCore » dans la zone de recherche.
     * Sélectionnez le package « Swashbuckle.AspNetCore » sous l’onglet **Parcourir** et cliquez sur **Installer**.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Cliquez avec le bouton droit sur le dossier *Packages* dans **Panneau Solutions** > **Ajouter des packages**.
* Dans la fenêtre **Ajouter des packages**, sélectionnez « nuget.org » dans la liste déroulante **Source**.
* Entrez Swashbuckle.AspNetCore dans la zone de recherche.
* Sélectionnez le package Swashbuckle.AspNetCore dans le volet de résultats, puis cliquez sur **Ajouter un package**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Exécutez la commande suivante à partir du **Terminal intégré** :

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[CLI .NET Core](#tab/netcore-cli)

Exécutez la commande suivante :

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a>Ajouter et configurer Swagger à l’intergiciel

Ajoutez le générateur Swagger à la collection de services dans la méthode `ConfigureServices` de *Startup.cs* :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]

Ajoutez l’instruction using suivante pour la classe `Info` :

```csharp
using Swashbuckle.AspNetCore.Swagger;
```

Dans la méthode `Configure` de *Startup.cs*, activez l’intergiciel pour traiter le document JSON généré et SwaggerUI :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]

Lancez l’application et accédez à `http://localhost:<random_port>/swagger/v1/swagger.json`. Le document généré décrivant les points de terminaison s’affiche.

**Remarque :** Microsoft Edge, Google Chrome et Firefox affichent les documents JSON en mode natif. Il existe des extensions de Chrome qui mettent en forme le document afin d’en faciliter la lecture. *L’exemple suivant est réduit par souci de concision.*

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

Ce document pilote l’interface utilisateur de Swagger, qui peut être affichée en accédant à `http://localhost:<random_port>/swagger` :

![Interface utilisateur de Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Chaque méthode d’action publique dans `TodoController` peut être testée à partir de l’interface utilisateur. Cliquez sur un nom de méthode pour développer la section. Ajoutez tous les paramètres nécessaires, puis cliquez sur « Try it out! ».

![Exemple de test GET Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a>Personnalisation et extensibilité

Swagger fournit des options pour documenter le modèle objet et personnaliser l’interface utilisateur en fonction de votre thème.

### <a name="api-info-and-description"></a>Informations sur l’API et description

Vous pouvez utiliser l’action de configuration transmise à la méthode `AddSwaggerGen` pour ajouter des informations telles que l’auteur, la licence et la description :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]

L’image suivante représente l’interface utilisateur de Swagger montrant les informations de version :

![Interface utilisateur de Swagger avec les informations de version : description, auteur et lien pour en savoir plus](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>Commentaires XML

Vous pouvez activer les commentaires XML en adoptant l’une des approches suivantes :

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Cliquez avec le bouton droit sur le projet dans **l’Explorateur de solutions**, puis sélectionnez **Propriétés**.
* Cochez la zone **Fichier de documentation XML** dans la section **Sortie** de l’onglet **Générer** :

![Onglet Générer des propriétés du projet](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio pour Mac](#tab/visual-studio-mac)

* Ouvrez la boîte de dialogue **Options du projet** > **Générer** > **Compilateur**.
* Cochez la zone **Générer la documentation XML** dans la section **Options générales** :

![Section Options générales des options du projet](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ajoutez manuellement l’extrait de code suivant au fichier *.csproj* :

[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]

---

Configurez Swagger pour utiliser le fichier XML généré. Pour les systèmes d’exploitation Linux ou autres que Windows, les chemins et les noms de fichiers peuvent respecter la casse. Par exemple, un fichier *ToDoApi.XML* peut exister sur Windows, mais pas sur CentOS.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]

Dans le code précédent, `ApplicationBasePath` obtient le chemin de base de l’application. Le chemin de base sert à trouver le fichier de commentaires XML. *TodoApi.xml* fonctionne uniquement pour cet exemple, étant donné que le nom du fichier de commentaires XML généré est basé sur le nom de l’application.

Le fait d’ajouter les commentaires à trois barres obliques à la méthode améliore l’interface utilisateur de Swagger en ajoutant la description à l’en-tête de section :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]

![Interface utilisateur de Swagger affichant le commentaire XML « Deletes a specific TodoItem. » pour la méthode DELETE.](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

L’interface utilisateur est pilotée par le fichier JSON généré, qui contient également ces commentaires :

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

Ajoutez une balise [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) à la documentation de méthode d’action `Create`. Elle complète les informations spécifiées dans la balise `<summary>` et fournit une interface utilisateur de Swagger plus robuste. Le contenu de la balise `<remarks>` peut être constitué de texte, JSON ou XML.

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

Notez les améliorations de l’interface utilisateur avec ces commentaires supplémentaires.

![Interface utilisateur de Swagger avec des commentaires supplémentaires affichés](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Annotations de données

Décorez le modèle avec des attributs, disponibles dans `System.ComponentModel.DataAnnotations`, afin d’optimiser les composants de l’interface utilisateur de Swagger.

Ajoutez l’attribut `[Required]` à la propriété `Name` de la classe `TodoItem` :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]

La présence de cet attribut change le comportement de l’interface utilisateur et modifie le schéma JSON sous-jacent :

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Ajoutez l’attribut `[Produces("application/json")]` au contrôleur d’API. Son objectif est de déclarer que les actions du contrôleur prennent en charge le retour d’un type de contenu *application/json* :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]

La zone de liste déroulante **Response Content Type** permet de sélectionner ce type de contenu comme valeur par défaut pour les actions GET du contrôleur :

![Interface utilisateur de Swagger avec le type de contenu de réponse par défaut](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

À mesure que l’utilisation des annotations de données dans l’API web augmente, l’interface utilisateur et les pages d’aide de l’API deviennent de plus en plus descriptives et utiles.

### <a name="describing-response-types"></a>Description des types de réponse

Les développeurs consommateurs se soucient davantage de ce qui est retourné, plus spécifiquement par les types de réponse et les codes d’erreur (s’ils ne sont pas standard). Ceux-ci sont gérés dans les annotations de données et les commentaires XML.

L’action `Create` retourne `201 Created` en cas de réussite ou `400 Bad Request` quand le corps de la requête envoyée est null. Sans documentation appropriée dans l’interface utilisateur de Swagger, le consommateur n’a pas connaissance de ces résultats attendus. Pour résoudre ce problème, il faut ajouter les lignes en surbrillance dans l’exemple suivant :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

L’interface utilisateur de Swagger documente maintenant clairement les codes de réponse HTTP attendus :

![Interface utilisateur de Swagger affichant la description de la classe de réponse POST « Returns the newly created Todo item » et « 400 - If the item is null » pour le code d’état et la raison sous Response Messages](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a>Personnalisation de l’interface utilisateur

L’interface utilisateur par défaut est fonctionnelle et présentable, mais lors de la création des pages de documentation de votre API, vous souhaiterez probablement qu’elle représente votre marque ou votre thème. Pour accomplir cette tâche avec les composants Swashbuckle, vous devez ajouter les ressources de prise en charge des fichiers statiques, puis générer la structure de dossiers pour héberger ces fichiers.

Si vous ciblez le .NET Framework, ajoutez le package NuGet `Microsoft.AspNetCore.StaticFiles` au projet :

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Activez l’intergiciel des fichiers statiques :

[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]

Faites l’acquisition du contenu du dossier *dist* à partir du [dépôt GitHub de l’interface utilisateur de Swagger](https://github.com/swagger-api/swagger-ui/tree/2.x/dist). Ce dossier contient les ressources nécessaires pour la page de l’interface utilisateur de Swagger. Copiez le contenu de ce dossier dans le dossier *wwwroot/swagger/ui*.

Créez un fichier *wwwroot/swagger/ui/css/custom.css* avec le code CSS suivant pour personnaliser l’en-tête de page :

[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]

Référencez *custom.css* dans le fichier *index.html* :

[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]

Accédez à la page *index.html* à l’adresse `http://localhost:<random_port>/swagger/ui/index.html`. Entrez `http://localhost:<random_port>/swagger/v1/swagger.json` dans la zone de texte de l’en-tête, puis cliquez sur le bouton **Explorer**. La page résultante ressemble à ceci :

![Interface utilisateur de Swagger avec titre d’en-tête personnalisé](web-api-help-pages-using-swagger/_static/custom-header.png)

Vous pouvez faire bien d’autres choses avec cette page. Pour découvrir toutes les fonctionnalités pour les ressources d’interface utilisateur, accédez au [dépôt GitHub de l’interface utilisateur de Swagger](https://github.com/swagger-api/swagger-ui).
