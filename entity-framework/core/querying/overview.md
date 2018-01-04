---
title: Como as consultas trabalho - Core EF
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
ms.technology: entity-framework-core
uid: core/querying/overview
ms.openlocfilehash: 7fd2940d559f82016d7a8fc3fdcf3af0d5b8bc8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="how-queries-work"></a><span data-ttu-id="5a3d6-102">Como funcionam as consultas</span><span class="sxs-lookup"><span data-stu-id="5a3d6-102">How Queries Work</span></span>

<span data-ttu-id="5a3d6-103">Entity Framework Core usa linguagem integrar LINQ (consulta) para consultar dados do banco de dados.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-103">Entity Framework Core uses Language Integrate Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="5a3d6-104">LINQ permite que você use c# (ou .NET idioma de sua escolha) para escrever consultas com rigidez de tipos com base em suas classes derivadas de contexto e a entidade.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-104">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="5a3d6-105">A vida útil de uma consulta</span><span class="sxs-lookup"><span data-stu-id="5a3d6-105">The life of a query</span></span>

<span data-ttu-id="5a3d6-106">A seguir está uma visão geral de alto nível do processo de que cada consulta atravessa.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-106">The following is a high level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="5a3d6-107">A consulta LINQ é processada pelo Entity Framework Core para criar uma representação que está pronta para ser processado pelo provedor de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5a3d6-107">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="5a3d6-108">O resultado é armazenado em cache para que esse processamento não precisa ser feito sempre que a consulta é executada</span><span class="sxs-lookup"><span data-stu-id="5a3d6-108">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="5a3d6-109">O resultado é passado para o provedor de banco de dados</span><span class="sxs-lookup"><span data-stu-id="5a3d6-109">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="5a3d6-110">O provedor de banco de dados identifica quais partes da consulta podem ser avaliadas no banco de dados</span><span class="sxs-lookup"><span data-stu-id="5a3d6-110">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="5a3d6-111">Essas partes da consulta são convertidos em linguagem de consulta específica de banco de dados (por exemplo, SQL para um banco de dados relacional)</span><span class="sxs-lookup"><span data-stu-id="5a3d6-111">These parts of the query are translated to database specific query language (e.g. SQL for a relational database)</span></span>
   3. <span data-ttu-id="5a3d6-112">Uma ou mais consultas são enviadas para o banco de dados e o conjunto de resultados retornado (resultados são valores do banco de dados, não as instâncias de entidade)</span><span class="sxs-lookup"><span data-stu-id="5a3d6-112">One or more queries are sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="5a3d6-113">Para cada item no conjunto de resultados</span><span class="sxs-lookup"><span data-stu-id="5a3d6-113">For each item in the result set</span></span>
   1. <span data-ttu-id="5a3d6-114">Se esse for um rastreamento de consulta, EF verifica se os dados representam uma entidade já no controlador de alterações para a instância de contexto</span><span class="sxs-lookup"><span data-stu-id="5a3d6-114">If this is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="5a3d6-115">Nesse caso, a entidade existente será retornada</span><span class="sxs-lookup"><span data-stu-id="5a3d6-115">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="5a3d6-116">Caso contrário, uma nova entidade é criada, o controle de alterações estão configurado e a nova entidade for retornada</span><span class="sxs-lookup"><span data-stu-id="5a3d6-116">If not, a new entity is created, change tracking is setup, and the new entity is returned</span></span>
   2. <span data-ttu-id="5a3d6-117">Se essa é uma consulta de acompanhamento não, EF verifica se os dados representam uma entidade que já estão no conjunto de resultados para esta consulta</span><span class="sxs-lookup"><span data-stu-id="5a3d6-117">If this is a no-tracking query, EF checks if the data represents an entity already in the result set for this query</span></span>
      * <span data-ttu-id="5a3d6-118">Se assim, a entidade existente for retornada <sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="5a3d6-118">If so, the existing entity is returned <sup>(1)</sup></span></span>
      * <span data-ttu-id="5a3d6-119">Caso contrário, uma nova entidade é criada e retornada</span><span class="sxs-lookup"><span data-stu-id="5a3d6-119">If not, a new entity is created and returned</span></span>

<span data-ttu-id="5a3d6-120"><sup>(1) </sup> Consultas sem controle usam referências fracas para manter o controle de entidades que já foram retornadas.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-120"><sup>(1)</sup> No tracking queries use weak references to keep track of entities that have already been returned.</span></span> <span data-ttu-id="5a3d6-121">Se um resultado anterior com a mesma identidade sai do escopo e coleta de lixo é executado, você pode receber uma nova instância da entidade.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-121">If a previous result with the same identity goes out of scope, and garbage collection runs, you may get a new entity instance.</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="5a3d6-122">Quando as consultas são executadas</span><span class="sxs-lookup"><span data-stu-id="5a3d6-122">When queries are executed</span></span>

<span data-ttu-id="5a3d6-123">Quando você chama operadores LINQ, você está criando simplesmente uma representação na memória da consulta.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-123">When you call LINQ operators, you are simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="5a3d6-124">A consulta é enviada somente para o banco de dados quando os resultados são consumidos.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-124">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="5a3d6-125">As operações mais comuns que resultam na consulta que está sendo enviada para o banco de dados são:</span><span class="sxs-lookup"><span data-stu-id="5a3d6-125">The most common operations that result in the query being sent to the database are:</span></span>
* <span data-ttu-id="5a3d6-126">Iteração os resultados em um `for` loop</span><span class="sxs-lookup"><span data-stu-id="5a3d6-126">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="5a3d6-127">Usando um operador como `ToList`, `ToArray`, `Single`,`Count`</span><span class="sxs-lookup"><span data-stu-id="5a3d6-127">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`</span></span>
* <span data-ttu-id="5a3d6-128">Associação de dados de resultados de uma consulta para uma interface de usuário</span><span class="sxs-lookup"><span data-stu-id="5a3d6-128">Databinding the results of a query to a UI</span></span>

> [!WARNING]  
> <span data-ttu-id="5a3d6-129">**Sempre valide a entrada do usuário:** EF enquanto fornecem proteção contra ataques de injeção de SQL, ele não faz nenhuma validação geral de entrada.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-129">**Always validate user input:** While EF does provide protection from SQL injection attacks, it does not do any general validation of input.</span></span> <span data-ttu-id="5a3d6-130">Portanto, se os valores sejam passados para APIs, usado em consultas LINQ, atribuídas para propriedades de entidade, etc., vêm de uma fonte não confiável, em seguida, validação apropriadas, por seus requisitos de aplicativo deve ser executado.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-130">Therefore if values being passed to APIs, used in LINQ queries, assigned to entity properties, etc., come from an untrusted source then appropriate validation, per your application requirements, should be performed.</span></span> <span data-ttu-id="5a3d6-131">Isso inclui qualquer entrada do usuário usada para construir dinamicamente consultas.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-131">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="5a3d6-132">Mesmo ao usar o LINQ, se você está aceitando entrada do usuário construir expressões que você precisa garantir que somente as expressões pretendidas pode ser criada.</span><span class="sxs-lookup"><span data-stu-id="5a3d6-132">Even when using LINQ, if you are accepting user input to build expressions you need to make sure than only intended expressions can be constructed.</span></span>