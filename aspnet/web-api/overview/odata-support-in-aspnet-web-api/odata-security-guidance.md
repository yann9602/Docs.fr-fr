---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-security-guidance
title: "Guide de sécurité pour ASP.NET Web API 2 OData | Documents Microsoft"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/06/2013
ms.topic: article
ms.assetid: b91e6424-1544-4747-bd0b-d1f8418c9653
ms.technology: dotnet-webapi
ms.prod: .net-framework
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-security-guidance
msc.type: authoredcontent
ms.openlocfilehash: 799e2a0c742b545acf3b5cd27531d734aa7def80
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="security-guidance-for-aspnet-web-api-2-odata"></a>Guide de sécurité pour ASP.NET Web API 2 OData
====================
par [Mike Wasson](https://github.com/MikeWasson)

Cette rubrique décrit certains des problèmes de sécurité vous devez prendre en compte lors de l’exposition d’un jeu de données via OData.

## <a name="edm-security"></a>Sécurité du modèle EDM

La sémantique de la requête est basée sur l’entity data model (EDM), pas les types de modèle sous-jacent. Vous pouvez exclure une propriété de l’EDM et il ne sera pas visible à la requête. Par exemple, supposons que votre modèle inclut un type d’employé avec une propriété de salaire. Vous pouvez souhaiter exclure cette propriété de l’EDM pour la masquer à partir de clients.

Il existe deux façons d’exclure une propriété à partir du modèle EDM. Vous pouvez définir le **[IgnoreDataMember]** attribut sur la propriété dans la classe de modèle :

[!code-csharp[Main](odata-security-guidance/samples/sample1.cs)]

Vous pouvez également supprimer par programme la propriété dans le modèle EDM :

[!code-csharp[Main](odata-security-guidance/samples/sample2.cs)]

## <a name="query-security"></a>Sécurité de la requête

Un client malveillant ou naïve peut être en mesure de créer une requête qui prend beaucoup de temps à exécuter. Dans le pire des cas cela peut perturber l’accès à votre service.

Le **[Queryable]** attribut est un filtre d’action qui analyse, valide et applique la requête. Le filtre convertit les options de requête dans une expression LINQ. Lorsque le contrôleur OData retourne un **IQueryable** type, le **IQueryable** fournisseur LINQ convertit l’expression LINQ dans une requête. Par conséquent, les performances dépendent sur le fournisseur LINQ qui est utilisé, ainsi que les caractéristiques particulières de votre schéma de base de données ou du jeu de données.

Pour plus d’informations sur l’utilisation des options de requête OData dans l’API Web ASP.NET, consultez [prise en charge des Options de requête OData](supporting-odata-query-options.md).

Si vous savez que tous les clients approuvés (par exemple, dans un environnement d’entreprise), ou si votre jeu de données est petite, les performances des requêtes est peut-être pas un problème. Dans le cas contraire, vous devez envisager les recommandations suivantes.

- Tester votre service avec différentes requêtes et la base de données de profil.
- Activer la pagination orientée serveur éviter le renvoi d’un grand ensemble de données dans une requête. Pour plus d’informations, consultez [Server-Driven pagination](supporting-odata-query-options.md#server-paging). 

    [!code-csharp[Main](odata-security-guidance/samples/sample3.cs)]
- Vous devez $filter et $orderby ? Certaines applications peuvent autoriser la pagination, à l’aide de $top et $skip de client, mais désactivez les autres options de requête. 

    [!code-csharp[Main](odata-security-guidance/samples/sample4.cs)]
- Pensez à restreindre $orderby aux propriétés dans un index cluster. Le tri des données de grande taille sans index cluster est lent. 

    [!code-csharp[Main](odata-security-guidance/samples/sample5.cs)]
- Nombre de nœuds maximale : le **à maxnodecount doit** propriété sur **[Queryable]** définit les nœuds de nombre maximales autorisés dans l’arborescence de syntaxe $filter. La valeur par défaut est 100, mais vous pouvez définir une valeur inférieure, car un grand nombre de nœuds peut être lent à compiler. Cela est particulièrement vrai si vous utilisez LINQ to Objects (autrement dit, les requêtes LINQ sur une collection en mémoire, sans l’utilisation d’un fournisseur LINQ intermédiaire). 

    [!code-csharp[Main](odata-security-guidance/samples/sample6.cs)]
- Pensez à désactiver les fonctions any() et all(), car il peuvent être lentes. 

    [!code-csharp[Main](odata-security-guidance/samples/sample7.cs)]
- Si toutes les propriétés de chaîne contient des chaînes de grande taille #8212for exemple, une description de produit ou une entrée de blog & 8212consider # désactiver les fonctions de chaîne. 

    [!code-csharp[Main](odata-security-guidance/samples/sample8.cs)]
- Envisagez d’en interdisant le filtrage sur les propriétés de navigation. Le filtrage sur les propriétés de navigation peut entraîner une jointure qui peut être lente, en fonction de votre schéma de base de données. Le code suivant montre un validateur de requête qui empêche le filtrage sur les propriétés de navigation. Pour plus d’informations sur les programmes de validation de requête, consultez [Validation de requête](supporting-odata-query-options.md#query-validation). 

    [!code-csharp[Main](odata-security-guidance/samples/sample9.cs)]
- Pensez à limiter les requêtes $filter en écrivant un validateur personnalisé pour votre base de données. Par exemple, considérez les deux requêtes suivantes : 

    - Tous les films avec acteurs dont le nom commence par « A ».
    - Tous les films publiées en 1994.

    À moins que les films sont indexés par acteurs, la première requête peut nécessiter le moteur de base de données à analyser l’intégralité de la liste de films. Tandis que la deuxième requête peut être acceptable, en supposant que films sont indexées par année de sortie.

    Le code suivant montre un validateur qui permet de filtrer sur les propriétés « ReleaseYear » et « Title », mais aucune autre propriété.

    [!code-csharp[Main](odata-security-guidance/samples/sample10.cs)]
- En règle générale, envisagez les fonctions $filter que vous avez besoin. Si vos clients ne doivent pas l’expressivité complète de $filter, vous pouvez limiter les fonctions admises.
