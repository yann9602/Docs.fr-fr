# <a name="aspnet-core-response-caching-sample-aspnet-core-1x"></a>Réponse ASP.NET Core exemple de mise en cache (ASP.NET Core 1.x)

Cet exemple illustre l’utilisation de ASP.NET Core [intergiciel (middleware) de réponse mise en cache](xref:performance/caching/middleware) avec ASP.NET Core 1.x. Pour l’exemple 2.x ASP.NET Core, consultez [exemple de mise en cache de réponse ASP.NET Core (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples/2.x).

L’application envoie un `Hello World!` message et l’heure actuelle avec un `Cache-Control` en-tête pour configurer le comportement de mise en cache. L’application envoie également un `Vary` en-tête pour configurer le cache pour prendre en charge la réponse uniquement si le `Accept-Encoding` en-tête des demandes ultérieures correspond à celle de la demande d’origine.

Lorsque vous exécutez l’exemple, une réponse est pris en charge à partir du cache si possible, stockées pendant 10 secondes.
