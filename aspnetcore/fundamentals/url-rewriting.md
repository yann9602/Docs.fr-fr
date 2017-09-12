---
title: "Intergiciel (middleware) dans ASP.NET Core de réécriture d’URL"
author: guardrex
description: "Introduction à l’URL de réécriture et en redirigeant des instructions sur l’utilisation d’intergiciel (middleware) réécriture d’URL dans les applications ASP.NET Core."
keywords: "ASP.NET Core, réécriture d’URL, réécriture d’URL, URL de redirection, la redirection d’URL, intergiciel (middleware), apache_mod"
ms.author: riande
manager: wpickett
ms.date: 08/17/2017
ms.topic: article
ms.assetid: e6130638-c410-4161-9921-b658ce988bd1
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/url-rewriting
ms.openlocfilehash: 05a92c4eee6b26e49831c11e1251aedba87ed717
ms.sourcegitcommit: 9cdbfd0d670d70b9c354216aabee260c52dad5ee
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2017
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>Intergiciel (middleware) dans ASP.NET Core de réécriture d’URL

Par [Luke Latham](https://github.com/GuardRex) et [Mikael Mengistu](https://github.com/mikaelm12)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)

Réécriture d’URL est le fait de la modification de requête URL basées sur une ou plusieurs règles prédéfinies. Réécriture d’URL de crée une abstraction entre les emplacements de ressources et leurs adresses afin que les emplacements et les adresses ne sont pas étroitement liés. Il existe plusieurs scénarios où il est utile de réécriture d’URL :
* Déplacez ou remplacez temporairement ou définitivement des ressources du serveur tout en conservant les localisateurs stables pour ces ressources
* Fractionnement entre différentes applications ou entre les zones d’une application de traitement des demandes
* Suppression, l’ajout ou la réorganisation des segments d’URL sur les requêtes entrantes
* Optimisation des URL publiques pour l’optimisation de moteur de recherche (SEO)
* Autoriser l’utilisation d’une URL publique conviviale pour aider les personnes à prédire le contenu, qu'ils trouveront en suivant un lien
* Redirection des demandes non sécurisées pour sécuriser les points de terminaison
* Empêche toute image hotlinking

Vous pouvez définir des règles pour modifier l’URL de plusieurs façons, notamment regex, les règles de module mod_rewrite Apache, les règles du Module de réécriture IIS et à l’aide de la logique de règle personnalisée. Ce document présente la réécriture d’URL avec des instructions sur l’utilisation d’intergiciel (middleware) réécriture d’URL dans les applications ASP.NET Core.

> [!NOTE]
> Réécriture d’URL peut réduire les performances d’une application. Si possible, vous devez limiter le nombre et la complexité des règles.

## <a name="url-redirect-and-url-rewrite"></a>Réécriture de redirection d’URL et l’URL
La différence dans la formulation entre *redirection d’URL* et *réécriture d’URL* peut sembler subtiles au premier mais a des implications importantes pour fournir des ressources pour les clients. Intergiciel (middleware) d’ASP.NET Core URL réécriture est capable de répondre la nécessité pour les deux.

A *redirection d’URL* est une opération côté client, où il est invité à accéder à une ressource à une autre adresse. Cela requiert un aller-retour vers le serveur, et l’URL de redirection retournée au client apparaît dans la barre d’adresses du navigateur lorsque le client envoie une nouvelle requête pour la ressource. Si `/resource` est *redirigé* à `/different-resource`, les demandes des clients `/resource`, et le serveur répond que le client doit obtenir la ressource au `/different-resource` avec un code d’état qui indique que la redirection est temporaire ou permanent. Le client exécute une nouvelle demande de la ressource à l’URL de redirection.

![Un point de terminaison de service WebAPI a été modifié temporairement de la version 1 (v1) vers la version 2 (v2) sur le serveur. Un client effectue une demande au service à le /v1/api de chemin d’accès de la version 1. Le serveur renvoie une réponse 302 de (trouvé) avec le chemin d’accès temporaire pour le service à la version 2 /v2/api. Le client effectue une deuxième demande au service à l’URL de redirection. Le serveur répond avec un code d’état 200 (OK).](url-rewriting/_static/url_redirect.png)

Lors de la redirection des demandes vers une URL différente, vous indiquez si la redirection est permanente ou temporaire. Le code d’état (déplacé définitivement) 301 sert à indiquer au client que toutes les futures demandes pour la ressource doivent utiliser la nouvelle URL où la ressource a une nouvelle URL permanente et que vous souhaitez. *Le client peut mettre en cache la réponse lors de la réception d’un code de 301 état.* Le code d’état 302 (trouvé) est utilisé lorsque la redirection est temporaire ou généralement l’objet à modifier, de sorte que le client ne doit pas stocker et réutiliser l’URL de redirection dans le futur. Pour plus d’informations, consultez [le document RFC 2616 : définitions de Code d’état](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

A *réécriture d’URL* est une opération côté serveur pour fournir une ressource à partir d’une adresse de ressources différent. Réécriture d’URL ne nécessite pas un aller-retour au serveur. L’URL réécrite n’est pas retourné au client et n’apparaître pas dans la barre d’adresses du navigateur. Lorsque `/resource` est *réécrit* à `/different-resource`, les demandes des clients `/resource`et le serveur *en interne* extrait la ressource au `/different-resource`. Bien que le client peut être en mesure de récupérer la ressource à l’URL réécrite, le client ne sera pas informé que la ressource existe à l’URL réécrite lorsqu’il effectue sa demande et reçoit la réponse.

![Un point de terminaison de service WebAPI a été modifié à partir de la version 1 (v1) vers la version 2 (v2) sur le serveur. Un client effectue une demande au service à le /v1/api de chemin d’accès de la version 1. L’URL de requête est réécrite pour accéder au service à le /v2/api de chemin d’accès de la version 2. Le service répond au client avec un code d’état 200 (OK).](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>URL réécriture exemple d’application
Vous pouvez explorer les fonctionnalités de l’intergiciel de réécriture d’URL avec le [URL réécriture exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/). L’application s’applique la réécriture et règles de redirection et affiche l’URL réécrite ou redirigée.

## <a name="when-to-use-url-rewriting-middleware"></a>Quand utiliser le Middleware de réécriture d’URL
Utiliser le Middleware de réécriture d’URL lorsque vous ne pouvez pas utiliser le [module de réécriture d’URL](https://www.iis.net/downloads/microsoft/url-rewrite) avec IIS sur Windows Server, le [module mod_rewrite d’Apache](https://httpd.apache.org/docs/2.4/rewrite/) sur serveur Apache, [réécriture d’URL sur Nginx](https://www.nginx.com/blog/creating-nginx-rewrite-rules/), ou si votre application est hébergée sur [serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé [WebListener](xref:fundamentals/servers/weblistener)). Les principales raisons d’utiliser l’URL réécriture technologies serveur dans IIS, Apache ou Nginx sont que l’intergiciel (middleware) ne prend pas en charge toutes les fonctionnalités de ces modules et les performances de l’intergiciel (middleware) probablement ne sera pas correspondre à celui des modules. Il existe toutefois certaines fonctionnalités des modules de serveur ne fonctionnent pas avec les projets ASP.NET Core, telles que la `IsFile` et `IsDirectory` contraintes du module de réécriture d’IIS. Dans ces scénarios, utilisez plutôt l’intergiciel (middleware).

## <a name="package"></a>Package
Pour inclure l’intergiciel (middleware) dans votre projet, ajoutez une référence à la [ `Microsoft.AspNetCore.Rewrite` ](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite/) package. Cette fonctionnalité est disponible pour les applications qui ciblent ASP.NET Core 1.1 ou version ultérieure.

## <a name="extension-and-options"></a>Extension et options
Établir votre réécriture d’URL et de rediriger les règles en créant une instance de la `RewriteOptions` classe avec des méthodes d’extension pour chacun de vos règles. Chaîner plusieurs règles dans l’ordre que vous souhaitez transformer. Le `RewriteOptions` sont passés dans l’intergiciel de réécriture d’URL lorsqu’elle est ajoutée au pipeline de demande avec `app.UseRewriter(options);`.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1)]

---

### <a name="url-redirect"></a>Redirection d’URL
Utilisez `AddRedirect` pour rediriger les demandes. Le premier paramètre contient votre expression régulière pour la correspondance sur le chemin d’accès de l’URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, s’il est présent, spécifie le code d’état. Si vous ne spécifiez pas le code d’état, la valeur par défaut 302 (trouvé), ce qui indique que la ressource est temporairement déplacée ou remplacée.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=2)]

