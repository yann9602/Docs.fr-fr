---
title: "Ôter la protection de charges utiles dont les clés ont été révoqués."
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 6c4e6591-45d2-4d25-855e-062ad352d648
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 5d176515792045545add66ba5aedb0358d8bdc70
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="unprotecting-payloads-whose-keys-have-been-revoked"></a>Ôter la protection de charges utiles dont les clés ont été révoqués.

<a name=data-protection-consumer-apis-dangerous-unprotect></a>

L’API de protection des données ASP.NET Core ne sont pas destinées principalement pour la persistance indéfini de charges utiles confidentielles. Autres technologies telles que [DPAPI CNG de Windows](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) et [Azure Rights Management](https://docs.microsoft.com/rights-management/) les plus adaptés au scénario de stockage illimité, et ils ont des fonctionnalités de gestion de clé forte en conséquence. Ceci dit, rien interdire à un développeur à l’aide de l’API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles. Les clés ne sont jamais supprimés à partir de l’anneau de clé, afin de IDataProtector.Unprotect peut toujours de récupérer des charges utiles existants tant que les clés sont disponibles et valides.

Toutefois, un problème survient lorsque le développeur tente d’ôter la protection des données qui a été protégées avec une clé révoquée, comme IDataProtector.Unprotect lève une exception dans ce cas. Cela peut être préci pour les charges temporaires (par exemple, des jetons d’authentification), car ces types de charges utiles peuvent facilement être recréés par le système, et au pire le visiteur du site peut être requise pour vous reconnecter. Mais pour les charges persistantes, ayant Unprotect lever peut entraîner une perte de données inacceptable.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Pour prendre en charge le scénario de permettre à des charges utiles d’être protégé, même en cas de clés révoqués, le système de protection des données contient un type de IPersistedDataProtector. Pour obtenir une instance de IPersistedDataProtector, simplement obtenir une instance de IDataProtector normalement et essayez de convertir le IDataProtector à IPersistedDataProtector.

> [!NOTE]
> Pas de toutes les instances de IDataProtector peuvent être converties en IPersistedDataProtector. Les développeurs doivent utiliser le langage c# en tant qu’opérateur ou préparé similaire pour éviter les exceptions du runtime due à des casts non valides, et qu’ils doivent disposer gérer le cas de défaillance de manière appropriée.

IPersistedDataProtector expose la surface API suivante :

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
   ```

Cette API prend la charge utile de protégé (en tant que tableau d’octets) et retourne la charge utile non protégée. Il n’existe aucune surcharge basé sur chaîne. Les deux paramètres de sortie sont les suivantes.

* requiresMigration : sera être possède la valeur true si la clé utilisée pour protéger cette charge utile n’est plus la clé active par défaut, par exemple, la clé utilisée pour protéger cette charge utile est ancien et une clé d’opération de restauration a eu lieu. L’appelant peut souhaiter prendre en compte la reprotection de la charge utile selon leurs besoins.

* wasRevoked : valeur de la valeur true si la clé utilisée pour protéger cette charge utile a été révoquée.

>[!WARNING]
> Soyez extrêmement prudent lorsque vous passez ignoreRevocationErrors : true pour la méthode DangerousUnprotect. Si après avoir appelé cette méthode, la valeur de wasRevoked est true, puis la clé utilisée pour protéger cette charge utile a été révoquée et authenticité de la charge utile doit être traitée comme étant suspecte. Dans ce cas uniquement fonctionner sur la charge utile non protégée si vous avez une garantie distincte qu’il est authentique, par exemple, qu’il le provenant d’une base de données sécurisée, plutôt que d’envoyées par un client web non fiable.

[!code-none[Main](dangerous-unprotect/samples/dangerous-unprotect.cs)]
