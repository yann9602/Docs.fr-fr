---
uid: web-pages/overview/security/using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site
title: "À l’aide d’un test CAPTCHA pour empêcher des robots d’utiliser votre Razor ASP.NET Web) de Site | Documents Microsoft"
author: microsoft
description: "Cet article explique comment utiliser ReCaptcha (une mesure de sécurité) pour empêcher les programmes automatiques (robots) d’effectuer des tâches dans une page Web ASP.NET (Razor) nous..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/21/2012
ms.topic: article
ms.assetid: 2b381a41-2cb3-40c0-8545-1d393e22877f
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/security/using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site
msc.type: authoredcontent
ms.openlocfilehash: 75e80a3e7ebe787852152404bf2e0bf88a1a6a56
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="using-a-captcha-to-prevent-bots-from-using-your-aspnet-web-razor-site"></a>À l’aide d’un test CAPTCHA pour empêcher des robots d’utiliser votre Razor ASP.NET Web) de Site
====================
par [Microsoft](https://github.com/microsoft)

> Cet article explique comment utiliser ReCaptcha (une mesure de sécurité) pour empêcher les programmes automatiques (robots) à effectuer des tâches dans un site Web ASP.NET Web Pages (Razor).
> 
> **Ce que vous allez apprendre :** 
> 
> - Comment ajouter un test CAPTCHA à votre site.
> 
> Voici les fonctionnalités d’ASP.NET introduites dans l’article :
> 
> - Le `ReCaptcha` helper.
> 
> > [!NOTE]
> > Les informations contenues dans cet article s’applique à la version 1.0 de Pages Web ASP.NET et Web Pages 2.


## <a name="about-captchas"></a>À propos de CAPTCHAs

Chaque fois que vous permettent aux utilisateurs inscrire dans votre site, ou même à entrer un nom et une URL (par exemple, pour un commentaire de blog), vous risquez d’obtenir un flux excessif de noms fausses. Ceux-ci sont souvent conservés par des programmes automatisés (robots) qui tentent de laisser les URL dans tous les sites Web qu’ils peuvent trouver. (Surtout est pour valider les URL de produits pour la vente.)

Vous pouvez aider à vous assurer qu’un utilisateur est la personne et non un programme de l’ordinateur en utilisant un *CAPTCHA* pour valider les utilisateurs lorsqu’ils inscriront ou sinon entrer leur nom et le site. CAPTCHA signifie test entièrement automatisée publique Turing indiquer les uns des autres utilisateurs et ordinateurs. Un test CAPTCHA est un *stimulation-réponse* test dans lequel l’utilisateur est invité à effectuer une opération facile pour une personne effectue mais difficile pour un programme automatisé à faire. Le type le plus courant de CAPTCHA est un où vous consultez certaines lettres de distorsion et que vous êtes invité à les taper. (La distorsion est prévu pour le rendre difficile robots à déchiffrer les lettres).

## <a name="adding-a-recaptcha-test"></a>Ajout d’un Test de ReCaptcha

Dans les pages ASP.NET, vous pouvez utiliser la `ReCaptcha` application d’assistance pour restituer un test CAPTCHA basé sur le service ReCaptcha ([http://recaptcha.net](http://recaptcha.net)). Le `ReCaptcha` affiche une image de deux mots déformés dont disposent les utilisateurs à entrer correctement avant que la page est validée. La réponse de l’utilisateur est validée par le service ReCaptcha.Net.

![](using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site/_static/image1.jpg)

1. Inscrire votre site Web, consultez ReCaptcha.Net ([http://recaptcha.net](http://recaptcha.net)). Lorsque vous avez terminé l’inscription, vous obtenez une clé publique et une clé privée.
2. Ajoutez la bibliothèque de programmes d’assistance ASP.NET Web à votre site Web, comme décrit dans [programmes d’assistance de l’installation dans un Site de Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=252372), si vous n’avez pas encore.
3. Si vous n’avez pas déjà un  *\_AppStart.cshtml* de fichiers, dans le dossier racine d’un site Web, créez un fichier nommé  *\_AppStart.cshtml*.
4. Ajoutez le code suivant `Recaptcha` les paramètres d’assistance dans le  *\_AppStart.cshtml* fichier : 

    [!code-cshtml[Main](using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site/samples/sample1.cshtml?highlight=6-7)]
5. Définir le `PublicKey` et `PrivateKey` propriétés à l’aide de vos propres clés publiques et privées.
6. Enregistrer le  *\_AppStart.cshtml* de fichiers et de le fermer.
7. Dans le dossier racine d’un site Web, créer une nouvelle page nommée *Recaptcha.cshtml*.
8. Remplacez le contenu existant avec les éléments suivants : 

    [!code-cshtml[Main](using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site/samples/sample2.cshtml)]
9. Exécutez le *Recaptcha.cshtml* page dans un navigateur. Si le `PrivateKey` valeur est valide, la page affiche le contrôle ReCaptcha et un bouton. Si vous n’aviez pas globalement dans définir les clés  *\_AppStart.html*, la page affiche une erreur. 

    ![](using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site/_static/image1.png)
10. Entrez les termes pour le test. Si vous passez le test ReCaptcha, vous consultez un message à cet effet. Dans le cas contraire, vous voyez un message d’erreur et le contrôle de ReCaptcha s’affiche de nouveau.

> [!NOTE]
> Si votre ordinateur se trouve sur un domaine qui utilise le serveur proxy, vous devrez peut-être configurer le `defaultproxy` élément de la *Web.config* fichier. L’exemple suivant montre un *Web.config* de fichiers avec le `defaultproxy` élément configuré pour activer le service ReCaptcha fonctionne.
> 
> [!code-xml[Main](using-a-catpcha-to-prevent-automated-programs-bots-from-using-your-aspnet-web-site/samples/sample3.xml)]


<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


- [Personnalisation du comportement de l’échelle du Site pour les Sites des Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=202906)
- [ReCaptcha site](https://www.google.com/recaptcha)
