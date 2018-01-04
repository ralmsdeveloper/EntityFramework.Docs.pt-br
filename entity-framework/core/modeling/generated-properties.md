---
title: Valores gerados - Core EF
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: eb082011-11a1-41b4-a108-15daafa03e80
ms.technology: entity-framework-core
uid: core/modeling/generated-properties
ms.openlocfilehash: 2d79bf1339ebe522c39fe8971d908c30e1f4dca0
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="generated-values"></a><span data-ttu-id="45c5e-102">Valores gerados</span><span class="sxs-lookup"><span data-stu-id="45c5e-102">Generated Values</span></span>

## <a name="value-generation-patterns"></a><span data-ttu-id="45c5e-103">Padrões de geração de valor</span><span class="sxs-lookup"><span data-stu-id="45c5e-103">Value Generation Patterns</span></span>

<span data-ttu-id="45c5e-104">Há três padrões de geração de valor que podem ser usados para propriedades.</span><span class="sxs-lookup"><span data-stu-id="45c5e-104">There are three value generation patterns that can be used for properties.</span></span>

### <a name="no-value-generation"></a><span data-ttu-id="45c5e-105">Não há geração de valor</span><span class="sxs-lookup"><span data-stu-id="45c5e-105">No value generation</span></span>

<span data-ttu-id="45c5e-106">Não há geração de valor significa que você sempre fornecerá um valor válido a ser salvo no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45c5e-106">No value generation means that you will always supply a valid value to be saved to the database.</span></span> <span data-ttu-id="45c5e-107">Esse valor válido deve ser atribuído para novas entidades antes de serem adicionados ao contexto.</span><span class="sxs-lookup"><span data-stu-id="45c5e-107">This valid value must be assigned to new entities before they are added to the context.</span></span>

### <a name="value-generated-on-add"></a><span data-ttu-id="45c5e-108">Adicionar valor gerado em</span><span class="sxs-lookup"><span data-stu-id="45c5e-108">Value generated on add</span></span>

<span data-ttu-id="45c5e-109">Valor gerado em Adicionar significa que um valor é gerado para novas entidades.</span><span class="sxs-lookup"><span data-stu-id="45c5e-109">Value generated on add means that a value is generated for new entities.</span></span>

<span data-ttu-id="45c5e-110">Dependendo do provedor de banco de dados que está sendo usado, os valores podem ser gerados do lado do cliente por EF ou no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45c5e-110">Depending on the database provider being used, values may be generated client side by EF or in the database.</span></span> <span data-ttu-id="45c5e-111">Se o valor é gerado pelo banco de dados, EF pode atribuir um valor temporário quando você adiciona a entidade ao contexto.</span><span class="sxs-lookup"><span data-stu-id="45c5e-111">If the value is generated by the database, then EF may assign a temporary value when you add the entity to the context.</span></span> <span data-ttu-id="45c5e-112">Esse valor temporário, em seguida, será substituído pelo valor do banco de dados gerado durante `SaveChanges()`.</span><span class="sxs-lookup"><span data-stu-id="45c5e-112">This temporary value will then be replaced by the database generated value during `SaveChanges()`.</span></span>

<span data-ttu-id="45c5e-113">Se você adicionar uma entidade ao contexto que tem um valor atribuído à propriedade, o EF tentará inserir esse valor em vez de gerar um novo.</span><span class="sxs-lookup"><span data-stu-id="45c5e-113">If you add an entity to the context that has a value assigned to the property, then EF will attempt to insert that value rather than generating a new one.</span></span> <span data-ttu-id="45c5e-114">Uma propriedade é considerada para receber um valor se ele não está atribuído o valor padrão CLR (`null` para `string`, `0` para `int`, `Guid.Empty` para `Guid`, etc.).</span><span class="sxs-lookup"><span data-stu-id="45c5e-114">A property is considered to have a value assigned if it is not assigned the CLR default value (`null` for `string`, `0` for `int`, `Guid.Empty` for `Guid`, etc.).</span></span> <span data-ttu-id="45c5e-115">Para obter mais informações, consulte [valores explícitos de propriedades gerado](..\saving\explicit-values-generated-properties.md).</span><span class="sxs-lookup"><span data-stu-id="45c5e-115">For more information, see [Explicit values for generated properties](..\saving\explicit-values-generated-properties.md).</span></span>

