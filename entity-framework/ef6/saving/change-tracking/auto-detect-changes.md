---
title: Automático detectar alterações - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
caps.latest.revision: 3
ms.openlocfilehash: 62f2f026426346fc1230a2f5743c8cb7d232ec7f
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39119709"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="8dab0-102">Automático detectar alterações</span><span class="sxs-lookup"><span data-stu-id="8dab0-102">Automatic detect changes</span></span>
<span data-ttu-id="8dab0-103">Ao usar a maioria das entidades POCO a determinação de como uma entidade foi alterada (e, portanto, quais atualizações precisam ser enviados para o banco de dados) é tratada pelo algoritmo detectar alterações.</span><span class="sxs-lookup"><span data-stu-id="8dab0-103">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="8dab0-104">Detecte works alterações Detectando as diferenças entre os valores de propriedade atuais da entidade e os valores de propriedade originais que são armazenados em um instantâneo quando a entidade foi consultada ou anexada.</span><span class="sxs-lookup"><span data-stu-id="8dab0-104">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="8dab0-105">As técnicas mostradas neste tópico se aplicam igualmente a modelos criados com o Code First e com o EF Designer.</span><span class="sxs-lookup"><span data-stu-id="8dab0-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="8dab0-106">Por padrão, o Entity Framework executa detectar alterações automaticamente quando são chamados de métodos a seguir:</span><span class="sxs-lookup"><span data-stu-id="8dab0-106">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="8dab0-107">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="8dab0-107">DbSet.Find</span></span>  
- <span data-ttu-id="8dab0-108">DbSet</span><span class="sxs-lookup"><span data-stu-id="8dab0-108">DbSet.Local</span></span>  
- <span data-ttu-id="8dab0-109">DbSet</span><span class="sxs-lookup"><span data-stu-id="8dab0-109">DbSet.Add</span></span>  
- <span data-ttu-id="8dab0-110">DbSet.AddRange</span><span class="sxs-lookup"><span data-stu-id="8dab0-110">DbSet.AddRange</span></span>
- <span data-ttu-id="8dab0-111">DbSet.Remove</span><span class="sxs-lookup"><span data-stu-id="8dab0-111">DbSet.Remove</span></span>  
- <span data-ttu-id="8dab0-112">DbSet.RemoveRange</span><span class="sxs-lookup"><span data-stu-id="8dab0-112">DbSet.RemoveRange</span></span>
- <span data-ttu-id="8dab0-113">DbSet</span><span class="sxs-lookup"><span data-stu-id="8dab0-113">DbSet.Attach</span></span>  
- <span data-ttu-id="8dab0-114">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8dab0-114">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="8dab0-115">DbContext.GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="8dab0-115">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="8dab0-116">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="8dab0-116">DbContext.Entry</span></span>  
- <span data-ttu-id="8dab0-117">DbChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="8dab0-117">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="8dab0-118">Desabilitar a detecção automática de alterações</span><span class="sxs-lookup"><span data-stu-id="8dab0-118">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="8dab0-119">Se você estiver rastreando um lote de entidades no seu contexto e chamar um desses métodos muitas vezes em um loop, você pode obter melhorias significativas de desempenho desativando a detecção de alterações para a duração do loop.</span><span class="sxs-lookup"><span data-stu-id="8dab0-119">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="8dab0-120">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="8dab0-120">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

<span data-ttu-id="8dab0-121">Não se esqueça de habilitar novamente a detecção de alterações após o loop, usamos um try/finally para garantir que ele está sempre habilitado novamente, mesmo se o código no loop lançará uma exceção.</span><span class="sxs-lookup"><span data-stu-id="8dab0-121">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="8dab0-122">Uma alternativa para desabilitar e reabilitar é deixar a detecção automática de alterações desativado em todos os momentos e o contexto de chamada. ChangeTracker.DetectChanges explicitamente ou use proxies de controle de alterações cuidadosamente.</span><span class="sxs-lookup"><span data-stu-id="8dab0-122">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="8dab0-123">Ambas as opções avançadas e pode facilmente introduzir bugs sutis em seu aplicativo então usá-las com cuidado.</span><span class="sxs-lookup"><span data-stu-id="8dab0-123">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="8dab0-124">Se você precisar adicionar ou remover muitos objetos de um contexto, considere usar DbSet.AddRange e DbSet.RemoveRange.</span><span class="sxs-lookup"><span data-stu-id="8dab0-124">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="8dab0-125">Esse método detecta automaticamente as alterações em apenas uma vez após a conclusão de operações de adicionar ou remover.</span><span class="sxs-lookup"><span data-stu-id="8dab0-125">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 