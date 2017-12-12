---
uid: web-forms/overview/ajax-control-toolkit/htmleditor/how-do-i-use-the-html-editor-control-vb
title: "Utilisation du contrôle de l’éditeur HTML (VB) | Documents Microsoft"
author: microsoft
description: "HTMLEditor est un contrôle ASP.NET AJAX qui permet de facilement créer et modifier le contenu HTML via les boutons dans une barre d’outils."
ms.author: aspnetcontent
manager: wpickett
ms.date: 05/12/2009
ms.topic: article
ms.assetid: 32ec9321-7c8c-4b0f-8234-99acb56df6b5
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/htmleditor/how-do-i-use-the-html-editor-control-vb
msc.type: authoredcontent
ms.openlocfilehash: a34b3dd53f031856906eca923b6ad6f43a0aaecc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="how-do-i-use-the-html-editor-control-vb"></a>Utilisation du contrôle de l’éditeur HTML (VB)
====================
par [Microsoft](https://github.com/microsoft)

> HTMLEditor est un contrôle ASP.NET AJAX qui permet de facilement créer et modifier le contenu HTML via les boutons dans une barre d’outils.


L’objectif de ce didacticiel est de vous fournir une vue d’ensemble du contrôle de l’éditeur HTML inclus avec les outils de contrôle AJAX. L’éditeur HTML inclut des options pour modifier la taille de police, en sélectionnant une police, modifier la couleur d’arrière-plan, la modification de la couleur de premier plan, ajout de liens, ajout d’images, modification de l’alignement de texte et effectuez les opérations couper, copier et coller des opérations (voir Figure 1).


[![L’éditeur HTML](how-do-i-use-the-html-editor-control-vb/_static/image1.jpg)](how-do-i-use-the-html-editor-control-vb/_static/image1.png)

**Figure 01**: l’éditeur HTML ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-html-editor-control-vb/_static/image2.png))


L’éditeur HTML vous permet d’entrer du contenu à l’aide d’un mode de conception, ou vous pouvez entrer directement HTML. Vous sont également fournies avec l’option pour afficher un aperçu de votre contenu HTML (voir Figure 2).


[![Conception et aperçu HTML boutons](how-do-i-use-the-html-editor-control-vb/_static/image2.jpg)](how-do-i-use-the-html-editor-control-vb/_static/image3.png)

**Figure 02**: conception, HTML et l’aperçu des boutons ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-html-editor-control-vb/_static/image4.png))


Dans ce didacticiel, vous découvrez comment afficher l’éditeur HTML, comment personnaliser les boutons de barre d’outils qui s’affichent dans l’éditeur HTML et comment éviter les attaques de script entre sites.

## <a name="displaying-the-html-editor"></a>Affichage de l’éditeur HTML

Avant de pouvoir utiliser l’éditeur HTML dans une page ASP.NET, vous devez d’abord ajouter un contrôle ScriptManager à la page. Le contrôle ScriptManager se trouve sous l’onglet Extensions AJAX dans la boîte à outils Visual Studio/Visual Web Developer Express.

Vous devez placer le contrôle ScriptManager en haut de la page avant les autres contrôles sur la page. Par exemple, vous pouvez le placer immédiatement sous ouverture côté serveur &lt;formulaire&gt; balise.

Le contrôle de l’éditeur HTML se trouve dans la boîte à outils avec le reste des contrôles de boîte à outils de contrôle AJAX. Il s’agit du contrôle (voir Figure 3).


[![Le contrôle de l’éditeur HTML](how-do-i-use-the-html-editor-control-vb/_static/image3.jpg)](how-do-i-use-the-html-editor-control-vb/_static/image5.png)

**Figure 03**: contrôle de l’éditeur HTML ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-html-editor-control-vb/_static/image6.png))


Une fois que vous faites glisser l’éditeur HTML sur une page, vous pouvez définir ses propriétés dans la feuille de propriétés. Par exemple, vous souhaitez normalement définir les propriétés Width et Height. La liste 1 contient la source d’une page ASP.NET qui contient un éditeur HTML.

**La liste 1 - SimpleEditor.aspx**

[!code-aspx[Main](how-do-i-use-the-html-editor-control-vb/samples/sample1.aspx)]

La page dans la liste 1 contient un contrôle de l’éditeur HTML, un contrôle bouton et un contrôle Literal. Lorsque vous cliquez sur le bouton, le contenu de l’éditeur HTML s’affiche dans le contrôle Literal (voir Figure 4).


[![Envoi d’un formulaire avec un éditeur HTML](how-do-i-use-the-html-editor-control-vb/_static/image4.jpg)](how-do-i-use-the-html-editor-control-vb/_static/image7.png)

**Figure 04**: envoi d’un formulaire avec un éditeur HTML ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-html-editor-control-vb/_static/image8.png))


La propriété de contenu de l’éditeur HTML est utilisée pour récupérer le contenu HTML entré dans l’éditeur HTML. N’oubliez pas que ce contenu HTML peut contenir JavaScript. Dans la section suivante, nous expliquent comment vous pouvez empêcher les attaques par Injection de JavaScript.

