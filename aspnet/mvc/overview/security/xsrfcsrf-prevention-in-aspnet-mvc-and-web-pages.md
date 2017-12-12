---
uid: mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages
title: "Prévention de XSRF/CSRF dans ASP.NET MVC et les Pages Web | Documents Microsoft"
author: Rick-Anderson
description: "Falsification de requête (également appelé XSRF ou CSRF) est une attaque contre les applications hébergées par le web dans laquelle un site web malveillant peut influencer l’interacti..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 03/14/2013
ms.topic: article
ms.assetid: aadc5fa4-8215-4fc7-afd5-bcd2ef879728
ms.technology: dotnet-mvc
ms.prod: .net-framework
msc.legacyurl: /mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages
msc.type: authoredcontent
ms.openlocfilehash: 4ff4ed20d0768a48f8afb2deeb7cdb6b4c60b5bc
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
<a name="xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages"></a>Prévention de XSRF/CSRF dans ASP.NET MVC et les Pages Web
====================
Par [Rick Anderson](https://github.com/Rick-Anderson)

> Requête falsification (également appelé XSRF ou CSRF) est une attaque contre les applications hébergées par le web dans laquelle un site web malveillant peut influencer l’interaction entre un navigateur client et un site web approuvé par ce navigateur. Ces attaques sont possible parce que les navigateurs web envoie des jetons d’authentification automatiquement avec chaque demande à un site web. L’exemple canonique est un cookie d’authentification, tels que ASP. Ticket d’authentification par formulaire de NET. Toutefois, les sites web qui utilisent un mécanisme d’authentification persistant (par exemple, l’authentification Windows, Basic et ainsi de suite) peuvent être ciblés par ces attaques.
> 
> Une attaque XSRF diffère d’une attaque par hameçonnage. Des attaques d’hameçonnage nécessitent l’interaction de la victime. Dans une attaque par hameçonnage, un site web malveillant reflétera le site web cible et la victime est croyez fournissant des informations sensibles à la personne malveillante. Dans une attaque XSRF, aucune interaction n’est souvent nécessaire de la victime. Au lieu de cela, l’attaquant repose sur le navigateur d’envoyer automatiquement tous les cookies pertinentes pour le site de destination.
> 
> Pour plus d’informations, consultez la [ouvrir un projet Web Application sécurité](https://www.owasp.org/index.php/Main_Page)(OWASP avoir) [XSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).


## <a name="anatomy-of-an-attack"></a>Anatomie d’une attaque

Pour étudier une attaque XSRF, prenons un utilisateur qui veut effectuer certaines opérations bancaires en ligne. Tout d’abord, cet utilisateur visite WoodgroveBank.com et les journaux dans, à quel point l’en-tête de réponse contiendra le cookie d’authentification :

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample1.cmd)]

Étant donné que le cookie d’authentification est un cookie de session, il sera automatiquement supprimé par le navigateur lorsque le processus de navigateur se ferme. Toutefois, en attendant, le navigateur inclut automatiquement le cookie avec chaque demande WoodgroveBank.com. L’utilisateur veut maintenant transférer 1 000 dollars à un autre compte, afin qu’elle remplit un formulaire sur le site de la banque, et le navigateur effectue cette demande au serveur :

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample2.cmd)]

Étant donné que cette opération a un effet secondaire (il lance une transaction monétaire), le site de la banque a choisi exiger une requête HTTP POST pour lancer cette opération. Le serveur lit le jeton d’authentification de la demande, consulte le numéro de compte de l’utilisateur actuel, vérifie que provision existe, puis lance la transaction dans le compte de destination.

Elle bancaires en ligne terminée, l’utilisateur quitte le site bancaire et visite d’autres emplacements sur le web. Un de ces sites – fabrikam.com – inclut le balisage suivant sur une page incorporé dans un &lt;iframe&gt;:

[!code-html[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample3.html)]

Ce qui entraîne le navigateur effectuer cette demande :

[!code-console[Main](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/samples/sample4.cmd)]

