---
uid: web-pages/overview/api-reference/asp-net-web-pages-api-reference
title: "Des Pages Web ASP.NET (Razor) Guide de référence rapide de l’API | Documents Microsoft"
author: tfitzmac
description: "Cette page contient une liste avec des exemples brièvement les objets couramment utilisés, les propriétés et les méthodes de programmation ASP.NET Web Pages avec syntaxe Razor."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/10/2014
ms.topic: article
ms.assetid: 4001cb9b-3bfd-4ace-8a89-1561d8421e2c
ms.technology: dotnet-webpages
ms.prod: .net-framework
msc.legacyurl: /web-pages/overview/api-reference/asp-net-web-pages-api-reference
msc.type: authoredcontent
ms.openlocfilehash: 35f91f4dbea4881d9dabc4ab7c6b96dbb6a01ea2
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="aspnet-web-pages-razor-api-quick-reference"></a>Référence des API rapide (Razor) des Pages Web ASP.NET
====================
par [Tom FitzMacken](https://github.com/tfitzmac)

> Cette page contient une liste avec des exemples brièvement les objets couramment utilisés, les propriétés et les méthodes de programmation ASP.NET Web Pages avec syntaxe Razor.
> 
> Descriptions marquées avec « (v2) » ont été introduites dans les Pages Web ASP.NET version 2.
> 
> Pour la documentation de référence d’API, consultez le [documentation de référence sur les Pages Web ASP.NET](https://go.microsoft.com/fwlink/?LinkId=208659) sur MSDN.
> 
> ## <a name="software-versions"></a>Versions du logiciel
> 
> 
> - Pages Web ASP.NET (Razor) 3
>   
> 
> Ce didacticiel fonctionne également avec ASP.NET Web Pages 2 et la version 1.0 de ASP.NET Web Pages (à l’exception des fonctions marquées v2).


Cette page contient des informations de référence pour les éléments suivants :

- [Classes](#Classes)
- [Données](#Data)
- [Programmes d’assistance](#Helpers)
- [Validation](#Validation)

<a id="Classes"></a>
## <a name="classes"></a>Classes

### `AppState[key], AppState[index],App`

Contient des données qui peuvent être partagées par toutes les pages dans l’application. Vous pouvez utiliser la dynamique `App` propriété pour accéder aux mêmes données, comme dans l’exemple suivant :

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample1.html)]

### `AsBool(), AsBool(true|false)`

Convertit une valeur de chaîne en valeur booléenne (true/false). Retourne la valeur false ou la valeur spécifiée si la chaîne ne représente pas la valeur true/false.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample2.cs)]

### `AsDateTime(), AsDateTime(value)`

Convertit une valeur de chaîne en date/heure. Retourne `DateTime.MinValue` ou la valeur spécifiée si la chaîne ne représente pas une date/heure.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample3.cs)]

### `AsDecimal(), AsDecimal(value)`

Convertit une valeur de chaîne en une valeur décimale. Retourne 0.0 ou la valeur spécifiée si la chaîne ne représente pas une valeur décimale.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample4.cs)]

### `AsFloat(), AsFloat(value)`

Convertit une valeur de chaîne à virgule flottante. Retourne 0.0 ou la valeur spécifiée si la chaîne ne représente pas une valeur décimale.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample5.cs)]

### `AsInt(), AsInt(value)`

Convertit une valeur de chaîne en entier. Renvoie la valeur 0 ou la valeur spécifiée si la chaîne ne représente pas un entier.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample6.cs)]

### `Href(path [, param1 [, param2]])`

Crée une URL de navigateur à partir d’un chemin d’accès local, avec des parties du chemin d’accès supplémentaire facultatif.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample7.cshtml)]

### `Html.Raw(value)`

Restitue *valeur* en tant que balisage HTML au lieu que codée en HTML la sortie de rendu.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample8.cshtml)]

### `IsBool(), IsDateTime(), IsDecimal(), IsFloat(), IsInt()`

Retourne la valeur true si la valeur peut être convertie à partir d’une chaîne vers le type spécifié.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample9.cs)]

