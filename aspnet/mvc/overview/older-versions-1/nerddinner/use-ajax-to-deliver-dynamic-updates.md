---
uid: mvc/overview/older-versions-1/nerddinner/use-ajax-to-deliver-dynamic-updates
title: "Utiliser AJAX pour fournir des mises à jour dynamiques | Documents Microsoft"
author: microsoft
description: "Étape 10 prend en charge les utilisateurs connectés à RSVP leur intérêt de participer à un dîner, à l’aide d’une approche basée sur Ajax intégrée dans le détail dîner..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 07/27/2010
ms.topic: article
ms.assetid: 18700815-8e6c-4489-91af-7ea9dab6529e
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/older-versions-1/nerddinner/use-ajax-to-deliver-dynamic-updates
msc.type: authoredcontent
ms.openlocfilehash: 7b75f8c6cf08112eb77d1a9a40222ed1425ef3a7
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="use-ajax-to-deliver-dynamic-updates"></a>Utiliser AJAX pour fournir des mises à jour dynamiques
====================
par [Microsoft](https://github.com/microsoft)

[Télécharger le PDF](http://aspnetmvcbook.s3.amazonaws.com/aspnetmvc-nerdinner_v1.pdf)

> Il s’agit d’étape 10 a gratuit [« « l’application NerdDinner](introducing-the-nerddinner-tutorial.md) qui parcours à comment générer un petit, mais se termine, application web à l’aide d’ASP.NET MVC 1.
> 
> Étape 10 prend en charge les utilisateurs connectés à RSVP leur intérêt de participer à un dîner, à l’aide d’une approche basée sur Ajax intégrée dans la page de détails dîner.
> 
> Si vous utilisez ASP.NET MVC 3, nous vous recommandons de suivre les [mise en route a démarré avec MVC 3](../../older-versions/getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) ou [magasin de musique MVC](../../older-versions/mvc-music-store/mvc-music-store-part-1.md) didacticiels.


## <a name="nerddinner-step-10-ajax-enabling-rsvps-accepts"></a>NerdDinner étape 10 : Accepte les AJAX Activation RSVP

Nous allons maintenant implémenter la prise en charge pour les utilisateurs connectés à RSVP leur intérêt de participer à un dîner. Nous allons activer cette option à l’aide d’une approche basée sur AJAX intégrée dans la page de détails dîner.

### <a name="indicating-whether-the-user-is-rsvpd"></a>Qui indique si l’utilisateur est auxquels elle répondu

Les utilisateurs peuvent visiter le */Dinners/détails / [id*] URL pour afficher les détails sur un dîner particulier :

![](use-ajax-to-deliver-dynamic-updates/_static/image1.png)

La méthode d’action est implémentée de Details() comme suit :

[!code-csharp[Main](use-ajax-to-deliver-dynamic-updates/samples/sample1.cs)]

La première étape pour implémenter la prise en charge RSVP seront pour ajouter une méthode d’assistance de « IsUserRegistered(username) » à notre objet Dinner (au sein de la classe partielle Dinner.cs que nous avons créé précédemment). Cette méthode d’assistance retourne true ou false selon si l’utilisateur est auxquels elle répondu actuellement pour le dîner :

[!code-csharp[Main](use-ajax-to-deliver-dynamic-updates/samples/sample2.cs)]

Nous pouvons ensuite ajouter le code suivant à notre modèle de vue Details.aspx pour afficher un message approprié indiquant si l’utilisateur est inscrit ou non pour l’événement :

[!code-html[Main](use-ajax-to-deliver-dynamic-updates/samples/sample3.html)]

Et maintenant lorsqu’un utilisateur visite un dîner, ils sont inscrits pour qu’ils s’affiche ce message :

![](use-ajax-to-deliver-dynamic-updates/_static/image2.png)

Et lorsqu’ils visitent un dîner ne sont pas inscrits pour ils verront le message ci-après :

![](use-ajax-to-deliver-dynamic-updates/_static/image3.png)

### <a name="implementing-the-register-action-method"></a>Implémentation de la méthode d’Action de Registre

Ajoutons maintenant les fonctionnalités nécessaires pour permettre aux utilisateurs RSVP pour un dîner à partir de la page de détails.

Pour l’implémenter, nous allons créer une nouvelle classe « RSVPController » en cliquant sur le répertoire \Controllers et en choisissant Ajouter -&gt;commande de menu de contrôleur.

Nous allons implémenter une méthode d’action « Register » dans la nouvelle classe RSVPController qui accepte un id pour un dîner en tant qu’argument, récupère l’objet Dinner approprié, vérifie si l’utilisateur connecté est actuellement dans la liste des utilisateurs qui se sont inscrits à elle et si pas ajoute un objet RSVP pour eux :

[!code-csharp[Main](use-ajax-to-deliver-dynamic-updates/samples/sample4.cs)]

Notez que ci-dessus comment nous sommes retournant une simple chaîne en tant que la sortie de la méthode d’action. Ce message dans un modèle d’affichage : nous avons ont incorporé, car il est si petit nous suivrons simplement la méthode d’assistance Content() sur la classe de base de contrôleur et le retour d’un message de type chaîne comme ci-dessus.

### <a name="calling-the-rsvpforevent-action-method-using-ajax"></a>Appel de la méthode d’Action RSVPForEvent à l’aide d’AJAX

Nous allons utiliser AJAX pour appeler la méthode d’action de Registre à partir de la vue des détails. Cette implémentation est assez simple. Tout d’abord, nous allons ajouter deux références de bibliothèque de script :

[!code-html[Main](use-ajax-to-deliver-dynamic-updates/samples/sample5.html)]

La première bibliothèque fait référence à la bibliothèque de script côté client de base ASP.NET AJAX. Ce fichier est d’environ 24 Ko taille (compressé) et contient des fonctionnalités AJAX core côté client. La deuxième bibliothèque contient des fonctions utilitaires qui s’intègrent intégrés AJAX méthodes d’assistance de ASP.NET MVC (ce que nous allons utiliser peu de temps).

Vous pouvez ensuite mettre à jour le code ajouté précédemment afin qu’au lieu d’outputing un message « Vous n’êtes pas inscrit pour cet événement », nous à la place d’afficher un lien que lors de l’objet d’un push du modèle de vue effectue un appel AJAX qui appelle la méthode d’action RSVPForEvent sur notre contrôleur RSVP et RSVPs l’utilisateur :

[!code-aspx[Main](use-ajax-to-deliver-dynamic-updates/samples/sample6.aspx)]

La méthode d’assistance Ajax.ActionLink() ci-dessus est intégrée à ASP.NET MVC et est semblable à la méthode d’assistance Html.ActionLink(), sauf qu’au lieu d’effectuer une navigation standard rend un appel AJAX de la méthode d’action lorsque vous cliquez sur le lien. Nous sommes au-dessus appelant la méthode d’action « Register » sur le contrôleur « RSVP » et le DinnerID en tant que le paramètre « id » en lui passant. Le dernier paramètre AjaxOptions nous passons indique que vous voulez prendre le contenu retourné à partir de la méthode d’action et de mettre à jour le code HTML &lt;div&gt; élément sur la page dont l’id est « rsvpmsg ».

Et maintenant lorsqu’un utilisateur accède à un dîner qu’ils ne sont pas enregistrés pour encore un lien vers RSVP pour qu’il s’affichent :

![](use-ajax-to-deliver-dynamic-updates/_static/image4.png)

S’il clique sur le lien « RSVP pour cet événement » ils veulent effectuer un appel AJAX de la méthode d’action de Registre sur le contrôleur RSVP, et quand elle se termine, il voit un message mis à jour comme ci-dessous :

![](use-ajax-to-deliver-dynamic-updates/_static/image5.png)

La bande passante réseau et le trafic impliqué lors de l’établissement de cet appel AJAX est très légère. Lorsque l’utilisateur clique sur le lien « RSVP pour cet événement », un petit HTTP POST réseau est demandé pour le */Dinners/Register/1* URL ressemble à ci-dessous sur le câble :

[!code-console[Main](use-ajax-to-deliver-dynamic-updates/samples/sample7.cmd)]

Et la réponse à partir de la méthode d’action Register est simplement :

[!code-console[Main](use-ajax-to-deliver-dynamic-updates/samples/sample8.cmd)]

Cet appel léger est rapide et fonctionne même sur un réseau lent.

### <a name="adding-a-jquery-animation"></a>Ajout d’une Animation de jQuery

Nous avons implémenté les fonctionnalités AJAX fonctionnent bien et rapide. Il peut parfois se produire si vite, cependant, qu’un utilisateur en ne rendiez pas compte que le lien RSVP a été remplacé par un nouveau texte. Pour rendre le résultat soit un peu plus évidente, nous pouvons ajouter une animation simple pour attirer l’attention sur le message de mise à jour.

La valeur par défaut du modèle de projet ASP.NET MVC inclut jQuery : une bibliothèque de JavaScript d’excellent (et très populaire) open source qui est également pris en charge par Microsoft. jQuery fournit un nombre de fonctionnalités, y compris une bibliothèque de sélection et effets nice DOM HTML.

Pour utiliser jQuery, nous allons tout d’abord ajouter une référence de script à celui-ci. Étant donné que nous allons utiliser jQuery dans différents emplacements au sein de notre site, nous allons ajouter la référence de script au sein de notre fichier de page maître Site.master afin que toutes les pages de l’utiliser.

[!code-html[Main](use-ajax-to-deliver-dynamic-updates/samples/sample9.html)]

*Conseil : Vérifiez que vous avez installé le correctif du JavaScript intellisense pour Visual Studio 2008 SP1 qui permet la prise en charge d’intellisense plus riche pour les fichiers JavaScript (y compris jQuery). Vous pouvez le télécharger à partir de : http://tinyurl.com/vs2008javascripthotfix*

Le code écrit à l’aide de JQuery souvent utilise un « $() » global méthode JavaScript qui Récupère un ou plusieurs des éléments HTML à l’aide d’un sélecteur CSS. Par exemple, *$("#rsvpmsg")* sélectionne un élément HTML portant l’id rsvpmsg, tandis que *$(".something")* Sélectionnez tous les éléments dont le « quelque chose « CSS nom de la classe. Vous pouvez également écrire des requêtes plus avancées telles que « retourner tous les boutons radio activé » à l’aide d’une requête de sélecteur comme : *$(« entrée [@type= radio] [@checked] »)*.

Une fois que vous avez sélectionné les éléments, vous pouvez appeler des méthodes sur ces derniers pour effectuer une action, comme les masquer : *$(#rsvpmsg").hide() » ;*

Dans notre scénario RSVP, nous allons définir une fonction JavaScript simple nommée « AnimateRSVPMessage » qui sélectionne la « rsvpmsg » &lt;div&gt; et réalise une animation de la taille de son contenu de texte. Le code ci-dessous démarre le texte de petite, puis les causes augmentation sur un laps de temps 400 millisecondes :

[!code-html[Main](use-ajax-to-deliver-dynamic-updates/samples/sample10.html)]

Nous pouvons ensuite câble de cette fonction JavaScript à appeler après exécution de notre appel AJAX en passant son nom à la méthode d’assistance de Ajax.ActionLink() (via les AjaxOptions « OnSuccess » propriété d’événement) :

[!code-aspx[Main](use-ajax-to-deliver-dynamic-updates/samples/sample11.aspx)]

Et maintenant lorsque vous cliquez sur le lien « RSVP pour cet événement » et notre appel AJAX termine correctement, le message contenu envoyé précédent sera animer et devenir volumineux :

![](use-ajax-to-deliver-dynamic-updates/_static/image6.png)

En plus de fournir un événement « OnSuccess », l’objet AjaxOptions expose les méthodes OnBegin OnFailure, événements et OnComplete que vous pouvez gérer (ainsi que diverses autres propriétés et les options utiles).

### <a name="cleanup---refactor-out-a-rsvp-partial-view"></a>Nettoyage - refactoriser une vue partielle RSVP

Notre modèle de vue Détails commence à obtenir un peu de temps, les heures supplémentaires rend un peu plus difficile à comprendre. Pour améliorer la lisibilité du code, nous allons terminer en créant une vue partielle – RSVPStatus.ascx – qui encapsulent le code de vue RSVP pour notre page de détails.

Vous pouvez le faire en cliquant sur le dossier \Views\Dinners puis en choisissant Ajouter -&gt;afficher la commande de menu. Nous allons qu’il procède à un objet dîner comme son ViewModel fortement typée. Nous pouvons ensuite copier/coller le contenu RSVP à partir de notre fichier details.aspx dans celui-ci.

Une fois que nous avons terminé, nous allons également créer une autre vue partielle – EditAndDeleteLinks.ascx - qui encapsule notre modifier et supprimer lien Afficher le code. Nous allons également l’avoir prennent un objet dîner comme son ViewModel fortement typée et copier/coller la logique de modifier et supprimer à partir de notre fichier details.aspx dans celui-ci.

Détails Afficher le modèle peut ensuitent simplement incluent deux appels de méthode Html.RenderPartial() en bas :

[!code-aspx[Main](use-ajax-to-deliver-dynamic-updates/samples/sample12.aspx)]

Cela rend le code plus propre pour lire et mettre à jour.

### <a name="next-step"></a>Étape suivante

Examinons à présent comment nous pouvons utiliser AJAX davantage et ajouter la prise en charge du mappage interactif à notre application.

>[!div class="step-by-step"]
[Précédent](secure-applications-using-authentication-and-authorization.md)
[Suivant](use-ajax-to-implement-mapping-scenarios.md)
