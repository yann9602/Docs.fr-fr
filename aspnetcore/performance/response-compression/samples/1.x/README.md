# <a name="response-compression-sample-application-aspnet-core-1x"></a>Application d’exemple de compression de réponse (ASP.NET Core 1.x)

Cet exemple illustre l’utilisation de ASP.NET Core 1.x intergiciel (middleware) de réponse Compression pour compresser les réponses HTTP pour. L’exemple illustre Gzip et fournisseurs de compression personnalisé pour les réponses de type text et image et montre comment ajouter un type MIME pour la compression. Pour l’exemple 2.x ASP.NET Core, consultez [réponse compression exemple d’application (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Exemples de cet exemple
* `GzipCompressionProvider`
  * `text/plain`
    * **/**-Réponse de fichier texte Lorem Ipsum à 2,044 octets qui compresse 927 octets
    * **/testfile1kb.txt** -réponse de fichier texte à 1,033 octets qui compresse octets 47
    * **/ segmentée** -réponse émise en tant que caractères uniques à des intervalles de 1 seconde 
  * `image/svg+xml`
    * **/Banner.SVG** -réponse d’image un graphique SVG (Scalable Vector) à des octets 9,707 compresse 4,459 octets
* `CustomCompressionProvider`<br>Montre comment implémenter un fournisseur personnalisé de la compression pour une utilisation avec l’intergiciel (middleware)

Lorsque la demande inclut le `Accept-Encoding` en-tête, l’exemple ajoute un `Vary: Accept-Encoding` en-tête dans la réponse. Le `Vary` en-tête fait en sorte que les caches maintenir plusieurs copies de la réponse en fonction des valeurs de remplacement de `Accept-Encoding`, donc une compressé (gzip) et une version non compressée sont stockés en mémoire cache pour les systèmes qui peuvent accepter le fichier compressé ou réponse non compressée.

## <a name="using-the-sample"></a>Utilisation de l'exemple
1. Effectuer une demande à l’aide de [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), ou [Postman](https://www.getpostman.com/) à l’application sans un `Accept-Encoding` en-tête et notez la charge utile de réponse, la taille de la réponse, et en-têtes de réponse.
2. Ajouter un `Accept-Encoding: gzip` en-tête et notez la taille de la réponse compressée et les en-têtes de réponse. Vous voyez la taille de la réponse pour supprimer et le `Content-Encoding: gzip` en-tête de réponse est inclus par l’exemple d’application. Lorsque vous examinez le corps de réponse pour la Lorem Ipsum ou **testfile1kb.txt** réponse, vous voyez que le texte est compressé et illisible.
3. Ajouter un `Accept-Encoding: mycustomcompression` en-tête et notez les en-têtes de réponse. Le `CustomCompressionProvider` est une implémentation vide qui ne compresser réellement la réponse, mais vous pouvez créer un wrapper de flux de compression personnalisé pour le `CreateStream()` (méthode).
