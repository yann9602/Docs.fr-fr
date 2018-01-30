L’élément `<form method="post">` est un [Tag Helper de formulaire](xref:mvc/views/working-with-forms#the-form-tag-helper). Le Tag Helper de formulaire inclut automatiquement un [jeton de protection contre les falsifications](xref:security/anti-request-forgery).

Le moteur de génération de modèles automatique crée le code Razor pour chaque champ du modèle (sauf l’ID) de la manière suivante :

[!code-cshtml[Main](../../tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

Les [Tag Helpers de validation](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` et ` <span asp-validation-for`) affichent des erreurs de validation. La validation est traitée de manière plus détaillée plus loin dans cette série.

Le [Tag Helper d’étiquette](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) génère la légende de l’étiquette et l’attribut `for` pour la propriété `Title`.

Le [Tag Helper d’entrée](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control" />`) utilise les attributs [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) et produit les attributs HTML nécessaires à la validation jQuery côté client.