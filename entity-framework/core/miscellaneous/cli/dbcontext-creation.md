---
title: Création au moment du design DbContext - EF Core
author: bricelam
ms.author: bricelam
ms.date: 10/27/2017
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: 66fec7605b6ac2da0af1e801f8a1dca0789aea35
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993716"
---
<a name="design-time-dbcontext-creation"></a>Création de DbContext d’au moment du design
==============================
Certaines commandes outils EF Core (par exemple, le [Migrations][1] commandes) nécessitent une dérivée `DbContext` instance doit être créé au moment du design afin de collecter des informations détaillées sur l’application types d’entité et comment elles correspondent à un schéma de base de données. Dans la plupart des cas, il est souhaitable que le `DbContext` ainsi créée est configuré de manière similaire à la façon dont il serait [configuré au moment de l’exécution][2].

Il existe différentes façons les outils essaie de créer le `DbContext`:

<a name="from-application-services"></a>À partir des services d’application
-------------------------
Si votre projet de démarrage est une application ASP.NET Core, les outils d’essayer d’obtenir l’objet DbContext à partir du fournisseur de services de l’application.

Les outils essayer tout d’abord d’obtenir le fournisseur de services en appelant `Program.BuildWebHost()` et l’accès à la `IWebHost.Services` propriété.

> [!NOTE]
> Lorsque vous créez une nouvelle application ASP.NET Core 2.0, ce hook est inclus par défaut. Dans les versions précédentes d’EF Core et ASP.NET Core, les outils tentez d’appeler `Startup.ConfigureServices` directement afin d’obtenir de fournisseur de services de l’application, mais ce modèle n’est plus fonctionne correctement dans les applications ASP.NET Core 2.0. Si vous mettez à niveau une application 1.x de ASP.NET Core 2.0, vous pouvez [modifier votre `Program` classe à suivre le nouveau modèle][3].

Le `DbContext` lui-même et les dépendances dans son constructeur doivent être inscrits en tant que services dans le fournisseur de services de l’application. Vous pouvez facilement faire en ayant [un constructeur sur la `DbContext` qui prend une instance de `DbContextOptions<TContext>` en tant qu’argument][ 4] et à l’aide de la [`AddDbContext<TContext>` (méthode)][5].

<a name="using-a-constructor-with-no-parameters"></a>À l’aide d’un constructeur sans paramètres
--------------------------------------
Si le DbContext ne peut pas être obtenu à partir du fournisseur de service d’application, les outils Recherchez dérivé `DbContext` type dans le projet. Puis, ils tentent de créer une instance à l’aide d’un constructeur sans paramètre. Il peut s’agir du constructeur par défaut si le `DbContext` est configuré à l’aide de la [`OnConfiguring`][6] (méthode).

<a name="from-a-design-time-factory"></a>À partir d’une fabrique au moment du design
--------------------------
Vous pouvez également indiquer aux outils comment créer votre DbContext en implémentant la `IDesignTimeDbContextFactory<TContext>` interface : si une classe qui implémente cette interface se trouve dans le même projet que dérivé `DbContext` ou dans le projet de démarrage de l’application, les outils de contournement les autres méthodes de création de DbContext et utiliser la fabrique au moment du design à la place.

``` csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;
using Microsoft.EntityFrameworkCore.Infrastructure;

namespace MyProject
{
    public class BloggingContextFactory : IDesignTimeDbContextFactory<BloggingContext>
    {
        public BloggingContext CreateDbContext(string[] args)
        {
            var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
            optionsBuilder.UseSqlite("Data Source=blog.db");

            return new BloggingContext(optionsBuilder.Options);
        }
    }
}
```

> [!NOTE]
> Le `args` paramètre est actuellement pas utilisé. Il est [un problème][7] la possibilité de spécifier des arguments au moment du design à partir des outils de suivi.

Une fabrique au moment du design peut être particulièrement utile si vous avez besoin configurer le DbContext différemment pour le moment du design qu’au moment de l’exécution, si le `DbContext` prend constructeur paramètres supplémentaires ne sont pas inscrits dans l’injection de dépendances, si vous n’utilisez pas du tout l’injection de dépendances, ou si pour certains vous préférez ne pas avoir de raison un `BuildWebHost` méthode dans votre application ASP.NET Core `Main` classe.

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/#update-main-method-in-programcs
  [4]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [5]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [6]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [7]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
