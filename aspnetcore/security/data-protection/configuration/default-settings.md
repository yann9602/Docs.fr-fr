---
title: "Durée de vie et de gestion de clés"
author: rick-anderson
description: "Décrit la durée de vie et de gestion de clés."
keywords: "ASP.NET Core, la gestion, DPAPI, DataProtection clé"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: ef7dad2a-7029-4ae5-8f06-1fbebedccaa4
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/configuration/default-settings
ms.openlocfilehash: 5ac2d80e7d1cebcbc792e1247608e67991ec36f4
ms.sourcegitcommit: 8f4d4fad1ca27adf9e396f5c205c9875a3963664
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2017
---
# <a name="key-management-and-lifetime"></a>Durée de vie et de gestion de clés

<a name="data-protection-default-settings"></a>

## <a name="key-management"></a>Gestion de clés

Le système tente de détecter de son environnement d’exploitation et fournissent une bonne sans aucune configuration comportementales valeurs par défaut. L’heuristique utilisée est la suivante.

1. Si le système est hébergé dans les Sites Web Azure, les clés sont conservés dans le dossier « % HOME%\ASP.NET\DataProtection-Keys ». Ce dossier est sauvegardé par le stockage réseau et est synchronisé sur tous les ordinateurs hébergeant l’application. Les clés ne sont pas protégées au repos. Ce dossier fournit l’anneau de clé à toutes les instances d’une application dans un emplacement de déploiement. Les emplacements de déploiement distinct, telles que les intermédiaires et de Production, pas partagent un anneau de clé. Lorsque vous échangez entre les emplacements de déploiement, par exemple le remplacement intermédiaire en Production ou à l’aide de / B test, n’importe quel système à l’aide de la protection des données ne sera pas en mesure de déchiffrer les données stockées à l’aide de l’anneau de clé à l’intérieur de l’emplacement précédent. Cela entraîne des utilisateurs est enregistrés en dehors d’une application ASP.NET qui utilise le middleware du cookie ASP.NET standard, car elle utilise la protection des données à protéger ses cookies. Si vous le souhaitez, indépendant de l’emplacement de clé anneaux utilisent un fournisseur de l’anneau de clé externe, telles que le stockage Blob Azure, Azure Key Vault, un magasin SQL, ou le cache Redis.

2. Si le profil utilisateur est disponible, les clés sont conservés dans le dossier « % LOCALAPPDATA%\ASP.NET\DataProtection-Keys ». En outre, si le système d’exploitation est Windows, ils allez chiffrées au repos à l’aide de DPAPI.

3. Si l’application est hébergée dans IIS, les clés sont conservées dans le Registre HKLM dans une clé de Registre spéciale qui est l’ACL sur uniquement pour le compte de processus de travail. Les clés sont chiffrées au repos à l’aide de DPAPI.

4. Si aucune de ces conditions correspond à, les clés ne sont pas conservés en dehors du processus en cours. Lorsque le processus s’arrête, tous les généré clés seront perdues.

Le développeur est toujours dans un contrôle total et peut remplacer comment et où les clés sont stockées. Les trois premières options ci-dessus doivent bonnes valeurs par défaut pour la plupart des applications similaires à la manière dont ASP.NET <machineKey> les routines de la génération automatique a fonctionné dans le passé. Finale, l’option de restauration sont classés est le seul scénario qui nécessite réellement au développeur de spécifier [configuration](overview.md) initial s’ils veulent persistance des clés, mais ce recours se produit uniquement dans les rares cas.

>[!WARNING]
> Si le développeur remplace cette heuristique et pointe le système de protection des données dans un dépôt de clé spécifique, le chiffrement automatique de clés au repos sera désactivé. Au repos la protection peut être réactivée via [configuration](overview.md).

## <a name="key-lifetime"></a>Durée de vie

Par défaut, les clés ont une durée de vie de 90 jours. Lorsqu’une clé expire, le système sera automatiquement générer une nouvelle clé et définir la nouvelle clé comme clé active. Tant que clés supprimées restent sur le système, que vous serez toujours en mesure de déchiffrer les données protégées avec eux. Consultez [gestion de clés](../implementation/key-management.md#data-protection-implementation-key-management-expiration) pour plus d’informations.

## <a name="default-algorithms"></a>Algorithmes par défaut

L’algorithme de protection de charge utile par défaut utilisé est AES-256-CBC pour la confidentialité et HMACSHA256 authenticité. Une clé principale de 512 bits, tous les 90 jours, de restauration est utilisée pour dériver les deux sous-clés utilisés pour ces algorithmes sur une base par charge. Consultez [des sous-clés de dérivation](../implementation/subkeyderivation.md#data-protection-implementation-subkey-derivation-aad) pour plus d’informations.
