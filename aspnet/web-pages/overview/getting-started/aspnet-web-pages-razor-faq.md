---
uid: web-pages/overview/getting-started/aspnet-web-pages-razor-faq
title: Des Pages Web ASP.NET (Razor) FAQ | Documents Microsoft
author: tfitzmac
description: "Cet article répertorie les questions fréquemment posées sur les Pages Web ASP.NET (Razor) et WebMatrix. Versions des logiciels utilisées dans le didacticiel ASP.NET Web Pages (R..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/07/2014
ms.topic: article
ms.assetid: b137bd04-25e1-47cb-9d96-ef2e179ecf1f
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/getting-started/aspnet-web-pages-razor-faq
msc.type: authoredcontent
ms.openlocfilehash: 60cc4ca364923cb131d5e91cd7b6307b1e68644b
ms.sourcegitcommit: 060879fcf3f73d2366b5c811986f8695fff65db8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
<a name="aspnet-web-pages-razor-faq"></a>Des Pages Web ASP.NET (Razor) FAQ
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> > [!NOTE] 
> > WebMatrix n’est plus recommandé comme un environnement de développement intégré pour ASP.NET Web Pages. Utilisez [Visual Studio](xref:aspnet/web-pages/overview/getting-started/program-asp-net-web-pages-in-visual-studio) ou [Visual Studio Code](https://code.visualstudio.com/).
>
> Cet article répertorie les questions fréquemment posées sur les Pages Web ASP.NET (Razor) et WebMatrix.
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>Versions du logiciel utilisées dans le didacticiel
> 
> 
> - Pages Web ASP.NET (Razor) 3
> - Visual Studio 2013
> - WebMatrix 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2, 2 de WebMatrix et Visual Studio 2012.


- [Quelle est la différence entre les Pages Web ASP.NET Web Forms ASP.NET et ASP.NET MVC ?](#Whats_the_difference_between_ASP.NET_Web_Pages,_ASP.NET_Web_Forms,_and_ASP.NET_MVC)
- [Ai-je besoin WebMatrix afin de fonctionner avec des Pages Web ?](#Do_I_need_WebMatrix_in_order_to_work_with_Web_Pages)
- [Puis-je utiliser des contrôles Web Forms ASP.NET sur une page Web Pages ?](#Can_I_use_ASP.NET_Web_Forms_controls_on_a_Web_Pages_page)
- [Puis-je déployer un site ASP.NET Web Pages sans utiliser de WebMatrix ?](#Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix)
- [Dois-je utiliser l’application d’assistance WebSecurity pour prendre en charge les connexions ?](#Do_I_have_to_use_the_WebSecurity_helper_to_support_logins)
- [Les Pages Web ASP.NET prend en charge HTML5 ?](#Does_ASP.NET_Web_Pages_support_HTML5)
- [Puis-je utiliser JavaScript et jQuery avec des Pages Web ?](#Can_I_use_JavaScript_and_jQuery_with_Web_Pages)
- [Ressources supplémentaires pour MSBuild](#AdditionalResources)

Pour toute question sur les erreurs et d’autres problèmes, consultez le [les Pages Web ASP.NET (Razor) Troubleshooting Guide](https://go.microsoft.com/fwlink/?LinkId=253001).

<a id="Whats_the_difference_between_ASP.NET_Web_Pages,_ASP.NET_Web_Forms,_and_ASP.NET_MVC"></a>
## <a name="whats-the-difference-between-aspnet-web-pages-aspnet-web-forms-and-aspnet-mvc"></a>Quelle est la différence entre les Pages Web ASP.NET Web Forms ASP.NET et ASP.NET MVC ?

Les trois sont des technologies ASP.NET pour la création d’applications web dynamiques :

- Les Pages Web ASP.NET se concentre sur l’ajout de code (côté serveur) dynamique et l’accès de base de données pour les pages HTML et la syntaxe simple et léger de fonctionnalités.
- ASP.NET Web Forms est basée sur un modèle d’objet de page et des contrôles de type de fenêtre traditionnel (boutons, listes, etc.). Web Forms utilise un modèle basé sur les événements qui vous est familier pour ceux ayant travaillé avec développement client (Windows forms).
- ASP.NET MVC implémente le modèle model-view-controller pour ASP.NET. L’accent est mis sur « séparation des préoccupations » (traitement, les données et couches d’interface utilisateur).

Toutes les trois infrastructures sont entièrement pris en charge et continuent à être développé par l’équipe ASP.NET. En général, le choix de l’infrastructure à utiliser dépend de l’arrière-plan et de l’expérience avec ASP.NET.

Les Pages Web ASP.NET en particulier a été conçu pour faciliter aux personnes qui savent déjà HTML pour ajouter le traitement du serveur à leurs pages. Il est un bon choix pour les étudiants, amateurs, personnes en général dans la programmation. Il peut également être un bon choix pour les développeurs familiarisés avec les technologies web de non-ASP.NET.

<a id="Do_I_need_WebMatrix_in_order_to_work_with_Web_Pages"></a>
## <a name="do-i-need-webmatrix-in-order-to-work-with-web-pages"></a>Ai-je besoin WebMatrix afin de fonctionner avec des Pages Web ?

Non. WebMatrix n’est plus recommandé comme un environnement de développement intégré pour ASP.NET Web Pages. Utilisez [Visual Studio](program-asp-net-web-pages-in-visual-studio.md) ou [Visual Studio Code](https://code.visualstudio.com/).

Si vous ne souhaitez pas utiliser Visual Studio ou Visual Studio Code, vous pouvez installer les produits composant individuellement à l’aide de [Microsoft Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx). Vous devez les produits suivants :

- Microsoft .NET Framework 4.5
- ASP.NET MVC 5 (qui installe également l’infrastructure de Pages Web ASP.NET)
- IIS Express (serveur web)
- Microsoft SQL Server Compact 4.0 (il s’agit de la base de données)

Vous pouvez utiliser un éditeur de texte pour modifier *.cshtml* (ou *.vbhtml*) pages.

Gestion des bases de données SQL Server Compact (*.sdf* fichiers) sans un outil est un peu plus difficile. Outils de containds Visual Studio pour la gestion des *.sdf* bases de données. Vous pouvez également exécuter des commandes SQL dans le code pour effectuer des tâches de gestion de SQL Server.

Pour tester *.cshtml* pages sans utiliser un environnement de développement intégré (IDE), vous pouvez les déployer sur un serveur de production. (Consultez [puis-je déployer un site ASP.NET Web Pages sans utiliser de WebMatrix ?](#Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix))

### <a name="running-iis-express-without-using-an-ide"></a>IIS Express en cours d’exécution sans l’aide d’un IDE

Si vous installez IIS Express sur votre ordinateur comme un serveur web, vous pouvez l’utiliser que pour tester les pages. Vous pouvez exécuter IIS Express à partir de la ligne de commande et l’associer à un numéro de port spécifique. Puis, vous spécifiez ce port lorsque vous demandez *.cshtml* fichiers dans votre navigateur.

Dans Windows, ouvrez une invite de commandes avec des privilèges d’administrateur et modifier à *C:\Program Files\IIS Express.* (Pour les systèmes 64 bits, utilisez le dossier *C:\Program Files (x86) \IIS Express.)* Puis, entrez la commande suivante, en utilisant le chemin d’accès réel à votre site :

`iisexpress.exe /port:35896 /path:C:\BasicWebSite`

Vous pouvez utiliser n’importe quel numéro de port qui n’est pas déjà réservé par un autre processus. (Les numéros de port au-delà de 1024 sont généralement disponible). Pour le `path` , utilisez le chemin d’accès du dossier du site Web où les *.cshtml* sont des fichiers.

Après avoir exécuté cette commande pour configurer IIS Express à répondre à vos pages, vous pouvez ouvrir un navigateur et accédez à un *.cshtml* fichier. Utiliser une URL comme suit :

`http://localhost:35896/default.cshtml`

Pour plus d’informations IIS Express des options de ligne de commande, entrez `iisexpress.exe /?` à la ligne de commande.

<a id="Can_I_use_ASP.NET_Web_Forms_controls_on_a_Web_Pages_page"></a>
## <a name="can-i-use-aspnet-web-forms-controls-on-a-web-pages-page"></a>Puis-je utiliser des contrôles Web Forms ASP.NET sur une page Web Pages ?

Non. Contrôles Web Forms, comme le [case à cocher](https://msdn.microsoft.com/library/system.web.ui.webcontrols.checkbox) (contrôle), le [des contrôles de validation](https://msdn.microsoft.com/library/bwd43d0x)et le [GridView](https://msdn.microsoft.com/library/system.web.ui.webcontrols.gridview) contrôle fonctionnent uniquement dans les pages Web Forms (*.aspx* fichiers). Ces contrôles requièrent l’infrastructure de page Web Forms.

<a id="Can_I_deploy_an_ASP.NET_Web_Pages_site_without_using_WebMatrix"></a>
## <a name="can-i-deploy-an-aspnet-web-pages-site-without-using-webmatrix"></a>Puis-je déployer un site ASP.NET Web Pages sans utiliser de WebMatrix ?

Oui. Vous pouvez copier manuellement les fichiers du site Web sur un serveur (en général par FTP). Si vous effectuez une copie manuelle, vous devez également copier les fichiers qui prennent en charge de SQL Server Compact (il s’agit de la base de données). Pour plus d’informations, consultez le billet de blog [les applications de déploiement Web Pages sans un outil](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2317).

<a id="Do_I_have_to_use_the_WebSecurity_helper_to_support_logins"></a>
## <a name="do-i-have-to-use-the-websecurity-helper-to-support-logins"></a>Dois-je utiliser l’application d’assistance WebSecurity pour prendre en charge les connexions ?

Non. Le `SimpleMembership` fournisseur qui fait partie d’ASP.NET Web Pages est une option. Les fournisseurs de sécurité qui font partie d’ASP.NET (que vous avez peut être utilisé à l’utilisation dans les formulaires Web) sont également disponibles. Par exemple, vous pouvez utiliser l’authentification par formulaire dans ASP.NET Web Pages comme vous le feriez dans les formulaires Web. Pour un exemple d’utilisation de l’authentification par formulaire, consultez l’article de Support technique de Microsoft [How To Implement Forms-Based l’authentification dans votre Application ASP.NET à l’aide de C# .NET](https://support.microsoft.com/kb/301240). Pour télécharger un exemple simple, consultez [version ASP.NET de « connexion &amp; mot de passe](http://www.codeguru.com/csharp/.net/net_asp/scripting/article.php/c19295/ASPNET-version-of-Login--Password.htm).

Pour plus d’informations sur la façon d’utiliser l’authentification Windows, voir le blog [à l’aide de Windows authentication in ASP.NET Web Pages](http://mikepope.com/blog/DisplayBlog.aspx?permalink=2298).

<a id="Does_ASP.NET_Web_Pages_support_HTML5"></a>
## <a name="does-aspnet-web-pages-support-html5"></a>Les Pages Web ASP.NET prend en charge HTML5 ?

Oui. Les pages que vous créez avec ASP.NET Web Pages (*.cshtml* ou *.vbhtml* pages) sont essentiellement des pages HTML qui contiennent également du code qui s’exécute sur le serveur, avant le rendu de la page. Tant que le navigateur prend en charge de HTML5, vous pouvez utiliser des éléments de HTML5 dans un *.cshtml* ou *.vbhtml* page.

<a id="Can_I_use_JavaScript_and_jQuery_with_Web_Pages"></a>
## <a name="can-i-use-javascript-and-jquery-with-web-pages"></a>Puis-je utiliser JavaScript et jQuery avec des Pages Web ?

Absolument. Les pages que vous créez avec ASP.NET Web Pages (*.cshtml* ou *.vbhtml* pages) sont simplement des pages HTML avec un code serveur dans celles-ci. Par conséquent, vous pouvez faire dans une page HTML normale à l’aide de JavaScript ou jQuery vous pouvez également faire dans un *.cshtml* ou *.vbhtml* page.

Le **Starter Site** modèle dans WebMatrix contient un nombre de bibliothèques de jQuery. Si vous créez un site à l’aide de ce modèle, le *Scripts* dossier contient une bibliothèque de noyaux jQuery (*jquery-1.6.2.js)* et des bibliothèques pour la validation jQuery (*jquery.validate.js*, etc..).

Voici des billets de blog qui montrent comment utiliser jQuery avec ASP.NET Web Pages :

- [Ajout d’adéquation de jQuery pour les Pages Web ASP.NET à l’aide de WebMatrix](http://rachelappel.com/jquery/adding-jquery-goodness-to-asp-net-web-pages-using-webmatrix/) par Rachel Appel
- [5 min : WebMatrix jQuery UI + json + jQuery modèles](http://joeriks.com/2011/01/30/5-min-webmatrix-jquery-ui-json-jquery-templates/) par Jonas Eriksson
- [Formulaires de jQuery WebMatrix et](http://mikesdotnetting.com/Article/155/WebMatrix-And-jQuery-Forms) par Mike Brind

<a id="AdditionalResources"></a>
## <a name="additional-resources"></a>Ressources supplémentaires


[ASP.NET Web Pages (Razor) - Guide de résolution des problèmes](https://go.microsoft.com/fwlink/?LinkId=253001)

[Forum de WebMatrix et ASP.NET Web Pages](https://forums.asp.net/1224.aspx/1?WebMatrix) sur le site Web ASP.NET