L’attaquant exploite le fait que l’utilisateur peut avoir toujours un jeton d’authentification valide pour le site web cible, et elle est à l’aide d’un petit extrait de code JavaScript pour que le navigateur effectuer une requête HTTP POST au site cible automatiquement. Si le jeton d’authentification est toujours valide, le site bancaire initie un transfert de 250 $ dans le compte de son choix.

### <a name="ineffective-mitigations"></a>Solutions d’atténuation inefficaces

Il est intéressant de noter que dans le scénario ci-dessus, le fait que WoodgroveBank.com a été accédée via SSL et avait un cookie d’authentification SSL uniquement était insuffisant pour contrer les attaques. La personne malveillante est en mesure de spécifier la [schéma d’URI](http://en.wikipedia.org/wiki/URI_scheme) (https) dans son &lt;formulaire&gt; élément et le navigateur continue à envoyer des cookies non expirés pour le site cible tant que ces cookies sont cohérents avec l’URI schéma de la cible prévue.

Disent que l’utilisateur ne doit simplement pas consulter les sites non fiables, comme la visite de sites de confiance est permet de rester sans échec en ligne. Il existe une part de vérité, mais malheureusement, ce Conseil n’est pas toujours pratique. Peut-être l’utilisateur « approuve » du site local news ConsolidatedMessenger. ConsolidatedMessenger.com et permet d’accéder à la visite du site au lieu de cela, mais ce site possède une vulnérabilité XSS qui permet à une personne malveillante d’injecter la même extrait de code qui s’exécutait sur fabrikam.com.

Vous pouvez vous assurer que les demandes entrantes un [en-tête Referer](http://www.w3.org/Protocols/HTTP/HTRQ_Headers.html#z14) faisant référence à votre domaine. Cette commande arrête les demandes envoyées involontairement à partir d’un domaine d’application tierce. Toutefois, certaines personnes désactiver l’en-tête du référent de son navigateur pour des raisons de confidentialité, et des personnes malveillantes peuvent parfois usurper l’identité de cet en-tête si la victime équipé du logiciel certain non sécurisé. Vérification de la [en-tête Referer](http://www.w3.org/Protocols/HTTP/HTRQ_Headers.html#z14) n’est pas considéré comme une approche sûre pour empêcher les attaques XSRF.

## <a name="web-stack-runtime-xsrf-mitigations"></a>Solutions d’atténuation Web pile Runtime XSRF

Le Runtime de pile Web ASP.NET utilise une variante de la [du modèle de jeton synchronisateur](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet#General_Recommendation:_Synchronizer_Token_Pattern) pour vous défendre contre les attaques XSRF. La forme générale du modèle de jeton synchronisateur est que les deux jetons d’anti-XSRF sont envoyées au serveur avec chaque requête HTTP POST (en plus du jeton d’authentification) : un jeton en tant qu’un cookie et l’autre comme une valeur de formulaire. Les valeurs de jeton générés par le runtime ASP.NET ne sont pas déterministes ou prévisibles par une personne malveillante. Lorsque les jetons sont envoyés, le serveur autorisera la demande continuer uniquement si les deux jetons réussissent la vérification de comparaison.

La vérification de la demande XSRF *du jeton de session* est stocké comme un cookie HTTP et actuellement contient les informations suivantes dans sa charge utile :

- Un jeton de sécurité, consistant en un identificateur aléatoire de 128 bits.   
 L’illustration suivante montre le jeton de session vérification XSRF demande affiché avec les outils de développement Internet Explorer F12 : (Notez il s’agit de l’implémentation actuelle et est soumis, même probable, à modifier.)

![](xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages/_static/image1.png)

Le *champ jeton* est stocké comme un `<input type="hidden" />` et contient les informations suivantes dans sa charge utile :

- Connecté de l’utilisateur (s’il est authentifié).
- Toutes les données supplémentaires fournies par une [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx).

Les charges utiles des jetons anti-XSRF sont chiffrés et signés, donc vous ne pouvez pas afficher le nom d’utilisateur lors de l’utilisation des outils pour examiner les jetons. Lors de l’application web cible ASP.NET 4.0, les services de chiffrement sont fournis par le [MachineKey.Encode](https://msdn.microsoft.com/en-us/library/system.web.security.machinekey.encode.aspx) routine. Lorsque l’application web cible ASP.NET 4.5 ou version ultérieure, le chiffrement des services sont fournis par le [MachineKey.Protect](https://msdn.microsoft.com/en-us/library/system.web.security.machinekey.protect(v=vs.110)) routine, qui offre de meilleures performances, extensibilité et la sécurité. Consultez que le blog suivant publie pour plus d’informations :

- [Améliorations des services de chiffrement dans ASP.NET 4.5, pt. 1](https://blogs.msdn.com/b/webdev/archive/2012/10/22/cryptographic-improvements-in-asp-net-4-5-pt-1.aspx)
- [Améliorations des services de chiffrement dans ASP.NET 4.5, pt. 2](https://blogs.msdn.com/b/webdev/archive/2012/10/23/cryptographic-improvements-in-asp-net-4-5-pt-2.aspx)
- [Améliorations des services de chiffrement dans ASP.NET 4.5, pt. 3](https://blogs.msdn.com/b/webdev/archive/2012/10/24/cryptographic-improvements-in-asp-net-4-5-pt-3.aspx)

## <a name="generating-the-tokens"></a>Génération de jetons

Pour générer les jetons anti-XSRF, appelez le [ @Html.AntiForgeryToken ](https://msdn.microsoft.com/en-us/library/dd470175.aspx) méthode à partir d’une vue MVC ou @AntiForgery.GetHtml() à partir d’une page Razor. Le runtime effectue ensuite les étapes suivantes :

1. Si la requête HTTP actuelle contient déjà un jeton de session anti-XSRF (le cookie anti-XSRF \_ \_RequestVerificationToken), le jeton de sécurité est extraite à partir de celui-ci. Si la requête HTTP ne contient pas un jeton de session anti-XSRF ou si l’extraction du jeton de sécurité échoue, un nouveau jeton anti-XSRF aléatoire sera généré.
2. Un jeton de champ anti-XSRF est généré à l’aide du jeton de sécurité de l’étape (1) et l’identité de l’utilisateur connecté actuel. (Pour plus d’informations sur la définition de l’identité de l’utilisateur, consultez la  **[des scénarios avec prise en charge spéciale](#_Scenarios_with_special)**  section ci-dessous.) En outre, si un [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/jj158328(v=vs.111).aspx) est configuré, le runtime appelle sa [GetAdditionalData](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider.getadditionaldata(v=vs.111).aspx) (méthode) et inclure la chaîne retournée dans le jeton de champ. (Consultez la  **[Configuration et l’extensibilité](#_Configuration_and_extensibility)**  section pour plus d’informations.)
3. Si un nouveau jeton anti-XSRF a été généré à l’étape (1), un jeton de session nouvelle sera créé pour contenir et sera ajouté à la collection de cookies HTTP sortante. Le jeton de champ de l’étape (2) à encapsuler dans un `<input type="hidden" />` élément et ce balisage HTML sera la valeur de retour de `Html.AntiForgeryToken()` ou `AntiForgery.GetHtml()`.

## <a name="validating-the-tokens"></a>Valide les jetons

Pour valider les jetons anti-XSRF entrant, le développeur inclut un [ValidateAntiForgeryToken](https://msdn.microsoft.com/en-us/library/system.web.mvc.validateantiforgerytokenattribute(VS.108).aspx) attribut sur son action MVC ou contrôleur ou les appels she `@AntiForgery.Validate()` à partir de sa page Razor. Le runtime effectue les étapes suivantes :

1. Le jeton de session entrante et le jeton de champ sont lus et le jeton anti-XSRF extraites à partir de chacun. Les jetons d’anti-XSRF doivent être identiques par étape (2) dans la routine de génération.
2. Si l’utilisateur actuel est authentifié, son nom d’utilisateur est comparée avec le nom d’utilisateur stocké dans le jeton de champ. Les noms d’utilisateur doivent correspondre.
3. Si un [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) est configuré, le runtime appelle sa *ValidateAdditionalData* (méthode). La méthode doit retourner la valeur booléenne *true*.

Si la validation réussit, la demande est autorisée à se poursuivre. Si la validation échoue, le framework lèvera une *HttpAntiForgeryException*.

## <a name="failure-conditions"></a>Conditions d’échec

En commençant par le Runtime de pile Web ASP.NET v2, de tout *HttpAntiForgeryException* qui est levée pendant la validation contiendra des informations détaillées. Les conditions d’échec actuellement définies sont :

- Le jeton de formulaire ou un jeton de session n’est pas présent dans la demande.
- Le jeton de session ou un jeton de formulaire est illisible. La cause la plus probable est une batterie de serveurs exécutant des versions incompatibles de l’exécution de pile Web ASP.NET ou une batterie de serveurs où le &lt;machineKey&gt; élément dans le fichier Web.config diffère entre les ordinateurs. Vous pouvez utiliser un outil tel que Fiddler pour forcer cette exception en falsifier un jeton anti-XSRF.
- Le jeton de session et le jeton de champ ont été permutées.
- Le jeton de session et le jeton de champ contiennent des jetons de sécurité ne correspondent pas.
- Le nom d’utilisateur incorporée dans le jeton de champ ne correspond pas nom d’utilisateur connecté l’utilisateur actuel.
- Le  *[IAntiForgeryAdditionalDataProvider.ValidateAdditionalData](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider.validateadditionaldata(v=vs.111).aspx)*  méthode retournée *false*.

Les installations d’anti-XSRF peuvent également effectuer une vérification supplémentaire pendant la génération de jetons ou de validation, et les échecs au cours de ces vérifications peuvent entraîner exceptions sont levées. Consultez le [WIF / ACS / basée sur les revendications authentification](#_WIF_ACS) et  **[Configuration et l’extensibilité](#_Configuration_and_extensibility)**  sections pour plus d’informations.

<a id="_Scenarios_with_special"></a>

## <a name="scenarios-with-special-support"></a>Scénarios de prise en charge spéciale

### <a name="anonymous-authentication"></a>Authentification anonyme

Le système anti-XSRF contient la prise en charge spéciale pour les utilisateurs anonymes, où « anonyme » est définie en tant qu’utilisateur où le *sur IIdentity.IsAuthenticated* propriété renvoie *false*. Les scénarios incluent une protection XSRF à la page de connexion (avant que l’utilisateur est authentifié) et les schémas d’authentification personnalisés où l’application utilise un mécanisme autre que *IIdentity* pour identifier les utilisateurs.

Pour prendre en charge ces scénarios, rappelez-vous que les jetons de session et les champs sont jointes à un jeton de sécurité, qui est un identificateur opaque 128 bits générée de manière aléatoire. Ce jeton de sécurité est utilisé pour effectuer le suivi de session d’un utilisateur individuel comme elle accède le site, afin qu’il a pour objectif d’un identificateur anonyme efficacement. Une chaîne vide est utilisée à la place du nom d’utilisateur pour les routines de génération et de validation décrites ci-dessus.

<a id="_WIF_ACS"></a>

### <a name="wif--acs--claims-based-authentication"></a>WIF / ACS / basée sur les revendications à l’authentification

Normalement, le *IIdentity* classes générées le .NET Framework ont la propriété qui *IIdentity.Name* est suffisante pour identifier de façon unique un utilisateur particulier au sein d’une application particulière. Par exemple, *FormsIdentity.Name* retourne le nom d’utilisateur stocké dans la base de données d’appartenance (qui est unique pour toutes les applications en fonction de cette base de données), *WindowsIdentity.Name* retourne le identité de domaine complet de l’utilisateur et ainsi de suite. Ces systèmes fournissent non seulement l’authentification ; ils également *identifier* aux utilisateurs d’une application.

L’authentification basée sur les revendications, quant à elle, ne requiert pas nécessairement identifier un utilisateur particulier. Au lieu de cela, le *ClaimsPrincipal* et *ClaimsIdentity* types sont associés à un ensemble de *revendication* instances, où les revendications individuelles peuvent être « plus de 18 ans d’âge » ou » est un administrateur » pour tout autre élément. Étant donné que l’utilisateur n’a pas nécessairement identifié, le runtime ne peut pas utiliser le *ClaimsIdentity.Name* propriété comme identificateur unique pour cet utilisateur particulier. L’équipe a constaté des exemples réels où *ClaimsIdentity.Name* retourne *null*, retourne un nom convivial, ou sinon retourne une chaîne qui n’est pas appropriée pour une utilisation comme identificateur unique pour l’utilisateur.

La plupart des déploiements qui utilisent l’authentification basée sur les revendications sont à l’aide de [Azure Access Control Service](https://msdn.microsoft.com/en-us/library/windowsazure/gg429786.aspx) (ACS) en particulier. ACS permet au développeur de configurer des *fournisseurs d’identité* (tel qu’AD FS, le fournisseur Microsoft Account, fournisseurs OpenID comme Yahoo!, etc.), et les fournisseurs d’identité retournent *nom identificateurs*. Ces identificateurs de nom peuvent contenir des informations personnellement identifiables (PII) à une adresse électronique, ou ils pourraient être présentées de façon anonyme comme un identificateur personnel privé (PPID). Tous les cas, le tuple (fournisseur d’identité, identificateur de nom) suffisamment sert un jeton de suivi approprié pour un utilisateur particulier pendant qu’elle parcourt le site, par conséquent, le Runtime de pile Web ASP.NET peuvent utiliser le tuple à la place du nom d’utilisateur lors de la génération et valider des jetons de champ anti-XSRF. Les URI particulier pour le fournisseur d’identité et l’identificateur de nom sont :

- `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`
- `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`

(voir [page de documentation des services ACS](https://msdn.microsoft.com/en-us/library/windowsazure/gg185971.aspx) pour plus d’informations.)

Lors de la génération ou de validation d’un jeton, le Runtime de pile Web ASP.NET essaiera lors de l’exécution pour les types de liaison de :

- `Microsoft.IdentityModel.Claims.IClaimsIdentity, Microsoft.IdentityModel, Version=3.5.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35`(Pour le SDK de WIF.)
- `System.Security.Claims.ClaimsIdentity`(Pour .NET 4.5).

Si ces types existent et si l’utilisateur actuel *IIIIdentity* implémente ou sous-classes un de ces types, la fonctionnalité anti-XSRF utilise (fournisseur d’identité, identificateur de nom) tuple à la place du nom d’utilisateur lors de la génération et valide les jetons. Si aucun tuple de ce type n’est présent, la demande échoue avec une erreur décrivant au développeur comment configurer le système anti-XSRF pour comprendre le mécanisme d’authentification basée sur les revendications particulier en cours d’utilisation. Consultez le  **[Configuration et l’extensibilité](#_Configuration_and_extensibility)**  section pour plus d’informations.

### <a name="oauth--openid-authentication"></a>OAuth / OpenID authentification

Enfin, l’installation d’anti-XSRF dispose d’une prise en charge spéciale pour les applications qui utilisent l’authentification OAuth ou OpenID. Cette prise en charge est basé sur l’heuristique : si actuel *IIdentity.Name* commence par http:// ou https://, puis les comparaisons de nom d’utilisateur seront effectuées à l’aide d’un comparateur Ordinal plutôt que le comparateur de OrdinalIgnoreCase par défaut.

<a id="_Configuration_and_extensibility"></a>

## <a name="configuration-and-extensibility"></a>Configuration et l’extensibilité

Parfois, les développeurs peuvent faire d’un contrôle plus strict sur la génération de l’anti-XSRF et les comportements de validation. Par exemple, peut-être par défaut assistance MVC et les Pages Web de l’ajout automatique des cookies HTTP pour la réponse n’est pas souhaitable, et le développeur peut souhaitez conserver les jetons ailleurs. Il existe deux API pour vous aider à cela :

`AntiForgery.GetTokens(string oldCookieToken, out string newCookieToken, out string formToken);`  
`AntiForgery.Validate(string cookieToken, string formToken);`

Le *GetTokens* méthode prend comme entrée un XSRF demande vérification session jeton existant (qui peut être null) et génère en sortie un nouveau jeton de session de vérification XSRF demande et un jeton de champ. Les jetons sont des chaînes opaques simplement sans ornement ; le *formToken* valeur pour l’instance pas à encapsuler dans un &lt;d’entrée&gt; balise. Le *newCookieToken* valeur peut être null ; si cela se produit, puis le *oldCookieToken* valeur est toujours valide et aucun nouveau cookie de réponse ne doive être définie. L’appelant de *GetTokens* est responsable de la persistance des cookies de réponse nécessaire ou la génération du tout balisage nécessaire ; le *GetTokens* méthode proprement dite ne modifie pas la réponse sous la forme d’un effet secondaire. Le *Validate* méthode accepte la session entrante et champ des jetons et exécute la logique de validation susmentionnés au-dessus d’eux.

### <a name="antiforgeryconfig"></a>AntiForgeryConfig

Le développeur peut configurer le système anti-XSRF à partir de l’Application\_Démarrer. La configuration est par programmation. Les propriétés de la méthode statique *AntiForgeryConfig* type sont décrites ci-dessous. Vous devez définir la propriété UniqueClaimTypeIdentifier la plupart des utilisateurs à l’aide de revendications.

| **Property** | **Description** |
| --- | --- |
| **AdditionalDataProvider** | Un [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) qui fournit des données supplémentaires pendant la génération du jeton et consomme des données supplémentaires pendant la validation du jeton. La valeur par défaut est *null*. Pour plus d’informations, consultez la [IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx) section. |
| **CookieName** | Chaîne qui fournit le nom du cookie HTTP qui est utilisé pour stocker le jeton de session anti-XSRF. Si cette valeur n’est pas définie, un nom est automatiquement généré en fonction du chemin d’accès virtuel de l’application déployée. La valeur par défaut est *null*. |
| **RequireSsl** | Valeur booléenne qui indique si les jetons anti-XSRF sont nécessaires pour être envoyée via un canal sécurisé par SSL. Si cette valeur est *true*, tous les cookies automatiquement généré aura l’indicateur « sécurisé » définie, et les API anti-XSRF lève si appelée à partir d’une demande qui n’est pas envoyée via SSL. La valeur par défaut est *false*. |
| **SuppressIdentityHeuristicChecks** | Valeur booléenne qui indique si le système anti-XSRF est recommandé de désactiver la prise en charge pour les identités basées sur les revendications. Si cette valeur est *true*, le système suppose que *IIdentity.Name* est appropriée pour une utilisation en tant qu’un identificateur d’utilisateur unique et ne tente pas de cas spéciaux *IClaimsIdentity*ou *ClClaimsIdentity* comme décrit dans la [WIF / ACS / basée sur les revendications authentification](#_WIF_ACS) section. La valeur par défaut est `false`. |
| **UniqueClaimTypeIdentifier** | Une chaîne qui indique les revendications de type est appropriée pour une utilisation en tant qu’un identificateur d’utilisateur unique. Si cette valeur est définie et actuel *IIdentity* -basée sur les revendications, le système tente d’extraire une revendication du type spécifié par *UniqueClaimTypeIdentifier*, et la valeur correspondante sera utilisée. à la place de l’utilisateur lors de la génération du jeton de champ. Si le type de revendication n’est pas trouvé, le système fait échouer la requête. La valeur par défaut est *null*, ce qui signifie que le système doit utiliser (fournisseur d’identité, identificateur de nom) tuple comme décrit précédemment à la place de l’utilisateur. |

<a id="_IAntiForgeryAdditionalDataProvider"></a>

### <a name="iantiforgeryadditionaldataprovider"></a>IAntiForgeryAdditionalDataProvider

Le  *[IAntiForgeryAdditionalDataProvider](https://msdn.microsoft.com/en-us/library/system.web.helpers.iantiforgeryadditionaldataprovider(v=vs.111).aspx)*  permet aux développeurs d’étendre le comportement du système anti-XSRF par des données supplémentaires aller-retour dans chaque jeton. Le *GetAdditionalData* méthode est appelée chaque fois qu’un jeton de champ est généré, et la valeur de retour est incorporée dans le jeton généré. Un implémenteur peut retourner un horodatage, une valeur à usage unique ou toute autre valeur si qu'elle veut à partir de cette méthode.

De même, la *ValidateAdditionalData* méthode est appelée chaque fois qu’un jeton de champ est validé, et la chaîne « données supplémentaires » qui a été incorporée dans le jeton est passée à la méthode. La routine de validation peut implémenter un délai d’attente (en vérifiant l’heure actuelle par rapport à l’heure qui a été enregistrée lors de la création du jeton), si vous le souhaitez une valeur à usage unique, la vérification de la routine, ou toute autre logique.

## <a name="design-decisions-and-security-considerations"></a>Considérations de sécurité et les décisions de conception

Le jeton de sécurité qui lie les jetons de session et le champ est techniquement nécessaire uniquement lorsque vous tentez de protéger les utilisateurs anonymes / non authentifiées attaques XSRF. Lorsque l’utilisateur est authentifié, le jeton d’authentification lui-même (vraisemblablement présentée sous la forme d’un cookie) peut être utilisé comme une moitié d’un synchronisateur paire de jetons. Toutefois, il existe des scénarios valides pour la protection des pages de connexion d’accès par des utilisateurs non authentifiés, et la logique d’anti-XSRF a été effectuée plus simple en générant et en validant le jeton de sécurité, même pour les utilisateurs authentifiés toujours. Il fournit un niveau de protection supplémentaire au cas où un jeton de champ est compromis par un intrus, en tant que paramètre ou de deviner que le jeton de session serait également possible pour la personne malveillante surmonter.

Les développeurs doivent utiliser attention lorsque plusieurs applications sont hébergées dans un seul domaine. Par exemple, même si *example1.cloudapp.net* et *example2.cloudapp.net* sont des hôtes différents, il existe une relation d’approbation implicite entre tous les hôtes de la  *\*. cloudapp.net* domaine. Cette relation de confiance implicite [permet aux hôtes potentiellement non fiables affecter l’autre les cookies](http://stackoverflow.com/questions/9636857/how-can-asp-net-or-asp-net-mvc-be-protected-from-related-domain-cookie-attacks) (les même origine les stratégies qui régissent les requêtes AJAX ne pas nécessairement s’appliquent pour les cookies HTTP). Le Runtime de pile Web ASP.NET fournit une atténuation dans la mesure où le nom d’utilisateur est incorporée dans le jeton de champ, donc, même si un sous-domaine malveillant est en mesure de remplacer un jeton de session qu’il est impossible de générer un jeton de champ valide pour l’utilisateur. Toutefois, lorsqu’il est hébergé dans un tel environnement les routines intégrées anti-XSRF toujours ne peut pas vous défendre contre le piratage de session ou connexion XSRF.

Les routines d’anti-XSRF actuellement ne pas défendre [clickjacking](https://www.owasp.org/index.php/Clickjacking). Les applications qui souhaitent se défendre contre clickjacking peuvent facilement font en envoyant un X-Frame-Options : en-tête SAMEORIGIN avec chaque réponse. Cet en-tête est pris en charge par tous les navigateurs récents. Pour plus d’informations, consultez la [blog IE](https://blogs.msdn.com/b/ieinternals/archive/2010/03/30/combating-clickjacking-with-x-frame-options.aspx), le [blog SDL](https://blogs.msdn.com/b/sdl/archive/2009/02/05/clickjacking-defense-in-ie8.aspx), et [OWASP avoir](https://www.owasp.org/index.php/Clickjacking). Le Runtime de pile Web ASP.NET peut dans certains Assurez-vous de version ultérieure le MVC et programmes d’assistance de Pages Web anti-XSRF définir automatiquement cet en-tête afin que les applications sont automatiquement protégées contre ce genre d’attaque.

Les développeurs Web doivent continuer à s’assurer que leur site n’est pas vulnérable aux attaques XSS. Attaques XSS sont très puissants et une exploitation réussie compromettrait également des attaques XSRF les défenses du Runtime de pile Web ASP.NET.

## <a name="acknowledgment"></a>Accusé de réception

[@LeviBroderick](https://twitter.com/LeviBroderick), qui a une grande partie du code de sécurité ASP.NET écrit la majeure partie de ces informations.
