---
title: "Intergiciel (middleware) de réponse Compression pour ASP.NET Core"
author: guardrex
description: "En savoir plus sur la compression de la réponse et comment utiliser un intergiciel (middleware) de réponse Compression dans les applications ASP.NET Core."
keywords: "ASP.NET Core, performances, la compression de la réponse, gzip, encodage, intergiciel (middleware)"
ms.author: riande
manager: wpickett
ms.date: 08/20/2017
ms.topic: article
ms.assetid: de621887-c5c9-4ac8-9efd-f5cc0457a134
ms.technology: aspnet
ms.prod: asp.net-core
uid: performance/response-compression
ms.openlocfilehash: 7aea4db44764d5d8f47520adb6599e651e0e9000
ms.sourcegitcommit: 732cd2684246e49e796836596643a8d37e20c46d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2017
---
# <a name="response-compression-middleware-for-aspnet-core"></a>Intergiciel (middleware) de réponse Compression pour ASP.NET Core

Par [Luke Latham](https://github.com/guardrex)

[Afficher ou télécharger l’exemple de code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples) ([comment télécharger](xref:tutorials/index#how-to-download-a-sample))

La bande passante réseau est une ressource limitée. Réduire la taille de la réponse de généralement d’augmente souvent considérablement la réactivité d’une application. Une pour réduire la taille de la charge utile consiste à compresser les réponses de l’application.

## <a name="when-to-use-response-compression-middleware"></a>Quand utiliser l’intergiciel (middleware) de réponse Compression
Utiliser les technologies de compression de la réponse basée sur le serveur dans IIS, Apache ou Nginx où les performances de l’intergiciel (middleware) probablement ne sera pas correspondre à celui des modules de serveur. Utilisez l’intergiciel (middleware) de réponse Compression lorsque vous ne pouvez pas utiliser :
* [Module de la Compression dynamique IIS](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
* [Module de mod_deflate Apache](http://httpd.apache.org/docs/current/mod/mod_deflate.html)
* [La décompression et NGINX Compression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* [Serveur HTTP.sys](xref:fundamentals/servers/httpsys) (anciennement appelé [WebListener](xref:fundamentals/servers/weblistener))
* [Kestrel](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Compression de la réponse
En règle générale, toute réponse ne compressée pas en mode natif peut bénéficier de la compression de la réponse. Pas en mode natif compressées en général, les réponses sont : CSS, JavaScript, HTML, XML et JSON. Vous ne doivent pas compresser les actifs compressés en mode natif, telles que des fichiers PNG. Si vous tentez de davantage de compresser une réponse compressée en mode natif, toute petite réduction supplémentaire dans le temps de taille et de transmission sera probablement être éclipsée par le temps de traitement de la compression. Ne pas compresser les fichiers inférieurs à environ 150-1000 octets (selon le contenu du fichier et l’efficacité de compression). La surcharge de la compression des petits fichiers peut produire un fichier compressé plus volumineux que le fichier non compressé.

Lorsqu’un client peut traiter le contenu compressé, le client informe le serveur de ses capacités en envoyant la `Accept-Encoding` en-tête avec la demande. Lorsqu’un serveur envoie un contenu compressé, il doit inclure les informations contenues dans le `Content-Encoding` en-tête sur la façon dont la réponse compressée est encodée. Contenu désignations de codage pris en charge par l’intergiciel (middleware) sont affichées dans le tableau suivant.

| `Accept-Encoding`valeurs d’en-tête | Intergiciel (middleware) pris en charge | Description                                                 |
| :-----------------------------: | :------------------: | ----------------------------------------------------------- |
| `br`                            | Non                   | Format de données compressées Brotli                               |
| `compress`                      | Non                   | Format de données « compresser » UNIX                                 |
| `deflate`                       | Non                   | les données compressées dans le format de données « zlib » « deflate »     |
| `exi`                           | Non                   | Échange efficace de XML de W3C                               |
| `gzip`                          | Oui (valeur par défaut)        | format de fichier GZIP                                            |
| `identity`                      | Oui                  | Identificateur de « Aucun codage » : la réponse ne doit pas être encodée. |
| `pack200-gzip`                  | Non                   | Format de transfert réseau pour les Archives de Java                   |
| `*`                             | Oui                  | Aucun contenu disponible n'encodage pas explicitement demandée     |

Pour plus d’informations, consultez la [IANA officiel de codage liste contenu](http://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

L’intergiciel (middleware) vous permet d’ajouter des fournisseurs de compression supplémentaire personnalisée `Accept-Encoding` les valeurs d’en-tête. Pour plus d’informations, consultez [fournisseurs personnalisés](#custom-providers) ci-dessous.

L’intergiciel (middleware) est capable de réagir à la valeur de qualité (qvalue, `q`) lors de l’envoi par le client afin de hiérarchiser les schémas de compression de pondération. Pour plus d’informations, consultez [7231 relative aux RFC : encodage](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Algorithmes de compression sont soumis à un compromis entre la vitesse de la compression et l’efficacité de la compression. *Efficacité* dans ce contexte fait référence à la taille de la sortie après la compression. La plus petite taille est obtenue par le meilleur *optimale* la compression.

Les en-têtes impliquées dans la demande, envoi, la mise en cache et la réception de contenu compressé sont décrits dans le tableau ci-dessous.

| Header             | Rôle |
| ------------------ | ---- |
| `Accept-Encoding`  | Envoyé à partir du client au serveur pour indiquer le contenu de codage des schémas acceptables pour le client. |
| `Content-Encoding` | Envoyé à partir du serveur au client pour indiquer le codage du contenu de la charge utile. |
| `Content-Length`   | Lors de la compression, le `Content-Length` en-tête est supprimé, depuis les modifications de contenu du corps lors de la compression de la réponse. |
| `Content-MD5`      | Lors de la compression, le `Content-MD5` en-tête est supprimé, car le contenu du corps a changé et le hachage n’est plus valide. |
| `Content-Type`     | Spécifie le type MIME du contenu. Chaque réponse doit spécifier son `Content-Type`. L’intergiciel (middleware) vérifie cette valeur pour déterminer si la réponse doit être compressée. L’intergiciel (middleware) spécifie un jeu de [par défaut des types MIME](#mime-types) il peut encoder, mais vous pouvez remplacer ou ajouter des types MIME. |
| `Vary`             | Lors de l’envoi par le serveur avec la valeur `Accept-Encoding` aux clients et serveurs proxy, le `Vary` en-tête indique au client ou au proxy qu’il doit mettre en cache (différentes) réponses en fonction de la valeur de la `Accept-Encoding` en-tête de la demande. Le résultat de contenu avec le `Vary: Accept-Encoding` en-tête est que les deux compressées et non compressées réponses sont mis en cache séparément. |

Vous pouvez explorer les fonctionnalités de l’intergiciel de compression de la réponse avec la [exemple d’application](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples). L’exemple illustre :
* La compression des réponses d’application à l’aide de gzip et fournisseurs de compression personnalisé.
* Comment ajouter un type MIME à la liste par défaut des types MIME pour la compression.

## <a name="package"></a>Package
Pour inclure l’intergiciel (middleware) dans votre projet, ajoutez une référence à la [ `Microsoft.AspNetCore.ResponseCompression` ](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) du package ou utilisez le [ `Microsoft.AspNetCore.All` ](https://www.nuget.org/packages/Microsoft.AspNetCore.All/) package. Cette fonctionnalité est disponible pour les applications qui ciblent ASP.NET Core 1.1 ou version ultérieure.

## <a name="configuration"></a>Configuration
Le code suivant illustre l’activation de l’intergiciel de compression de la réponse avec l’avec la compression gzip par défaut et pour les types MIME par défaut.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/StartupBasic.cs?name=snippet1&highlight=4,8)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/StartupBasic.cs?name=snippet1&highlight=3,8)]

---

> [!NOTE]
> Utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/) pour définir le `Accept-Encoding` en-tête de demande et d’étudier les en-têtes de réponse, la taille et le corps.

Soumettre une demande pour l’exemple d’application sans la `Accept-Encoding` en-tête et observez que la réponse n’est pas compressée. Le `Content-Encoding` et `Vary` en-têtes ne sont pas présents sur la réponse.

![Fenêtre Fiddler indiquant le résultat d’une demande sans l’en-tête Accept-Encoding. La réponse n’est pas compressée.](response-compression/_static/request-uncompressed.png)

Soumettre une demande pour l’exemple d’application avec le `Accept-Encoding: gzip` en-tête et observez que la réponse est compressée. Le `Content-Encoding` et `Vary` en-têtes sont présents sur la réponse.

![Fenêtre Fiddler affichant le résultat d’une demande avec l’en-tête Accept-Encoding et la valeur gzip. Les en-têtes Content-Encoding et de variable sont ajoutés à la réponse. La réponse est compressée.](response-compression/_static/request-compressed.png)

## <a name="providers"></a>Fournisseurs
### <a name="gzipcompressionprovider"></a>GzipCompressionProvider
Utilisez le `GzipCompressionProvider` pour compresser les réponses avec gzip. Il s’agit du fournisseur de compression par défaut si aucune n’est spécifiée. Vous pouvez définir la compression au niveau de la `GzipCompressionProviderOptions`. 

Le fournisseur de la compression gzip par défaut est le niveau de compression plus rapide (`CompressionLevel.Fastest`), ce qui ne peut pas produire la compression la plus efficace. Si la compression la plus efficace est souhaitée, vous pouvez configurer l’intergiciel (middleware) de compression optimale.

| Niveau de compression                | Description                                                                                                   |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `CompressionLevel.Fastest`       | La compression doit se terminer aussi rapidement que possible, même si le résultat n’est pas compressé de façon optimale. |
| `CompressionLevel.NoCompression` | Aucune compression ne doit être effectuée.                                                                           |
| `CompressionLevel.Optimal`       | Les réponses doivent être compressées optimale, même si la compression prend plus de temps pour terminer.                |


# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=3,8-11)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5,10-13)]

---

## <a name="mime-types"></a>types MIME
L’intergiciel (middleware) spécifie un ensemble par défaut des types MIME pour la compression :
* `text/plain`
* `text/css`
* `application/javascript`
* `text/html`
* `application/xml`
* `text/xml`
* `application/json`
* `text/json`

Vous pouvez remplacer ou ajouter des types MIME avec les options de l’intergiciel de compression de la réponse. Notez que les caractères génériques MIME types, tels que `text/*` ne sont pas pris en charge. L’exemple d’application ajoute un type MIME pour `image/svg+xml` et compresse et sert les ASP.NET Core image de bannière (*banner.svg*).

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=5)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7)]

---

### <a name="custom-providers"></a>Fournisseurs personnalisés
Vous pouvez créer des implémentations personnalisées de la compression avec `ICompressionProvider`. Le `EncodingName` représente le contenu que cet encodage `ICompressionProvider` produit. L’intergiciel (middleware) utilise ces informations pour choisir le fournisseur en fonction de la liste spécifiée dans le `Accept-Encoding` en-tête de la demande.

À l’aide de l’exemple d’application, le client envoie une demande avec le `Accept-Encoding: mycustomcompression` en-tête. L’intergiciel (middleware) utilise l’implémentation personnalisée de la compression et retourne la réponse avec un `Content-Encoding: mycustomcompression` en-tête. Le client doit être en mesure de décompresser l’encodage personnalisé dans l’ordre pour une implémentation personnalisée de la compression fonctionne.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

[!code-csharp[Main](response-compression/samples/2.x/Program.cs?name=snippet1&highlight=4)]

[!code-csharp[Main](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[Main](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

---

Soumettre une demande pour l’exemple d’application avec le `Accept-Encoding: mycustomcompression` en-tête et observez les en-têtes de réponse. Le `Vary` et `Content-Encoding` en-têtes sont présents sur la réponse. Le corps de réponse (non affiché) n’est pas compressé par l’exemple. Il n’est pas une implémentation de la compression dans le `CustomCompressionProvider` classe de l’exemple. Toutefois, l’exemple montre où vous pouvez implémenter un algorithme de compression de ce type.

![Fenêtre Fiddler affichant le résultat d’une demande avec l’en-tête Accept-Encoding et la valeur mycustomcompression. Les en-têtes Content-Encoding et de variable sont ajoutés à la réponse.](response-compression/_static/request-custom-compression.png)

## <a name="compression-with-secure-protocol"></a>Compression avec un protocole sécurisé
Les réponses compressées via des connexions sécurisées peuvent être contrôlées par le `EnableForHttps` option, qui est désactivée par défaut. À l’aide de la compression des pages générées dynamiquement permettre entraîner des problèmes de sécurité telles que la [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) et [violation](https://wikipedia.org/wiki/BREACH_(security_exploit)) attaques.

## <a name="adding-the-vary-header"></a>Ajout de l’en-tête Vary
Si la compression des réponses basée sur la `Accept-Encoding` en-tête, il existe potentiellement plusieurs versions compressées de la réponse et une version non compressée. Afin d’indiquer les caches de client et le proxy que plusieurs versions existent et doivent être stockées, les `Vary` en-tête est ajouté avec un `Accept-Encoding` valeur. Dans ASP.NET Core 1.x, ajout de la `Vary` en-tête dans la réponse s’effectue manuellement. Dans ASP.NET Core ajoute de l’intergiciel (middleware) 2.x, le `Vary` en-tête automatiquement lors de la réponse est compressée.

**ASP.NET Core 1.x uniquement**

[!code-csharp[Main](response-compression/samples/1.x/Startup.cs?name=snippet1)]

## <a name="middlware-issue-when-behind-an-nginx-reverse-proxy"></a>Problème Middlware lorsque derrière un proxy inverse Nginx
Lorsqu’une demande est transmise par Nginx, le `Accept-Encoding` en-tête est supprimé. Cela empêche l’intergiciel (middleware) de la compression de la réponse. Pour plus d’informations, consultez [NGINX : la Compression et décompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Ce problème est suivi par [Figure exclut la compression de pass-through pour nginx (BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Utilisation de la compression dynamique IIS
Si vous avez un IIS dynamique Compression Module actif configuré au niveau du serveur que vous voulez désactiver pour une application, vous pouvez le faire avec un ajout à votre *web.config* fichier. Pour plus d’informations, consultez [modules IIS de la désactivation de](xref:hosting/iis-modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Résolution des problèmes
Utiliser un outil tel que [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/), qui permettent de définir la `Accept-Encoding` en-tête de demande et d’étudier les en-têtes de réponse, la taille et le corps. L’intergiciel de compression de la réponse compresse les réponses qui remplissent les conditions suivantes :
* Le `Accept-Encoding` en-tête est présent avec la valeur `gzip`, `*`, ou l’encodage personnalisé qui correspond à un fournisseur de compression personnalisé que vous avez établi. La valeur ne doit pas être `identity` ou avoir une valeur de qualité (qvalue, `q`) définissant égale à 0 (zéro).
* Le type MIME (`Content-Type`) doit être défini et doit correspondre à un type MIME configuré sur le `ResponseCompressionOptions`.
* La demande ne doit pas inclure le `Content-Range` en-tête.
* La demande doit utiliser un protocole non sécurisé (http), sauf si un protocole sécurisé (https) est configuré dans les options de l’intergiciel de compression de la réponse. *Notez le danger [décrite ci-dessus](#compression-with-secure-protocol) lors de l’activation de la compression du contenu sécurisée.*

## <a name="additional-resources"></a>Ressources supplémentaires
* [Démarrage d’une application](xref:fundamentals/startup)
* [Intergiciel (middleware)](xref:fundamentals/middleware)
* [Mozilla MSDN : Accepter-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [La Section 7231 relative aux RFC 3.1.2.1 : Codings de contenu](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [La Section 7230 RFC 4.2.3 : Codage Gzip](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Version de spécification du format fichier GZIP 4.3](http://www.ietf.org/rfc/rfc1952.txt)
