---
title: "Fournisseur de base de données IBM Data Server (DB2) - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 02/15/2017
ms.assetid: 825e5332-5aa3-4600-9efb-ab71aaff59ec
ms.technology: entity-framework-core
uid: core/providers/ibm/index
ms.openlocfilehash: a9caa8df63850d4f6b5f2164dad7ac5af7504076
ms.sourcegitcommit: 5e2d97e731f975cf3405ff3deab2a3c75ad1b969
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="ibm-data-server-db2-ef-core-database-providers"></a><span data-ttu-id="d2463-102">Fournisseurs de base de données EF Core IBM Data Server (DB2)</span><span class="sxs-lookup"><span data-stu-id="d2463-102">IBM Data Server (DB2) EF Core Database Providers</span></span>

<span data-ttu-id="d2463-103">Ce fournisseur de base de données permet d’utiliser Entity Framework Core avec IBM Data Server.</span><span class="sxs-lookup"><span data-stu-id="d2463-103">This database provider allows Entity Framework Core to be used with IBM Data Server.</span></span> <span data-ttu-id="d2463-104">Le fournisseur est géré par IBM.</span><span class="sxs-lookup"><span data-stu-id="d2463-104">The provider is maintained by IBM.</span></span>

> [!NOTE]  
> <span data-ttu-id="d2463-105">Il n’est pas géré dans le cadre du projet Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d2463-105">This provider is not maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="d2463-106">Quand vous envisagez un fournisseur tiers, veillez à évaluer la qualité, la gestion des licences, la prise en charge, etc., pour vous assurer qu’il répond à vos besoins.</span><span class="sxs-lookup"><span data-stu-id="d2463-106">When considering a third party provider, be sure to evaluate quality, licensing, support, etc. to ensure they meet your requirements.</span></span>

## <a name="install"></a><span data-ttu-id="d2463-107">Installer</span><span class="sxs-lookup"><span data-stu-id="d2463-107">Install</span></span>

<span data-ttu-id="d2463-108">Pour utiliser IBM Data Server sur Windows, installez le [package NuGet IBM.EntityFrameworkCore](https://www.nuget.org/packages/IBM.EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="d2463-108">To work with IBM Data Server on Windows, install the [IBM.EntityFrameworkCore NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore
```

<span data-ttu-id="d2463-109">Pour utiliser IBM Data Server sur Linux, installez le [package NuGet IBM.EntityFrameworkCore-lnx](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx).</span><span class="sxs-lookup"><span data-stu-id="d2463-109">To work with IBM Data Server on Linux, install the [IBM.EntityFrameworkCore-lnx NuGet package](https://www.nuget.org/packages/IBM.EntityFrameworkCore-lnx).</span></span>

``` powershell
Install-Package IBM.EntityFrameworkCore-lnx
```

## <a name="get-started"></a><span data-ttu-id="d2463-110">Bien démarrer</span><span class="sxs-lookup"><span data-stu-id="d2463-110">Get Started</span></span>

<span data-ttu-id="d2463-111">[Getting started with IBM .NET Provider for .NET Core](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en) (Bien démarrer avec le fournisseur IBM .NET pour .NET Core)</span><span class="sxs-lookup"><span data-stu-id="d2463-111">[Getting started with IBM .NET Provider for .NET Core](https://www.ibm.com/developerworks/community/blogs/96960515-2ea1-4391-8170-b0515d08e4da/entry/DB2DotnetCore?lang=en)</span></span>

## <a name="supported-database-engines"></a><span data-ttu-id="d2463-112">Moteurs de base de données pris en charge</span><span class="sxs-lookup"><span data-stu-id="d2463-112">Supported Database Engines</span></span>

* <span data-ttu-id="d2463-113">zOS</span><span class="sxs-lookup"><span data-stu-id="d2463-113">zOS</span></span>
* <span data-ttu-id="d2463-114">LUW, y compris IBM dashDB</span><span class="sxs-lookup"><span data-stu-id="d2463-114">LUW including IBM dashDB</span></span>
* <span data-ttu-id="d2463-115">IBM I</span><span class="sxs-lookup"><span data-stu-id="d2463-115">IBM I</span></span>
* <span data-ttu-id="d2463-116">Informix</span><span class="sxs-lookup"><span data-stu-id="d2463-116">Informix</span></span>

## <a name="supported-platforms"></a><span data-ttu-id="d2463-117">Plateformes prises en charge</span><span class="sxs-lookup"><span data-stu-id="d2463-117">Supported Platforms</span></span>

* <span data-ttu-id="d2463-118">.NET Framework (versions 4.6 et suivantes)</span><span class="sxs-lookup"><span data-stu-id="d2463-118">.NET Framework (4.6 onwards)</span></span>
* <span data-ttu-id="d2463-119">.NET Core</span><span class="sxs-lookup"><span data-stu-id="d2463-119">.NET Core</span></span>