---
title: Relations, les propriétés de navigation et les clés étrangères - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
caps.latest.revision: 3
ms.openlocfilehash: 0cc18ee8d1b9d1633535e4b8186ffc4c68daf32b
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39120858"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="3e66e-102">Relations, les propriétés de navigation et les clés étrangères</span><span class="sxs-lookup"><span data-stu-id="3e66e-102">Relationships, navigation properties and foreign keys</span></span>
<span data-ttu-id="3e66e-103">Cette rubrique donne une vue d’ensemble de la façon dont Entity Framework gère les relations entre entités.</span><span class="sxs-lookup"><span data-stu-id="3e66e-103">This topic gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="3e66e-104">Il fournit également des conseils sur la façon de mapper et de manipuler des relations.</span><span class="sxs-lookup"><span data-stu-id="3e66e-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="3e66e-105">Relations dans EF</span><span class="sxs-lookup"><span data-stu-id="3e66e-105">Relationships in EF</span></span>

<span data-ttu-id="3e66e-106">Dans les bases de données relationnelles, les relations (également appelées associations) entre les tables sont définies via les clés étrangères.</span><span class="sxs-lookup"><span data-stu-id="3e66e-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="3e66e-107">Une clé étrangère (FK) est une colonne ou une combinaison de colonnes est utilisé pour établir et conserver un lien entre les données de deux tables.</span><span class="sxs-lookup"><span data-stu-id="3e66e-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="3e66e-108">Il existe généralement trois types de relations :-à-un, un-à-plusieurs et plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="3e66e-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="3e66e-109">Dans une relation un-à-plusieurs, la clé étrangère est définie sur la table qui représente la fin de nombreux de la relation.</span><span class="sxs-lookup"><span data-stu-id="3e66e-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="3e66e-110">La relation plusieurs-à-plusieurs implique la définition d’une troisième table (appelée une table de jonction ou de jointure), dont la clé primaire est composée des clés étrangères des deux tables connexes.</span><span class="sxs-lookup"><span data-stu-id="3e66e-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="3e66e-111">Dans une relation un à un, la clé primaire se comporte en outre, comme une clé étrangère, et il n’existe aucune colonne de clé étrangère distinct pour des tables.</span><span class="sxs-lookup"><span data-stu-id="3e66e-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="3e66e-112">L’illustration suivante montre les deux tables impliquées dans une relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="3e66e-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="3e66e-113">Le **cours** table est la table dépendante, car elle contient le **DepartmentID** colonne qui lie à la **département** table.</span><span class="sxs-lookup"><span data-stu-id="3e66e-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Database2](~/ef6/media/database2.png)