> [!WARNING]  
> <span data-ttu-id="45c5e-116">Como o valor é gerado para entidades adicionadas dependem do provedor de banco de dados que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="45c5e-116">How the value is generated for added entities will depend on the database provider being used.</span></span> <span data-ttu-id="45c5e-117">Provedores de banco de dados podem configurar automaticamente a geração de valor para alguns tipos de propriedade, mas outras pessoas podem exigir a instalação manualmente como o valor é gerado.</span><span class="sxs-lookup"><span data-stu-id="45c5e-117">Database providers may automatically setup value generation for some property types, but others may require you to manually setup how the value is generated.</span></span>
>
> <span data-ttu-id="45c5e-118">Por exemplo, ao usar o SQL Server, valores serão gerados automaticamente para `GUID` propriedades (usando o algoritmo GUID sequencial do SQL Server).</span><span class="sxs-lookup"><span data-stu-id="45c5e-118">For example, when using SQL Server, values will be automatically generated for `GUID` properties (using the SQL Server sequential GUID algorithm).</span></span> <span data-ttu-id="45c5e-119">No entanto, se você especificar que um `DateTime` propriedade é gerada em Adicionar, em seguida, você deve configurar uma maneira para que os valores a serem gerados.</span><span class="sxs-lookup"><span data-stu-id="45c5e-119">However, if you specify that a `DateTime` property is generated on add, then you must setup a way for the values to be generated.</span></span> <span data-ttu-id="45c5e-120">Uma maneira de fazer isso, é configurar um valor padrão de `GETDATE()`, consulte [valores padrão](relational/default-values.md).</span><span class="sxs-lookup"><span data-stu-id="45c5e-120">One way to do this, is to configure a default value of `GETDATE()`, see [Default Values](relational/default-values.md).</span></span>

### <a name="value-generated-on-add-or-update"></a><span data-ttu-id="45c5e-121">Valor gerado em Adicionar ou atualizar</span><span class="sxs-lookup"><span data-stu-id="45c5e-121">Value generated on add or update</span></span>

<span data-ttu-id="45c5e-122">Valor gerado ao adicionar ou atualizar significa que um novo valor é gerado sempre que o registro é salvo (insert ou update).</span><span class="sxs-lookup"><span data-stu-id="45c5e-122">Value generated on add or update means that a new value is generated every time the record is saved (insert or update).</span></span>

<span data-ttu-id="45c5e-123">Como `value generated on add`, se você especificar um valor para a propriedade em uma instância recém adicionada de uma entidade, que o valor será inserido em vez de um valor que está sendo gerado.</span><span class="sxs-lookup"><span data-stu-id="45c5e-123">Like `value generated on add`, if you specify a value for the property on a newly added instance of an entity, that value will be inserted rather than a value being generated.</span></span> <span data-ttu-id="45c5e-124">Também é possível definir um valor explícito ao atualizar.</span><span class="sxs-lookup"><span data-stu-id="45c5e-124">It is also possible to set an explicit value when updating.</span></span> <span data-ttu-id="45c5e-125">Para obter mais informações, consulte [valores explícitos de propriedades gerado](..\saving\explicit-values-generated-properties.md).</span><span class="sxs-lookup"><span data-stu-id="45c5e-125">For more information, see [Explicit values for generated properties](..\saving\explicit-values-generated-properties.md).</span></span>

