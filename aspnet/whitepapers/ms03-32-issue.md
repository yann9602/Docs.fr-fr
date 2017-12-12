---
uid: whitepapers/ms03-32-issue
title: "Correctif pour l’erreur de « Application serveur non disponible » après avoir appliqué la mise à jour de sécurité pour Internet Explorer | Documents Microsoft"
author: rick-anderson
description: "Ce document décrit le correctif logiciel qui résout un problème avec la mise à jour de sécurité MS03-32 pour Internet Explorer qui affecte les applications ASP.NET 1.0 en cours d’exécution sur Wi..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2010
ms.topic: article
ms.assetid: 1365eebb-bdf7-4a05-8d18-7f200531be55
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /whitepapers/ms03-32-issue
msc.type: content
ms.openlocfilehash: 8658e387aeb4ea0340080666906b2b89db49a31a
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="fix-for-server-application-unavailable-error-after-applying-security-update-for-ie"></a>Corrigez d’erreur de « Application serveur non disponible » après avoir appliqué la mise à jour de sécurité pour Internet Explorer
====================
> Ce document décrit le correctif logiciel qui résout un problème avec la mise à jour de sécurité MS03-32 pour Internet Explorer qui affecte les applications ASP.NET 1.0 s’exécutant sur Windows XP Professionnel.
> 
> S’applique à ASP.NET 1.0 et Windows XP Professionnel.


Microsoft a identifié un problème avec la mise à jour de sécurité MS03-32 pour le correctif de sécurité Internet Explorer et de la version 1.0 de ASP.NET s’exécutant sur Windows XP. Ce correctif peut être installé manuellement ou en obtenant les récentes mises à jour critiques à partir du site Windows Update.

Le symptôme de ce problème est qu’après avoir installé le correctif logiciel sur un ordinateur Windows XP, toutes les demandes pour les applications ASP.NET exécutées sur le serveur web IIS 5.1 local entraînent un message d’erreur indiquant que « Serveur des applications non disponible ». Les demandes aux serveurs web à distance ne sont pas affectés.

Ce problème affecte uniquement les installations en cours d’exécution ASP.NET 1.0 sur Windows XP. Il n’affecte pas les ordinateurs exécutant Windows 2000 ou Windows Server 2003. Il également ne pas avoir un impact sur les ordinateurs exécutant Windows XP avec ASP.NET 1.1 est installé.

Notez que ce problème **n’est pas** un bogue de sécurité avec ASP.NET. Il **pas** ouvrir ou autoriser les attaques malveillantes contre un serveur ou une application ASP.NET. Au lieu de cela, il est purement un bogue fonctionnel provoqué par le correctif lui-même.

Nous nous efforçons d’une solution permanente pour résoudre ce problème. En attendant, vous pouvez exécuter le fichier de commandes suivant comme solution de contournement pour le problème. Le fichier de commandes effectue les opérations suivantes :

1. Arrête les services d’état IIS et ASP.NET
2. Supprime et recrée le compte ASPNET avec un mot de passe temporaire connu
3. Utilise Windows `runas` commande pour lancer un exécutable qui crée un profil utilisateur ASPNET
4. Réinscrit ASP.NET. Cela crée un nouveau mot de passe aléatoire pour le compte et applique les paramètres de contrôle d’accès par défaut ASP.NET pour celui-ci
5. Redémarre les services IIS

Le fichier de commandes contient un mot de passe temporaire codé en dur de «**1pass@word**» qui vous serez invité à entrer de pour la commande runas, exécutez lorsque le fichier de commandes est exécuté. Une fois la commande runas terminée, le mot de passe du compte ASPNET est recréé avec une valeur aléatoire forte. Notez que le fichier de commandes peut échouer si le mot de passe codé en dur ne répond pas aux exigences de complexité de mot de passe dans votre environnement. Si tel est le cas, vous pouvez le modifier à une autre valeur qui est appropriée pour votre environnement.

*> [!IMPORTANT]*Si vous avez ajouté des paramètres de contrôle d’accès personnalisés ou des autorisations de compte de base de données pour le compte ASPNET, ils devront être recréés à l’issue de ce fichier de commandes. Il s’agit, car lorsque le compte est recréé, il sera obtenir un nouvel identificateur de sécurité (SID).

*> [!IMPORTANT]*Si vous exécutez le processus de travail ASP.NET avec un compte personnalisé autre que le compte ASPNET, vous ne devez pas exécuter ce fichier de commandes. Au lieu de cela, vous devez ouvrir une session manière interactive ou utiliser la commande runas avec ce compte qui a créé un profil utilisateur pour ce compte.

Le fichier de commandes est inclus dans l’archive à extraction automatique ci-dessous. À utiliser :

1. Vous devez exécuter sous un compte avec des privilèges d’administrateur
2. [Téléchargez et ouvrez le fichier exécutable à extraction automatique](ms03-32-issue/_static/fixup1.exe)
3. Extrayez le contenu dans c:\
4. Sélectionnez Exécuter... dans le menu Démarrer, puis entrez`cmd.exe`
5. Dans les fenêtres commande Ouvrir, tapez `c:\fixup.cmd`.
6. Lorsque vous y êtes invité, entrez  **1pass@word**  en tant que le mot de passe.
7. Si vous avez des paramètres de contrôle d’accès précédemment personnalisés ou des autorisations de compte de base de données pour le compte ASPNET, vous devez ré-appliquer ces paramètres maintenant.

Nombre d’excuses pour le désagrément occasionné à. Nous allons publier des informations supplémentaires qu’il est disponible.

Le tableau ci-dessous détaille les plateformes et versions affectées par ce problème.

| .NET Framework | Plateforme | Affectés |
| --- | --- | --- |
| Version 1.0 | Windows 2000 Professionnel | Non |
| Version 1.0 | Windows 2000 Server | Non |
| Version 1.0 | Windows XP Professionnel | Oui |
| Version 1.0 | Windows Server 2003 | Non |
| Version 1.0 | Windows XP Édition familiale avec Cassini | Non |
| Version 1.1 | Windows 2000 Professionnel | Non |
| Version 1.1 | Windows 2000 Server | Non |
| Version 1.1 | Windows XP Professionnel | Non |
| Version 1.1 | Windows Server 2003 | Non |
| Version 1.1 | Windows XP Édition familiale avec Cassini | Non |

Merci,   
 L’équipe ASP.NET