<span data-ttu-id="3e66e-115">Dans Entity Framework, une entité peut être associée à d’autres entités via une association ou de la relation.</span><span class="sxs-lookup"><span data-stu-id="3e66e-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="3e66e-116">Chaque relation comporte deux terminaisons qui décrivent le type d’entité et la multiplicité du type (un, zéro-ou-un ou plusieurs) pour les deux entités dans cette relation.</span><span class="sxs-lookup"><span data-stu-id="3e66e-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="3e66e-117">La relation peut-être être régie par une contrainte référentielle, qui décrit quelle fin dans la relation est un rôle principal et qui est un rôle dépendant.</span><span class="sxs-lookup"><span data-stu-id="3e66e-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="3e66e-118">Propriétés de navigation permettent de parcourir une association entre deux types d’entité.</span><span class="sxs-lookup"><span data-stu-id="3e66e-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="3e66e-119">Chaque objet peut avoir une propriété de navigation pour chaque relation à laquelle il participe.</span><span class="sxs-lookup"><span data-stu-id="3e66e-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="3e66e-120">Propriétés de navigation permettent de parcourir et de gérer les relations dans les deux directions, en retournant un objet de la référence (si la multiplicité est soit un ou zéro-ou-un) ou une collection (si la multiplicité est nombreuses).</span><span class="sxs-lookup"><span data-stu-id="3e66e-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="3e66e-121">Vous pouvez également choisir d’avoir une navigation unidirectionnelle, auquel cas vous définissez la propriété de navigation sur un seul des types qui participe à la relation et non sur les deux.</span><span class="sxs-lookup"><span data-stu-id="3e66e-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="3e66e-122">Il est recommandé d’inclure des propriétés dans le modèle correspondant aux clés étrangères dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3e66e-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="3e66e-123">Lorsque les propriétés de clé étrangère sont incluses, vous pouvez créer ou modifier une relation en changeant la valeur de clé étrangère d'un objet dépendant.</span><span class="sxs-lookup"><span data-stu-id="3e66e-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="3e66e-124">Ce type d'association s'appelle une association de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="3e66e-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="3e66e-125">À l’aide de clés étrangères est encore plus indispensable lorsque vous travaillez avec des entités déconnectées.</span><span class="sxs-lookup"><span data-stu-id="3e66e-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="3e66e-126">Notez que quand utilisation 1-à-1 ou 1-0... relations 1, il n’existe aucune colonne de clé étrangère distinct, agit comme la clé étrangère de la propriété de clé primaire et est toujours incluse dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="3e66e-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="3e66e-127">Lorsque les colonnes clés étrangères ne sont pas inclus dans le modèle, les informations d’association sont gérées comme un objet indépendant.</span><span class="sxs-lookup"><span data-stu-id="3e66e-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="3e66e-128">Les relations sont suivies par le biais des références d’objet au lieu de propriétés de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="3e66e-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="3e66e-129">Ce type d’association est appelé un *association indépendante*.</span><span class="sxs-lookup"><span data-stu-id="3e66e-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="3e66e-130">La méthode la plus courante pour modifier un *association indépendante* consiste à modifier les propriétés de navigation sont générées pour chaque entité qui participe à l’association.</span><span class="sxs-lookup"><span data-stu-id="3e66e-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="3e66e-131">Vous pouvez choisir d'utiliser un type d'association dans votre modèle ou les deux à la fois.</span><span class="sxs-lookup"><span data-stu-id="3e66e-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="3e66e-132">Toutefois, si vous avez une relation plusieurs-à-plusieurs pure qui est connectée par une table de jointure qui contient uniquement des clés étrangères, EF utilisera une association indépendante pour gérer cette relation plusieurs-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="3e66e-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="3e66e-133">L’illustration suivante montre un modèle conceptuel qui a été créé avec l’Entity Framework Designer.</span><span class="sxs-lookup"><span data-stu-id="3e66e-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="3e66e-134">Le modèle contient deux entités qui participent de relation un-à-plusieurs.</span><span class="sxs-lookup"><span data-stu-id="3e66e-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="3e66e-135">Les deux entités ont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="3e66e-135">Both entities have navigation properties.</span></span> <span data-ttu-id="3e66e-136">**Cours** est l’entité depend et a la **DepartmentID** propriété de clé étrangère définie.</span><span class="sxs-lookup"><span data-stu-id="3e66e-136">**Course** is the depend entity and has the **DepartmentID** foreign key property defined.</span></span>