### `IsEmpty()`

Retourne la valeur true si l’objet ou la variable n’a aucune valeur.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample10.cs)]

### `IsPost`

Retourne la valeur true si la demande est une demande POST. (Demandes initiales sont généralement une opération GET).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample11.cs)]

### `Layout`

Spécifie le chemin d’accès d’une page de disposition à appliquer à cette page.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample12.html)]

### `PageData[key], PageData[index],Page`

Contient des données partagées entre la page, les pages de disposition et les pages partielles dans la requête actuelle. Vous pouvez utiliser la dynamique `Page` propriété pour accéder aux mêmes données, comme dans l’exemple suivant :

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample13.html)]

### `RenderBody()`

(Pages de disposition) Restitue le contenu d’une page de contenu qui n’est pas dans les sections nommées.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample14.cs)]

### `RenderPage(path, values)`  
`RenderPage(path[,param1 [, param2]])`

Restitue une page de contenu à l’aide du chemin d’accès spécifié et des données supplémentaires facultatives. Vous pouvez obtenir les valeurs des paramètres supplémentaires à partir de `PageData` par position (par exemple, 1) ou par clé (par exemple, 2).

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample15.js)]

### `RenderSection(sectionName [, required = true|false])`

(Pages de disposition) Restitue une section de contenu qui a un nom. Définissez *requis* sur false pour rendre une section facultative.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample16.js)]

### `Request.Cookies[key]`

Obtient ou définit la valeur d’un cookie HTTP.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample17.cs)]

### `Request.Files[key]`

Obtient les fichiers qui ont été téléchargés dans la requête actuelle.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample18.js)]

### `Request.Form[key]`

Obtient les données qui a été validées dans un formulaire (sous forme de chaînes). `Request[key]`vérifie à la fois le `Request.Form` et `Request.QueryString` collections.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample19.cs)]

### `Request.QueryString[key]`

Obtient les données qui a été spécifiées dans la chaîne de requête URL. `Request[key]`vérifie à la fois le `Request.Form` et `Request.QueryString` collections.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample20.cs)]

### `Request.Unvalidated(key)`  
`Request.Unvalidated().QueryString|Form|Cookies|Headers[key]`

Sélectivement désactive la validation pour un élément de formulaire, une valeur de chaîne de requête, un cookie ou une valeur d’en-tête de requête. Validation de la demande est activée par défaut et empêche les utilisateurs de la validation de balisage ou tout autre contenu potentiellement dangereux.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample21.cs)]

### `Response.AddHeader(name, value)`

Ajoute un en-tête de serveur HTTP à la réponse.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample22.cs)]

### `Response.OutputCache(seconds [, sliding] [, varyByParams])`

Met en cache la sortie de page pendant une période spécifiée. Définissez éventuellement *glissante* pour réinitialiser le délai d’attente sur chaque accès à la page et *varyByParams* pour mettre en cache les différentes versions de la page pour chaque chaîne de requête différents dans la demande de page.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample23.js)]

### `Response.Redirect(path)`

Redirige la demande de navigateur à un nouvel emplacement.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample24.js)]

### `Response.SetStatus(httpStatusCode)`

Définit le code d’état HTTP envoyé au navigateur.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample25.cs)]

### `Response.WriteBinary(data [, mimetype])`

Écrit le contenu de *données* à la réponse avec un type MIME facultatif.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample26.js)]

### `Response.WriteFile(file)`

Écrit le contenu d’un fichier dans la réponse.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample27.cs)]

### `@section(sectionName) {content }`

(Pages de disposition) Définit une section de contenu qui a un nom.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample28.cshtml)]

### `Server.HtmlDecode(htmlText)`

Décode une chaîne est encodée en HTML.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample29.cs)]

### `Server.HtmlEncode(text)`

Encode une chaîne pour le rendu dans le balisage HTML.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample30.cs)]

### `Server.MapPath(virtualPath)`

Retourne le chemin d’accès physique de serveur pour le chemin d’accès virtuel spécifié.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample31.cs)]

