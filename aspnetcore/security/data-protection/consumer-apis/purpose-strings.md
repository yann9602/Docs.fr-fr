---
title: "Chaînes d’objectif"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: c96ed361-c382-4980-8933-800e740cfc38
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 799c3dc2768e264307783efafee626a346a9362c
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="purpose-strings"></a>Chaînes d’objectif

<a name="data-protection-consumer-apis-purposes"></a>

Les composants qui consomment IDataProtectionProvider doivent passer un unique *à des fins* paramètre à la méthode CreateProtector. Les besoins *paramètre* est inhérent à la sécurité du système de protection de données, car il fournit une isolation entre les consommateurs de services de chiffrement, même si les clés de chiffrement racine sont les mêmes.

Lorsqu’un consommateur spécifie un objectif, la chaîne de l’objet est utilisée, ainsi que les clés de chiffrement racine pour dériver des sous-clés de chiffrement uniques pour ce consommateur. Cela permet d’isoler le consommateur à partir de tous les autres consommateurs de services de chiffrement dans l’application : aucun autre composant ne peut lire les charges utiles, et il ne peut pas lire les charges de tout autre composant. Cette isolation rend également irréalisable différentes catégories d’attaque par rapport au composant.

![Exemple de diagramme d’objectif](purpose-strings/_static/purposes.png)

Dans le diagramme ci-dessus IDataProtector instances A et B **ne peut pas** chacune des charges utiles, uniquement de lire leurs propres.

La chaîne de l’objet ne peut être divulgué. Il doit simplement être unique dans le sens qu’aucun autre composant valide ne fournira jamais la même chaîne de l’objet.

>[!TIP]
> À l’aide de l’espace de noms et nom de type du composant de consommation de l’API de protection des données est une règle empirique, comme dans les exercices pratiques de que ces informations ne seront jamais en conflit.
>
>Un composant créé par Contoso qui est responsable de minting des jetons de support peut utiliser Contoso.Security.BearerToken comme chaîne de son objectif. Ou - plus -, il peut utiliser Contoso.Security.BearerToken.v1 en tant que chaîne de son objectif. Ajoutez le numéro de version permet à une version ultérieure utiliser Contoso.Security.BearerToken.v2 comme son objectif et les différentes versions seraient totalement isolées les unes des autres aussi loin que charges utiles.

Étant donné que le paramètre à des fins CreateProtector est un tableau de chaînes, ci-dessus pourrait ont été plutôt spécifiés en tant que [« Contoso.Security.BearerToken », « v1 »]. Cela permet l’établissement d’une hiérarchie des objectifs et ouvrez la possibilité d’une architecture mutualisées des scénarios avec le système de protection des données.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Composants ne doivent pas permettre d’entrée d’utilisateur non fiable être la seule source d’entrée pour la chaîne à des fins.
>
>Par exemple, considérez un composant Contoso.Messaging.SecureMessage qui est responsable du stockage des messages sécurisés. Si le composant de messagerie sécurisé devait appeler CreateProtector ([username]), un utilisateur malveillant peut créer un compte avec un nom d’utilisateur « Contoso.Security.BearerToken » dans la tentative d’obtention du composant à appeler CreateProtector([« Contoso.Security.BearerToken »]), par conséquent par inadvertance à l’origine du système de messagerie sécurisé pour les charges utiles de monnaies qui peut être perçu comme les jetons d’authentification.
>
>Une chaîne à des fins de meilleures pour le composant de messagerie serait CreateProtector ([« Contoso.Messaging.SecureMessage », « utilisateur : nom d’utilisateur »]), qui fournit d’isolation appropriée.

L’isolation assurée par et comportements de IDataProtectionProvider, IDataProtector et à des fins de sont les suivantes :

* Pour un objet IDataProtectionProvider donné, la méthode CreateProtector crée un objet IDataProtector exclusivement lié à l’objet IDataProtectionProvider qui l’a créé et le paramètre à des fins qui a été passé dans la méthode.

* Le paramètre d’objet ne doit pas être null. (Si à des fins est spécifié sous forme de tableau, cela signifie que le tableau ne doit pas être de longueur nulle, et tous les éléments du tableau doivent être non null.) Un objectif de la chaîne vide est autorisée techniquement mais est déconseillé.

* Arguments de deux objectifs sont équivalentes si et seulement si elles contiennent les mêmes chaînes (à l’aide d’un comparateur ordinal) dans le même ordre. Un argument de fonction unique est équivalent à la baie à des fins de l’élément correspondant.

* Deux objets IDataProtector sont équivalentes si et seulement si elles sont créés à partir des objets de IDataProtectionProvider équivalentes avec des paramètres à des fins équivalentes.

* Pour un objet IDataProtector donné, un appel à Unprotect(protectedData) retourne l’unprotectedData d’origine si et seulement si protectedData : = Protect(unprotectedData) pour un objet IDataProtector équivalent.

> [!NOTE]
> Nous envisageons pas le cas où un composant choisit intentionnellement une chaîne de l’objectif qui est en conflit avec un autre composant. Un tel composant essentiellement est considéré comme malveillant, et ce système n’est pas destiné à fournir des garanties de sécurité dans le cas où un code malveillant est déjà en cours d’exécution à l’intérieur du processus de travail.