> [!WARNING]  
> <span data-ttu-id="45c5e-126">Como o valor é gerado para entidades adicionadas e atualizadas dependem do provedor de banco de dados que está sendo usado.</span><span class="sxs-lookup"><span data-stu-id="45c5e-126">How the value is generated for added and updated entities will depend on the database provider being used.</span></span> <span data-ttu-id="45c5e-127">Provedores de banco de dados automaticamente podem configurar a geração de valor para alguns tipos de propriedade, enquanto outros exigem instalação manualmente como o valor é gerado.</span><span class="sxs-lookup"><span data-stu-id="45c5e-127">Database providers may automatically setup value generation for some property types, while others will require you to manually setup how the value is generated.</span></span>
>
> <span data-ttu-id="45c5e-128">Por exemplo, ao usar o SQL Server, `byte[]` propriedades que são definidas como gerado em Adicionar ou atualizar e marcado como tokens de simultaneidade, será instalado com o `rowversion` de tipo de dados - para que os valores serão criados no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="45c5e-128">For example, when using SQL Server, `byte[]` properties that are set as generated on add or update and marked as concurrency tokens, will be setup with the `rowversion` data type - so that values will be generated in the database.</span></span> <span data-ttu-id="45c5e-129">No entanto, se você especificar que um `DateTime` propriedade é gerada em Adicionar ou atualizar, em seguida, você deve configurar uma maneira para que os valores a serem gerados.</span><span class="sxs-lookup"><span data-stu-id="45c5e-129">However, if you specify that a `DateTime` property is generated on add or update, then you must setup a way for the values to be generated.</span></span> <span data-ttu-id="45c5e-130">Uma maneira de fazer isso, é configurar um valor padrão de `GETDATE()` (consulte [valores padrão](relational/default-values.md)) para gerar valores para novas linhas.</span><span class="sxs-lookup"><span data-stu-id="45c5e-130">One way to do this, is to configure a default value of `GETDATE()` (see [Default Values](relational/default-values.md)) to generate values for new rows.</span></span> <span data-ttu-id="45c5e-131">Você pode usar um gatilho de banco de dados para gerar valores de durante as atualizações (como o gatilho de exemplo a seguir).</span><span class="sxs-lookup"><span data-stu-id="45c5e-131">You could then use a database trigger to generate values during updates (such as the following example trigger).</span></span>
>
> [!code-sql[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.sql)]

## <a name="conventions"></a><span data-ttu-id="45c5e-132">Convenções</span><span class="sxs-lookup"><span data-stu-id="45c5e-132">Conventions</span></span>

<span data-ttu-id="45c5e-133">Por convenção, as chaves primárias de um inteiro ou um tipo de dados GUID será instalado para ter valores gerados em Adicionar.</span><span class="sxs-lookup"><span data-stu-id="45c5e-133">By convention, primary keys that are of an integer or GUID data type will be setup to have values generated on add.</span></span> <span data-ttu-id="45c5e-134">Todas as outras propriedades será instalado com nenhuma geração de valor.</span><span class="sxs-lookup"><span data-stu-id="45c5e-134">All other properties will be setup with no value generation.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="45c5e-135">Anotações de dados</span><span class="sxs-lookup"><span data-stu-id="45c5e-135">Data Annotations</span></span>

### <a name="no-value-generation-data-annotations"></a><span data-ttu-id="45c5e-136">Não há geração de valor (anotações de dados)</span><span class="sxs-lookup"><span data-stu-id="45c5e-136">No value generation (Data Annotations)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-data-annotations"></a><span data-ttu-id="45c5e-137">Valor gerado em Adicionar (anotações de dados)</span><span class="sxs-lookup"><span data-stu-id="45c5e-137">Value generated on add (Data Annotations)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> <span data-ttu-id="45c5e-138">Isso apenas permite EF souber que valores são gerados para entidades adicionadas, ele não garante que o EF irá configurar o mecanismo real para gerar os valores.</span><span class="sxs-lookup"><span data-stu-id="45c5e-138">This just lets EF know that values are generated for added entities, it does not guarantee that EF will setup the actual mechanism to generate values.</span></span> <span data-ttu-id="45c5e-139">Consulte [Adicionar valor gerado no](#value-generated-on-add) seção para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="45c5e-139">See [Value generated on add](#value-generated-on-add) section for more details.</span></span>