---

Dans un navigateur avec des outils de développement activé, faire une demande à l’exemple d’application avec le chemin d’accès `/redirect-rule/1234/5678`. L’expression régulière correspond au chemin de la demande sur `redirect-rule/(.*)`, et le chemin d’accès est remplacé par `/redirected/1234/5678`. La redirection des URL est envoyée au client avec un code d’état 302 (trouvé). Le navigateur envoie une nouvelle demande à l’URL de redirection, qui apparaît dans la barre d’adresses du navigateur. Dans la mesure où aucune règle dans l’exemple d’application ne correspond à l’URL de redirection, la deuxième demande reçoit la réponse 200 (OK) à partir de l’application et le corps de la réponse indique l’URL de redirection. Un aller-retour vers le serveur lorsqu’une URL est *redirigé*.

> [!WARNING]
> Soyez prudent lors de l’établissement de vos règles de redirection. Vos règles de redirection sont évaluées à chaque demande à l’application, y compris après une redirection. Il est facile d’accidentellement une boucle de redirection infinie.

Demande d’origine :`/redirect-rule/1234/5678`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses de suivi](url-rewriting/_static/add_redirect.png)

La partie de l’expression entre parenthèses est appelée un *groupe de capture*. Le point (`.`) de l’expression signifie *correspond à n’importe quel caractère*. L’astérisque (`*`) indique *correspond au caractère précédent zéro ou plusieurs fois*. Par conséquent, les segments de chemin de deux derniers accès de l’URL, `1234/5678`, sont capturées par le groupe de capture `(.*)`. Toute valeur fournie dans l’URL de demande après `redirect-rule/` est capturé par ce groupe de capture unique.

