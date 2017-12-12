---
title: "En alternant entre plusieurs modèles avec le même type DbContext - EF Core"
author: AndriySvyryd
uid: core/modeling/dynamic-model
ms.openlocfilehash: e6828c62c55ae6f48d46a20528268264c3f22b26
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2017
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="835a3-102">En alternant entre plusieurs modèles avec le même type de DbContext</span><span class="sxs-lookup"><span data-stu-id="835a3-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="835a3-103">Le modèle généré `OnModelCreating` Impossible d’utiliser une propriété sur le contexte pour modifier la façon dont le modèle est construit.</span><span class="sxs-lookup"><span data-stu-id="835a3-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="835a3-104">Par exemple, il peut être utilisé pour exclure une propriété donnée :</span><span class="sxs-lookup"><span data-stu-id="835a3-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="835a3-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="835a3-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="835a3-106">Toutefois si vous avez essayé de faire ci-dessus sans apporter de modifications supplémentaires vous pourriez obtenir le même modèle chaque fois qu’un nouveau contexte est créé pour toute valeur de `IgnoreIntProperty`.</span><span class="sxs-lookup"><span data-stu-id="835a3-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="835a3-107">Cela est provoqué par le modèle de mise en cache de mécanisme EF utilise pour améliorer les performances en appelant uniquement `OnModelCreating` qu’une seule fois et le modèle de mise en cache.</span><span class="sxs-lookup"><span data-stu-id="835a3-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="835a3-108">Par défaut EF suppose que pour n’importe quel contexte donné le type du modèle sera le même.</span><span class="sxs-lookup"><span data-stu-id="835a3-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="835a3-109">Pour cela l’implémentation par défaut de `IModelCacheKeyFactory` retourne une clé qui contient seulement le type de contexte.</span><span class="sxs-lookup"><span data-stu-id="835a3-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="835a3-110">Pour modifier ce vous devez remplacer le `IModelCacheKeyFactory` service.</span><span class="sxs-lookup"><span data-stu-id="835a3-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="835a3-111">La nouvelle implémentation doit retourner un objet qui peut être comparé à d’autres clés de modèle à l’aide de la `Equals` méthode qui prend en compte toutes les variables qui affectent le modèle :</span><span class="sxs-lookup"><span data-stu-id="835a3-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]