### <a name="value-generated-on-add-or-update-data-annotations"></a><span data-ttu-id="45c5e-140">Valor gerado em Adicionar ou atualizar (anotações de dados)</span><span class="sxs-lookup"><span data-stu-id="45c5e-140">Value generated on add or update (Data Annotations)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> <span data-ttu-id="45c5e-141">Isso apenas permite EF souber que os valores são gerados para entidades foram adicionadas ou atualizadas, isso não garante que o EF irá configurar o mecanismo real para gerar os valores.</span><span class="sxs-lookup"><span data-stu-id="45c5e-141">This just lets EF know that values are generated for added or updated entities, it does not guarantee that EF will setup the actual mechanism to generate values.</span></span> <span data-ttu-id="45c5e-142">Consulte [valor gerado em Adicionar ou atualizar](#value-generated-on-add-or-update) seção para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="45c5e-142">See [Value generated on add or update](#value-generated-on-add-or-update) section for more details.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="45c5e-143">API fluente</span><span class="sxs-lookup"><span data-stu-id="45c5e-143">Fluent API</span></span>

<span data-ttu-id="45c5e-144">Você pode usar a API fluente para alterar o padrão de geração de valor para uma determinada propriedade.</span><span class="sxs-lookup"><span data-stu-id="45c5e-144">You can use the Fluent API to change the value generation pattern for a given property.</span></span>

### <a name="no-value-generation-fluent-api"></a><span data-ttu-id="45c5e-145">Não há geração de valor (API fluente)</span><span class="sxs-lookup"><span data-stu-id="45c5e-145">No value generation (Fluent API)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedNever.cs#Sample)]

### <a name="value-generated-on-add-fluent-api"></a><span data-ttu-id="45c5e-146">Valor gerado em Adicionar (API fluente)</span><span class="sxs-lookup"><span data-stu-id="45c5e-146">Value generated on add (Fluent API)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAdd.cs#Sample)]

> [!WARNING]  
> <span data-ttu-id="45c5e-147">`ValueGeneratedOnAdd()`apenas permite que o EF que valores são gerados para entidades adicionadas, eles não garantem que o EF irá configurar o mecanismo real para gerar os valores.</span><span class="sxs-lookup"><span data-stu-id="45c5e-147">`ValueGeneratedOnAdd()` just lets EF know that values are generated for added entities, it does not guarantee that EF will setup the actual mechanism to generate values.</span></span>  <span data-ttu-id="45c5e-148">Consulte [Adicionar valor gerado no](#value-generated-on-add) seção para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="45c5e-148">See [Value generated on add](#value-generated-on-add) section for more details.</span></span>

### <a name="value-generated-on-add-or-update-fluent-api"></a><span data-ttu-id="45c5e-149">Valor gerado em Adicionar ou atualização (API fluente)</span><span class="sxs-lookup"><span data-stu-id="45c5e-149">Value generated on add or update (Fluent API)</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Samples/ValueGeneratedOnAddOrUpdate.cs#Sample)]

> [!WARNING]  
> <span data-ttu-id="45c5e-150">Isso apenas permite EF souber que os valores são gerados para entidades foram adicionadas ou atualizadas, isso não garante que o EF irá configurar o mecanismo real para gerar os valores.</span><span class="sxs-lookup"><span data-stu-id="45c5e-150">This just lets EF know that values are generated for added or updated entities, it does not guarantee that EF will setup the actual mechanism to generate values.</span></span> <span data-ttu-id="45c5e-151">Consulte [valor gerado em Adicionar ou atualizar](#value-generated-on-add-or-update) seção para obter mais detalhes.</span><span class="sxs-lookup"><span data-stu-id="45c5e-151">See [Value generated on add or update](#value-generated-on-add-or-update) section for more details.</span></span>