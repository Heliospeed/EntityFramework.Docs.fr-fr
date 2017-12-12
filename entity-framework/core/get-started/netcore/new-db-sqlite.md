---
title: "Bien démarrer avec .NET Core - Nouvelle base de données - EF Core"
author: rick-anderson
ms.author: riande
ms.author2: tdykstra
description: "Bien démarrer avec .NET Core à l’aide d’Entity Framework Core"
keywords: .NET Core, Entity Framework Core, VS Code, Visual Studio Code, Mac, Linux
ms.date: 04/05/2017
ms.assetid: 099d179e-dd7b-4755-8f3c-fcde914bf50b
ms.technology: entity-framework-core
uid: core/get-started/netcore/new-db-sqlite
ms.openlocfilehash: 22fc0446dee71dd0d2402b47d76cc8b7307fbe5f
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="getting-started-with-ef-core-on-net-core-console-app-with-a-new-database"></a><span data-ttu-id="d3d21-104">Bien démarrer avec EF Core sur une application console .NET Core avec une nouvelle base de données</span><span class="sxs-lookup"><span data-stu-id="d3d21-104">Getting Started with EF Core on .NET Core Console App with a New database</span></span>

<span data-ttu-id="d3d21-105">Dans cette procédure pas à pas, vous allez créer une application console .NET Core qui exécute l’accès aux données de base d’une base de données SQLite à l’aide d’Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d3d21-105">In this walkthrough, you will create a .NET Core console app that performs basic data access against a SQLite database using Entity Framework Core.</span></span> <span data-ttu-id="d3d21-106">Vous utiliserez des migrations pour créer la base de données à partir de votre modèle.</span><span class="sxs-lookup"><span data-stu-id="d3d21-106">You will use migrations to create the database from your model.</span></span> <span data-ttu-id="d3d21-107">Consultez [ASP.NET Core - Nouvelle base de données](xref:core/get-started/aspnetcore/new-db) pour une version de Visual Studio utilisant ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="d3d21-107">See [ASP.NET Core - New database](xref:core/get-started/aspnetcore/new-db) for a Visual Studio version using ASP.NET Core MVC.</span></span>