### `Server.UrlDecode(urlText)`

Décode le texte à partir d’une URL.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample32.cs)]

### `Server.UrlEncode(text)`

Encode le texte à placer dans une URL.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample33.cs)]

### `Session[key]`

Obtient ou définit une valeur qui existe jusqu'à ce que l’utilisateur ferme le navigateur.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample34.css)]

### `ToString()`

Affiche une représentation sous forme de chaîne de valeur de l’objet.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample35.html)]

### `UrlData[index]`

Obtient les données supplémentaires à partir de l’URL (par exemple, *MyPage/ExtraData*).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample36.cs)]

### `WebSecurity.ChangePassword(userName,currentPassword,newPassword)`

Modifie le mot de passe pour l’utilisateur spécifié.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample37.cs)]

### `WebSecurity.ConfirmAccount(accountConfirmationToken)`

Confirme un compte à l’aide du jeton de confirmation du compte.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample38.cs)]

### `WebSecurity.CreateAccount(userName, password`  
 `[, requireConfirmationToken = true|false])`

Crée un nouveau compte d’utilisateur avec le nom d’utilisateur spécifié et le mot de passe. Pour demander un jeton de confirmation, passez la valeur true pour *requireConfirmationToken.*

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample39.cs)]

### `WebSecurity.CurrentUserId`

Obtient l’identificateur entier pour l’utilisateur actuellement connecté.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample40.cs)]

### `WebSecurity.CurrentUserName`

Obtient le nom de l’utilisateur actuellement connecté.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample41.cs)]

### `WebSecurity.GeneratePasswordResetToken(username`  
 `[, tokenExpirationInMinutesFromNow])`

Génère un jeton de réinitialisation de mot de passe qui peut être envoyé par courrier électronique à un utilisateur afin que l’utilisateur peut réinitialiser le mot de passe.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample42.cs)]

### `WebSecurity.GetUserId(userName)`

Retourne l’ID d’utilisateur à partir du nom d’utilisateur.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample43.cs)]

### `WebSecurity.IsAuthenticated`

Retourne la valeur true si l’utilisateur actuel s’est connecté.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample44.cs)]

### `WebSecurity.IsConfirmed(userName)`

Retourne la valeur true si l’utilisateur a été confirmé (par exemple, via un message électronique de confirmation).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample45.cs)]

### `WebSecurity.IsCurrentUser(userName)`

Retourne la valeur true si le nom de l’utilisateur actuel correspond au nom d’utilisateur spécifié.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample46.cs)]

### `WebSecurity.Login(userName,password[, persistCookie])`

Connecte l’utilisateur en définissant un jeton d’authentification dans le cookie.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample47.cs)]

### `WebSecurity.Logout()`

Se connecte à l’utilisateur en supprimant le cookie de jeton d’authentification.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample48.css)]

### `WebSecurity.RequireAuthenticatedUser()`

Si l’utilisateur n’est pas authentifié, définit l’état HTTP 401 (non autorisé).

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample49.css)]

### `WebSecurity.RequireRoles(roles)`

Si l’utilisateur actuel n’est pas un membre de l’un des rôles spécifiés, définit l’état HTTP 401 (non autorisé).

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample50.html)]

### `WebSecurity.RequireUser(userId)`  
`WebSecurity.RequireUser(userName)`

Si l’utilisateur actuel n’est pas l’utilisateur spécifié par *nom d’utilisateur*, définit l’état HTTP 401 (non autorisé).

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample51.css)]

### `WebSecurity.ResetPassword(passwordResetToken,newPassword)`

Si le jeton de réinitialisation de mot de passe est valide, modifie le mot de passe pour le nouveau mot de passe.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample52.css)]

<a id="Data"></a>
## <a name="data"></a>Données

### `Database.Execute(SQLstatement [,parameters]`

Exécute *SQLstatement* (avec les paramètres facultatifs) telles que INSERT, DELETE ou UPDATE et retourne le nombre d’enregistrements affectés.

