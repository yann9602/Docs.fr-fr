---
uid: mvc/overview/older-versions-1/overview/understanding-the-asp-net-mvc-execution-process
title: "Comprendre le processus de l’exécution d’ASP.NET MVC | Documents Microsoft"
author: microsoft
description: "Découvrez comment l’infrastructure ASP.NET MVC traite une demande de navigateur pas à pas."
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/27/2009
ms.topic: article
ms.assetid: d1608db3-660d-4079-8c15-f452ff01f1db
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/overview/understanding-the-asp-net-mvc-execution-process
msc.type: authoredcontent
ms.openlocfilehash: 5837c6e49709d6b86ee52cd88ffd4759c1850544
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="understanding-the-aspnet-mvc-execution-process"></a>Comprendre le processus de l’exécution d’ASP.NET MVC
====================
par [Microsoft](https://github.com/microsoft)

> Découvrez comment l’infrastructure ASP.NET MVC traite une demande de navigateur pas à pas.


Demandes à une application Web basée sur ASP.NET MVC traversent d’abord le **UrlRoutingModule** objet, qui est un module HTTP. Ce module analyse la demande et effectue une sélection de l’itinéraire. Le **UrlRoutingModule** objet sélectionne le premier objet d’itinéraire qui correspond à la requête actuelle. (Un objet d’itinéraire est une classe qui implémente **RouteBase**, et est généralement une instance de la **itinéraire** classe.) Si aucun itinéraire ne correspond, la **UrlRoutingModule** objet ne fait rien et la demande vous permet de revenir à la demande ASP.NET ou IIS normal le traitement.

À partir de l’élément **itinéraire** objet, le **UrlRoutingModule** Obtient le **IRouteHandler** objet auquel est associé le **itinéraire**objet. En règle générale, dans une application MVC, ce sera une instance de **MvcRouteHandler**. Le **IRouteHandler** instance crée un **IHttpHandler** de l’objet et lui passe la **IHttpContext** objet. Par défaut, le **IHttpHandler** d’instance pour MVC est la **MvcHandler** objet. Le **MvcHandler** objet sélectionne ensuite le contrôleur qui gérera finalement la demande.

> [!NOTE]
> Lorsqu’une application Web ASP.NET MVC s’exécute dans IIS 7.0, aucune extension de nom de fichier n’est requise pour les projets MVC. Toutefois, dans IIS 6.0, le gestionnaire requiert que vous mappez l’extension de nom de fichier .mvc à la DLL ISAPI ASP.NET.


Le module et le gestionnaire sont les points d’entrée de l’infrastructure ASP.NET MVC. Ils effectuent les actions suivantes :

- Sélectionnez le contrôleur approprié dans une application Web MVC.
- Pour obtenir une instance de contrôleur spécifique.
- Appeler du contrôleur **Execute** (méthode).

La liste suivante décrit les étapes d’exécution d’un projet Web MVC :

- Première demande concernant l’application de réception 

    - Dans le fichier Global.asax, **itinéraire** objets sont ajoutés à la **RouteTable** objet.
- Effectuer un routage 

    - Le **UrlRoutingModule** module utilise la correspondance de première **itinéraire** de l’objet dans le **RouteTable** collection pour créer le **RouteData** objet, qu’il utilise ensuite pour créer un **RequestContext** (**IHttpContext**) objet.
- Créer le Gestionnaire de demandes MVC 

    - Le **MvcRouteHandler** objet crée une instance de la **MvcHandler** classe et lui passe la **RequestContext** instance.
- Créer le contrôleur 

    - Le **MvcHandler** object utilise le **RequestContext** instance pour identifier le **IControllerFactory** objet (généralement une instance de la  **DefaultControllerFactory** classe) pour créer l’instance de contrôleur.
- Contrôleur d’exécution - le **MvcHandler** instance appelle le contrôleur s **Execute** (méthode). |
- Invoquer l’action 

    - La plupart des contrôleurs héritent de la **contrôleur** classe de base. Pour les contrôleurs qui dans ce cas, le **ControllerActionInvoker** objet qui est associé au contrôleur détermine la méthode d’action de la classe de contrôleur à appeler, puis appelle cette méthode.
- Exécution du résultat 

    - Une méthode d’action par défaut peut recevoir une entrée d’utilisateur, préparer les données de réponse appropriée, puis exécutez le résultat en retournant un résultat de type. Les types de résultats intégrés qui peuvent être exécutés incluent les éléments suivants : **ViewResult** (qui restitue une vue et est le type de résultat plus fréquemment utilisé), **RedirectToRouteResult**,  **RedirectResult**, **ContentResult**, **JsonResult**, et **EmptyResult**.
