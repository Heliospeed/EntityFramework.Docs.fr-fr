---
title: EF Core références relatives aux outils (Console du Gestionnaire de Package) - EF Core
author: bricelam
ms.author: bricelam
ms.date: 09/18/2018
uid: core/miscellaneous/cli/powershell
ms.openlocfilehash: 468698d1bbd17d4ad10b1b1601bfbc315a01c1ff
ms.sourcegitcommit: b3c2b34d5f006ee3b41d6668f16fe7dcad1b4317
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51688704"
---
# <a name="entity-framework-core-tools-reference---package-manager-console-in-visual-studio"></a>Référence - Console du Gestionnaire de Package dans Visual Studio des outils Entity Framework Core

Les outils de la Console de gestionnaire de Package (PMC) pour Entity Framework Core effectuer des tâches de développement au moment du design. Par exemple, ils créent [migrations](/aspnet/core/data/ef-mvc/migrations?view=aspnetcore-2.0#introduction-to-migrations), appliquer des migrations et générer du code pour un modèle basé sur une base de données existante. Les commandes s’exécutent à l’intérieur de Visual Studio en utilisant le [Console du Gestionnaire de Package](/nuget/tools/package-manager-console). Ces outils fonctionnent avec les projets .NET Framework et .NET Core.

Si vous n’utilisez pas Visual Studio, nous vous recommandons du [outils de ligne de commande de EF Core](dotnet.md) à la place. Les outils CLI sont multiplateformes et d’exécution à l’intérieur d’une invite de commandes.

## <a name="installing-the-tools"></a>Installation des outils

Les procédures d’installation et de la mise à jour les outils diffèrent entre ASP.NET Core 2.1 + et les versions antérieures ou autres types de projets.

### <a name="aspnet-core-version-21-and-later"></a>ASP.NET Core 2.1 et versions ultérieures

Les outils sont automatiquement inclus dans un projet ASP.NET Core 2.1 +, car le `Microsoft.EntityFrameworkCore.Tools` package est inclus dans le [Microsoft.AspNetCore.App métapackage](/aspnet/core/fundamentals/metapackage-app).

Par conséquent, vous n’avez rien à faire pour installer les outils, mais vous avez à :
* Restaurer les packages avant d’utiliser les outils dans un nouveau projet.
* Installer un package pour mettre à jour les outils vers une version plus récente.

Pour vous assurer que vous obtenez la dernière version des outils, nous vous recommandons d’également effectuer l’étape suivante :

* Modifier votre *.csproj* fichier, puis ajoutez une ligne spécifiant la dernière version de la [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/) package. Par exemple, le *.csproj* fichier peut inclure un `ItemGroup` qui ressemble à ceci :

  ```xml
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.1.3" />
    <PackageReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Design" Version="2.1.1" />
  </ItemGroup>
  ```

Mettre à jour les outils lorsque vous recevez un message similaire à l’exemple suivant :

> La version des outils EF Core « 2.1.1-rtm-30846 » est antérieure à celle du runtime « 2.1.3-rtm-32065 ». Mettre à jour les outils pour les dernières fonctionnalités et correctifs de bogues.

Pour mettre à jour les outils :
* Installez la dernière version du .NET Core SDK.
* Mettre à jour de Visual Studio vers la dernière version.
* Modifier le *.csproj* fichier afin qu’il inclue une référence de package pour le dernier package d’outils, comme indiqué précédemment.

### <a name="other-versions-and-project-types"></a>Autres versions et les types de projets

Installer les outils de la Console du Gestionnaire de Package en exécutant la commande suivante **Console du Gestionnaire de Package**:

``` powershell
Install-Package Microsoft.EntityFrameworkCore.Tools
```

Mettre à jour les outils en exécutant la commande suivante dans **Console du Gestionnaire de Package**.

``` powershell
Update-Package Microsoft.EntityFrameworkCore.Tools
```

### <a name="verify-the-installation"></a>Vérifier l’installation

Vérifiez que les outils sont installés en exécutant cette commande :

``` powershell
Get-Help about_EntityFrameworkCore
```

La sortie ressemble à ceci (il n’indique pas quelle version des outils que vous utilisez) :

```console

                     _/\__
               ---==/    \\
         ___  ___   |.    \|\
        | __|| __|  |  )   \\\
        | _| | _|   \_/ |  //|\\
        |___||_|       /   \\\/\\

TOPIC
    about_EntityFrameworkCore

SHORT DESCRIPTION
    Provides information about the Entity Framework Core Package Manager Console Tools.

<A list of available commands follows, omitted here.>
```

## <a name="using-the-tools"></a>L’utilisation des outils

Avant d’utiliser les outils :
* Comprendre la différence entre le projet de démarrage et cible.
* Découvrez comment utiliser les outils avec des bibliothèques de classes .NET Standard.
* Pour les projets ASP.NET Core, définissez l’environnement.

### <a name="target-and-startup-project"></a>Projet de démarrage et cible

Les commandes font référence à un *projet* et un *projet de démarrage*.

* Le *projet* est également connu sous le *projet cible* , car il est où les commandes ajoutent ou supprimer des fichiers. Par défaut, le **projet par défaut** sélectionné dans **Console du Gestionnaire de Package** est le projet cible. Vous pouvez spécifier un autre projet comme projet cible à l’aide de la <nobr> `--project` </nobr> option.

* Le *projet de démarrage* est celui que les outils de générer et exécuter. Les outils ont exécuter du code de l’application au moment du design pour obtenir des informations sur le projet, telles que la chaîne de connexion de base de données et la configuration du modèle. Par défaut, le **projet de démarrage** dans **l’Explorateur de solutions** est le projet de démarrage. Vous pouvez spécifier un autre projet comme projet de démarrage à l’aide de la <nobr> `--startup-project` </nobr> option.

Le projet de démarrage et le projet cible sont souvent le même projet. Un scénario classique dans lequel ils sont des projets distincts est cas suivants :

* Les classes d’entité et de contexte EF Core sont dans une bibliothèque de classes .NET Core.
* Une application console .NET Core ou une application web fait référence à la bibliothèque de classes.

Il est également possible de [mettre le code de migrations dans une bibliothèque de classes distincte à partir du contexte EF Core](xref:core/managing-schemas/migrations/projects).

### <a name="other-target-frameworks"></a>Autres frameworks cibles

Les outils de la Console du Gestionnaire de Package fonctionnent avec des projets .NET Core ou .NET Framework. Les applications qui ont le modèle EF Core dans une bibliothèque de classes .NET Standard peut-être pas un projet de .NET Framework ou le .NET Core. Par exemple, cela est vrai pour les applications Xamarin et de la plateforme Windows universelle. Dans ce cas, vous pouvez créer un projet d’application console .NET Core ou .NET Framework dont le seul but est d’agir en tant que projet de démarrage pour les outils. Le projet peut être un projet factice sans code réel &mdash; il est uniquement nécessaire pour fournir une cible pour les outils.

Pourquoi est-un projet factice requis ? Comme mentionné précédemment, les outils ont exécuter du code de l’application au moment du design. Pour ce faire, ils doivent utiliser le runtime .NET Core ou .NET Framework. Lorsque le modèle EF Core est dans un projet qui cible .NET Core ou .NET Framework, les outils EF Core emprunt le runtime à partir du projet. Ils ne le sauront pas si le modèle EF Core se trouve dans une bibliothèque de classes .NET Standard. .NET Standard n’est pas une implémentation réelle de .NET ; Il est une spécification d’un ensemble d’API implémentations .NET doivent prendre en charge. Par conséquent, .NET Standard n’est pas suffisant pour les outils EF Core exécuter du code d’application. Le projet factice que vous créez à utiliser en tant que projet de démarrage fournit une plateforme cible concrète dans lequel les outils peuvent charger la bibliothèque de classes .NET Standard.

### <a name="aspnet-core-environment"></a>Environnement ASP.NET Core

Pour spécifier l’environnement pour les projets ASP.NET Core, définissez **env:ASPNETCORE_ENVIRONMENT** avant d’exécuter des commandes.

## <a name="common-parameters"></a>Paramètres communs

Le tableau suivant présente les paramètres qui sont communes à toutes les commandes EF Core :

| Paramètre                 | Description                                                                                                                                                                                                          |
|:--------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -Contexte \<chaîne >        | Le `DbContext` classe à utiliser. Nom de classe complet avec des espaces de noms ou uniquement.  Si ce paramètre est omis, EF Core recherche la classe de contexte. S’il existe plusieurs classes de contexte, ce paramètre est obligatoire. |
| -Projet \<chaîne >        | Le projet cible. Si ce paramètre est omis, le **projet par défaut** pour **Console du Gestionnaire de Package** est utilisé en tant que le projet cible.                                                                             |
| -StartupProject \<chaîne > | Le projet de démarrage. Si ce paramètre est omis, le **projet de démarrage** dans **propriétés de la Solution** est utilisé en tant que le projet cible.                                                                                 |
| -Verbose                  | Afficher la sortie détaillée.                                                                                                                                                                                                 |

Pour afficher les informations d’aide sur une commande, utilisez PowerShell `Get-Help` commande.

> [!TIP]
> Les paramètres de contexte, le projet et StartupProject prend en charge d’extension de l’onglet.

## <a name="add-migration"></a>Add-Migration

Ajoute une nouvelle migration.

Paramètres :

| Paramètre                         | Description                                                                                                             |
|:----------------------------------|:------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Name \<chaîne ><nobr>       | Le nom de la migration. Ceci est un paramètre positionnel et est nécessaire.                                              |
| <nobr>-OutputDir \<chaîne ></nobr> | Le répertoire (et espace de noms secondaire) à utiliser. Chemins d’accès sont relatifs au répertoire de projet cible. La valeur par défaut est « Migrations ». |

## <a name="drop-database"></a>DROP Database

Supprime la base de données.

Paramètres :

| Paramètre | Description                                              |
|:----------|:---------------------------------------------------------|
| -WhatIf   | Afficher la base de données serait supprimée, mais ne la supprimez. |

## <a name="get-dbcontext"></a>Get-DbContext

Listes disponibles `DbContext` types.

## <a name="remove-migration"></a>Remove-Migration

Supprime la dernière migration (annule les modifications de code qui ont été effectuées pour la migration).

Paramètres :

| Paramètre | Description                                                                     |
|:----------|:--------------------------------------------------------------------------------|
| -Force    | Rétablir la migration (annuler les modifications qui ont été appliquées à la base de données). |

## <a name="scaffold-dbcontext"></a>Scaffold-DbContext

Génère du code pour un `DbContext` et types d’entité pour une base de données. Dans l’ordre pour `Scaffold-DbContext` pour générer un type d’entité, la table de base de données doit avoir une clé primaire.

Paramètres :

| Paramètre                          | Description                                                                                                                                                                                                                                                             |
|:-----------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>-Connexion \<chaîne ></nobr> | La chaîne de connexion à la base de données. Pour les projets ASP.NET Core 2.x, la valeur peut être *nom =\<nom de chaîne de connexion >*. Dans ce cas, le nom est fourni à partir des sources de configuration qui sont configurées pour le projet. Ceci est un paramètre positionnel et est nécessaire. |
| <nobr>-Fournisseur \<chaîne ></nobr>   | Le fournisseur à utiliser. En général, c’est le nom du package NuGet, par exemple : `Microsoft.EntityFrameworkCore.SqlServer`. Ceci est un paramètre positionnel et est nécessaire.                                                                                           |
| -OutputDir \<chaîne >               | Répertoire à placer les fichiers dans. Chemins d’accès sont relatif au répertoire de projet.                                                                                                                                                                                             |
| -ContextDir \<chaîne >              | Le répertoire de placer le `DbContext` de fichiers dans. Chemins d’accès sont relatif au répertoire de projet.                                                                                                                                                                              |
| -Contexte \<chaîne >                 | Le nom de la `DbContext` classe à générer.                                                                                                                                                                                                                          |
| -Schemas \<String [] >               | Les schémas des tables pour générer des types d’entité. Si ce paramètre est omis, tous les schémas sont inclus.                                                                                                                                                             |
| -Tables \<String [] >                | Les tables pour générer des types d’entité. Si ce paramètre est omis, toutes les tables sont inclus.                                                                                                                                                                         |
| -DataAnnotations                   | Utilisez des attributs pour configurer le modèle (le cas échéant). Si ce paramètre est omis, uniquement l’API fluent est utilisé.                                                                                                                                                      |
| -UseDatabaseNames                  | Utiliser des noms de table et colonne exactement telles qu’elles apparaissent dans la base de données. Si ce paramètre est omis, les noms de base de données sont modifiés pour mieux se conformer aux conventions de style de nom C#.                                                                                       |
| -Force                             | Remplacer les fichiers existants.                                                                                                                                                                                                                                               |

Exemple :

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models
```

Exemple de structure que les tables sélectionnées et crée le contexte dans un dossier distinct avec un nom spécifié :

```powershell
Scaffold-DbContext "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer -OutputDir Models -Tables "Blog","Post" -ContextDir Context -Context BlogContext
```

## <a name="script-migration"></a>Migration de script

Génère un script SQL qui s’applique toutes les modifications à partir d’une migration sélectionnée à une autre migration sélectionnée.

Paramètres :

| Paramètre                | Description                                                                                                                                                                                                                |
|:-------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| *-From* \<chaîne >        | La migration de départ. Migrations peuvent être identifiées par nom ou par ID. La valeur 0 est un cas spécial signifie *avant la première migration*. La valeur par défaut est 0.                                                              |
| *-* \<Chaîne >          | La migration de fin. Valeur par défaut est la dernière migration.                                                                                                                                                                      |
| <nobr>-Idempotent</nobr> | Générer un script qui peut être utilisé sur toute migration d’une base de données.                                                                                                                                                         |
| -Sortie \<chaîne >        | Fichier dans lequel écrire le résultat. Si ce paramètre est omis, le fichier est créé avec un nom généré dans le même dossier que les fichiers exécutables de l’application sont créés, par exemple : */obj/Debug/netcoreapp2.1/ghbkztfz.sql/*. |

> [!TIP]
> To, From, et les paramètres de sortie prend en charge d’extension de l’onglet.

L’exemple suivant crée un script pour la migration InitialCreate, en utilisant le nom de la migration.

```powershell
Script-Migration -To InitialCreate
```

L’exemple suivant crée un script pour toutes les migrations après la migration InitialCreate, à l’aide de l’ID de la migration.

```powershell
Script-Migration -From 20180904195021_InitialCreate
```

## <a name="update-database"></a>Mise à jour la base de données

Met à jour la base de données pour la dernière migration ou pour une migration spécifiée.

| Paramètre                           | Description                                                                                                                                                                                                                                                     |
|:------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <nobr>*-Migration* \<chaîne ></nobr> | La migration de la cible. Migrations peuvent être identifiées par nom ou par ID. La valeur 0 est un cas spécial signifie *avant la première migration* et oblige toutes les migrations à rétablir. Si aucune migration n’est spécifiée, la commande par défaut est la dernière migration. |

> [!TIP]
> Le paramètre de Migration prend en charge d’extension de l’onglet.

L’exemple suivant rétablit toutes les migrations.

```powershell
Update-Database -Migration 0
```

Les exemples suivants mettre à jour la base de données pour une migration spécifiée. La première utilise le nom de la migration et la seconde utilise l’ID de la migration :

```powershell
Update-Database -Migration InitialCreate
Update-Database -Migration 20180904195021_InitialCreate
```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Migrations](xref:core/managing-schemas/migrations/index)
* [Ingénierie à rebours](xref:core/managing-schemas/scaffolding)