[!code-sql[Main](asp-net-web-pages-api-reference/samples/sample53.sql)]

### `Database.GetLastInsertId()`

Retourne la colonne d’identité à partir de la dernière ligne insérée.

[!code-sql[Main](asp-net-web-pages-api-reference/samples/sample54.sql)]

### `Database.Open(filename)`  
`Database.Open(connectionStringName)`

Ouvre le fichier de base de données spécifiée ou la base de données spécifiée à l’aide d’une chaîne de connexion nommée à partir de la *Web.config* fichier.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample55.cs)]

### `Database.OpenConnectionString(connectionString)`

Ouvre une base de données à l’aide de la chaîne de connexion. (Elle s’oppose à `Database.Open`, qui utilise un nom de chaîne de connexion.)

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample56.cs)]

### `Database.Query(SQLstatement[,parameters])`

La base de données à l’aide des requêtes *SQLstatement* (passant éventuellement des paramètres) et retourne les résultats sous forme de collection.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample57.html)]

### `Database.QuerySingle(SQLstatement [, parameters])`

Exécute *SQLstatement* (avec les paramètres facultatifs) et retourne un enregistrement unique.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample58.cs)]

### `Database.QueryValue(SQLstatement [, parameters])`

Exécute *SQLstatement* (avec les paramètres facultatifs) et retourne une valeur unique.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample59.cs)]

<a id="Helpers"></a>
## <a name="helpers"></a>Programmes d’assistance

### `Analytics.GetGoogleHtml(webPropertyId)`

Restitue le code JavaScript d’Analytique Google pour l’ID spécifié.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample60.js)]

### `Analytics.GetStatCounterHtml(project,security)`

Restitue le code StatCounter Analytique JavaScript pour le projet spécifié.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample61.css)]

### `Analytics.GetYahooHtml(account)`

Restitue le code Yahoo Analytique JavaScript pour le compte spécifié.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample62.js)]

### `Bing.SearchBox([boxWidth])`

Transmet une recherche à Bing. Pour spécifier le site à la recherche et un titre pour la zone de recherche, vous pouvez définir le `Bing.SiteUrl` et `Bing.SiteTitle` propriétés. Normalement, vous définissez ces propriétés le  *\_AppStart* page.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample63.html)]

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample64.cshtml)]

### `Chart(width,height [, template] [, templatePath])`

Initialise un graphique.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample65.cshtml)]

### `Chart.AddLegend([title] [, name])`

Ajoute une légende à un graphique.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample66.cshtml)]

### `Chart.AddSeries([name] [, chartType] [, chartArea]`  
 `[, axisLabel] [, legend] [, markerStep] [, xValue]`  
 `[, xField] [, yValues] [, yFields] [, options])`

Ajoute une série de valeurs au graphique.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample67.cshtml)]

### `Crypto.Hash(string [, algorithm])`  
`Crypto.Hash(bytes [, algorithm])`

Retourne un hachage pour les données spécifiées. L’algorithme par défaut est `sha256`.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample68.html)]

### `Facebook.LikeButton(href [, buttonLayout] [, showFaces] [, width] [, height]`   
 `[, action] [, font] [, colorScheme] [, refLabel])`

Permet aux utilisateurs de Facebook établir une connexion à des pages.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample69.js)]

### `FileUpload.GetHtml([initialNumberOfFiles] [, allowMoreFilesToBeAdded]`  
 `[, includeFormTag] [, addText] [, uploadText])`

Restitue l’interface utilisateur pour le téléchargement des fichiers.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample70.html)]

### `GamerCard.GetHtml(gamerTag)`

Restitue la balise de jeux Xbox spécifiée.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample71.js)]

### `Gravatar.GetHtml(email [, imageSize] [, defaultImage] [, rating]`  
 `[, imageExtension] [, attributes])`

Restitue l’image Gravatar pour l’adresse de messagerie spécifiée.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample72.css)]

### `Json.Encode(object)`

Convertit un objet de données en une chaîne au format JavaScript Objet Notation (JSON).

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample73.cs)]