Dans la chaîne de remplacement, les groupes capturés sont injectées dans la chaîne avec le signe dollar (`$`) suivi du numéro de séquence de la capture. La première valeur de groupe de capture est obtenue avec `$1`, le second avec `$2`, ils continuent de séquence pour les groupes de capture dans votre expression régulière. Il est uniquement un groupe capturé dans l’expression régulière règle de redirection dans l’exemple d’application, qu’un seul groupe injecté dans la chaîne de remplacement, qui est `$1`. Lorsque la règle est appliquée, l’URL devient `/redirected/1234/5678`.

<a name=url-redirect-to-secure-endpoint></a>
### <a name="url-redirect-to-a-secure-endpoint"></a>Redirection d’URL à un point de terminaison sécurisé
Utilisez `AddRedirectToHttps` pour rediriger les demandes HTTP vers le même hôte et le chemin d’accès à l’aide de HTTPS (`https://`). Si le code d’état n’est pas fourni, la valeur de l’intergiciel (middleware) par défaut 302 (trouvé). Si le port n’est pas fourni, l’intergiciel (middleware) par défaut est `null`, ce qui signifie que le protocole modifie à `https://` et le client accède à la ressource sur le port 443. L’exemple montre comment définir le code d’état à partir de 301 (déplacé définitivement) et modifiez le port à 5001.
```csharp
var options = new RewriteOptions()
    .AddRedirectToHttps(301, 5001);

app.UseRewriter(options);
```
Utilisez `AddRedirectToHttpsPermanent` pour rediriger les demandes non sécurisées vers le même hôte et le chemin d’accès avec le protocole HTTPS sécurisé (`https://` sur le port 443). L’intergiciel (middleware) définit le code d’état à partir de 301 (déplacé définitivement).

L’exemple d’application est capable de montrant comment utiliser `AddRedirectToHttps` ou `AddRedirectToHttpsPermanent`. Ajoutez la méthode d’extension pour le `RewriteOptions`. Effectuer une demande non sécurisée à l’application à n’importe quelle URL. Ignorer l’avertissement que le certificat auto-signé n’est pas fiable de sécurité de navigateur.

Demande d’origine à l’aide `AddRedirectToHttps(301, 5001)`:`/secure`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses de suivi](url-rewriting/_static/add_redirect_to_https.png)

Demande d’origine à l’aide `AddRedirectToHttpsPermanent`:`/secure`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses de suivi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Réécriture d’URL
Utilisez `AddRewrite` pour créer une règle de réécriture d’URL. Le premier paramètre contient votre expression régulière pour la correspondance sur le chemin d’accès URL entrante. Le deuxième paramètre est la chaîne de remplacement. Le troisième paramètre, `skipRemainingRules: {true|false}`, indique à l’intergiciel (middleware) ou non ignorer les règles de réécriture supplémentaires si la règle actuelle est appliquée.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=6)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=3)]

---

Demande d’origine :`/rewrite-rule/1234/5678`

