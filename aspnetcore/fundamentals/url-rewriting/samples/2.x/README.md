# <a name="aspnet-core-url-rewriting-sample-aspnet-core-2x"></a>ASP.NET Core réécriture d’URL exemple (ASP.NET Core 2.x)

Cet exemple illustre l’utilisation d’ASP.NET Core 2.x intergiciel (middleware) réécriture d’URL. L’application montre la redirection d’URL et les options de réécriture d’URL. Pour l’exemple 1.x ASP.NET Core, consultez [exemple réécriture d’URL de base ASP.NET (ASP.NET Core 1.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/1.x).

Lorsque vous exécutez l’exemple, une réponse sera pris en charge qui affiche l’URL réécrite ou redirigée quand l’une des règles est appliquée à une URL de demande.

## <a name="examples-in-this-sample"></a>Exemples de cet exemple

* `AddRedirect("redirect-rule/(.*)", "$1")`
  - Code d’état de réussite : 302 (trouvé)
  - Exemple (redirection) : **/redirect-rule / {capture_group}** à **/redirected/ {capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Code d’état de réussite : 200 (OK)
  - Exemple (réécriture) : **/rewrite-rule / {capture_group_1} / {capture_group_2}** à **/ réécrit ? var1 = {capture_group_1} & var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Code d’état de réussite : 302 (trouvé)
  - Exemple (redirection) : **/apache-mod-rules-redirect / {capture_group}** à **/ redirigé ? id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Code d’état de réussite : 200 (OK)
  - Exemple (réécriture) : **/iis-rules-rewrite / {capture_group}** à **/ réécrit ? id = {capture_group}**
* `Add(RedirectXMLRequests)`
  - Code d’état de réussite : 301 (déplacé définitivement)
  - Exemple (redirection) : **/file.xml** à **/xmlfiles/file.xml**
* `Add(new RedirectPNGRequests(".png", "/png-images")))`<br>`Add(new RedirectPNGRequests(".jpg", "/jpg-images")))`
  - Code d’état de réussite : 301 (déplacé définitivement)
  - Exemple (redirection) : **/image.png** à **/png-images/image.png**
  - Exemple (redirection) : **/image.jpg** à **/jpg-images/image.jpg**

## <a name="using-a-physicalfileprovider"></a>À l’aide un`PhysicalFileProvider`
Vous pouvez également obtenir un `IFileProvider` en créant un `PhysicalFileProvider` à passer à la `AddApacheModRewrite()` et `AddIISUrlRewrite()` méthodes :
```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```
## <a name="secure-redirection-extensions"></a>Extensions de redirection sécurisée
Cet exemple inclut `WebHostBuilder` configuration de l’application d’utiliser des URL (**https://localhost:5001**, **https://localhost**) et un certificat de test (**testCert.pfx**) pour vous aider vous Explorer ces méthodes de redirection. Ajouter un d'entre eux à la `RewriteOptions()` dans **Startup.cs** pour étudier leur comportement.

Méthode | Code d’état | Port
--- | :---: | :---:
`.AddRedirectToHttpsPermanent()` | 301 | null (465)
`.AddRedirectToHttps()` | 302 | null (465)
`.AddRedirectToHttps(301)` | 301 | null (465)
`.AddRedirectToHttps(301, 5001)` | 301 | 5001
