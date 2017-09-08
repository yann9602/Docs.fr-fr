---
title: "Introduction à la Protection des données"
author: rick-anderson
description: 
keywords: ASP.NET Core,
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 4542cd37-b47c-454c-be19-d1b5810d67fe
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/introduction
ms.openlocfilehash: bcf1ce5a272a374c9605e50dee5c5fb27305527d
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2017
---
# <a name="introduction-to-data-protection"></a>Introduction à la Protection des données

Les applications Web doivent souvent stocker les données sensibles pour la sécurité. Windows fournit DPAPI pour les applications de bureau, mais il ne convient pas pour les applications web. La pile de protection de données ASP.NET Core fournissent une API de chiffrement simple et facile à utiliser, un développeur peut utiliser pour protéger les données, y compris la rotation et gestion de clés.

La pile de protection de données ASP.NET Core est conçue pour servir de remplacement à long terme pour le <machineKey> élément dans ASP.NET 1.x - 4.x. Il a été conçu pour traiter une bonne partie des lacunes de la pile de services de chiffrement ancien tout en fournissant une solution à l’emploi pour la majorité des cas d’utilisation des applications modernes sont susceptibles de rencontrer.

## <a name="problem-statement"></a>Énoncé du problème

L’instruction problème global peut être établie succinctement dans une phrase unique : j’ai besoin de conserver les informations approuvées pour une récupération ultérieure, mais je n’autorise pas le mécanisme de persistance. Cela peut être écrite en termes de web, comme « Ai-je besoin pour effectuer un aller-retour état approuvé via un client non fiable ».

L’exemple canonique de ce est un cookie d’authentification ou au porteur jeton. Le serveur génère un « Je suis Groot et disposer des autorisations de xyz » du jeton et qu’il transmet au client. Ultérieurement, le client présentera ce jeton sur le serveur, mais le serveur doit quelconque d’assurance que le client n’est pas falsifié le jeton. Par conséquent, la première exigence : authenticité (aussi appelé) l’intégrité, vérification de falsification).

Étant donné que l’état persistant est approuvé par le serveur, nous estimons que cet état peut contenir des informations spécifiques à l’environnement d’exploitation. Cela peut être sous la forme d’un chemin d’accès de fichier, une autorisation, un handle ou une autre référence indirecte, ou certains autres éléments de données de serveur spécifiques. Ces informations ne doivent généralement pas être divulguées à un client non fiable. Par conséquent, la deuxième spécification : la confidentialité.

Enfin, étant donné que les applications modernes sont basées sur des composants que nous avons vu est que les composants individuels voulez tirer parti de ce système sans tenir compte des autres composants dans le système. Par exemple, si un composant d’émission de jeton de support à l’aide de cette pile, elle doit fonctionner sans interférence à partir d’un mécanisme d’anti-CSRF qui peut également être à l’aide de la même pile. Par conséquent, la spécification finale : isolation.

Nous pouvons fournir des contraintes supplémentaires afin de limiter la portée de la configuration requise. Nous partons du principe que tous les services d’exploitation dans le système de cryptage par sont également approuvés et que les données n’a pas besoin d’être généré ou consommés en dehors des services sous notre contrôle direct. En outre, nous avons besoin que les opérations sont aussi rapides que possible, car chaque demande au service web peut accéder via le système de cryptage par une ou plusieurs fois. Cela rend le chiffrement symétrique idéale pour notre scénario, et nous pouvons discount cryptographie asymétrique tant que tel, une fois qu’il est nécessaire.

## <a name="design-philosophy"></a>Philosophie de conception

Nous avons commencé en identifiant les problèmes liés à la pile existante. Une fois que nous avons eu qui, nous le paysage des solutions existantes de mener une enquête et conclu qu’aucune solution existante ne devait tout à fait les fonctionnalités que nous avons recherché. Ensuite, nous avons conçu une solution basée sur plusieurs principes.

* Le système doit offrir la simplicité de configuration. Dans l’idéal, le système est sans aucune configuration et les développeurs peut appuyer sur le sol en cours d’exécution. Les situations où les développeurs doivent configurer un aspect spécifique (par exemple, le référentiel de clé), doit être envisagé faire en sorte que ces configurations spécifiques simple.

* Offre une API pour les consommateurs simple. Les API doivent être facile d’utiliser correctement et difficile à utiliser correctement.