![Fenêtre de navigateur avec des outils de développement le suivi de la demande et réponse](url-rewriting/_static/add_rewrite.png)

La première chose que vous remarquerez dans l’expression régulière est le premier (`^`) au début de l’expression. Cela signifie que correspondance commence au début du chemin d’accès d’URL.

Dans l’exemple précédent avec la règle de redirection, `redirect-rule/(.*)`, il n’existe aucun premier au début de l’expression régulière ; par conséquent, peuvent précéder tous les caractères `redirect-rule/` dans le chemin d’accès pour une correspondance réussie.

| Chemin d’accès                               | Faire correspondre à |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | Oui   |
| `/my-cool-redirect-rule/1234/5678` | Oui   |
| `/anotherredirect-rule/1234/5678`  | Oui   |

La règle de réécriture, `^rewrite-rule/(\d+)/(\d+)`, correspond uniquement aux chemins d’accès s’ils démarrent avec `rewrite-rule/`. Notez la différence dans la correspondance entre la règle de réécriture ci-dessous et la règle de redirection ci-dessus.

| Chemin d’accès                              | Faire correspondre à |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | Oui   |
| `/my-cool-rewrite-rule/1234/5678` | Non    |
| `/anotherrewrite-rule/1234/5678`  | Non    |

Suivant le `^rewrite-rule/` partie de l’expression, il existe deux groupes de capture, `(\d+)/(\d+)`. Le `\d` signifie *correspond à un chiffre (nombre)*. Le signe plus (`+`) signifie *correspond à un ou plusieurs au caractère précédent*. Par conséquent, l’URL doit contenir un nombre suivi d’une oblique suivie d’un autre nombre. Ces groupes sont injectées dans l’URL réécrite en tant que la capture `$1` et `$2`. La chaîne de remplacement de règle de réécriture place les groupes capturés dans la chaîne de requête. Le chemin d’accès demandé de `/rewrite-rule/1234/5678` est réécrite pour obtenir la ressource au `/rewritten?var1=1234&var2=5678`. Si une chaîne de requête est présent dans la demande d’origine, il est conservé lors de l’URL est réécrite.

Il n’existe aucun aller-retour vers le serveur pour obtenir la ressource. Si la ressource existe, il a extraite et retournée au client avec un code d’état du 200 (OK). Étant donné que le client n’est pas redirigé, l’URL dans la barre d’adresse de navigateur ne change pas. Comme le client est concerné, l’opération de réécriture d’URL ne s’est produite.

> [!NOTE]
> Utilisez `skipRemainingRules: true` autant que possible, car les règles de correspondance est un processus coûteux et réduit le temps de réponse d’application. Pour la réponse plus rapide de l’application :
> * Commande vos règles de réécriture de la règle de mise en correspondance plus fréquemment à la règle de mise en correspondance moins fréquemment.
> * Ignorer le traitement des règles restantes lorsqu’une correspondance est trouvée et aucun traitement de la règle supplémentaire n’est requis.

### <a name="apache-modrewrite"></a>Mod_rewrite d’Apache
Appliquer des règles de mod_rewrite Apache avec `AddApacheModRewrite`. Assurez-vous que le fichier de règles est déployé avec l’application. Pour plus d’informations et des exemples de règles de mod_rewrite, consultez [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A `StreamReader` est utilisé pour lire les règles à partir de la *ApacheModRewrite.txt* fichier de règles.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=1,7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le premier paramètre prend une `IFileProvider`, qui est fourni [Injection de dépendance](dependency-injection.md). Le `IHostingEnvironment` est injecté pour fournir le `ContentRootFileProvider`. Le deuxième paramètre est le chemin d’accès à votre fichier de règles, qui est *ApacheModRewrite.txt* dans l’exemple d’application.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=4)]

---

L’exemple d’application redirige les demandes de `/apache-mod-rules-redirect/(.\*)` à `/redirected?id=$1`. Le code d’état de réponse est 302 (trouvé).

[!code[Main](url-rewriting/samples/2.x/ApacheModRewrite.txt)]

Demande d’origine :`/apache-mod-rules-redirect/1234`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses de suivi](url-rewriting/_static/add_apache_mod_redirect.png)