### `Json.Decode(string)`

Convertit une chaîne d’entrée encodées en JSON en un objet de données que vous pouvez parcourir ou les insérer dans une base de données.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample74.cs)]

### `LinkShare.GetHtml(pageTitle[, pageLinkBack] [, twitterUserName]`  
 `[, additionalTweetText] [, linkSites])`

Effectue le rendu des liens de réseau sociales à l’aide du titre spécifié et l’URL facultatif.

[!code-xml[Main](asp-net-web-pages-api-reference/samples/sample75.xml)]

### `ModelStateDictionary.AddError(key, errorMessage)`

Associe un message d’erreur à un champ de formulaire. Utilisez le `ModelState` application d’assistance pour accéder à ce membre.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample76.cs)]

### `ModelStateDictionary.AddFormError(errorMessage)`

Associe un message d’erreur à un formulaire. Utilisez le `ModelState` application d’assistance pour accéder à ce membre.

[!code-powershell[Main](asp-net-web-pages-api-reference/samples/sample77.ps1)]

### `ModelStateDictionary.IsValid`

Retourne true s’il n’y a aucune erreur de validation. Utilisez le `ModelState` application d’assistance pour accéder à ce membre.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample78.cs)]

### `ObjectInfo.Print(value [, depth] [, enumerationLength])`

Affiche les propriétés et les valeurs d’un objet et tout enfant objets.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample79.css)]

### `Recaptcha.GetHtml([, publicKey] [, theme] [, language] [, tabIndex])`

Restitue le test de vérification reCAPTCHA.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample80.css)]

### `ReCaptcha.PublicKey`  
 `ReCaptcha.PrivateKey`

Définit les clés publiques et privées pour le service reCAPTCHA. Normalement, vous définissez ces propriétés le  *\_AppStart* page.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample81.css)]

### `ReCaptcha.Validate([, privateKey])`

Retourne le résultat du test reCAPTCHA.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample82.cs)]

### `ServerInfo.GetHtml()`

Affiche les informations d’état sur les Pages Web ASP.NET.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample83.cshtml)]

### `Twitter.Profile(twitterUserName)`

Restitue un flux Twitter de l’utilisateur spécifié.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample84.js)]

### `Twitter.Search(searchQuery)`

Restitue un flux Twitter pour le texte de recherche spécifié.

[!code-xml[Main](asp-net-web-pages-api-reference/samples/sample85.xml)]

### `Video.Flash(filename [, width, height])`

Restitue un lecteur vidéo Flash pour le fichier spécifié avec l’option largeur et hauteur.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample86.cshtml)]

### `Video.MediaPlayer(filename [, width, height])`

Restitue un lecteur Windows Media pour le fichier spécifié avec l’option largeur et hauteur.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample87.cshtml)]

### `Video.Silverlight(filename, width, height)`

Restitue un lecteur Silverlight spécifié *.xap* fichier avec la largeur et hauteur.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample88.cshtml)]

### `WebCache.Get(key)`

Retourne l’objet spécifié par *clé*, ou null si l’objet est introuvable.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample89.cs)]

### `WebCache.Remove(key)`

Supprime l’objet spécifié par *clé* à partir du cache.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample90.cs)]

### `WebCache.Set(key, value [, minutesToCache] [, slidingExpiration])`

Place *valeur* dans le cache sous le nom spécifié par *clé*.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample91.html)]

### `WebGrid(data)`

Crée un `WebGrid` à l’aide des données à partir d’une requête de l’objet.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample92.cs)]

### `WebGrid.GetHtml()`

Restitue la balise pour afficher des données dans une table HTML.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample93.html)]

### `WebGrid.Pager()`

Restitue un récepteur de radiomessagerie pour la `WebGrid` objet.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample94.html)]

### `WebImage(path)`

Charge une image à partir du chemin d’accès spécifié.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample95.cs)]

### `WebImage.AddImagesWatermark(image)`

Ajoute l’image spécifiée en tant que filigrane.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample96.cs)]

### `WebImage.AddTextWatermark(text)`

