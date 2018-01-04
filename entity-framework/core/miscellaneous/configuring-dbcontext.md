---
title: Configurando um DbContext - Core EF
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d7a22b5a-4c5b-4e3b-9897-4d7320fcd13f
ms.technology: entity-framework-core
uid: core/miscellaneous/configuring-dbcontext
ms.openlocfilehash: de26e3b28851d4dc4e50f0490093dd05ad489b31
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="configuring-a-dbcontext"></a><span data-ttu-id="673c6-102">Configurando um DbContext</span><span class="sxs-lookup"><span data-stu-id="673c6-102">Configuring a DbContext</span></span>

<span data-ttu-id="673c6-103">Este artigo mostra padrões básicos para configurar um `DbContext` por meio de um `DbContextOptions` para se conectar a um banco de dados usando um provedor EF Core e comportamentos opcionais.</span><span class="sxs-lookup"><span data-stu-id="673c6-103">This article shows basic patterns for configuring a `DbContext` via a `DbContextOptions` to connect to a database using a specific EF Core provider and optional behaviors.</span></span>

## <a name="design-time-dbcontext-configuration"></a><span data-ttu-id="673c6-104">Configuração do tempo de design DbContext</span><span class="sxs-lookup"><span data-stu-id="673c6-104">Design-time DbContext configuration</span></span>

<span data-ttu-id="673c6-105">Tempo de design EF principais ferramentas como [migrações](xref:core/managing-schemas/migrations/index) precisa ser capaz de detectar e criar uma instância de trabalho de um `DbContext` tipo para obter detalhes sobre os tipos de entidade e como eles serão mapeados para um esquema de banco de dados do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="673c6-105">EF Core design-time tools such as [migrations](xref:core/managing-schemas/migrations/index) need to be able to discover and create a working instance of a `DbContext` type in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="673c6-106">Esse processo pode ser automático enquanto a ferramenta pode criar facilmente o `DbContext` de forma que ele será configurado da mesma forma como seria configurado no tempo.</span><span class="sxs-lookup"><span data-stu-id="673c6-106">This process can be automatic as long as the tool can easily create the `DbContext` in such a way that it will be configured similarly to how it would be configured at runt-time.</span></span>

<span data-ttu-id="673c6-107">Enquanto qualquer padrão que fornece as informações de configuração necessárias para o `DbContext` pode trabalhar em tempo de execução, ferramentas que exigem o uso de um `DbContext` em tempo de design só pode trabalhar com um número limitado de padrões.</span><span class="sxs-lookup"><span data-stu-id="673c6-107">While any pattern that provides the necessary configuration information to the `DbContext` can work at run-time, tools that require using a `DbContext` at design-time can only work with a limited number of patterns.</span></span> <span data-ttu-id="673c6-108">Eles são abordados mais detalhadamente o [criação do contexto de tempo de Design](xref:core/miscellaneous/cli/dbcontext-creation) seção.</span><span class="sxs-lookup"><span data-stu-id="673c6-108">These are covered in more detail in the [Design-Time Context Creation](xref:core/miscellaneous/cli/dbcontext-creation) section.</span></span>

## <a name="configuring-dbcontextoptions"></a><span data-ttu-id="673c6-109">Configurando DbContextOptions</span><span class="sxs-lookup"><span data-stu-id="673c6-109">Configuring DbContextOptions</span></span>

<span data-ttu-id="673c6-110">`DbContext`deve ter uma instância de `DbContextOptions` para executar qualquer trabalho.</span><span class="sxs-lookup"><span data-stu-id="673c6-110">`DbContext` must have an instance of `DbContextOptions` in order to perform any work.</span></span> <span data-ttu-id="673c6-111">O `DbContextOptions` instância contém informações de configuração, como:</span><span class="sxs-lookup"><span data-stu-id="673c6-111">The `DbContextOptions` instance carries configuration information such as:</span></span>

- <span data-ttu-id="673c6-112">O provedor de banco de dados a ser usado, normalmente selecionada invocando um método como `UseSqlServer` ou`UseSqlite`</span><span class="sxs-lookup"><span data-stu-id="673c6-112">The database provider to use, typically selected by invoking a method such as `UseSqlServer` or `UseSqlite`</span></span>
- <span data-ttu-id="673c6-113">Qualquer cadeia de caracteres de conexão necessárias ou o identificador da instância de banco de dados, normalmente passado como um argumento para o método de seleção de provedor mencionado acima</span><span class="sxs-lookup"><span data-stu-id="673c6-113">Any necessary connection string or identifier of the database instance, typically passed as an argument to the provider selection method mentioned above</span></span>
- <span data-ttu-id="673c6-114">Quaisquer seletores de comportamento opcional de nível de provedor, normalmente também encadeados dentro da chamada para o método de seleção de provedor</span><span class="sxs-lookup"><span data-stu-id="673c6-114">Any provider-level optional behavior selectors, typically also chained inside the call to the provider selection method</span></span>
- <span data-ttu-id="673c6-115">Quaisquer seletores de comportamento EF Core gerais, normalmente encadeados depois ou antes que o método de seletor de provedor</span><span class="sxs-lookup"><span data-stu-id="673c6-115">Any general EF Core behavior selectors, typically chained after or before the provider selector method</span></span>

