---
uid: mvc/overview/security/preventing-open-redirection-attacks
title: "Prévention des attaques de Redirection ouvert (c#) | Documents Microsoft"
author: jongalloway
description: "Ce didacticiel explique comment vous pouvez empêcher des attaques de redirection ouvert dans vos applications ASP.NET MVC. Ce didacticiel décrit les modifications qui ont été apportées en cours..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 69fb02e0-f5b7-4c35-878c-fa87164fc785
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/preventing-open-redirection-attacks
msc.type: authoredcontent
ms.openlocfilehash: 97e0aacbf21914bf95f01019cf4dcc9e7ca1c4be
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="preventing-open-redirection-attacks-c"></a>Prévention des attaques de Redirection ouvert (c#)
====================
par [Jon Galloway](https://github.com/jongalloway)

> Ce didacticiel explique comment vous pouvez empêcher des attaques de redirection ouvert dans vos applications ASP.NET MVC. Ce didacticiel décrit les modifications qui ont été apportées dans le AccountController dans ASP.NET MVC 3 et montre comment vous pouvez appliquer ces modifications dans votre ASP.NET MVC 1.0 existant et les applications de 2.


## <a name="what-is-an-open-redirection-attack"></a>Qu’est une attaque de Redirection ouvrir ?

Toutes les applications web qui redirige vers une URL qui est spécifiée via la demande tels que les données de chaîne de requête ou un formulaire peuvent potentiellement être falsifiées rediriger les utilisateurs vers une URL externe, malveillante. Cette manipulation est appelée une attaque de redirection ouvert.

Chaque fois que votre logique d’application redirige vers une URL spécifiée, vous devez vérifier que l’URL de redirection n’a pas été falsifié. La connexion utilisée dans la valeur par défaut AccountController pour ASP.NET MVC 1.0 et ASP.NET MVC 2 est vulnérable aux attaques de redirection d’ouvrir. Heureusement, il est facile de mettre à jour vos applications existantes pour utiliser les corrections à partir de la version préliminaire de ASP.NET MVC 3.

Pour comprendre la vulnérabilité, examinons le fonctionne de la redirection de connexion dans un projet d’Application Web de ASP.NET MVC 2 par défaut. Dans cette application, essayez de visiter une action du contrôleur qui a l’attribut [Authorize] redirige les utilisateurs non autorisés à la vue /Account/LogOn. Cette redirection vers /Account/LogOn inclut un paramètre de chaîne de requête returnUrl afin que l’utilisateur peut être retourné à l’URL demandée à l’origine une fois qu’ils ont connecté avec succès.

Dans la capture d’écran ci-dessous, nous constatons qu’une tentative d’accéder à la vue /Account/ChangePassword quand ne pas connecté entraîne une redirection pour /Account/LogOn ? ReturnUrl = % 2fAccount % 2fChangePassword % 2f.

[![](preventing-open-redirection-attacks/_static/image2.png)](preventing-open-redirection-attacks/_static/image1.png)

**Figure 01**: page de connexion avec une redirection ouverte

Étant donné que le paramètre de chaîne de requête ReturnUrl n’est pas validé, un intrus peut modifier pour injecter de n’importe quelle adresse URL dans le paramètre pour effectuer une attaque de redirection ouvert. Pour illustrer cela, nous pouvons modifier le paramètre ReturnUrl [http://bing.com](http://bing.com), de sorte que l’URL de connexion résultant sera/Account / d’ouverture de session ? ReturnUrl = http://www.bing.com/. Lors de la connexion avec succès au site, nous allons redirigés vers [http://bing.com](http://bing.com). Étant donné que cette redirection n’est pas validée, elle peut pointer à la place à un site malveillant qui tente d’amener l’utilisateur.

### <a name="a-more-complex-open-redirection-attack"></a>Une attaque de Redirection ouvert plus complexes

Les attaques par redirection ouverts sont particulièrement dangereux, car un utilisateur malveillant sait que nous essayons de se connecter à un site Web spécifique, ce qui nous rend vulnérables à un [hameçonnage](https://www.microsoft.com/protect/fraud/phishing/symptoms.aspx). Par exemple, un attaquant pourrait envoyer des messages électroniques malveillants pour les utilisateurs du site Web afin de capturer leur mot de passe. Examinons ce fonctionnement sur le site NerdDinner. (Notez que le site de NerdDinner actif a été mis à jour pour vous protéger contre les attaques de redirection ouvert).

Tout d’abord, un attaquant nous envoie un lien vers la page de connexion sur NerdDinner qui inclut une redirection pour leurs pages contrefaites :

[http://NerdDinner.com/Account/Logon?returnUrl=http://nerddiner.com/Account/Logon](http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn)

Notez que l’URL de retour pointe vers nerddiner.com, ce qui n’a pas un « n » à partir du dîner de word. Dans cet exemple, il s’agit d’un domaine qu’il contrôle. Lorsque nous accéder au lien ci-dessus, nous mettons dirigés vers la page de connexion NerdDinner.com légitime.

[![](preventing-open-redirection-attacks/_static/image4.png)](preventing-open-redirection-attacks/_static/image3.png)

**Figure 02**: page de connexion de NerdDinner avec une redirection ouverte

Lorsque nous connecter correctement, action d’ouverture de session de l’ASP.NET MVC AccountController nous redirige vers l’URL spécifiée dans le paramètre de chaîne de requête returnUrl. Dans ce cas, il est l’URL qui est passé à la personne malveillante, qui est [http://nerddiner.com/Account/LogOn](http://nerddiner.com/Account/LogOn). À moins que nous sommes sont extrêmement, qu'il est très probable que nous ne remarquerez, en particulier, car la personne malveillante a été prudent pour vous assurer que leurs pages contrefaites est identique à la page de connexion légitimes. Cette page de connexion inclut un message d’erreur demandant que nous connecter à nouveau. Maladroit us, nous devons avoir saisi correctement votre mot de passe.

[![](preventing-open-redirection-attacks/_static/image6.png)](preventing-open-redirection-attacks/_static/image5.png)

**Figure 03**: écran de connexion de NerdDinner falsifiées

Lorsque nous retapez votre nom d’utilisateur et un mot de passe, la page de connexion falsifiées enregistre les informations et nous envoie vers le site NerdDinner.com légitime. À ce stade, le site NerdDinner.com a déjà authentifié, afin de la page de connexion falsifiées permettre rediriger directement à cette page. Le résultat final est que la personne malveillante a notre nom d’utilisateur et un mot de passe, et nous ne savent pas que nous vous avons fourni il leur.

## <a name="looking-at-the-vulnerable-code-in-the-accountcontroller-logon-action"></a>Le code vulnérable à une Action d’ouverture de session AccountController

Le code de l’action d’ouverture de session dans une application ASP.NET MVC 2 est indiqué ci-dessous. Notez que lors d’une connexion réussie, le contrôleur retourne une redirection vers le returnUrl. Vous pouvez voir qu’aucune validation n’est effectuée sur le paramètre returnUrl.

**Affichage de 1 – action d’ouverture de session ASP.NET MVC 2 dans la liste`AccountController.cs`**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample1.cs)]

Maintenant nous allons examiner les modifications apportées à l’action d’ouverture de session ASP.NET MVC 3. Ce code a été modifié pour valider le paramètre returnUrl en appelant une méthode dans la classe d’assistance System.Web.Mvc.Url nommée `IsLocalUrl()`.

**Affichage de 2 – action d’ouverture de session ASP.NET MVC 3 dans la liste`AccountController.cs`**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample2.cs)]

Cela a été modifié pour valider le paramètre URL de retour en appelant une méthode dans la classe d’assistance System.Web.Mvc.Url, `IsLocalUrl()`.

## <a name="protecting-your-aspnet-mvc-10-and-mvc-2-applications"></a>Protection de la version 1.0 de ASP.NET MVC et MVC 2 Applications

Nous pouvons profiter des modifications d’ASP.NET MVC 3 dans notre ASP.NET MVC 1.0 existant et 2 applications en ajoutant la méthode d’assistance IsLocalUrl() et de mise à jour de l’action d’ouverture de session pour valider le paramètre returnUrl.

La méthode UrlHelper IsLocalUrl() fait appel à une méthode dans System.Web.WebPages, en tant que cette validation est également utilisée par les applications ASP.NET Web Pages.

**La liste 3 – la méthode IsLocalUrl() à partir de la UrlHelper de 3 ASP.NET MVC`class`**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample3.cs)]

