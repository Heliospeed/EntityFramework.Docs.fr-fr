---
title: Index - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 85b92003-b692-417d-ac1d-76d40dce664b
ms.technology: entity-framework-core
uid: core/modeling/indexes
ms.openlocfilehash: f57b545d53613cec6887734bf434958ee8fff4d8
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="indexes"></a><span data-ttu-id="2f09b-102">Index</span><span class="sxs-lookup"><span data-stu-id="2f09b-102">Indexes</span></span>

<span data-ttu-id="2f09b-103">Les index sont un concept commun dans plusieurs bases de données.</span><span class="sxs-lookup"><span data-stu-id="2f09b-103">Indexes are a common concept across many data stores.</span></span> <span data-ttu-id="2f09b-104">Bien que leur implémentation dans le magasin de données peut varier, ils sont utilisés pour faire des recherches basées sur une colonne (ou un ensemble de colonnes) plus efficace.</span><span class="sxs-lookup"><span data-stu-id="2f09b-104">While their implementation in the data store may vary, they are used to make lookups based on a column (or set of columns) more efficient.</span></span>

## <a name="conventions"></a><span data-ttu-id="2f09b-105">Conventions</span><span class="sxs-lookup"><span data-stu-id="2f09b-105">Conventions</span></span>

<span data-ttu-id="2f09b-106">Par convention, un index est créé dans chaque propriété (ou un ensemble de propriétés) qui sont utilisées comme une clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="2f09b-106">By convention, an index is created in each property (or set of properties) that are used as a foreign key.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="2f09b-107">Annotations de données</span><span class="sxs-lookup"><span data-stu-id="2f09b-107">Data Annotations</span></span>

<span data-ttu-id="2f09b-108">Index ne peuvent pas être créés à l’aide des annotations de données.</span><span class="sxs-lookup"><span data-stu-id="2f09b-108">Indexes can not be created using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="2f09b-109">API Fluent</span><span class="sxs-lookup"><span data-stu-id="2f09b-109">Fluent API</span></span>

<span data-ttu-id="2f09b-110">Vous pouvez utiliser l’API Fluent pour spécifier un index sur une seule propriété.</span><span class="sxs-lookup"><span data-stu-id="2f09b-110">You can use the Fluent API to specify an index on a single property.</span></span> <span data-ttu-id="2f09b-111">Par défaut, les index ne sont pas uniques.</span><span class="sxs-lookup"><span data-stu-id="2f09b-111">By default, indexes are non-unique.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/Index.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url);
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="2f09b-112">Vous pouvez également spécifier qu’un index doit être unique, c'est-à-dire qu’aucune deux entités ne peuvent avoir la même valeur (s) pour la propriété donnée.</span><span class="sxs-lookup"><span data-stu-id="2f09b-112">You can also specify that an index should be unique, meaning that no two entities can have the same value(s) for the given property(s).</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexUnique.cs?highlight=3)] -->
``` csharp
        modelBuilder.Entity<Blog>()
            .HasIndex(b => b.Url)
            .IsUnique();
```

<span data-ttu-id="2f09b-113">Vous pouvez également spécifier un index sur plusieurs colonnes.</span><span class="sxs-lookup"><span data-stu-id="2f09b-113">You can also specify an index over more than one column.</span></span>

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/IndexComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .HasIndex(p => new { p.FirstName, p.LastName });
    }
}

public class Person
{
    public int PersonId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="2f09b-114">Il existe un seul index par un ensemble distinct de propriétés.</span><span class="sxs-lookup"><span data-stu-id="2f09b-114">There is only one index per distinct set of properties.</span></span> <span data-ttu-id="2f09b-115">Si vous utilisez l’API Fluent pour configurer un index sur un ensemble de propriétés qui possède déjà un index défini, soit par convention ou par la configuration précédente, puis vous allez modifier la définition de cet index.</span><span class="sxs-lookup"><span data-stu-id="2f09b-115">If you use the Fluent API to configure an index on a set of properties that already has an index defined, either by convention or previous configuration, then you will be changing the definition of that index.</span></span> <span data-ttu-id="2f09b-116">Cela est utile si vous souhaitez configurer un index qui a été créé par convention.</span><span class="sxs-lookup"><span data-stu-id="2f09b-116">This is useful if you want to further configure an index that was created by convention.</span></span>