# <a name="aspnet-core-response-caching-sample"></a>Exemple de mise en cache de réponse ASP.NET Core

Cet exemple illustre l’utilisation de ASP.NET Core [intergiciel (middleware) de réponse mise en cache](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

L’application répond avec sa page d’Index, y compris un `Cache-Control` en-tête pour configurer le comportement de mise en cache. L’application définit également la `Vary` en-tête pour configurer le cache pour prendre en charge la réponse uniquement si le `Accept-Encoding` en-tête des demandes ultérieures correspond à celle de la demande d’origine.

Lorsque vous exécutez l’exemple, la page d’Index est pris en charge à partir du cache lorsque stocké et mis en cache pendant 10 secondes.
