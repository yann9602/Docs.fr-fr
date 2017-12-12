---
title: Journalisation de hautes performances avec LoggerMessage dans ASP.NET Core
author: guardrex
description: "Découvrez comment utiliser les fonctionnalités de LoggerMessage pour créer des délégués pouvant être nécessitant moins d’allocations objet celle des méthodes d’extension d’enregistreur d’événements pour les scénarios de journalisation de hautes performances."
ms.author: riande
manager: wpickett
ms.date: 11/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: defba75c6c9ea13d24af4cd8515d82d9e7cf9853
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Journalisation de hautes performances avec LoggerMessage dans ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

[LoggerMessage](/dotnet/api/microsoft.extensions.logging.loggermessage) fonctionnalités créer pouvant être délégués qui nécessitent moins d’allocations objets et réduit la charge de calcul à [les méthodes d’extension d’enregistreur d’événements](/dotnet/api/Microsoft.Extensions.Logging.LoggerExtensions), tel que `LogInformation`, `LogDebug`et `LogError`. Pour les scénarios de journalisation de hautes performances, utilisez la `LoggerMessage` modèle.

`LoggerMessage`fournit les avantages de performances suivants sur les méthodes d’extension d’enregistreur d’événements :

* Méthodes d’extension d’enregistreur d’événements nécessitent des types de valeur « conversion boxing » (conversion), tel que `int`, dans `object`. Le `LoggerMessage` boxing évite de modèle à l’aide de static `Action` champs et méthodes d’extension avec des paramètres fortement typés.
* Méthodes d’extension d’enregistreur d’événements doivent analyser le modèle de message (chaîne de format nommé) chaque fois qu’un message de journal est écrit. `LoggerMessage`ne nécessite que l’analyse d’un modèle une fois lorsque le message est défini.

[Affichez ou téléchargez l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/sample/) ([procédure de téléchargement](xref:tutorials/index#how-to-download-a-sample))

L’exemple d’application montre `LoggerMessage` fonctionnalités avec une demande de base système de suivi. L’application ajoute et supprime les guillemets doubles à l’aide d’une base de données en mémoire. Comme ces opérations se produisent, des messages de journal sont générées à l’aide de la `LoggerMessage` modèle.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Définir (LogLevel, ID d’événement, String)](/dotnet/api/microsoft.extensions.logging.loggermessage.define) crée un `Action` délégué pour la journalisation d’un message. `Define`surcharges autorisent le passage de paramètres de type jusqu'à six à une chaîne de format nommée (modèle).

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) crée un `Func` delegate pour définir un [connecter étendue](xref:fundamentals/logging/index#log-scopes). `DefineScope`surcharges autorisent le passage de jusqu'à trois paramètres de type à une chaîne de format nommée (modèle).

## <a name="message-template-named-format-string"></a>Modèle de message (nommé la chaîne de format)

La chaîne fournie pour le `Define` et `DefineScope` méthodes est un modèle et pas une chaîne interpolée. Les espaces réservés sont remplis dans l’ordre que les types sont spécifiés. Noms d’espace réservé dans le modèle doivent être descriptif et cohérent entre les modèles. Ils servent de noms de propriété dans les données structurées de journal. Nous vous recommandons de [Pascal casse](/dotnet/standard/design-guidelines/capitalization-conventions) pour les noms d’espace réservé. Par exemple, `{Count}`, `{FirstName}`.

## <a name="implementing-loggermessagedefine"></a>Implémentation de LoggerMessage.Define

Chaque message du journal est une `Action` contenues dans un champ statique créé par `LoggerMessage.Define`. Par exemple, l’exemple d’application crée un champ pour décrire un message de journal pour une demande GET pour la page d’Index (*Internal/LoggerExtensions.cs*) :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet1)]

Pour le `Action`, spécifiez :

* Le niveau de journal.
* Un identificateur d’événement unique ([EventId](/dotnet/api/microsoft.extensions.logging.eventid)) avec le nom de la méthode d’extension statique.
* Le modèle de message (nommé la chaîne de format). 

Une demande de la page d’Index de l’application d’exemple définit le :

* Ouvrez une session au niveau de `Information`.
* Id d’événement de `1` avec le nom de la `IndexPageRequested` (méthode).
* Modèle de message (nommé la chaîne de format) en une chaîne.

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet5)]