La méthode IsUrlLocalToHost contient la logique de validation réelle, comme indiqué dans la liste 4.

**La liste 4 – IsUrlLocalToHost() à partir de la classe System.Web.WebPages RequestExtensions (méthode)**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample4.cs)]

Dans notre version 1.0 de ASP.NET MVC ou l’application 2, nous allons ajouter une méthode IsLocalUrl() à la AccountController, mais vous êtes invités à ajouter à une classe d’assistance distincte si possible. Nous sera apportée deux petite à la version d’ASP.NET MVC 3 de IsLocalUrl() afin qu’il fonctionne à l’intérieur du AccountController. Tout d’abord, nous allons modifier il à partir d’une méthode publique à une méthode privée, étant donné que les méthodes publiques dans des contrôleurs est accessible en tant qu’actions de contrôleur. Ensuite, nous allons modifier l’appel qui vérifie l’hôte de l’URL par rapport à l’hôte d’application. Qu’appel utilise un RequestContext local de la classe UrlHelper. Au lieu d’utiliser cela. RequestContext.HttpContext.Request.Url.Host, nous pourrons alors. Request.Url.Host. Le code suivant montre la méthode IsLocalUrl() modifiée pour une utilisation avec une classe de contrôleur dans ASP.NET MVC 1.0 et les applications de 2.

