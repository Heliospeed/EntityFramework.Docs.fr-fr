---
title: Liaison de données avec WPF - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: e90d48e6-bea5-47ef-b756-7b89cce4daf0
caps.latest.revision: 3
ms.openlocfilehash: 1756ec14fe83d80199b6040bd345dc2fe6294281
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121139"
---
# <a name="databinding-with-wpf"></a><span data-ttu-id="afc5b-102">Liaison de données avec WPF</span><span class="sxs-lookup"><span data-stu-id="afc5b-102">Databinding with WPF</span></span>
<span data-ttu-id="afc5b-103">Cette procédure pas à pas montre comment lier les types POCO à des contrôles WPF dans un formulaire « maître / détail ».</span><span class="sxs-lookup"><span data-stu-id="afc5b-103">This step-by-step walkthrough shows how to bind POCO types to WPF controls in a “master-detail" form.</span></span> <span data-ttu-id="afc5b-104">L’application utilise les API Entity Framework pour remplir des objets avec des données à partir de la base de données, le suivi des modifications et conserver les données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-104">The application uses the Entity Framework APIs to populate objects with data from the database, track changes, and persist data to the database.</span></span>

<span data-ttu-id="afc5b-105">Le modèle définit deux types impliquées dans une relation un-à-plusieurs : **catégorie** (principal\\master) et **produit** (dépendants\\détail).</span><span class="sxs-lookup"><span data-stu-id="afc5b-105">The model defines two types that participate in one-to-many relationship: **Category** (principal\\master) and **Product** (dependent\\detail).</span></span> <span data-ttu-id="afc5b-106">Ensuite, les outils de Visual Studio sont utilisés pour lier les types définis dans le modèle pour les contrôles WPF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-106">Then, the Visual Studio tools are used to bind the types defined in the model to the WPF controls.</span></span> <span data-ttu-id="afc5b-107">L’infrastructure de liaison de données WPF permet la navigation entre les objets connexes : sélection de lignes dans la vue principale, la vue de détail mettre à jour avec les données enfants correspondantes.</span><span class="sxs-lookup"><span data-stu-id="afc5b-107">The WPF data-binding framework enables navigation between related objects: selecting rows in the master view causes the detail view to update with the corresponding child data.</span></span>

<span data-ttu-id="afc5b-108">Les captures d’écran et les listings de code dans cette procédure pas à pas sont effectuées à partir de Visual Studio 2013, mais vous pouvez effectuer cette procédure pas à pas avec Visual Studio 2012 ou Visual Studio 2010.</span><span class="sxs-lookup"><span data-stu-id="afc5b-108">The screen shots and code listings in this walkthrough are taken from Visual Studio 2013 but you can complete this walkthrough with Visual Studio 2012 or Visual Studio 2010.</span></span>

## <a name="use-the-object-option-for-creating-wpf-data-sources"></a><span data-ttu-id="afc5b-109">Utilisez l’Option 'Object' pour la création de Sources de données WPF</span><span class="sxs-lookup"><span data-stu-id="afc5b-109">Use the 'Object' Option for Creating WPF Data Sources</span></span>

<span data-ttu-id="afc5b-110">Avec une version précédente d’Entity Framework, nous avons utilisé est recommandé d’utiliser le **base de données** option lorsque vous créez une nouvelle Source de données basé sur un modèle créé avec le Concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-110">With previous version of Entity Framework we used to recommend using the **Database** option when creating a new Data Source based on a model created with the EF Designer.</span></span> <span data-ttu-id="afc5b-111">C’est parce que le concepteur génère un contexte dérivé ObjectContext et les classes d’entité dérivé à partir d’EntityObject.</span><span class="sxs-lookup"><span data-stu-id="afc5b-111">This was because the designer would generate a context that derived from ObjectContext and entity classes that derived from EntityObject.</span></span> <span data-ttu-id="afc5b-112">À l’aide de l’option de base de données vous permet d’écrire le meilleur code permettant d’interagir avec cette surface d’API.</span><span class="sxs-lookup"><span data-stu-id="afc5b-112">Using the Database option would help you write the best code for interacting with this API surface.</span></span>

<span data-ttu-id="afc5b-113">Les concepteurs d’EF pour Visual Studio 2012 et Visual Studio 2013 générer un contexte qui dérive de DbContext ainsi que de simples classes d’entité POCO.</span><span class="sxs-lookup"><span data-stu-id="afc5b-113">The EF Designers for Visual Studio 2012 and Visual Studio 2013 generate a context that derives from DbContext together with simple POCO entity classes.</span></span> <span data-ttu-id="afc5b-114">Avec Visual Studio 2010, nous vous recommandons de passer en un modèle de génération de code qui utilise le DbContext comme décrit plus loin dans cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="afc5b-114">With Visual Studio 2010 we recommend swapping to a code generation template that uses DbContext as described later in this walkthrough.</span></span>

<span data-ttu-id="afc5b-115">Lors de l’utilisation de la surface de cette API, vous devez utiliser le **objet** option lors de la création d’une nouvelle Source de données, comme illustré dans cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="afc5b-115">When using the DbContext API surface you should use the **Object** option when creating a new Data Source, as shown in this walkthrough.</span></span>

<span data-ttu-id="afc5b-116">Si nécessaire, vous pouvez [revenir à la génération de code en fonction de ObjectContext](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) pour les modèles créés avec le Concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-116">If needed, you can [revert to ObjectContext based code generation](~/ef6/modeling/designer/codegen/legacy-objectcontext.md) for models created with the EF Designer.</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="afc5b-117">Conditions préalables</span><span class="sxs-lookup"><span data-stu-id="afc5b-117">Pre-Requisites</span></span>

<span data-ttu-id="afc5b-118">Vous devez disposer de Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 est installé pour terminer cette procédure pas à pas.</span><span class="sxs-lookup"><span data-stu-id="afc5b-118">You need to have Visual Studio 2013, Visual Studio 2012 or Visual Studio 2010 installed to complete this walkthrough.</span></span>

<span data-ttu-id="afc5b-119">Si vous utilisez Visual Studio 2010, vous devez également installer le package NuGet.</span><span class="sxs-lookup"><span data-stu-id="afc5b-119">If you are using Visual Studio 2010, you also have to install NuGet.</span></span> <span data-ttu-id="afc5b-120">Pour plus d’informations, consultez [installation NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).</span><span class="sxs-lookup"><span data-stu-id="afc5b-120">For more information, see [Installing NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).</span></span>  

