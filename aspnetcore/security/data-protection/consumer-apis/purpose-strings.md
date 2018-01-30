---
title: "Chaînes d’objectif"
author: rick-anderson
description: "Ce document décrit en détail comment les chaînes de fin sont utilisées dans l’API de protection des données ASP.NET Core."
manager: wpickett
ms.author: riande
ms.date: 10/14/2016
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: b4a0db801ecc1c4ba0762f0c9faf7429b4ac097b
ms.sourcegitcommit: a510f38930abc84c4b302029d019a34dfe76823b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2018
---
# <a name="purpose-strings"></a>Chaînes d’objectif

<a name="data-protection-consumer-apis-purposes"></a>

Les composants qui consomment `IDataProtectionProvider` doit passer un unique *à des fins* paramètre à la `CreateProtector` (méthode). Les besoins *paramètre* est inhérent à la sécurité du système de protection de données, car il fournit une isolation entre les consommateurs de services de chiffrement, même si les clés de chiffrement racine sont les mêmes.

Lorsqu’un consommateur spécifie un objectif, la chaîne de l’objet est utilisée, ainsi que les clés de chiffrement racine pour dériver des sous-clés de chiffrement uniques pour ce consommateur. Cela permet d’isoler le consommateur à partir de tous les autres consommateurs de services de chiffrement dans l’application : aucun autre composant ne peut lire les charges utiles, et il ne peut pas lire les charges de tout autre composant. Cette isolation rend également irréalisable différentes catégories d’attaque par rapport au composant.

![Exemple de diagramme d’objectif](purpose-strings/_static/purposes.png)

Dans le diagramme ci-dessus, `IDataProtector` instances A et B **ne peut pas** chacune des charges utiles, uniquement de lire leurs propres.

La chaîne de l’objet ne peut être divulgué. Il doit simplement être unique dans le sens qu’aucun autre composant valide ne fournira jamais la même chaîne de l’objet.

>[!TIP]
> À l’aide de l’espace de noms et nom de type du composant de consommation de l’API de protection des données est une règle empirique, comme dans les exercices pratiques de que ces informations ne seront jamais en conflit.
>
>Un composant créé par Contoso qui est responsable de minting des jetons de support peut utiliser Contoso.Security.BearerToken comme chaîne de son objectif. Ou - plus -, il peut utiliser Contoso.Security.BearerToken.v1 en tant que chaîne de son objectif. Ajoutez le numéro de version permet à une version ultérieure utiliser Contoso.Security.BearerToken.v2 comme son objectif et les différentes versions seraient totalement isolées les unes des autres aussi loin que charges utiles.

Depuis le paramètre à des fins de `CreateProtector` est un tableau de chaînes, ci-dessus pourrait avoir été à la place spécifiée comme `[ "Contoso.Security.BearerToken", "v1" ]`. Cela permet l’établissement d’une hiérarchie des objectifs et ouvrez la possibilité d’une architecture mutualisées des scénarios avec le système de protection des données.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Composants ne doit pas autoriser l’entrée utilisateur non fiable être la seule source d’entrée pour la chaîne à des fins.
>
>Par exemple, considérez un composant Contoso.Messaging.SecureMessage qui est responsable du stockage des messages sécurisés. Si le composant de messagerie sécurisé devait appeler `CreateProtector([ username ])`, puis un utilisateur malveillant peut créer un compte avec un nom d’utilisateur « Contoso.Security.BearerToken » dans la tentative d’obtention du composant à appeler `CreateProtector([ "Contoso.Security.BearerToken" ])`, donc par inadvertance à l’origine de la messagerie sécurisée système de charges utiles de monnaies qui peut être perçu comme les jetons d’authentification.
>
>Une chaîne à des fins de meilleures pour le composant de messagerie serait `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, qui fournit d’isolation appropriée.

L’isolation assurée par et les comportements de `IDataProtectionProvider`, `IDataProtector`, et à des fins de sont les suivantes :

* Pour une donnée `IDataProtectionProvider` objet, le `CreateProtector` méthode crée une `IDataProtector` objet exclusivement liés à la fois à la `IDataProtectionProvider` l’objet qui a créé et le paramètre à des fins qui a été passé dans la méthode.

* Le paramètre d’objet ne doit pas être null. (Si à des fins est spécifié sous forme de tableau, cela signifie que le tableau ne doit pas être de longueur nulle, et tous les éléments du tableau doivent être non null.) Un objectif de la chaîne vide est autorisée techniquement mais est déconseillé.

* Arguments de deux objectifs sont équivalentes si et seulement si elles contiennent les mêmes chaînes (à l’aide d’un comparateur ordinal) dans le même ordre. Un argument de fonction unique est équivalent à la baie à des fins de l’élément correspondant.

* Deux `IDataProtector` objets sont équivalents si et seulement si elles sont créées à partir de l’équivalent `IDataProtectionProvider` objets avec des paramètres à des fins équivalentes.

* Pour une donnée `IDataProtector` objet, un appel à `Unprotect(protectedData)` l’original `unprotectedData` si et seulement si `protectedData := Protect(unprotectedData)` pour un équivalent `IDataProtector` objet.

> [!NOTE]
> Nous envisageons pas le cas où un composant choisit intentionnellement une chaîne de l’objectif qui est en conflit avec un autre composant. Un tel composant essentiellement est considéré comme malveillant, et ce système n’est pas destiné à fournir des garanties de sécurité dans le cas où un code malveillant est déjà en cours d’exécution à l’intérieur du processus de travail.