##### <a name="supported-server-variables"></a>Variables de serveur pris en charge
L’intergiciel (middleware) prend en charge les variables de serveur Apache mod_rewrite suivantes :
* CONN_REMOTE_ADDR
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_FORWARDED
* HTTP_HOST
* HTTP_REFERER
* HTTP_USER_AGENT
* HTTPS
* IPV6
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_METHOD
* REQUEST_SCHEME
* REQUEST_URI
* SCRIPT_FILENAME
* SERVER_ADDR
* SERVER_PORT
* SERVER_PROTOCOL
* HEURE
* TIME_DAY
* TIME_HOUR
* TIME_MIN
* TIME_MON
* TIME_SEC
* TIME_WDAY
* TIME_YEAR

### <a name="iis-url-rewrite-module-rules"></a>Règles du Module de réécriture d’URL IIS
Pour utiliser les règles qui s’appliquent pour le Module de réécriture d’URL IIS, utilisez `AddIISUrlRewrite`. Assurez-vous que le fichier de règles est déployé avec l’application. Ne pas diriger l’intergiciel (middleware) à utiliser votre *web.config* fichier lors de l’exécution sur Windows Server IIS. Avec IIS, ces règles doivent être stockées en dehors de votre *web.config* pour éviter les conflits avec le module de réécriture d’IIS. Pour plus d’informations et des exemples de règles du Module de réécriture d’URL IIS, consultez [à l’aide de réécriture d’Url Module 2.0](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) et [référence de Configuration de Module de réécriture de URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

A `StreamReader` est utilisé pour lire les règles à partir de la *IISUrlRewrite.xml* fichier de règles.

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=2,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Le premier paramètre prend une `IFileProvider`, tandis que le deuxième paramètre est le chemin d’accès à votre fichier de règles XML, qui est *IISUrlRewrite.xml* dans l’exemple d’application.

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=5)]

---

L’exemple d’application réécrit les demandes de `/iis-rules-rewrite/(.*)` à `/rewritten?id=$1`. La réponse est envoyée au client avec un code d’état 200 (OK).

[!code-xml[Main](url-rewriting/samples/2.x/IISUrlRewrite.xml)]

Demande d’origine :`/iis-rules-rewrite/1234`

![Fenêtre de navigateur avec des outils de développement le suivi de la demande et réponse](url-rewriting/_static/add_iis_url_rewrite.png)

Si vous avez un Module de réécriture IIS active avec des règles au niveau du serveur configurés qui auraient un impact sur votre application de façons indésirables, vous pouvez désactiver le Module de réécriture IIS pour une application. Pour plus d’informations, consultez [modules IIS de la désactivation de](xref:hosting/iis-modules#disabling-iis-modules).

#### <a name="unsupported-features"></a>Fonctionnalités prises en charge

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

L’intergiciel (middleware) publié avec ASP.NET Core 2.x ne prend pas en charge les fonctionnalités de Module de réécriture d’URL IIS suivantes :
* Règles de trafic sortant
* Variables de serveur personnalisé
* Caractères génériques
* LogRewrittenUrl

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

L’intergiciel (middleware) publié avec ASP.NET Core 1.x ne prend pas en charge les fonctionnalités de Module de réécriture d’URL IIS suivantes :
* Règles globales
* Règles de trafic sortant
* Réécrivez les mappages
* Action de CustomResponse
* Variables de serveur personnalisé
* Caractères génériques
* Action : CustomResponse
* LogRewrittenUrl

---

#### <a name="supported-server-variables"></a>Variables de serveur pris en charge
L’intergiciel (middleware) prend en charge les variables de serveur de Module de réécriture d’URL IIS suivantes :
* CONTENT_LENGTH
* TYPE_CONTENU
* HTTP_ACCEPT
* HTTP_CONNECTION
* HTTP_COOKIE
* HTTP_HOST
* HTTP_REFERER
* HTTP_URL
* HTTP_USER_AGENT
* HTTPS
* LOCAL_ADDR
* QUERY_STRING
* REMOTE_ADDR
* REMOTE_PORT
* REQUEST_FILENAME
* REQUEST_URI

> [!NOTE]
> Vous pouvez également obtenir un `IFileProvider` via un `PhysicalFileProvider`. Cette approche peut fournir une plus grande flexibilité pour l’emplacement de la réécriture de vos fichiers de règles. Assurez-vous que vos fichiers de règles de réécriture sont déployés sur le serveur dans le chemin d’accès que vous fournissez.
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>Règle basée sur une méthode
Utilisez `Add(Action<RewriteContext> applyRule)` pour implémenter votre propre logique de règle dans une méthode. Le `RewriteContext` expose le `HttpContext` pour une utilisation dans votre méthode. Le `context.Result` détermine comment supplémentaires du pipeline de gestion du traitement.

| contexte. Résultat                       | Action                                                          |
| ------------------------------------ | --------------------------------------------------------------- |
| `RuleResult.ContinueRules` (par défaut) | Application des règles                                         |
| `RuleResult.EndResponse`             | Cesser d’appliquer les règles et envoyer la réponse                       |
| `RuleResult.SkipRemainingRules`      | Cesser d’appliquer les règles et d’envoyer le contexte de l’intergiciel (middleware) suivant |

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=9)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=6)]