## <a name="create-the-application"></a><span data-ttu-id="afc5b-121">Création de l’application</span><span class="sxs-lookup"><span data-stu-id="afc5b-121">Create the Application</span></span>

-   <span data-ttu-id="afc5b-122">Ouvrir Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afc5b-122">Open Visual Studio</span></span>
-   <span data-ttu-id="afc5b-123">**Fichier -&gt; nouveau -&gt; projet...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-123">**File -&gt; New -&gt; Project….**</span></span>
-   <span data-ttu-id="afc5b-124">Sélectionnez **Windows** dans le volet gauche et **WPFApplication** dans le volet droit</span><span class="sxs-lookup"><span data-stu-id="afc5b-124">Select **Windows** in the left pane and **WPFApplication** in the right pane</span></span>
-   <span data-ttu-id="afc5b-125">Entrez **WPFwithEFSample** comme nom</span><span class="sxs-lookup"><span data-stu-id="afc5b-125">Enter **WPFwithEFSample** as the name</span></span>
-   <span data-ttu-id="afc5b-126">Sélectionnez **OK**.</span><span class="sxs-lookup"><span data-stu-id="afc5b-126">Select **OK**</span></span>

## <a name="install-the-entity-framework-nuget-package"></a><span data-ttu-id="afc5b-127">Installez le package NuGet Entity Framework</span><span class="sxs-lookup"><span data-stu-id="afc5b-127">Install the Entity Framework NuGet package</span></span>

-   <span data-ttu-id="afc5b-128">Dans l’Explorateur de solutions, cliquez sur le **WinFormswithEFSample** projet</span><span class="sxs-lookup"><span data-stu-id="afc5b-128">In Solution Explorer, right-click on the **WinFormswithEFSample** project</span></span>
-   <span data-ttu-id="afc5b-129">Sélectionnez **gérer les Packages NuGet...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-129">Select **Manage NuGet Packages…**</span></span>
-   <span data-ttu-id="afc5b-130">Dans la boîte de dialogue Gérer les Packages NuGet, sélectionnez le **Online** onglet et sélectionnez le **EntityFramework** package</span><span class="sxs-lookup"><span data-stu-id="afc5b-130">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
-   <span data-ttu-id="afc5b-131">Cliquez sur **installer**</span><span class="sxs-lookup"><span data-stu-id="afc5b-131">Click **Install**</span></span>  
    >[!NOTE]
    > <span data-ttu-id="afc5b-132">En plus de l’assembly EntityFramework une référence à System.ComponentModel.DataAnnotations est également ajoutée.</span><span class="sxs-lookup"><span data-stu-id="afc5b-132">In addition to the EntityFramework assembly a reference to System.ComponentModel.DataAnnotations is also added.</span></span> <span data-ttu-id="afc5b-133">Si le projet a une référence à System.Data.Entity, puis elle va être supprimée lorsque le package EntityFramework est installé.</span><span class="sxs-lookup"><span data-stu-id="afc5b-133">If the project has a reference to System.Data.Entity, then it will be removed when the EntityFramework package is installed.</span></span> <span data-ttu-id="afc5b-134">L’assembly System.Data.Entity est n’est plus utilisé pour les applications Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="afc5b-134">The System.Data.Entity assembly is no longer used for Entity Framework 6 applications.</span></span>

## <a name="define-a-model"></a><span data-ttu-id="afc5b-135">Définir un modèle</span><span class="sxs-lookup"><span data-stu-id="afc5b-135">Define a Model</span></span>

<span data-ttu-id="afc5b-136">Dans cette procédure pas à pas, que vous pouvez choisi d’implémenter un modèle à l’aide de Code First ou le Concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-136">In this walkthrough you can chose to implement a model using Code First or the EF Designer.</span></span> <span data-ttu-id="afc5b-137">Effectuez l’une des deux sections suivantes.</span><span class="sxs-lookup"><span data-stu-id="afc5b-137">Complete one of the two following sections.</span></span>

### <a name="option-1-define-a-model-using-code-first"></a><span data-ttu-id="afc5b-138">Option 1 : Définir un modèle à l’aide de Code First</span><span class="sxs-lookup"><span data-stu-id="afc5b-138">Option 1: Define a Model using Code First</span></span>

<span data-ttu-id="afc5b-139">Cette section montre comment créer un modèle et sa base de données associée à l’aide de Code First.</span><span class="sxs-lookup"><span data-stu-id="afc5b-139">This section shows how to create a model and its associated database using Code First.</span></span> <span data-ttu-id="afc5b-140">Passez à la section suivante (**Option 2 : définir un modèle à l’aide de la première base de données)** si vous préférez utiliser Database First pour inverser concevoir votre modèle à partir d’une base de données à l’aide du Concepteur EF</span><span class="sxs-lookup"><span data-stu-id="afc5b-140">Skip to the next section (**Option 2: Define a model using Database First)** if you would rather use Database First to reverse engineer your model from a database using the EF designer</span></span>

<span data-ttu-id="afc5b-141">Lors de l’utilisation du développement Code First vous commencez généralement par écriture de classes .NET Framework qui définissent votre modèle conceptuel (domaine).</span><span class="sxs-lookup"><span data-stu-id="afc5b-141">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span>

-   <span data-ttu-id="afc5b-142">Ajoutez une nouvelle classe à la **WPFwithEFSample :**</span><span class="sxs-lookup"><span data-stu-id="afc5b-142">Add a new class to the **WPFwithEFSample:**</span></span>
    -   <span data-ttu-id="afc5b-143">Avec le bouton droit sur le nom du projet</span><span class="sxs-lookup"><span data-stu-id="afc5b-143">Right-click on the project name</span></span>
    -   <span data-ttu-id="afc5b-144">Sélectionnez **ajouter**, puis **nouvel élément**</span><span class="sxs-lookup"><span data-stu-id="afc5b-144">Select **Add**, then **New Item**</span></span>
    -   <span data-ttu-id="afc5b-145">Sélectionnez **classe** et entrez **produit** pour le nom de classe</span><span class="sxs-lookup"><span data-stu-id="afc5b-145">Select **Class** and enter **Product** for the class name</span></span>
-   <span data-ttu-id="afc5b-146">Remplacez le **produit** définition avec le code suivant de la classe :</span><span class="sxs-lookup"><span data-stu-id="afc5b-146">Replace the **Product** class definition with the following code:</span></span>