![RelationshipEFDesigner](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="3e66e-138">L’extrait de code suivant montre le même modèle que celui qui a été créé avec Code First.</span><span class="sxs-lookup"><span data-stu-id="3e66e-138">The following code snippet shows the same model that was created with Code First.</span></span>

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class DepartmentID
{
   public Department()
   {
     this.Course = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="3e66e-139">Configuration ou le mappage des relations</span><span class="sxs-lookup"><span data-stu-id="3e66e-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="3e66e-140">Le reste de cette page décrit comment accéder à et manipuler des données à l’aide de relations.</span><span class="sxs-lookup"><span data-stu-id="3e66e-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="3e66e-141">Pour plus d’informations sur la configuration des relations dans votre modèle, consultez les pages suivantes.</span><span class="sxs-lookup"><span data-stu-id="3e66e-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="3e66e-142">Pour configurer des relations dans le Code First, voir [Annotations de données](~/ef6/modeling/code-first/data-annotations.md) et [API Fluent – relations](~/ef6/modeling/code-first/fluent/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="3e66e-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="3e66e-143">Pour configurer des relations à l’aide d’Entity Framework Designer, consultez [relations avec le Concepteur EF](~/ef6/modeling/designer/relationships.md).</span><span class="sxs-lookup"><span data-stu-id="3e66e-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="3e66e-144">Création et modification de relations</span><span class="sxs-lookup"><span data-stu-id="3e66e-144">Creating and modifying relationships</span></span>

<span data-ttu-id="3e66e-145">Dans un *association de clé étrangère*, lorsque vous modifiez la relation, l’état d’un objet dépendant avec une EntityState.Unchanged l’état passe à EntityState.Modified.</span><span class="sxs-lookup"><span data-stu-id="3e66e-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an EntityState.Unchanged state changes to EntityState.Modified.</span></span> <span data-ttu-id="3e66e-146">Dans une relation indépendante, modification de la relation ne pas mettre à jour l’état de l’objet dépendant.</span><span class="sxs-lookup"><span data-stu-id="3e66e-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="3e66e-147">Les exemples suivants montrent comment utiliser les propriétés de clé étrangère et les propriétés de navigation pour associer les objets connexes.</span><span class="sxs-lookup"><span data-stu-id="3e66e-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="3e66e-148">Avec les associations de clé étrangère, vous pouvez utiliser soit la méthode à modifier, créer ou modifier des relations.</span><span class="sxs-lookup"><span data-stu-id="3e66e-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="3e66e-149">Avec les associations indépendantes, vous ne pouvez pas utiliser la propriété de clé étrangère.</span><span class="sxs-lookup"><span data-stu-id="3e66e-149">With independent associations, you cannot use the foreign key property.</span></span>

-   <span data-ttu-id="3e66e-150">En assignant une nouvelle valeur à une propriété de clé étrangère, comme dans l’exemple suivant.</span><span class="sxs-lookup"><span data-stu-id="3e66e-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
    ``` csharp
    course.DepartmentID = newCourse.DepartmentID;
    ```

-   <span data-ttu-id="3e66e-151">Le code suivant supprime une relation en définissant la clé étrangère sur **null**.</span><span class="sxs-lookup"><span data-stu-id="3e66e-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="3e66e-152">Notez que la propriété de clé étrangère doit être nullable.</span><span class="sxs-lookup"><span data-stu-id="3e66e-152">Note, that the foreign key property must be nullable.</span></span>  
    ``` csharp
    course.DepartmentID = null;
    ```  
    >[!NOTE]
    > <span data-ttu-id="3e66e-153">Si la référence est dans l’état ajouté (dans cet exemple, l’objet de cours), la propriété de navigation de référence ne sera pas synchronisée avec les valeurs de clé d’un nouvel objet jusqu'à ce que SaveChanges est appelée.</span><span class="sxs-lookup"><span data-stu-id="3e66e-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="3e66e-154">La synchronisation n'a pas lieu, car le contexte de l'objet ne contient pas de clés permanentes pour les objets ajoutés tant qu'ils ne sont pas enregistrés.</span><span class="sxs-lookup"><span data-stu-id="3e66e-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="3e66e-155">Si vous devez disposer des nouveaux objets entièrement synchronisés dès que vous définissez la relation, utilisez une du methods.\* suivant</span><span class="sxs-lookup"><span data-stu-id="3e66e-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

-   <span data-ttu-id="3e66e-156">En affectant un nouvel objet à une propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="3e66e-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="3e66e-157">Le code suivant crée une relation entre un cours et un `department`.</span><span class="sxs-lookup"><span data-stu-id="3e66e-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="3e66e-158">Si les objets sont attachés au contexte, le `course` est également ajouté à la `department.Courses` collection et la clé étrangère propriété sur le `course` objet est défini sur la valeur de propriété de clé du service.</span><span class="sxs-lookup"><span data-stu-id="3e66e-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
    ``` csharp
    course.Department = department;
    ```

 -   <span data-ttu-id="3e66e-159">Pour supprimer la relation, définissez la propriété de navigation sur `null`.</span><span class="sxs-lookup"><span data-stu-id="3e66e-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="3e66e-160">Si vous travaillez avec Entity Framework qui repose sur .NET 4.0, la terminaison connexe doit être chargé avant que vous le définissez sur null.</span><span class="sxs-lookup"><span data-stu-id="3e66e-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="3e66e-161">Exemple :</span><span class="sxs-lookup"><span data-stu-id="3e66e-161">For example:</span></span>  
    ``` chsarp
    context.Entry(course).Reference(c => c.Department).Load();  
    course.Department = null;
    ```  
    <span data-ttu-id="3e66e-162">En commençant par Entity Framework 5.0, qui est basé sur .NET 4.5, vous pouvez définir la relation NULL sans charger de la terminaison connexe.</span><span class="sxs-lookup"><span data-stu-id="3e66e-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="3e66e-163">Vous pouvez également définir la valeur actuelle sur null à l’aide de la méthode suivante.</span><span class="sxs-lookup"><span data-stu-id="3e66e-163">You can also set the current value to null using the following method.</span></span>  
    ``` csharp
    context.Entry(course).Reference(c => c.Department).CurrentValue = null;
    ```

-   <span data-ttu-id="3e66e-164">En supprimant ou en ajoutant un objet dans une collection d'entités.</span><span class="sxs-lookup"><span data-stu-id="3e66e-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="3e66e-165">Par exemple, vous pouvez ajouter un objet de type `Course` à la `department.Courses` collection.</span><span class="sxs-lookup"><span data-stu-id="3e66e-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="3e66e-166">Cette opération crée une relation entre un particulier **cours** et un particulier `department`.</span><span class="sxs-lookup"><span data-stu-id="3e66e-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="3e66e-167">Si les objets sont attachés pour le contexte, la référence de service et la propriété de clé étrangère sur la **cours** objet définira appropriés `department`.</span><span class="sxs-lookup"><span data-stu-id="3e66e-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
    ``` csharp
    department.Courses.Add(newCourse);
    ```

- <span data-ttu-id="3e66e-168">À l’aide de la `ChangeRelationshipState` méthode pour modifier l’état de la relation entre deux objets d’entité spécifiée.</span><span class="sxs-lookup"><span data-stu-id="3e66e-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="3e66e-169">Cette méthode est couramment utilisée lorsque vous travaillez avec des applications multicouches et un *association indépendante* (il ne peut pas être utilisé avec une association de clé étrangère).</span><span class="sxs-lookup"><span data-stu-id="3e66e-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="3e66e-170">En outre, pour utiliser cette méthode, vous devez supprimer jusqu'à `ObjectContext`, comme illustré dans l’exemple ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="3e66e-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="3e66e-171">Dans l’exemple suivant, il existe une relation plusieurs-à-plusieurs entre formateurs et cours.</span><span class="sxs-lookup"><span data-stu-id="3e66e-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="3e66e-172">Appelant le `ChangeRelationshipState` (méthode) et en passant le `EntityState.Added` vous permet de paramètre, le `SchoolContext` savoir qu’une relation a été ajoutée entre les deux objets :</span><span class="sxs-lookup"><span data-stu-id="3e66e-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>

``` csharp

       ((IObjectContextAdapter)context).ObjectContext.
                 ObjectStateManager.
                  ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
```

    Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:

``` csharp
       ((IObjectContextAdapter)context).ObjectContext.
                  ObjectStateManager.
                  ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="3e66e-173">Synchronisation des modifications entre les clés étrangères et les propriétés de navigation</span><span class="sxs-lookup"><span data-stu-id="3e66e-173">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="3e66e-174">Lorsque vous modifiez la relation des objets attachés au contexte en utilisant l’une des méthodes décrites ci-dessus, Entity Framework a besoin synchroniser les clés étrangères, des références et des collections. Entity Framework gère automatiquement cette synchronisation (également appelé relation correctives) pour les entités POCO avec proxys.</span><span class="sxs-lookup"><span data-stu-id="3e66e-174">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="3e66e-175">Pour plus d’informations, consultez [utilisation de proxys](~/ef6/fundamentals/proxies.md).</span><span class="sxs-lookup"><span data-stu-id="3e66e-175">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="3e66e-176">Si vous utilisez des entités POCO sans proxys, vous devez vous assurer que le **DetectChanges** méthode est appelée pour synchroniser les objets connexes dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="3e66e-176">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="3e66e-177">Notez que les API suivantes déclenchent automatiquement un **DetectChanges** appeler.</span><span class="sxs-lookup"><span data-stu-id="3e66e-177">Note, that the following APIs automatically trigger a **DetectChanges** call.</span></span>

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   <span data-ttu-id="3e66e-178">Exécution d’un LINQ de la requête par rapport à un `DbSet`</span><span class="sxs-lookup"><span data-stu-id="3e66e-178">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="3e66e-179">Charger des objets connexes</span><span class="sxs-lookup"><span data-stu-id="3e66e-179">Loading related objects</span></span>

<span data-ttu-id="3e66e-180">Dans Entity Framework, que vous utilisez le plus souvent utiliser les propriétés de navigation pour charger des entités qui sont liées à l’entité retournée par l’association définie.</span><span class="sxs-lookup"><span data-stu-id="3e66e-180">In Entity Framework you use most commonly use the navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="3e66e-181">Pour plus d’informations, consultez [le chargement des objets connexes](~/ef6/querying/related-data.md).</span><span class="sxs-lookup"><span data-stu-id="3e66e-181">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="3e66e-182">Dans une association de clé étrangère, lorsque vous chargez une terminaison connexe d'un objet dépendant, l'objet connexe est chargé en fonction de la valeur de clé étrangère du dépendant actuellement en mémoire :</span><span class="sxs-lookup"><span data-stu-id="3e66e-182">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 1.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="3e66e-183">Dans une association indépendante, la terminaison connexe d'un objet dépendant est interrogée en fonction de la valeur de clé étrangère actuellement présente dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3e66e-183">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="3e66e-184">Toutefois, si la relation a été modifiée, et la propriété de référence sur l’objet dépendant pointe vers un autre objet principal qui est chargé dans le contexte de l’objet, Entity Framework tente de créer une relation en tant qu’il est défini sur le client.</span><span class="sxs-lookup"><span data-stu-id="3e66e-184">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="3e66e-185">Gestion de l'accès concurrentiel</span><span class="sxs-lookup"><span data-stu-id="3e66e-185">Managing concurrency</span></span>

<span data-ttu-id="3e66e-186">Dans la clé étrangère et associations indépendantes, les contrôles d’accès concurrentiel sont basées sur les clés d’entité et d’autres propriétés d’entité qui sont définies dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="3e66e-186">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="3e66e-187">Lorsque vous utilisez le Concepteur EF pour créer un modèle, définissez le `ConcurrencyMode` attribut **fixe** pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="3e66e-187">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="3e66e-188">Lorsque vous utilisez Code First pour définir un modèle, utilisez le `ConcurrencyCheck` annotation sur les propriétés que vous souhaitez être vérifiée pour l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="3e66e-188">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="3e66e-189">Lorsque vous travaillez avec Code First, vous pouvez également utiliser le `TimeStamp` annotation pour spécifier que la propriété doit être vérifiée pour l’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="3e66e-189">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="3e66e-190">Vous pouvez avoir qu’une seule propriété d’horodatage dans une classe donnée.</span><span class="sxs-lookup"><span data-stu-id="3e66e-190">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="3e66e-191">Tout d’abord les cartes de code cette propriété à un champ non nullable dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="3e66e-191">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="3e66e-192">Nous recommandons de toujours utiliser l’association de clé étrangère lorsque vous travaillez avec des entités qui participent à la résolution et de contrôle d’accès concurrentiel.</span><span class="sxs-lookup"><span data-stu-id="3e66e-192">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="3e66e-193">Pour plus d’informations, consultez [gère les conflits d’accès concurrentiel](~/ef6/saving/concurrency.md).</span><span class="sxs-lookup"><span data-stu-id="3e66e-193">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="3e66e-194">Utilisation de clés qui se chevauchent</span><span class="sxs-lookup"><span data-stu-id="3e66e-194">Working with overlapping Keys</span></span>

<span data-ttu-id="3e66e-195">Des clés qui se chevauchent sont des clés composites qui ont certaines propriétés en commun dans l'entité qu'elles constituent.</span><span class="sxs-lookup"><span data-stu-id="3e66e-195">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="3e66e-196">Une association indépendante ne peut pas contenir de clés qui se chevauchent.</span><span class="sxs-lookup"><span data-stu-id="3e66e-196">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="3e66e-197">Pour modifier une association de clé étrangère qui comporte des clés qui se chevauchent, nous vous conseillons de modifier les valeurs de clé étrangère plutôt que d'utiliser les références d'objet.</span><span class="sxs-lookup"><span data-stu-id="3e66e-197">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>