---

L’exemple d’application montre une méthode qui redirige les demandes de chemins d’accès qui se termine par *.xml*. Si vous faites une demande `/file.xml`, il est redirigé vers `/xmlfiles/file.xml`. Le code d’état est défini sur 301 (déplacé définitivement). Pour une redirection, vous devez définir explicitement le code d’état de la réponse ; Sinon, un code d’état 200 (OK) est retourné et la redirection ne se produise sur le client.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet2)]

---

Demande d’origine :`/file.xml`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses pour file.xml de suivi](url-rewriting/_static/add_redirect_xml_requests.png)

### <a name="irule-based-rule"></a>Règle basée sur les IRule
Utilisez `Add(IRule)` pour implémenter votre propre logique de règle dans une classe qui dérive de `IRule`. À l’aide un `IRule` offre davantage de flexibilité principal à l’aide de l’approche d’une règle basée sur une méthode. Votre classe dérivée peut inclure un constructeur, où vous pouvez passer des paramètres pour le `ApplyRule` (méthode).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/Program.cs?name=snippet1&highlight=10-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/Startup.cs?name=snippet1&highlight=7-8)]

---

Les valeurs des paramètres dans l’exemple d’application pour le `extension` et `newPath` sont vérifiées afin de répondre à plusieurs conditions. Le `extension` doit contenir une valeur, et la valeur doit être *.png*, *.jpg*, ou *.gif*. Si le `newPath` n’est pas valide, un `ArgumentException` est levée. Si vous faites une demande *image.png*, il est redirigé vers `/png-images/image.png`. Si vous faites une demande *image.jpg*, il est redirigé vers `/jpg-images/image.jpg`. Le code d’état a la valeur 301 (déplacé définitivement) et le `context.Result` est définie pour arrêter les règles de traitement et d’envoyer la réponse.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](url-rewriting/samples/2.x/RewriteRules.cs?name=snippet2)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](url-rewriting/samples/1.x/RewriteRule.cs?name=snippet1)]

---

Demande d’origine :`/image.png`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses pour image.png de suivi](url-rewriting/_static/add_redirect_png_requests.png)

Demande d’origine :`/image.jpg`

![Fenêtre de navigateur avec des outils de développement les demandes et réponses pour image.jpg de suivi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>Exemples d’expressions régulières

| Goal | Chaîne d’expression régulière &<br>Exemple de correspondance | Chaîne de remplacement &<br>Exemple de sortie |
| ---- | :-----------------------------: | :------------------------------------: |
| Chemin d’accès de réécriture dans la chaîne de requête | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| Barre oblique de fin de la frange | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| Appliquer la barre oblique de fin | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| Éviter la réécriture des demandes spécifiques | `(.*[^(\.axd)])$`<br>Oui :`/resource.htm`<br>No :`/resource.axd` | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| Réorganiser les segments d’URL | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| Remplacer un segment d’URL | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

## <a name="additional-resources"></a>Ressources supplémentaires
* [Démarrage d’une application](startup.md)
* [Intergiciel (middleware)](middleware.md)
* [Expressions régulières dans .NET](/dotnet/articles/standard/base-types/regular-expressions)
* [Langage des expressions régulières - Aide-mémoire](/dotnet/articles/standard/base-types/quick-ref)
* [Mod_rewrite d’Apache](https://httpd.apache.org/docs/2.4/rewrite/)
* [À l’aide du Module de réécriture d’Url 2.0 (pour IIS)](https://docs.microsoft.com/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [Référence de Configuration de Module de réécriture d’URL](https://docs.microsoft.com/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [Forum de Module de réécriture de URL IIS](https://forums.iis.net/1152.aspx)
* [Conserver une structure simple de l’URL](https://support.google.com/webmasters/answer/76329?hl=en)
* [Réécriture d’URL 10 conseils et astuces](http://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [Barre oblique ou ne pas en barre oblique](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