``` csharp
    namespace WPFwithEFSample
    {
        public class Product
        {
            public int ProductId { get; set; }
            public string Name { get; set; }

            public int CategoryId { get; set; }
            public virtual Category Category { get; set; }
        }
    }

-   Add a **Category** class with the following definition:

    using System.Collections.ObjectModel;

    namespace WPFwithEFSample
    {
        public class Category
        {
            public Category()
            {
                this.Products = new ObservableCollection<Product>();
            }

            public int CategoryId { get; set; }
            public string Name { get; set; }

            public virtual ObservableCollection<Product> Products { get; private set; }
        }
    }
```

<span data-ttu-id="afc5b-147">Le **produits** propriété sur le **catégorie** classe et **catégorie** propriété sur le **produit** classe sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="afc5b-147">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="afc5b-148">Dans Entity Framework, les propriétés de navigation permettent de naviguer d’une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="afc5b-148">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="afc5b-149">Outre la définition des entités, vous devez définir une classe qui dérive de DbContext et expose DbSet&lt;TEntity&gt; propriétés.</span><span class="sxs-lookup"><span data-stu-id="afc5b-149">In addition to defining entities, you need to define a class that derives from DbContext and exposes DbSet&lt;TEntity&gt; properties.</span></span> <span data-ttu-id="afc5b-150">Le DbSet&lt;TEntity&gt; propriétés permettent le contexte de connaître les types que vous souhaitez inclure dans le modèle.</span><span class="sxs-lookup"><span data-stu-id="afc5b-150">The DbSet&lt;TEntity&gt; properties let the context know which types you want to include in the model.</span></span>

<span data-ttu-id="afc5b-151">Une instance du type DbContext dérivée gère les objets d’entité pendant l’exécution, ce qui inclut le remplissage des objets avec des données à partir d’une base de données, modifier le suivi et la persistance des données à la base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-151">An instance of the DbContext derived type manages the entity objects during run time, which includes populating objects with data from a database, change tracking, and persisting data to the database.</span></span>

-   <span data-ttu-id="afc5b-152">Ajouter un nouveau **ProductContext** classe au projet avec la définition suivante :</span><span class="sxs-lookup"><span data-stu-id="afc5b-152">Add a new **ProductContext** class to the project with the following definition:</span></span>

``` csharp
    using System.Data.Entity;

    namespace WPFwithEFSample
    {
        public class ProductContext : DbContext
        {
            public DbSet<Category> Categories { get; set; }
            public DbSet<Product> Products { get; set; }
        }
    }
```

<span data-ttu-id="afc5b-153">Compilez le projet.</span><span class="sxs-lookup"><span data-stu-id="afc5b-153">Compile the project.</span></span>

### <a name="option-2-define-a-model-using-database-first"></a><span data-ttu-id="afc5b-154">Option 2 : Définir un modèle à l’aide de la première base de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-154">Option 2: Define a model using Database First</span></span>

<span data-ttu-id="afc5b-155">Cette section montre comment utiliser la première base de données à l’ingénierie inverse de votre modèle à partir d’une base de données à l’aide du Concepteur EF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-155">This section shows how to use Database First to reverse engineer your model from a database using the EF designer.</span></span> <span data-ttu-id="afc5b-156">Si vous avez terminé la section précédente (**Option 1 : définir un modèle à l’aide de Code First)**, puis ignorez cette section et passer directement à la **le chargement différé** section.</span><span class="sxs-lookup"><span data-stu-id="afc5b-156">If you completed the previous section (**Option 1: Define a model using Code First)**, then skip this section and go straight to the **Lazy Loading** section.</span></span>

#### <a name="create-an-existing-database"></a><span data-ttu-id="afc5b-157">Créer une base de données existante</span><span class="sxs-lookup"><span data-stu-id="afc5b-157">Create an Existing Database</span></span>

<span data-ttu-id="afc5b-158">En général, lorsque vous ciblez une base de données existante, qu'il est déjà créé, mais pour cette procédure pas à pas, nous devons créer une base de données pour accéder à.</span><span class="sxs-lookup"><span data-stu-id="afc5b-158">Typically when you are targeting an existing database it will already be created, but for this walkthrough we need to create a database to access.</span></span>

<span data-ttu-id="afc5b-159">Le serveur de base de données qui est installé avec Visual Studio est différent selon la version de Visual Studio que vous avez installé :</span><span class="sxs-lookup"><span data-stu-id="afc5b-159">The database server that is installed with Visual Studio is different depending on the version of Visual Studio you have installed:</span></span>

