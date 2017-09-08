# <a name="custom-model-binding-demo"></a>Démo de liaison de modèle personnalisé

Vous pouvez tester le `ByteArrayModelBinder` en exécutant l’application et de validation d’une chaîne codée en base64 au point de terminaison ImageController (/ api/images /). Vous devez spécifier le nom et un fichier proparties dans la demande de corps sous forme de données (à l’aide de Postman ou un outil similaire). Vous pouvez utiliser [cet exemple de chaîne](Base64String.txt). Le résultat sera enregistré dans le dossier wwwroot/images/téléchargement avec le nom de fichier que vous avez spécifié.

Pour tester l’exemple de liaison personnalisée, essayez les points de terminaison suivants : /api/authors/1 /api/authors/2 (introuvable) /api/boundauthors/1 /api/boundauthors/2 (introuvable) /api/boundauthors/get/2 /api/boundauthors/get/1 (aucun contenu) - cette action ne vérifie pas valeur null, retournent un introuvable