**La liste 5 – IsLocalUrl() (méthode), qui est modifié pour une utilisation avec une classe de contrôleur MVC**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample5.cs)]

Maintenant que la méthode IsLocalUrl() est en place, nous pouvons l’appeler à partir de notre action d’ouverture de session pour valider le paramètre returnUrl, comme indiqué dans le code suivant.

**La liste 6 – méthode d’ouverture de session de mise à jour qui valide le paramètre returnUrl**

[!code-csharp[Main](preventing-open-redirection-attacks/samples/sample6.cs)]

Maintenant, nous pouvons tester une attaque de redirection ouvert en essayant de se connecter à l’aide d’une URL de retournée externe. Nous allons utiliser/Account / d’ouverture de session ? ReturnUrl = http://www.bing.com/ à nouveau.

[![](preventing-open-redirection-attacks/_static/image8.png)](preventing-open-redirection-attacks/_static/image7.png)

**Figure 04**: pour tester l’Action d’ouverture de session de mise à jour

Une fois connecté, nous allons redirigés à l’action du contrôleur d’accueil ou d’Index plutôt qu’à l’URL externe.

[![](preventing-open-redirection-attacks/_static/image10.png)](preventing-open-redirection-attacks/_static/image9.png)

**Figure 05**: attaque Redirection ouvrir mises en échec

## <a name="summary"></a>Résumé

Les attaques par redirection ouvert peuvent se produire lors de la redirection des URL sont passés comme paramètres dans l’URL pour une application. Attaques de redirection d’ouvrir le modèle inclut le code pour vous protéger contre ASP.NET MVC 3. Vous pouvez ajouter ce code avec quelques modifications de la version 1.0 de ASP.NET MVC et les applications de 2. Pour protéger contre les attaques de redirection ouvert lors de la journalisation dans ASP.NET 1.0 et 2 applications, ajoutez une méthode IsLocalUrl() et valider le paramètre returnUrl dans l’action d’ouverture de session.