> [!NOTE]  
> <span data-ttu-id="d3d21-108">Le [Kit SDK .NET Core](https://www.microsoft.com/net/download/core) ne prend plus en charge `project.json` ni Visual Studio 2015.</span><span class="sxs-lookup"><span data-stu-id="d3d21-108">The [.NET Core SDK](https://www.microsoft.com/net/download/core) no longer supports `project.json` or Visual Studio 2015.</span></span> <span data-ttu-id="d3d21-109">Nous vous recommandons de [migrer de project.json vers csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span><span class="sxs-lookup"><span data-stu-id="d3d21-109">We recommend you [migrate from project.json to csproj](https://docs.microsoft.com/dotnet/articles/core/migration/).</span></span> <span data-ttu-id="d3d21-110">Si vous utilisez Visual Studio, nous vous recommandons de migrer vers [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="d3d21-110">If you are using Visual Studio, we recommend you migrate to [Visual Studio 2017](https://www.visualstudio.com/downloads/).</span></span>

> [!TIP]  
> <span data-ttu-id="d3d21-111">Vous pouvez afficher cet [exemple](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) sur GitHub.</span><span class="sxs-lookup"><span data-stu-id="d3d21-111">You can view this article's [sample](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/GetStarted/NetCore/ConsoleApp.SQLite) on GitHub.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d3d21-112">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="d3d21-112">Prerequisites</span></span>

<span data-ttu-id="d3d21-113">Pour effectuer cette procédure pas à pas, vous devez satisfaire les prérequis suivants :</span><span class="sxs-lookup"><span data-stu-id="d3d21-113">The following prerequisites are needed to complete this walkthrough:</span></span>
* <span data-ttu-id="d3d21-114">Un système d’exploitation prenant en charge .NET Core</span><span class="sxs-lookup"><span data-stu-id="d3d21-114">An operating system that supports .NET Core.</span></span>
* <span data-ttu-id="d3d21-115">Le [Kit SDK .NET Core](https://www.microsoft.com/net/core) 2.0 (bien que les instructions puissent être utilisées pour créer une application avec une version précédente avec très peu de modifications)</span><span class="sxs-lookup"><span data-stu-id="d3d21-115">[The .NET Core SDK](https://www.microsoft.com/net/core) 2.0 (although the instructions can be used to create an application with a previous version with very few modifications).</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="d3d21-116">Créer un projet</span><span class="sxs-lookup"><span data-stu-id="d3d21-116">Create a new project</span></span>

* <span data-ttu-id="d3d21-117">Créez un dossier `ConsoleApp.SQLite` pour votre projet et utilisez la commande `dotnet` pour la renseigner avec une application .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d3d21-117">Create a new `ConsoleApp.SQLite` folder for your project and use the `dotnet` command to populate it with a .NET Core app.</span></span>

``` Console
mkdir ConsoleApp.SQLite
cd ConsoleApp.SQLite/
dotnet new console
```

## <a name="install-entity-framework-core"></a><span data-ttu-id="d3d21-118">Installer Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d3d21-118">Install Entity Framework Core</span></span>

<span data-ttu-id="d3d21-119">Pour utiliser EF Core, installez le package pour le ou les fournisseurs de bases de données à cibler.</span><span class="sxs-lookup"><span data-stu-id="d3d21-119">To use EF Core, install the package for the database provider(s) you want to target.</span></span> <span data-ttu-id="d3d21-120">Cette procédure pas à pas utilise SQLite.</span><span class="sxs-lookup"><span data-stu-id="d3d21-120">This walkthrough uses SQLite.</span></span> <span data-ttu-id="d3d21-121">Pour obtenir la liste des fournisseurs disponibles, consultez [Fournisseurs de bases de données](../../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="d3d21-121">For a list of available providers see [Database Providers](../../providers/index.md).</span></span>

* <span data-ttu-id="d3d21-122">Installez Microsoft.EntityFrameworkCore.Sqlite et Microsoft.EntityFrameworkCore.Design.</span><span class="sxs-lookup"><span data-stu-id="d3d21-122">Install Microsoft.EntityFrameworkCore.Sqlite and Microsoft.EntityFrameworkCore.Design</span></span>

``` Console
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```

* <span data-ttu-id="d3d21-123">Modifiez manuellement `ConsoleApp.SQLite.csproj` pour ajouter un élément DotNetCliToolReference à Microsoft.EntityFrameworkCore.Tools.DotNet :</span><span class="sxs-lookup"><span data-stu-id="d3d21-123">Manually edit `ConsoleApp.SQLite.csproj` to add a DotNetCliToolReference to Microsoft.EntityFrameworkCore.Tools.DotNet:</span></span>

  ``` xml
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
  </ItemGroup>
  ```

 <span data-ttu-id="d3d21-124">Remarque : une version future de `dotnet` prendra en charge DotNetCliToolReferences via `dotnet add tool`.</span><span class="sxs-lookup"><span data-stu-id="d3d21-124">Note: A future version of `dotnet` will support DotNetCliToolReferences via `dotnet add tool`</span></span>

<span data-ttu-id="d3d21-125">`ConsoleApp.SQLite.csproj` doit désormais avoir le contenu suivant :</span><span class="sxs-lookup"><span data-stu-id="d3d21-125">`ConsoleApp.SQLite.csproj` should now contain the following:</span></span>

[!code[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/ConsoleApp.SQLite.csproj)]

 <span data-ttu-id="d3d21-126">Remarque : les numéros de version ci-dessus étaient corrects au moment de cette publication.</span><span class="sxs-lookup"><span data-stu-id="d3d21-126">Note: The version numbers used above were correct at the time of publishing.</span></span>

*  <span data-ttu-id="d3d21-127">Exécutez `dotnet restore` pour installer les nouveaux packages.</span><span class="sxs-lookup"><span data-stu-id="d3d21-127">Run `dotnet restore` to install the new packages.</span></span>

## <a name="create-the-model"></a><span data-ttu-id="d3d21-128">Créer le modèle</span><span class="sxs-lookup"><span data-stu-id="d3d21-128">Create the model</span></span>

<span data-ttu-id="d3d21-129">Définissez le contexte et les classes d’entité qui composeront le modèle.</span><span class="sxs-lookup"><span data-stu-id="d3d21-129">Define a context and entity classes that make up your model.</span></span>

* <span data-ttu-id="d3d21-130">Créez un nouveau fichier *Model.cs* avec le contenu suivant.</span><span class="sxs-lookup"><span data-stu-id="d3d21-130">Create a new *Model.cs* file with the following contents.</span></span>

[!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Model.cs)]

<span data-ttu-id="d3d21-131">Conseil : dans une application réelle, vous placeriez chaque classe dans un fichier distinct et la chaîne de connexion dans un fichier de configuration.</span><span class="sxs-lookup"><span data-stu-id="d3d21-131">Tip: In a real application you would put each class in a separate file and put the connection string in a configuration file.</span></span> <span data-ttu-id="d3d21-132">Pour simplifier le didacticiel, nous plaçons tout dans un même fichier.</span><span class="sxs-lookup"><span data-stu-id="d3d21-132">To keep the tutorial simple, we are putting everything in one file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="d3d21-133">Créer la base de données</span><span class="sxs-lookup"><span data-stu-id="d3d21-133">Create the database</span></span>

<span data-ttu-id="d3d21-134">Une fois que vous avez un modèle, vous pouvez utiliser des [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) pour créer une base de données.</span><span class="sxs-lookup"><span data-stu-id="d3d21-134">Once you have a model, you can use [migrations](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) to create a database.</span></span>

* <span data-ttu-id="d3d21-135">Exécutez `dotnet ef migrations add InitialCreate` pour générer automatiquement un modèle de migration et créer l’ensemble initial de tables du modèle.</span><span class="sxs-lookup"><span data-stu-id="d3d21-135">Run `dotnet ef migrations add InitialCreate` to scaffold a migration and create the initial set of tables for the model.</span></span>
* <span data-ttu-id="d3d21-136">Exécutez `dotnet ef database update` pour appliquer la nouvelle migration à la base de données.</span><span class="sxs-lookup"><span data-stu-id="d3d21-136">Run `dotnet ef database update` to apply the new migration to the database.</span></span> <span data-ttu-id="d3d21-137">Cette commande crée la base de données avant d’appliquer des migrations.</span><span class="sxs-lookup"><span data-stu-id="d3d21-137">This command creates the database before applying migrations.</span></span>

> [!NOTE]  
> <span data-ttu-id="d3d21-138">Durant l’utilisation de chemins d’accès relatifs avec SQLite, le chemin d’accès dépend de l’assembly principal de l’application.</span><span class="sxs-lookup"><span data-stu-id="d3d21-138">When using relative paths with SQLite, the path will be relative to the application's main assembly.</span></span> <span data-ttu-id="d3d21-139">Dans cet exemple, le fichier binaire principal est `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`, de sorte que la base de données SQLite se trouvera dans `bin/Debug/netcoreapp2.0/blogging.db`.</span><span class="sxs-lookup"><span data-stu-id="d3d21-139">In this sample, the main binary is `bin/Debug/netcoreapp2.0/ConsoleApp.SQLite.dll`, so the SQLite database will be in `bin/Debug/netcoreapp2.0/blogging.db`.</span></span>

## <a name="use-your-model"></a><span data-ttu-id="d3d21-140">Utiliser le modèle</span><span class="sxs-lookup"><span data-stu-id="d3d21-140">Use your model</span></span>

* <span data-ttu-id="d3d21-141">Ouvrez le fichier *Program.cs* et remplacez le contenu par le code suivant :</span><span class="sxs-lookup"><span data-stu-id="d3d21-141">Open *Program.cs* and replace the contents with the following code:</span></span>

 [!code-csharp[Main](../../../../samples/core/GetStarted/NetCore/ConsoleApp.SQLite/Program.cs)]

* <span data-ttu-id="d3d21-142">Testez l’application :</span><span class="sxs-lookup"><span data-stu-id="d3d21-142">Test the app:</span></span>

 `dotnet run`

 <span data-ttu-id="d3d21-143">Un blog est enregistré dans la base de données et les détails de tous les blogs s’affichent dans la console.</span><span class="sxs-lookup"><span data-stu-id="d3d21-143">One blog is saved to the database and the details of all blogs are displayed in the console.</span></span>

  ``` Console
  ConsoleApp.SQLite>dotnet run
  1 records saved to database

  All blogs in database:
   - http://blogs.msdn.com/adonet
  ```

### <a name="changing-the-model"></a><span data-ttu-id="d3d21-144">Modification du modèle :</span><span class="sxs-lookup"><span data-stu-id="d3d21-144">Changing the model:</span></span>

- <span data-ttu-id="d3d21-145">Si vous apportez des modifications au modèle, vous pouvez utiliser la commande `dotnet ef migrations add` pour générer automatiquement un nouveau modèle de [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations) pour appliquer les modifications du schéma correspondantes à la base de données.</span><span class="sxs-lookup"><span data-stu-id="d3d21-145">If you make changes to your model, you can use the `dotnet ef migrations add` command to scaffold a new [migration](https://docs.microsoft.com/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations)  to make the corresponding schema changes to the database.</span></span> <span data-ttu-id="d3d21-146">Après avoir vérifié le code de modèle généré automatiquement (et effectué toutes les modifications nécessaires), vous pouvez utiliser la commande `dotnet ef database update` pour appliquer les modifications à la base de données.</span><span class="sxs-lookup"><span data-stu-id="d3d21-146">Once you have checked the scaffolded code (and made any required changes), you can use the `dotnet ef database update` command to apply the changes to the database.</span></span>
- <span data-ttu-id="d3d21-147">EF utilise une table `__EFMigrationsHistory` dans la base de données pour identifier les migrations qui ont déjà été appliquées à la base de données.</span><span class="sxs-lookup"><span data-stu-id="d3d21-147">EF uses a `__EFMigrationsHistory` table in the database to keep track of which migrations have already been applied to the database.</span></span>
- <span data-ttu-id="d3d21-148">SQLite ne prend pas en charge toutes les migrations (modifications du schéma) en raison des limitations de SQLite.</span><span class="sxs-lookup"><span data-stu-id="d3d21-148">SQLite does not support all migrations (schema changes) due to limitations in SQLite.</span></span> <span data-ttu-id="d3d21-149">Consultez [Limitations de SQLite](../../providers/sqlite/limitations.md).</span><span class="sxs-lookup"><span data-stu-id="d3d21-149">See [SQLite Limitations](../../providers/sqlite/limitations.md).</span></span> <span data-ttu-id="d3d21-150">Pour tout nouveau développement, il est préférable de supprimer la base de données et d’en créer une nouvelle plutôt que d’utiliser des migrations quand votre modèle est modifié.</span><span class="sxs-lookup"><span data-stu-id="d3d21-150">For new development, consider dropping the database and creating a new one rather than using migrations when your model changes.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3d21-151">Ressources supplémentaires</span><span class="sxs-lookup"><span data-stu-id="d3d21-151">Additional Resources</span></span>

* <span data-ttu-id="d3d21-152">[.NET Core - Nouvelle base de données avec SQLite](xref:core/get-started/netcore/new-db-sqlite) : didacticiel sur la console multiplateforme EF</span><span class="sxs-lookup"><span data-stu-id="d3d21-152">[.NET Core - New database with SQLite](xref:core/get-started/netcore/new-db-sqlite) -  a cross-platform console EF tutorial.</span></span>
* [<span data-ttu-id="d3d21-153">Introduction à ASP.NET Core MVC sur Mac ou Linux</span><span class="sxs-lookup"><span data-stu-id="d3d21-153">Introduction to ASP.NET Core MVC on Mac or Linux</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app-xplat/index)
* [<span data-ttu-id="d3d21-154">Introduction à ASP.NET Core MVC avec Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3d21-154">Introduction to ASP.NET Core MVC with Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/index)
* [<span data-ttu-id="d3d21-155">Bien démarrer avec ASP.NET Core et Entity Framework Core à l’aide de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3d21-155">Getting started with ASP.NET Core and Entity Framework Core using Visual Studio</span></span>](https://docs.microsoft.com/aspnet/core/data/ef-mvc/index)