* Les développeurs doivent apprendre pas les principes de la gestion de clés. Le système doit gérer la sélection d’un algorithme et la durée de vie de clé sur le compte de développeur. Dans l’idéal, le développeur ne doit jamais avoir accès au matériel de clé brut.

* Les clés doivent être protégées au repos lorsque cela est possible. Le système doit déterminer un mécanisme de protection par défaut approprié et l’applique automatiquement.

Avec ces principes à l’esprit que nous avons développées simple, [facile à utiliser](using-data-protection.md) pile de protection de données.

L’API de protection des données ASP.NET Core ne sont pas destinées principalement pour la persistance indéfini de charges utiles confidentielles. Autres technologies telles que [DPAPI CNG de Windows](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) et [Azure Rights Management](https://technet.microsoft.com/library/jj585024.aspx) les plus adaptés au scénario de stockage illimité, et ils ont des fonctionnalités de gestion de clé forte en conséquence. Ceci dit, rien interdire à un développeur à l’aide de l’API de protection des données ASP.NET Core pour la protection à long terme des données confidentielles.

## <a name="audience"></a>Public

Le système de protection des données est divisé en cinq packages principales. Divers aspects de ces API ciblent les trois principaux types de publics ;

1. Le [vue d’ensemble des API de consommateur](consumer-apis/overview.md) cibler des développeurs d’applications et d’infrastructure.

   « Je ne souhaite en savoir plus sur le fonctionnement de la pile ou sur la façon dont il est configuré. Je veux simplement effectuer une opération aussi simple d’une manière que possible avec une probabilité élevée de l’aide des API avec succès. »

2. Le [API de configuration](configuration/overview.md) ciblent les développeurs d’applications et les administrateurs système.

   « Ai-je besoin d’indiquer le système de protection des données que mon environnement requiert des paramètres ou des chemins d’accès par défaut ».

3. Les développeurs de la cible d’API d’extensibilité chargé d’implémenter la stratégie personnalisée. L’utilisation de ces API est limitée à rares situations et rencontrée, les développeurs prenant en charge de sécurité.

   « J’ai besoin de remplacer un composant entier au sein du système, car j’ai contraintes liées au comportement véritablement uniques. Je souhaite en savoir uncommonly utilisé des parties de la surface de l’API pour pouvoir créer un plug-in qui répond à mes besoins. »

## <a name="package-layout"></a>Disposition du package

La pile de protection de données se compose de cinq packages.

* Microsoft.AspNetCore.DataProtection.Abstractions contient les interfaces de base IDataProtectionProvider et IDataProtector. Il contient également des méthodes d’extension utiles qui peuvent faciliter l’utilisation de ces types (par exemple, les surcharges de IDataProtector.Protect). Consultez la section d’interfaces de consommateur pour plus d’informations. Si quelqu'un d’autre est responsable de l’instanciation du système de protection des données et que vous consommez simplement les API, vous souhaiterez référence Microsoft.AspNetCore.DataProtection.Abstractions.

* Microsoft.AspNetCore.DataProtection contient l’implémentation de base du système de protection de données, y compris les opérations de chiffrement de base de la gestion de clés, de la configuration et de l’extensibilité. Si vous êtes responsable de l’instanciation du système de protection des données (par exemple, ajouter à une IServiceCollection) ou de la modification ou l’extension de son comportement, vous souhaiterez référence Microsoft.AspNetCore.DataProtection.

* Microsoft.AspNetCore.DataProtection.Extensions contient des API supplémentaires qui permet aux développeurs s’avérer utile, mais qui n’appartiennent pas dans le package de base. Par exemple, ce package contient une API simple « instancier le système pointant vers un répertoire de stockage de clés spécifique sans configuration d’injection de dépendance » (plus d’informations). Il contient également des méthodes d’extension pour limiter la durée de vie de charges utiles protégés (plus d’informations).

* Microsoft.AspNetCore.DataProtection.SystemWeb peut être installé dans une application 4.x ASP.NET pour rediriger ses <machineKey> operations au lieu d’utiliser la nouvelle pile de protection des données. Consultez [compatibilité](compatibility/replacing-machinekey.md#compatibility-replacing-machinekey) pour plus d’informations.

* Microsoft.AspNetCore.Cryptography.KeyDerivation fournit une implémentation du mot de passe PBKDF2 hachage routine et peut être utilisé par les systèmes qui doivent gérer les mots de passe utilisateur en toute sécurité. Consultez [hachage de mot de passe](consumer-apis/password-hashing.md) pour plus d’informations.