Magasins de journalisation structuré peuvent utiliser le nom d’événement lorsqu’il est fourni avec l’id d’événement d’enrichir la journalisation. Par exemple, [Serilog](https://github.com/serilog/serilog-extensions-logging) utilise le nom de l’événement.

Le `Action` est appelé via une méthode d’extension de fortement typée. Le `IndexPageRequested` méthode consigne un message pour une demande d’obtention de page Index dans l’exemple d’application :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`est appelé sur l’enregistreur d’événements dans le `OnGetAsync` méthode dans *Pages/Index.cshtml.cs*:

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Examinez la sortie de l’application console :

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Pour passer des paramètres à un message de journal, définir jusqu'à six types lors de la création du champ statique. L’exemple d’application enregistre une chaîne lors de l’ajout d’un guillemet en définissant un `string` de type pour le `Action` champ :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet2)]

Modèle de message du journal du délégué reçoit ses valeurs d’espace réservé à partir des types fournis. L’exemple d’application définit un délégué pour l’ajout d’un guillemet, où le paramètre de devis est un `string`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet6)]

La méthode d’extension statique pour l’ajout d’un guillemet, `QuoteAdded`, reçoit la valeur d’argument devis et passe à la `Action` délégué :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet10)]

Dans le fichier de code-behind de la page d’Index (*Pages/Index.cshtml.cs*), `QuoteAdded` est appelée pour enregistrer le message :

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Examinez la sortie de l’application console :

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand')
```

L’application exemple implémente un `try` &ndash; `catch` modèle pour la suppression de devis. Un message d’information est consigné pour une opération de suppression réussit. Un message d’erreur est enregistré pour une opération de suppression lorsqu’une exception est levée. Le message du journal pour l’échec de suppression opération inclut la trace de pile d’exception (*Internal/LoggerExtensions.cs*) :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet7)]

Notez la manière dont l’exception est passée au délégué dans `QuoteDeleteFailed`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet11)]

Dans le fichier Index page code-behind, une suppression réussie de devis appelle la `QuoteDeleted` méthode sur l’enregistreur d’événements. Lorsqu’un guillemet n’est pas trouvé pour la suppression, un `ArgumentNullException` est levée. L’exception est interceptée par le `try` &ndash; `catch` instruction et enregistrés en appelant le `QuoteDeleteFailed` méthode sur l’enregistreur d’événements dans le `catch` bloc (*Pages/Index.cshtml.cs*) :

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Lorsqu’un devis est supprimé avec succès, examinez la sortie de la console de l’application :

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the consequences of avoiding reality. - Ayn Rand' Id = 1)
```

En cas d’échec de la suppression de devis, inspecter la sortie de la console de l’application. Notez que l’exception est incluse dans le message du journal :

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T](T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() in 
      <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="implementing-loggermessagedefinescope"></a>Implémentation de LoggerMessage.DefineScope

Définir un [connecter étendue](xref:fundamentals/logging/index#log-scopes) à appliquer à une série de messages du journal à l’aide de la [DefineScope(String)](/dotnet/api/microsoft.extensions.logging.loggermessage.definescope) (méthode).

L’exemple d’application a un **Effacer tout** bouton pour supprimer tous les guillemets dans la base de données. Les guillemets sont supprimés en supprimant une à la fois. Chaque fois qu’un guillemet est supprimé, le `QuoteDeleted` méthode est appelée sur l’enregistreur d’événements. Une étendue de journal est ajoutée à ces messages du journal.

Activer `IncludeScopes` dans les options de journalisation de console :

[!code-csharp[Main](loggermessage/sample/Program.cs?name=snippet1&highlight=22)]

Paramètre `IncludeScopes` est requis dans les applications ASP.NET Core 2.0 pour activer des étendues de journal. Paramètre `IncludeScopes` via *appsettings* les fichiers de configuration est une fonctionnalité qui a planifié pour la version de ASP.NET Core 2.1.

L’exemple d’application efface les autres fournisseurs et ajoute les filtres afin de réduire la sortie de journalisation. Cela rend plus facile de voir les messages du journal de l’exemple qui illustrent `LoggerMessage` fonctionnalités.

Pour créer une étendue de journal, ajoutez un champ pour contenir un `Func` delegate pour l’étendue. L’exemple d’application crée un champ intitulé `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*) :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet4)]

Utilisez `DefineScope` pour créer le délégué. Jusqu'à trois types peuvent être spécifiés pour une utilisation en tant qu’arguments de modèle lorsque le délégué est appelé. L’exemple d’application utilise un modèle de message qui inclut le nombre de devis supprimés (un `int` type) :

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet8)]

Fournir une méthode d’extension statique pour le message du journal. Inclure tous les paramètres de type pour les propriétés nommées qui s’affichent dans le modèle de message. L’exemple d’application utilise un `count` de devis à supprimer et renvoie `_allQuotesDeletedScope`:

[!code-csharp[Main](loggermessage/sample/Internal/LoggerExtensions.cs?name=snippet12)]

Le retour automatique à la portée l’extension de journalisation des appels dans un `using` bloc :

[!code-csharp[Main](loggermessage/sample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Examinez les messages du journal dans la sortie de la console de l’application. Le résultat suivant montre les trois devis supprimés avec le message d’étendue de journal inclus :

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

## <a name="see-also"></a>Voir aussi

* [Journalisation](xref:fundamentals/logging/index)
