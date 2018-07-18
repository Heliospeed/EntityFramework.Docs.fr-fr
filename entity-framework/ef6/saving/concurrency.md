---
title: Gestion des conflits d’accès concurrentiel - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
caps.latest.revision: 3
ms.openlocfilehash: b8608dbb4cadd60ff4ff4984583f8a9d843b3949
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120785"
---
# <a name="handling-concurrency-conflicts"></a><span data-ttu-id="0f384-102">Gestion de conflits d'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="0f384-102">Handling Concurrency Conflicts</span></span>
<span data-ttu-id="0f384-103">Tentative d’enregistrement de votre entité dans la base de données dans l’espoir que les données n’a pas changé depuis l’entité a été chargée optimiste implique l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="0f384-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="0f384-104">S’il s’avère que les données ont changé, une exception est levée et vous devez résoudre le conflit avant d’enregistrer à nouveau.</span><span class="sxs-lookup"><span data-stu-id="0f384-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="0f384-105">Cette rubrique explique comment gérer ces exceptions dans Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0f384-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="0f384-106">Les techniques présentées dans cette rubrique s’appliquent également aux modèles créés avec Code First et EF Designer.</span><span class="sxs-lookup"><span data-stu-id="0f384-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="0f384-107">Cette publication n’est pas l’emplacement approprié pour obtenir une présentation complète de l’accès concurrentiel optimiste.</span><span class="sxs-lookup"><span data-stu-id="0f384-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="0f384-108">Les sections ci-dessous supposent une connaissance de la résolution d’accès concurrentiel et affichent des modèles pour les tâches courantes.</span><span class="sxs-lookup"><span data-stu-id="0f384-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="0f384-109">Bon nombre de ces modèles font utilisent les sujets abordés dans [travaillez avec des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md).</span><span class="sxs-lookup"><span data-stu-id="0f384-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="0f384-110">Résolution des problèmes d’accès concurrentiel lorsque vous utilisez des associations indépendantes (où la clé étrangère n'est pas mappée à une propriété de votre entité) est beaucoup plus difficile que lorsque vous utilisez des associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="0f384-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="0f384-111">Par conséquent, si vous vous apprêtez à effectuer la résolution d’accès concurrentiel dans votre application, il est conseillé de toujours correspondre les clés étrangères dans vos entités.</span><span class="sxs-lookup"><span data-stu-id="0f384-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="0f384-112">Tous les exemples ci-dessous partent du principe que vous utilisez des associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="0f384-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="0f384-113">Une DbUpdateConcurrencyException est levée par SaveChanges lorsqu’une exception d’accès concurrentiel optimiste est détectée lors de la tentative d’enregistrement d’une entité qui utilise les associations de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="0f384-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="0f384-114">Résolution des exceptions d’accès concurrentiel optimiste avec recharger (wins de la base de données)</span><span class="sxs-lookup"><span data-stu-id="0f384-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="0f384-115">La méthode Reload peut être utilisée pour remplacer les valeurs actuelles de l’entité avec les valeurs maintenant dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0f384-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="0f384-116">L’entité est ensuite généralement envoyée à l’utilisateur dans une certaine forme et ils doivent essayer de le rendre de nouveau leurs modifications et enregistrez à nouveau.</span><span class="sxs-lookup"><span data-stu-id="0f384-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="0f384-117">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f384-117">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="0f384-118">Un bon moyen de simuler une exception d’accès concurrentiel consiste à définir un point d’arrêt sur l’appel de SaveChanges, puis modifiez une entité qui est enregistrée dans la base de données à l’aide d’un autre outil tel que SQL Management Studio.</span><span class="sxs-lookup"><span data-stu-id="0f384-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Management Studio.</span></span> <span data-ttu-id="0f384-119">Vous pouvez également insérer une ligne avant SaveChanges pour mettre à jour de la base de données directement à l’aide de SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="0f384-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="0f384-120">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f384-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="0f384-121">La méthode entrées sur DbUpdateConcurrencyException retourne les instances DbEntityEntry pour les entités qui n’a pas pu mettre à jour.</span><span class="sxs-lookup"><span data-stu-id="0f384-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="0f384-122">(Cette propriété actuellement retourne toujours une valeur unique pour les problèmes d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="0f384-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="0f384-123">Elle peut retourner plusieurs valeurs pour les exceptions de mise à jour générale.) Une alternative dans certains cas peut consister à obtenir les entrées pour toutes les entités devant être rechargées à partir de la base de données et appel recharger pour chacun d’eux.</span><span class="sxs-lookup"><span data-stu-id="0f384-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="0f384-124">Résolution des exceptions d’accès concurrentiel optimiste en tant que priorité au client</span><span class="sxs-lookup"><span data-stu-id="0f384-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="0f384-125">L’exemple ci-dessus qui utilise le rechargement est parfois appelé wins de la base de données ou priorité au magasin, car les valeurs de l’entité sont remplacées par les valeurs de la base de données.</span><span class="sxs-lookup"><span data-stu-id="0f384-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="0f384-126">Vous pouvez parfois souhaiter l’inverse, remplacer les valeurs dans la base de données avec les valeurs actuellement dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="0f384-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="0f384-127">Cela est parfois appelé priorité au client et peut être effectuée en obtenant les valeurs actuelles de la base de données et de les définir en tant que les valeurs d’origine pour l’entité.</span><span class="sxs-lookup"><span data-stu-id="0f384-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="0f384-128">(Consultez [travaillez avec des valeurs de propriété](~/ef6/saving/change-tracking/property-values.md) pour plus d’informations sur les valeurs actuelles et d’origine.) Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f384-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="0f384-129">Résolution personnalisée des exceptions d’accès concurrentiel optimiste</span><span class="sxs-lookup"><span data-stu-id="0f384-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="0f384-130">Vous pouvez être amené à combiner les valeurs actuellement dans la base de données avec les valeurs actuellement dans l’entité.</span><span class="sxs-lookup"><span data-stu-id="0f384-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="0f384-131">Cela nécessite généralement une interaction utilisateur ou de la logique personnalisée.</span><span class="sxs-lookup"><span data-stu-id="0f384-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="0f384-132">Par exemple, vous pouvez présenter un formulaire à l’utilisateur contenant les valeurs actuelles, les valeurs dans la base de données, et une valeur par défaut définie de valeurs résolues.</span><span class="sxs-lookup"><span data-stu-id="0f384-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="0f384-133">L’utilisateur est ensuite modifier les valeurs résolues en fonction des besoins, et il serait ces valeurs résolues enregistrées dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="0f384-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="0f384-134">Cela est possible en utilisant les objets DbPropertyValues retourné par CurrentValues et GetDatabaseValues sur l’entrée de l’entité.</span><span class="sxs-lookup"><span data-stu-id="0f384-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="0f384-135">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f384-135">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="0f384-136">Résolution personnalisée des exceptions d’accès concurrentiel optimiste à l’aide d’objets</span><span class="sxs-lookup"><span data-stu-id="0f384-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="0f384-137">Le code ci-dessus utilise des instances de DbPropertyValues pour passer des cours, base de données et valeurs résolues.</span><span class="sxs-lookup"><span data-stu-id="0f384-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="0f384-138">Parfois, il peut être plus facile à utiliser des instances de votre type d’entité pour cela.</span><span class="sxs-lookup"><span data-stu-id="0f384-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="0f384-139">Cela est possible à l’aide de méthodes ToObject et SetValues de DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="0f384-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="0f384-140">Exemple :</span><span class="sxs-lookup"><span data-stu-id="0f384-140">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  