Ajoute le texte spécifié à l’image.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample97.cs)]

### `WebImage.FlipHorizontal()`  
`WebImage.FlipVertical()`

Retourne l’image horizontalement ou verticalement.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample98.css)]

### `WebImage.GetImageFromRequest()`

Charge une image lorsqu’une image est publiée vers une page pendant le chargement d’un fichier.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample99.cs)]

### `WebImage.Resize(width,height)`

Redimensionne une l’image.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample100.css)]

### `WebImage.RotateLeft()`  
`WebImage.RotateRight()`

Fait pivoter l’image vers la gauche ou droite.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample101.css)]

### `WebImage.Save(path [, imageFormat])`

Enregistre l’image dans le chemin d’accès spécifié.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample102.js)]

### `WebMail.Password`

Définit le mot de passe pour le serveur SMTP. Normalement, vous définissez cette propriété le  *\_AppStart* page.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample103.cs)]

### `WebMail.Send(to, subject, body [, from] [, cc] [, filesToAttach] [, isBodyHtml]`  
 `[, additionalHeaders])`

Envoie un message électronique.

[!code-css[Main](asp-net-web-pages-api-reference/samples/sample104.css)]

### `WebMail.SmtpServer`

Définit le nom du serveur SMTP. Normalement, vous définissez cette propriété le*\_AppStart* page.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample105.html)]

### `WebMail.UserName`

Définit le nom d’utilisateur pour le serveur SMTP. Normalement, vous devez définir cette propriété le  *\_AppStart* page.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample106.html)]

<a id="Validation"></a>
## <a name="validation"></a>Validation

### `Html.ValidationMessage(field)`

(v2) Affiche un message d’erreur de validation pour le champ spécifié.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample107.cshtml)]

### `Html.ValidationSummary([message])`

(v2) Affiche une liste de toutes les erreurs de validation.

[!code-cshtml[Main](asp-net-web-pages-api-reference/samples/sample108.cshtml)]

### `Validation.Add(field, validationType)`

(v2) Enregistre un élément d’entrée d’utilisateur pour le type de validation spécifié.

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample109.js)]

### `Validation.ClassFor(field)`

(v2) Restitue dynamiquement les attributs de classe CSS pour la validation côté client afin que vous pouvez mettre les messages d’erreur de validation. (Nécessite que vous référencez les bibliothèques de script client approprié et que vous définissez des classes CSS.)

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample110.html)]

### `Validation.For(field)`

(v2) Active la validation côté client pour le champ d’entrée d’utilisateur. (Nécessite que vous référencez les bibliothèques de script client approprié).

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample111.html)]

### `Validation.IsValid()`

(v2) Retourne la valeur true si tous les éléments d’entrée utilisateur sont enregistrées pour la validation contiennent des valeurs valides.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample112.cs)]

### `Validation.RequireField(field[, errorMessage])`

(v2) Spécifie que les utilisateurs doivent fournir une valeur pour l’élément d’entrée.

[!code-csharp[Main](asp-net-web-pages-api-reference/samples/sample113.cs)]

### `Validation.RequireFields(field1[, field12, field3, ...])`

(v2) Spécifie que les utilisateurs doivent fournir des valeurs pour chacun des éléments d’entrée utilisateur. Cette méthode ne permet pas de spécifier un message d’erreur personnalisé.

[!code-html[Main](asp-net-web-pages-api-reference/samples/sample114.html)]

### `Validator.DateTime ([error message])`  
`Validator.Decimal([error message])`  
`Validator.EqualsTo(otherField,[error message])`  
`Validator.Float([error message])`  
`Validator.Integer([error message])`  
`Validator.Range(min,max [, error message])`  
`Validator.RegEx(pattern[, error message])`  
`Validator.Required([error message])`  
`Validator.StringLength(length)`  
`Validator.Url([error message])`

(v2) Spécifie un test de validation lorsque vous utilisez la `Validation.Add` (méthode).

[!code-javascript[Main](asp-net-web-pages-api-reference/samples/sample115.js)]