## <a name="customizing-the-html-editor-toolbar"></a>Personnalisation de la barre d’outils de l’éditeur HTML

Vous pouvez personnaliser exactement les boutons qui apparaissent dans l’éditeur. Par exemple, vous souhaiterez supprimer l’onglet HTML pour empêcher les utilisateurs de basculer l’éditeur HTML en mode HTML. Ou, vous souhaiterez peut-être supprimer la liste de déroulante de taille de police pour empêcher les utilisateurs de la création de texte trop important dans un forum de message post (voir Figure 5).


[![Un éditeur HTML personnalisé](how-do-i-use-the-html-editor-control-vb/_static/image5.jpg)](how-do-i-use-the-html-editor-control-vb/_static/image9.png)

**Figure 05**: A personnalisé éditeur HTML ([cliquez pour afficher l’image en taille réelle](how-do-i-use-the-html-editor-control-vb/_static/image10.png))


Vous personnalisez les boutons de barre d’outils en dérivant d’un nouvel éditeur HTML de la classe de l’éditeur de base. Par exemple, l’éditeur personnalisé dans la liste 2 contient uniquement les boutons de barre d’outils pour gras et italique. Tous les autres boutons de barre d’outils ont été supprimés. En outre, l’onglet HTML a été supprimé de la partie inférieure de l’éditeur (mais les onglets conception et aperçu ont toujours lieu).

**Listing 2 - application\_Code\CustomEditor.vb**

[!code-vb[Main](how-do-i-use-the-html-editor-control-vb/samples/sample2.vb)]

Vous devez ajouter la classe dans la liste 2 à votre application\_Code dossier afin que la classe sera compilée automatiquement. Si l’application\_dossier de Code n’existe pas dans votre site Web, puis vous pouvez ajouter simplement le dossier.

Après avoir créé un éditeur personnalisé, vous pouvez l’ajouter à une page ASP.NET de la même façon que vous ajoutez l’éditeur HTML normal (voir la liste 3).

**La liste 3 - ShowCustomEditor.aspx**

[!code-aspx[Main](how-do-i-use-the-html-editor-control-vb/samples/sample3.aspx)]

## <a name="avoiding-cross-site-scripting-xss-attacks"></a>En évitant les attaques par écriture de scripts entre sites (XSS)

Chaque fois que vous acceptez l’entrée d’un utilisateur et réaffichez cette entrée sur votre site Web, vous potentiellement ouvrez votre site Web à des attaques de script entre sites (XSS). En théorie, un pirate peut envoyer du code JavaScript qui est exécuté quand l’entrée s’affiche de nouveau. Le code JavaScript peut servir de voler des mots de passe utilisateur ou d’autres informations sensibles.

En règle générale, vous pouvez anéantir attaques XSS en codage toute entrée vous récupérez à partir d’un utilisateur avant de les afficher dans une page web HTML. Toutefois, la sortie de l’éditeur HTML de codage HTML ne serait pas encoder uniquement &lt;script&gt; balises, il serait Encoder également toutes les balises HTML. En d’autres termes, vous perdez toute la mise en forme telles que le type de police, la taille de police et la couleur d’arrière-plan.

Si vous collectez des informations sensibles à partir de vos utilisateurs, telles que les mots de passe, les numéros de carte de crédit et numéros de sécurité sociale - vous ne devez pas afficher le contenu que vous récupérez à partir d’un utilisateur avec l’éditeur HTML non codé. Vous devez utiliser l’éditeur HTML uniquement dans les situations dans lesquelles vous ne sont pas réaffichage du contenu HTML, ou le contenu HTML est envoyé à votre site Web par un tiers de confiance.

Par exemple, imaginez que vous créez une application de blog. Dans ce cas, il est judicieux d’utiliser l’éditeur HTML en répartissant les billets de blog. Vous êtes la seule personne qui soumet un billet de blog et, sans doute, vous pouvez faire confiance vous-même afin de ne pas soumettre JavaScript malveillant. Toutefois, il est inutile d’utiliser l’éditeur HTML lorsque vous autorisez les utilisateurs anonymes à publier des commentaires. Vous devez être particulièrement prudent dans les situations dans lesquelles les utilisateurs soumettent des informations sensibles telles que les mots de passe. Potentiellement, un utilisateur malveillant peut publier un commentaire qui contient le droit JavaScript pour le vol d’un mot de passe.

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ont été fournies avec une brève vue d’ensemble du contrôle de l’éditeur HTML inclus dans la boîte à outils de contrôle AJAX. Vous avez appris à utiliser l’éditeur HTML pour accepter le contenu d’un utilisateur et envoyer le contenu sur le serveur. Nous avons abordé également comment vous pouvez personnaliser les boutons de barre d’outils qui sont affichent dans l’éditeur HTML. Enfin, vous avez appris comment éviter les attaques de script entre sites lors de l’utilisation de l’éditeur HTML pour accepter les entrées potentiellement nuisible.

>[!div class="step-by-step"]
[Précédent](how-do-i-use-the-html-editor-control-cs.md)