<span data-ttu-id="673c6-116">O exemplo a seguir configura o `DbContextOptions` para usar o provedor do SQL Server, uma conexão contidas a `connectionString` variável, um tempo limite de comando de nível de provedor e um seletor de comportamento de EF principal que faz com que todas as consultas executadas no `DbContext` [nenhum controle](xref:core/querying/tracking#no-tracking-queries) por padrão:</span><span class="sxs-lookup"><span data-stu-id="673c6-116">The following example configures the `DbContextOptions` to use the SQL Server provider, a connection contained in the `connectionString` variable, a provider-level command timeout, and an EF Core behavior selector that makes all queries executed in the `DbContext` [no-tracking](xref:core/querying/tracking#no-tracking-queries) by default:</span></span>

``` csharp
optionsBuilder
    .UseSqlServer(connectionString, providerOptions=>providerOptions.CommandTimeout(60))
    .UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
```

> [!NOTE]  
> <span data-ttu-id="673c6-117">Métodos de seletor de provedor e outros métodos de seletor de comportamento mencionados acima são os métodos de extensão em `DbContextOptions` ou classes de opção específica do provedor.</span><span class="sxs-lookup"><span data-stu-id="673c6-117">Provider selector methods and other behavior selector methods mentioned above are extension methods on `DbContextOptions` or provider-specific option classes.</span></span> <span data-ttu-id="673c6-118">Para ter acesso a esses métodos de extensão, talvez seja necessário ter um namespace (geralmente `Microsoft.EntityFrameworkCore`) no escopo e incluir as dependências do pacote adicional no projeto.</span><span class="sxs-lookup"><span data-stu-id="673c6-118">In order to have access to these extension methods you may need to have a namespace (typically `Microsoft.EntityFrameworkCore`) in scope and include additional package dependencies in the project.</span></span>

<span data-ttu-id="673c6-119">O `DbContextOptions` podem ser fornecidos para o `DbContext` , substituindo o `OnConfiguring` método ou externamente por meio de um argumento de construtor.</span><span class="sxs-lookup"><span data-stu-id="673c6-119">The `DbContextOptions` can be supplied to the `DbContext` by overriding the `OnConfiguring` method or externally via a constructor argument.</span></span>

<span data-ttu-id="673c6-120">Se ambos forem usadas, `OnConfiguring` é aplicada por último e podem substituir as opções fornecidas para o argumento de construtor.</span><span class="sxs-lookup"><span data-stu-id="673c6-120">If both are used, `OnConfiguring` is applied last and can overwrite options supplied to the constructor argument.</span></span>

### <a name="constructor-argument"></a><span data-ttu-id="673c6-121">Argumento de construtor</span><span class="sxs-lookup"><span data-stu-id="673c6-121">Constructor argument</span></span>

<span data-ttu-id="673c6-122">Código de contexto com o construtor:</span><span class="sxs-lookup"><span data-stu-id="673c6-122">Context code with constructor:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
        : base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

> [!TIP]  
> <span data-ttu-id="673c6-123">O construtor base do DbContext também aceita a versão não genérica de `DbContextOptions`, mas não é recomendável usar a versão não genérica para aplicativos com vários tipos de contexto.</span><span class="sxs-lookup"><span data-stu-id="673c6-123">The base constructor of DbContext also accepts the non-generic version of `DbContextOptions`, but using the non-generic version is not recommended for applications with multiple context types.</span></span>

<span data-ttu-id="673c6-124">Código do aplicativo para inicializar a partir do argumento de construtor:</span><span class="sxs-lookup"><span data-stu-id="673c6-124">Application code to initialize from constructor argument:</span></span>

``` csharp
var optionsBuilder = new DbContextOptionsBuilder<BloggingContext>();
optionsBuilder.UseSqlite("Data Source=blog.db");

using (var context = new BloggingContext(optionsBuilder.Options))
{
  // do stuff
}
```

### <a name="onconfiguring"></a><span data-ttu-id="673c6-125">OnConfiguring</span><span class="sxs-lookup"><span data-stu-id="673c6-125">OnConfiguring</span></span>

<span data-ttu-id="673c6-126">Código de contexto com `OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="673c6-126">Context code with `OnConfiguring`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlite("Data Source=blog.db");
    }
}
```

<span data-ttu-id="673c6-127">Código do aplicativo para inicializar um `DbContext` que usa `OnConfiguring`:</span><span class="sxs-lookup"><span data-stu-id="673c6-127">Application code to initialize a `DbContext` that uses `OnConfiguring`:</span></span>

``` csharp
using (var context = new BloggingContext())
{
  // do stuff
}
```

> [!TIP]
> <span data-ttu-id="673c6-128">Essa abordagem não se prestam para teste, a menos que os testes de banco de dados completo de destino.</span><span class="sxs-lookup"><span data-stu-id="673c6-128">This approach does not lend itself to testing, unless the tests target the full database.</span></span>

### <a name="using-dbcontext-with-dependency-injection"></a><span data-ttu-id="673c6-129">Usando DbContext com injeção de dependência</span><span class="sxs-lookup"><span data-stu-id="673c6-129">Using DbContext with dependency injection</span></span>

<span data-ttu-id="673c6-130">EF Core dá suporte ao uso `DbContext` com um contêiner de injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="673c6-130">EF Core supports using `DbContext` with a dependency injection container.</span></span> <span data-ttu-id="673c6-131">O tipo DbContext pode ser adicionado ao contêiner de serviço usando o `AddDbContext<TContext>` método.</span><span class="sxs-lookup"><span data-stu-id="673c6-131">Your DbContext type can be added to the service container by using the `AddDbContext<TContext>` method.</span></span>

<span data-ttu-id="673c6-132">`AddDbContext<TContext>`fará com que os dois o tipo DbContext, `TContext`e o correspondente `DbContextOptions<TContext>` disponíveis para a injeção do contêiner de serviços.</span><span class="sxs-lookup"><span data-stu-id="673c6-132">`AddDbContext<TContext>` will make both your DbContext type, `TContext`, and the corresponding `DbContextOptions<TContext>` available for injection from the service container.</span></span>

<span data-ttu-id="673c6-133">Consulte [leitura mais](#more-reading) abaixo para obter informações adicionais sobre injeção de dependência.</span><span class="sxs-lookup"><span data-stu-id="673c6-133">See [more reading](#more-reading) below for additional information on dependency injection.</span></span>

<span data-ttu-id="673c6-134">Adicionando o `Dbcontext` a injeção de dependência:</span><span class="sxs-lookup"><span data-stu-id="673c6-134">Adding the `Dbcontext` to dependency injection:</span></span>

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<BloggingContext>(options => options.UseSqlite("Data Source=blog.db"));
}
```

<span data-ttu-id="673c6-135">Isso requer a adição de um [argumento do construtor](#constructor-argument) para o tipo DbContext que aceita `DbContextOptions<TContext>`.</span><span class="sxs-lookup"><span data-stu-id="673c6-135">This requires adding a [constructor argument](#constructor-argument) to your DbContext type that accepts `DbContextOptions<TContext>`.</span></span>

<span data-ttu-id="673c6-136">Código do contexto:</span><span class="sxs-lookup"><span data-stu-id="673c6-136">Context code:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext(DbContextOptions<BloggingContext> options)
      :base(options)
    { }

    public DbSet<Blog> Blogs { get; set; }
}
```

<span data-ttu-id="673c6-137">Código do aplicativo (no ASP.NET Core):</span><span class="sxs-lookup"><span data-stu-id="673c6-137">Application code (in ASP.NET Core):</span></span>

``` csharp
public class MyController
{
    private readonly BloggingContext _context;

    public MyController(BloggingContext context)
    {
      _context = context;
    }

    ...
}
```

<span data-ttu-id="673c6-138">Código do aplicativo (usando o provedor de serviços diretamente, menos comuns):</span><span class="sxs-lookup"><span data-stu-id="673c6-138">Application code (using ServiceProvider directly, less common):</span></span>

``` csharp
using (var context = serviceProvider.GetService<BloggingContext>())
{
  // do stuff
}

var options = serviceProvider.GetService<DbContextOptions<BloggingContext>>();
```

## <a name="more-reading"></a><span data-ttu-id="673c6-139">Ler mais</span><span class="sxs-lookup"><span data-stu-id="673c6-139">More reading</span></span>

* <span data-ttu-id="673c6-140">Leitura [para iniciar o ASP.NET Core](../get-started/aspnetcore/index.md) para obter mais informações sobre como usar o EF com ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="673c6-140">Read [Getting Started on ASP.NET Core](../get-started/aspnetcore/index.md) for more information on using EF with ASP.NET Core.</span></span>
* <span data-ttu-id="673c6-141">Leitura [injeção de dependência](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) para saber mais sobre como usar a DI.</span><span class="sxs-lookup"><span data-stu-id="673c6-141">Read [Dependency Injection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) to learn more about using DI.</span></span>
* <span data-ttu-id="673c6-142">Leitura [teste](testing/index.md) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="673c6-142">Read [Testing](testing/index.md) for more information.</span></span>