-   <span data-ttu-id="afc5b-160">Si vous utilisez Visual Studio 2010, vous créerez une base de données SQL Express.</span><span class="sxs-lookup"><span data-stu-id="afc5b-160">If you are using Visual Studio 2010 you'll be creating a SQL Express database.</span></span>
-   <span data-ttu-id="afc5b-161">Si vous utilisez Visual Studio 2012, vous créerez un [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-161">If you are using Visual Studio 2012 then you'll be creating a [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx) database.</span></span>

<span data-ttu-id="afc5b-162">Procédons à générer la base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-162">Let's go ahead and generate the database.</span></span>

-   <span data-ttu-id="afc5b-163">**Vue -&gt; Explorateur de serveurs**</span><span class="sxs-lookup"><span data-stu-id="afc5b-163">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="afc5b-164">Cliquez avec le bouton droit sur **des connexions de données -&gt; ajouter une connexion...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-164">Right click on **Data Connections -&gt; Add Connection…**</span></span>
-   <span data-ttu-id="afc5b-165">Si vous n’avez pas connecté à une base de données à partir de l’Explorateur de serveurs avant que vous devez sélectionner Microsoft SQL Server comme source de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-165">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![ChangeDataSource](~/ef6/media/changedatasource.png)

-   <span data-ttu-id="afc5b-167">Se connecter à la base de données locale ou de SQL Express, en fonction de celles que vous avez installé, puis entrez **produits** en tant que le nom de la base de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-167">Connect to either LocalDB or SQL Express, depending on which one you have installed, and enter **Products** as the database name</span></span>

    ![AddConnectionLocalDB](~/ef6/media/addconnectionlocaldb.png)

    ![AddConnectionExpress](~/ef6/media/addconnectionexpress.png)

-   <span data-ttu-id="afc5b-170">Sélectionnez **OK** et vous demandera si vous souhaitez créer une base de données, sélectionnez **Oui**</span><span class="sxs-lookup"><span data-stu-id="afc5b-170">Select **OK** and you will be asked if you want to create a new database, select **Yes**</span></span>

    ![CreateDatabase](~/ef6/media/createdatabase.png)

-   <span data-ttu-id="afc5b-172">La nouvelle base de données s’affiche maintenant dans l’Explorateur de serveurs, avec le bouton droit dessus et sélectionnez **nouvelle requête**</span><span class="sxs-lookup"><span data-stu-id="afc5b-172">The new database will now appear in Server Explorer, right-click on it and select **New Query**</span></span>
-   <span data-ttu-id="afc5b-173">Copiez le code SQL suivant dans la nouvelle requête, puis avec le bouton droit sur la requête, puis sélectionnez **Execute**</span><span class="sxs-lookup"><span data-stu-id="afc5b-173">Copy the following SQL into the new query, then right-click on the query and select **Execute**</span></span>

``` SQL
    CREATE TABLE [dbo].[Categories] (
        [CategoryId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        CONSTRAINT [PK_dbo.Categories] PRIMARY KEY ([CategoryId])
    )

    CREATE TABLE [dbo].[Products] (
        [ProductId] [int] NOT NULL IDENTITY,
        [Name] [nvarchar](max),
        [CategoryId] [int] NOT NULL,
        CONSTRAINT [PK_dbo.Products] PRIMARY KEY ([ProductId])
    )

    CREATE INDEX [IX_CategoryId] ON [dbo].[Products]([CategoryId])

    ALTER TABLE [dbo].[Products] ADD CONSTRAINT [FK_dbo.Products_dbo.Categories_CategoryId] FOREIGN KEY ([CategoryId]) REFERENCES [dbo].[Categories] ([CategoryId]) ON DELETE CASCADE
```

#### <a name="reverse-engineer-model"></a><span data-ttu-id="afc5b-174">Modèle d’ingénierie à rebours</span><span class="sxs-lookup"><span data-stu-id="afc5b-174">Reverse Engineer Model</span></span>

<span data-ttu-id="afc5b-175">Nous allons utiliser Entity Framework Designer, qui est inclus dans le cadre de Visual Studio, pour créer notre modèle.</span><span class="sxs-lookup"><span data-stu-id="afc5b-175">We’re going to make use of Entity Framework Designer, which is included as part of Visual Studio, to create our model.</span></span>

-   <span data-ttu-id="afc5b-176">**Projet -&gt; ajouter un nouvel élément...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-176">**Project -&gt; Add New Item…**</span></span>
-   <span data-ttu-id="afc5b-177">Sélectionnez **données** dans le menu de gauche, puis **ADO.NET Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="afc5b-177">Select **Data** from the left menu and then **ADO.NET Entity Data Model**</span></span>
-   <span data-ttu-id="afc5b-178">Entrez **ProductModel** comme nom et cliquez sur **OK**</span><span class="sxs-lookup"><span data-stu-id="afc5b-178">Enter **ProductModel** as the name and click **OK**</span></span>
-   <span data-ttu-id="afc5b-179">Cette opération lance le **Assistant Entity Data Model**</span><span class="sxs-lookup"><span data-stu-id="afc5b-179">This launches the **Entity Data Model Wizard**</span></span>
-   <span data-ttu-id="afc5b-180">Sélectionnez **générer à partir de la base de données** et cliquez sur **suivant**</span><span class="sxs-lookup"><span data-stu-id="afc5b-180">Select **Generate from Database** and click **Next**</span></span>

    ![ChooseModelContents](~/ef6/media/choosemodelcontents.png)

-   <span data-ttu-id="afc5b-182">Sélectionnez la connexion à la base de données que vous avez créé dans la première section, entrez **ProductContext** comme nom de la chaîne de connexion et cliquez sur **suivant**</span><span class="sxs-lookup"><span data-stu-id="afc5b-182">Select the connection to the database you created in the first section, enter **ProductContext** as the name of the connection string and click **Next**</span></span>

    ![ChooseYourConnection](~/ef6/media/chooseyourconnection.png)

-   <span data-ttu-id="afc5b-184">Cliquez sur la case à cocher en regard de « Tables » pour importer toutes les tables, cliquez sur « Terminer »</span><span class="sxs-lookup"><span data-stu-id="afc5b-184">Click the checkbox next to ‘Tables’ to import all tables and click ‘Finish’</span></span>

    ![ChooseYourObjects](~/ef6/media/chooseyourobjects.png)

<span data-ttu-id="afc5b-186">Une fois que le processus d’ingénierie à rebours est terminé le nouveau modèle est ajouté à votre projet et ouvert pour l’afficher dans le Concepteur d’Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="afc5b-186">Once the reverse engineer process completes the new model is added to your project and opened up for you to view in the Entity Framework Designer.</span></span> <span data-ttu-id="afc5b-187">Un fichier App.config a également été ajouté à votre projet avec les détails de connexion pour la base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-187">An App.config file has also been added to your project with the connection details for the database.</span></span>

#### <a name="additional-steps-in-visual-studio-2010"></a><span data-ttu-id="afc5b-188">Étapes supplémentaires dans Visual Studio 2010</span><span class="sxs-lookup"><span data-stu-id="afc5b-188">Additional Steps in Visual Studio 2010</span></span>

<span data-ttu-id="afc5b-189">Si vous travaillez dans Visual Studio 2010 vous devrez mettre à jour le Concepteur EF pour utiliser la génération de code EF6.</span><span class="sxs-lookup"><span data-stu-id="afc5b-189">If you are working in Visual Studio 2010 then you will need to update the EF designer to use EF6 code generation.</span></span>

-   <span data-ttu-id="afc5b-190">Avec le bouton droit sur un endroit vide de votre modèle dans le Concepteur EF et sélectionnez **ajouter un élément de génération de Code...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-190">Right-click on an empty spot of your model in the EF Designer and select **Add Code Generation Item…**</span></span>
-   <span data-ttu-id="afc5b-191">Sélectionnez **modèles en ligne** dans le menu de gauche et recherchez **DbContext**</span><span class="sxs-lookup"><span data-stu-id="afc5b-191">Select **Online Templates** from the left menu and search for **DbContext**</span></span>
-   <span data-ttu-id="afc5b-192">Sélectionnez le **EF 6.x générateur DbContext pour C\#,** entrez **ProductsModel** comme nom et cliquez sur Ajouter</span><span class="sxs-lookup"><span data-stu-id="afc5b-192">Select the **EF 6.x DbContext Generator for C\#,** enter **ProductsModel** as the name and click Add</span></span>

#### <a name="updating-code-generation-for-data-binding"></a><span data-ttu-id="afc5b-193">Mise à jour de la génération de code pour la liaison de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-193">Updating code generation for data binding</span></span>

<span data-ttu-id="afc5b-194">Entity Framework génère du code à partir de votre modèle à l’aide de modèles T4.</span><span class="sxs-lookup"><span data-stu-id="afc5b-194">EF generates code from your model using T4 templates.</span></span> <span data-ttu-id="afc5b-195">Les modèles fournis avec Visual Studio ou téléchargé à partir de la galerie Visual Studio sont destinés à usage général.</span><span class="sxs-lookup"><span data-stu-id="afc5b-195">The templates shipped with Visual Studio or downloaded from the Visual Studio gallery are intended for general purpose use.</span></span> <span data-ttu-id="afc5b-196">Cela signifie que les entités générées à partir de ces modèles ont ICollection simple&lt;T&gt; propriétés.</span><span class="sxs-lookup"><span data-stu-id="afc5b-196">This means that the entities generated from these templates have simple ICollection&lt;T&gt; properties.</span></span> <span data-ttu-id="afc5b-197">Toutefois, lors de données de la liaison à l’aide de WPF il est souhaitable d’utiliser **ObservableCollection** pour les propriétés de collection afin que WPF peut effectuer le suivi de modifications apportées aux collections.</span><span class="sxs-lookup"><span data-stu-id="afc5b-197">However, when doing data binding using WPF it is desirable to use **ObservableCollection** for collection properties so that WPF can keep track of changes made to the collections.</span></span> <span data-ttu-id="afc5b-198">Dans cette optique, nous allons pour modifier les modèles pour utiliser ObservableCollection.</span><span class="sxs-lookup"><span data-stu-id="afc5b-198">To this end we will to modify the templates to use ObservableCollection.</span></span>

-   <span data-ttu-id="afc5b-199">Ouvrez le **l’Explorateur de solutions** et recherchez **ProductModel.edmx** fichier</span><span class="sxs-lookup"><span data-stu-id="afc5b-199">Open the **Solution Explorer** and find **ProductModel.edmx** file</span></span>
-   <span data-ttu-id="afc5b-200">Rechercher la **ProductModel.tt** fichier qui doit être imbriqué sous le fichier ProductModel.edmx</span><span class="sxs-lookup"><span data-stu-id="afc5b-200">Find the **ProductModel.tt** file which will be nested under the ProductModel.edmx file</span></span>

    ![WpfProductModelTemplate](~/ef6/media/wpfproductmodeltemplate.png)

-   <span data-ttu-id="afc5b-202">Double-cliquez sur le fichier ProductModel.tt pour l’ouvrir dans l’éditeur Visual Studio</span><span class="sxs-lookup"><span data-stu-id="afc5b-202">Double-click on the ProductModel.tt file to open it in the Visual Studio editor</span></span>
-   <span data-ttu-id="afc5b-203">Rechercher et remplacer les deux occurrences de «**ICollection**« avec »**ObservableCollection**».</span><span class="sxs-lookup"><span data-stu-id="afc5b-203">Find and replace the two occurrences of “**ICollection**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="afc5b-204">Il s’agit trouve environ à lignes 296 et 484.</span><span class="sxs-lookup"><span data-stu-id="afc5b-204">These are located approximately at lines 296 and 484.</span></span>
-   <span data-ttu-id="afc5b-205">Rechercher et remplacer la première occurrence de «**HashSet**« avec »**ObservableCollection**».</span><span class="sxs-lookup"><span data-stu-id="afc5b-205">Find and replace the first occurrence of “**HashSet**” with “**ObservableCollection**”.</span></span> <span data-ttu-id="afc5b-206">Cet événement se trouve approximativement à la ligne 50.</span><span class="sxs-lookup"><span data-stu-id="afc5b-206">This occurrence is located approximately at line 50.</span></span> <span data-ttu-id="afc5b-207">**Ne le faites pas** remplacer la deuxième occurrence de HashSet figure plus loin dans le code.</span><span class="sxs-lookup"><span data-stu-id="afc5b-207">**Do not** replace the second occurrence of HashSet found later in the code.</span></span>
-   <span data-ttu-id="afc5b-208">Rechercher et remplacer la seule occurrence de «**System.Collections.Generic**« avec »**System.Collections.ObjectModel**».</span><span class="sxs-lookup"><span data-stu-id="afc5b-208">Find and replace the only occurrence of “**System.Collections.Generic**” with “**System.Collections.ObjectModel**”.</span></span> <span data-ttu-id="afc5b-209">Cela se trouve approximativement à la ligne 424.</span><span class="sxs-lookup"><span data-stu-id="afc5b-209">This is located approximately at line 424.</span></span>
-   <span data-ttu-id="afc5b-210">Enregistrez le fichier ProductModel.tt.</span><span class="sxs-lookup"><span data-stu-id="afc5b-210">Save the ProductModel.tt file.</span></span> <span data-ttu-id="afc5b-211">Cela doit provoquer le code pour les entités d’être régénérée.</span><span class="sxs-lookup"><span data-stu-id="afc5b-211">This should cause the code for entities to be regenerated.</span></span> <span data-ttu-id="afc5b-212">Si le code ne régénère pas automatiquement, puis avec le bouton droit sur ProductModel.tt et choisissez « Exécuter un outil personnalisé ».</span><span class="sxs-lookup"><span data-stu-id="afc5b-212">If the code does not regenerate automatically, then right click on ProductModel.tt and choose “Run Custom Tool”.</span></span>

<span data-ttu-id="afc5b-213">Si vous ouvrez le fichier Category.cs (qui est imbriqué sous ProductModel.tt), vous devez voir que la collection de produits a le type **ObservableCollection&lt;produit&gt;**.</span><span class="sxs-lookup"><span data-stu-id="afc5b-213">If you now open the Category.cs file (which is nested under ProductModel.tt) then you should see that the Products collection has the type **ObservableCollection&lt;Product&gt;**.</span></span>

<span data-ttu-id="afc5b-214">Compilez le projet.</span><span class="sxs-lookup"><span data-stu-id="afc5b-214">Compile the project.</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="afc5b-215">Chargement différé</span><span class="sxs-lookup"><span data-stu-id="afc5b-215">Lazy Loading</span></span>

<span data-ttu-id="afc5b-216">Le **produits** propriété sur le **catégorie** classe et **catégorie** propriété sur le **produit** classe sont des propriétés de navigation.</span><span class="sxs-lookup"><span data-stu-id="afc5b-216">The **Products** property on the **Category** class and **Category** property on the **Product** class are navigation properties.</span></span> <span data-ttu-id="afc5b-217">Dans Entity Framework, les propriétés de navigation permettent de naviguer d’une relation entre deux types d’entités.</span><span class="sxs-lookup"><span data-stu-id="afc5b-217">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span>

<span data-ttu-id="afc5b-218">Entity Framework vous offre une option de chargement des entités connexes à partir de la base de données automatiquement la première fois que vous accédez à la propriété de navigation.</span><span class="sxs-lookup"><span data-stu-id="afc5b-218">EF gives you an option of loading related entities from the database automatically the first time you access the navigation property.</span></span> <span data-ttu-id="afc5b-219">Avec ce type de chargement (appelé chargement différé), n’oubliez pas que la première fois que vous accédez à chaque propriété de navigation une requête distincte sera exécutée la base de données si le contenu n’est pas déjà dans le contexte.</span><span class="sxs-lookup"><span data-stu-id="afc5b-219">With this type of loading (called lazy loading), be aware that the first time you access each navigation property a separate query will be executed against the database if the contents are not already in the context.</span></span>

<span data-ttu-id="afc5b-220">Lorsque vous utilisez des types d’entités POCO, EF réalise le chargement différé par la création d’instances de types de proxy dérivée pendant l’exécution, puis en remplaçant les propriétés virtuelles dans vos classes pour ajouter le raccordement de chargement.</span><span class="sxs-lookup"><span data-stu-id="afc5b-220">When using POCO entity types, EF achieves lazy loading by creating instances of derived proxy types during runtime and then overriding virtual properties in your classes to add the loading hook.</span></span> <span data-ttu-id="afc5b-221">Pour obtenir le chargement différé d’objets connexes, vous devez déclarer les accesseurs Get de propriété en tant que navigation **public** et **virtuels** (**Overridable** en Visual Basic), et vous classe ne doit pas être **sealed** (**NotOverridable** en Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="afc5b-221">To get lazy loading of related objects, you must declare navigation property getters as **public** and **virtual** (**Overridable** in Visual Basic), and you class must not be **sealed** (**NotOverridable** in Visual Basic).</span></span> <span data-ttu-id="afc5b-222">Lors de la base de données à l’aide des propriétés de navigation premier sont automatiquement effectuées virtuelles pour activer le chargement différé.</span><span class="sxs-lookup"><span data-stu-id="afc5b-222">When using Database First navigation properties are automatically made virtual to enable lazy loading.</span></span> <span data-ttu-id="afc5b-223">Dans la section de Code First que nous avons choisi créer les propriétés de navigation virtuelle pour la même raison</span><span class="sxs-lookup"><span data-stu-id="afc5b-223">In the Code First section we chose to make the navigation properties virtual for the same reason</span></span>

## <a name="bind-object-to-controls"></a><span data-ttu-id="afc5b-224">Lier des objets aux contrôles</span><span class="sxs-lookup"><span data-stu-id="afc5b-224">Bind Object to Controls</span></span>

<span data-ttu-id="afc5b-225">Ajoutez les classes qui sont définies dans le modèle en tant que sources de données pour cette application WPF.</span><span class="sxs-lookup"><span data-stu-id="afc5b-225">Add the classes that are defined in the model as data sources for this WPF application.</span></span>

-   <span data-ttu-id="afc5b-226">Double-cliquez sur **MainWindow.xaml** dans l’Explorateur de solutions pour ouvrir le formulaire principal</span><span class="sxs-lookup"><span data-stu-id="afc5b-226">Double-click **MainWindow.xaml** in Solution Explorer to open the main form</span></span>
-   <span data-ttu-id="afc5b-227">Dans le menu principal, sélectionnez **projet -&gt; ajouter une nouvelle Source de données...**</span><span class="sxs-lookup"><span data-stu-id="afc5b-227">From the main menu, select **Project -&gt; Add New Data Source …**</span></span>
    <span data-ttu-id="afc5b-228">(dans Visual Studio 2010, vous devez sélectionner **données -&gt; ajouter nouvelle Source de données...** )</span><span class="sxs-lookup"><span data-stu-id="afc5b-228">(in Visual Studio 2010, you need to select **Data -&gt; Add New Data Source…**)</span></span>
-   <span data-ttu-id="afc5b-229">Dans la zone Choisir un Typewindow de Source de données, sélectionnez **objet** et cliquez sur **suivant**</span><span class="sxs-lookup"><span data-stu-id="afc5b-229">In the Choose a Data Source Typewindow, select **Object** and click **Next**</span></span>
-   <span data-ttu-id="afc5b-230">Dans le, sélectionnez la boîte de dialogue des objets de données, dérouler les **WPFwithEFSample** deux fois, puis sélectionnez **catégorie**</span><span class="sxs-lookup"><span data-stu-id="afc5b-230">In the Select the Data Objects dialog, unfold the **WPFwithEFSample** two times and select **Category**</span></span>  
    <span data-ttu-id="afc5b-231">*Il est inutile de sélectionner le **produit** de source de données, car nous le verrons par le biais le **produit**de propriété sur le **catégorie** source de données*</span><span class="sxs-lookup"><span data-stu-id="afc5b-231">*There is no need to select the **Product** data source, because we will get to it through the **Product**’s property on the **Category** data source*</span></span>  

    ![SelectDataObjects](~/ef6/media/selectdataobjects.png)

-   <span data-ttu-id="afc5b-233">Cliquez sur **terminer.**</span><span class="sxs-lookup"><span data-stu-id="afc5b-233">Click **Finish.**</span></span>
-   <span data-ttu-id="afc5b-234">La fenêtre Sources de données est ouverte en regard de la fenêtre de MainWindow.xaml *si la fenêtre Sources de données ne s’affichent pas, sélectionnez **vue -&gt; autres Windows -&gt; des Sources de données***</span><span class="sxs-lookup"><span data-stu-id="afc5b-234">The Data Sources window is opened next to the MainWindow.xaml window *If the Data Sources window is not showing up, select **View -&gt; Other Windows-&gt; Data Sources***</span></span>
-   <span data-ttu-id="afc5b-235">Appuyez sur l’icône d’épingle, afin de la fenêtre Sources de données ne sont pas automatique masquer.</span><span class="sxs-lookup"><span data-stu-id="afc5b-235">Press the pin icon, so the Data Sources window does not auto hide.</span></span> <span data-ttu-id="afc5b-236">Vous devrez peut-être appuyer sur le bouton de rafraîchissement si la fenêtre a été déjà visible.</span><span class="sxs-lookup"><span data-stu-id="afc5b-236">You may need to hit the refresh button if the window was already visible.</span></span>

    ![DataSources](~/ef6/media/datasources.png)

-   <span data-ttu-id="afc5b-238">Sélectionnez le ** catégorie ** source de données et faites-la glisser sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="afc5b-238">Select the **Category **data source and drag it on the form.</span></span>

<span data-ttu-id="afc5b-239">Les éléments suivants s’est produite lorsque nous avons fait glisser cette source :</span><span class="sxs-lookup"><span data-stu-id="afc5b-239">The following happened when we dragged this source:</span></span>

-   <span data-ttu-id="afc5b-240">Le **categoryViewSource** ressource et le ** categoryDataGrid ** contrôle ont été ajoutés à XAML.</span><span class="sxs-lookup"><span data-stu-id="afc5b-240">The **categoryViewSource** resource and the** categoryDataGrid** control were added to XAML.</span></span> <span data-ttu-id="afc5b-241">Pour plus d’informations sur DataViewSources, consultez http://bea.stollnitz.com/blog/?p=387.</span><span class="sxs-lookup"><span data-stu-id="afc5b-241">For more information about DataViewSources, see http://bea.stollnitz.com/blog/?p=387.</span></span>
-   <span data-ttu-id="afc5b-242">La propriété DataContext sur l’élément de grille parent a été définie sur « {StaticResource **categoryViewSource** } ».</span><span class="sxs-lookup"><span data-stu-id="afc5b-242">The DataContext property on the parent Grid element was set to "{StaticResource **categoryViewSource** }".</span></span>  <span data-ttu-id="afc5b-243">Le **categoryViewSource** ressources servant de source de liaison externe\\élément de grille parent.</span><span class="sxs-lookup"><span data-stu-id="afc5b-243">The **categoryViewSource** resource serves as a binding source for the outer\\parent Grid element.</span></span> <span data-ttu-id="afc5b-244">Les éléments internes de la grille puis héritent de la valeur DataContext parent grille (propriété ItemsSource de la categoryDataGrid est définie sur « {Binding} »).</span><span class="sxs-lookup"><span data-stu-id="afc5b-244">The inner Grid elements then inherit the DataContext value from the parent Grid (the categoryDataGrid’s ItemsSource property is set to "{Binding}").</span></span> 

``` xml
    <Window.Resources>
        <CollectionViewSource x:Key="categoryViewSource"
                                d:DesignSource="{d:DesignInstance {x:Type local:Category}, CreateList=True}"/>
    </Window.Resources>
    <Grid DataContext="{StaticResource categoryViewSource}">
        <DataGrid x:Name="categoryDataGrid" AutoGenerateColumns="False" EnableRowVirtualization="True"
                    ItemsSource="{Binding}" Margin="13,13,43,191"
                    RowDetailsVisibilityMode="VisibleWhenSelected">
            <DataGrid.Columns>
                <DataGridTextColumn x:Name="categoryIdColumn" Binding="{Binding CategoryId}"
                                    Header="Category Id" Width="SizeToHeader"/>
                <DataGridTextColumn x:Name="nameColumn" Binding="{Binding Name}"
                                    Header="Name" Width="SizeToHeader"/>
            </DataGrid.Columns>
        </DataGrid>
    </Grid>
```

## <a name="adding-a-details-grid"></a><span data-ttu-id="afc5b-245">Ajout d’une grille de détails</span><span class="sxs-lookup"><span data-stu-id="afc5b-245">Adding a Details Grid</span></span>

<span data-ttu-id="afc5b-246">Maintenant que nous avons une grille pour afficher les catégories de nous allons ajouter une grille de détails pour afficher les produits associés.</span><span class="sxs-lookup"><span data-stu-id="afc5b-246">Now that we have a grid to display Categories let's add a details grid to display the associated Products.</span></span>

-   <span data-ttu-id="afc5b-247">Sélectionnez le ** produits ** propriété sous la ** catégorie ** source de données et faites-la glisser sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="afc5b-247">Select the **Products **property from under the **Category **data source and drag it on the form.</span></span>
    -   <span data-ttu-id="afc5b-248">Le **categoryProductsViewSource** ressource et **productDataGrid** grille sont ajoutés à XAML</span><span class="sxs-lookup"><span data-stu-id="afc5b-248">The **categoryProductsViewSource** resource and **productDataGrid** grid are added to XAML</span></span>
    -   <span data-ttu-id="afc5b-249">Le chemin de liaison pour cette ressource est défini pour les produits</span><span class="sxs-lookup"><span data-stu-id="afc5b-249">The binding path for this resource is set to Products</span></span>
    -   <span data-ttu-id="afc5b-250">Infrastructure de liaison de données WPF permet de s’assurer que seuls les produits liés à la catégorie sélectionnée apparaissent dans **productDataGrid**</span><span class="sxs-lookup"><span data-stu-id="afc5b-250">WPF data-binding framework ensures that only Products related to the selected Category show up in **productDataGrid**</span></span>
-   <span data-ttu-id="afc5b-251">Dans la boîte à outils, faites glisser **bouton** une session sur le formulaire.</span><span class="sxs-lookup"><span data-stu-id="afc5b-251">From the Toolbox, drag **Button** on to the form.</span></span> <span data-ttu-id="afc5b-252">Définir le **nom** propriété **buttonSave** et **contenu** propriété **enregistrer**.</span><span class="sxs-lookup"><span data-stu-id="afc5b-252">Set the **Name** property to **buttonSave** and the **Content** property to **Save**.</span></span>

<span data-ttu-id="afc5b-253">Le formulaire doit ressembler à ceci :</span><span class="sxs-lookup"><span data-stu-id="afc5b-253">The form should look similar to this:</span></span>

![Designer](~/ef6/media/designer.png) 

## <a name="add-code-that-handles-data-interaction"></a><span data-ttu-id="afc5b-255">Ajoutez le Code qui gère l’Interaction de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-255">Add Code that Handles Data Interaction</span></span>

<span data-ttu-id="afc5b-256">Il est temps d’ajouter des gestionnaires d’événements à la fenêtre principale.</span><span class="sxs-lookup"><span data-stu-id="afc5b-256">It's time to add some event handlers to the main window.</span></span>

-   <span data-ttu-id="afc5b-257">Dans la fenêtre XAML, cliquez sur le  **&lt;fenêtre** élément, cette opération sélectionne la fenêtre principale</span><span class="sxs-lookup"><span data-stu-id="afc5b-257">In the XAML window, click on the **&lt;Window** element, this selects the main window</span></span>
-   <span data-ttu-id="afc5b-258">Dans le **propriétés** fenêtre choisissez **événements** en haut à droite, puis double-cliquez sur la zone de texte à droite de la **Loaded** étiquette</span><span class="sxs-lookup"><span data-stu-id="afc5b-258">In the **Properties** window choose **Events** at the top right, then double-click the text box to right of the **Loaded** label</span></span>

    ![MainWindowProperties](~/ef6/media/mainwindowproperties.png)

-   <span data-ttu-id="afc5b-260">Également ajouter le **cliquez sur** événement pour le **enregistrer** bouton en double-cliquant sur le bouton Enregistrer dans le concepteur.</span><span class="sxs-lookup"><span data-stu-id="afc5b-260">Also add the **Click** event for the **Save** button by double-clicking the Save button in the designer.</span></span> 

<span data-ttu-id="afc5b-261">Cela vous amène à du code-behind pour le formulaire, nous allons maintenant modifier le code pour utiliser le ProductContext pour accéder aux données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-261">This brings you to the code behind for the form, we'll now edit the code to use the ProductContext to perform data access.</span></span> <span data-ttu-id="afc5b-262">Mettre à jour le code pour le MainWindow comme indiqué ci-dessous.</span><span class="sxs-lookup"><span data-stu-id="afc5b-262">Update the code for the MainWindow as shown below.</span></span>

<span data-ttu-id="afc5b-263">Le code déclare une instance d’exécution longue de **ProductContext**.</span><span class="sxs-lookup"><span data-stu-id="afc5b-263">The code declares a long-running instance of **ProductContext**.</span></span> <span data-ttu-id="afc5b-264">Le **ProductContext** objet est utilisé pour interroger et enregistrer les données dans la base de données.</span><span class="sxs-lookup"><span data-stu-id="afc5b-264">The **ProductContext** object is used to query and save data to the database.</span></span> <span data-ttu-id="afc5b-265">Le **Dispose**() sur le **ProductContext** instance est ensuite appelée à partir de l’élément substitué **OnClosing** (méthode).</span><span class="sxs-lookup"><span data-stu-id="afc5b-265">The **Dispose**() on the **ProductContext** instance is then called from the overridden **OnClosing** method.</span></span> <span data-ttu-id="afc5b-266">Les commentaires du code fournissent des informations sur ce que fait le code.</span><span class="sxs-lookup"><span data-stu-id="afc5b-266">The code comments provide details about what the code does.</span></span>

``` csharp
    using System.Data.Entity;
    using System.Linq;
    using System.Windows;

    namespace WPFwithEFSample
    {
        public partial class MainWindow : Window
        {
            private ProductContext _context = new ProductContext();
            public MainWindow()
            {
                InitializeComponent();
            }

            private void Window_Loaded(object sender, RoutedEventArgs e)
            {
                System.Windows.Data.CollectionViewSource categoryViewSource =
                    ((System.Windows.Data.CollectionViewSource)(this.FindResource("categoryViewSource")));

                // Load is an extension method on IQueryable,
                // defined in the System.Data.Entity namespace.
                // This method enumerates the results of the query,
                // similar to ToList but without creating a list.
                // When used with Linq to Entities this method
                // creates entity objects and adds them to the context.
                _context.Categories.Load();

                // After the data is loaded call the DbSet<T>.Local property
                // to use the DbSet<T> as a binding source.
                categoryViewSource.Source = _context.Categories.Local;
            }

            private void buttonSave_Click(object sender, RoutedEventArgs e)
            {
                // When you delete an object from the related entities collection
                // (in this case Products), the Entity Framework doesn’t mark
                // these child entities as deleted.
                // Instead, it removes the relationship between the parent and the child
                // by setting the parent reference to null.
                // So we manually have to delete the products
                // that have a Category reference set to null.

                // The following code uses LINQ to Objects
                // against the Local collection of Products.
                // The ToList call is required because otherwise the collection will be modified
                // by the Remove call while it is being enumerated.
                // In most other situations you can use LINQ to Objects directly
                // against the Local property without using ToList first.
                foreach (var product in _context.Products.Local.ToList())
                {
                    if (product.Category == null)
                    {
                        _context.Products.Remove(product);
                    }
                }

                _context.SaveChanges();
                // Refresh the grids so the database generated values show up.
                this.categoryDataGrid.Items.Refresh();
                this.productsDataGrid.Items.Refresh();
            }

            protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
            {
                base.OnClosing(e);
                this._context.Dispose();
            }
        }

    }
```

## <a name="test-the-wpf-application"></a><span data-ttu-id="afc5b-267">Tester l’Application WPF</span><span class="sxs-lookup"><span data-stu-id="afc5b-267">Test the WPF Application</span></span>

-   <span data-ttu-id="afc5b-268">Compilez et exécutez l'application.</span><span class="sxs-lookup"><span data-stu-id="afc5b-268">Compile and run the application.</span></span> <span data-ttu-id="afc5b-269">Si vous avez utilisé un Code First, vous verrez qu’un **WPFwithEFSample.ProductContext** base de données est créée pour vous.</span><span class="sxs-lookup"><span data-stu-id="afc5b-269">If you used Code First, then you will see that a **WPFwithEFSample.ProductContext** database is created for you.</span></span>
-   <span data-ttu-id="afc5b-270">Entrez un nom de catégorie dans les noms de produit et de la grille supérieures dans la grille inférieure *n’entrez pas quoi que ce soit dans les colonnes ID, car la clé primaire est générée par la base de données*</span><span class="sxs-lookup"><span data-stu-id="afc5b-270">Enter a category name in the top grid and product names in the bottom grid *Do not enter anything in ID columns, because the primary key is generated by the database*</span></span>

    ![Screen1](~/ef6/media/screen1.png)

-   <span data-ttu-id="afc5b-272">Appuyez sur la **enregistrer** bouton pour enregistrer les données dans la base de données</span><span class="sxs-lookup"><span data-stu-id="afc5b-272">Press the **Save** button to save the data to the database</span></span>

<span data-ttu-id="afc5b-273">Après l’appel à du DbContext **SaveChanges**(), les ID sont remplies avec les valeurs de la base de données générée.</span><span class="sxs-lookup"><span data-stu-id="afc5b-273">After the call to DbContext’s **SaveChanges**(), the IDs are populated with the database generated values.</span></span> <span data-ttu-id="afc5b-274">Étant donné que nous avons appelé **Actualiser**() après **SaveChanges**() le **DataGrid** contrôles sont mis à jour avec les nouvelles valeurs également.</span><span class="sxs-lookup"><span data-stu-id="afc5b-274">Because we called **Refresh**() after **SaveChanges**() the **DataGrid** controls are updated with the new values as well.</span></span>

![Écran2](~/ef6/media/screen2.png)