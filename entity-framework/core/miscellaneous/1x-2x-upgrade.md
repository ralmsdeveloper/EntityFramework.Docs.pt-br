---
title: Atualizando de versões anteriores ao EF Core 2 – EF Core
author: divega
ms.date: 08/13/2017
ms.assetid: 8BD43C8C-63D9-4F3A-B954-7BC518A1B7DB
uid: core/miscellaneous/1x-2x-upgrade
ms.openlocfilehash: 5371c8f3b7c6102c621296bbae145d13779e0c6e
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283765"
---
# <a name="upgrading-applications-from-previous-versions-to-ef-core-20"></a>Atualizando aplicativos de versões anteriores para o EF Core 2.0

Podemos ter aproveitado a oportunidade para refinar significativamente nossas APIs existentes e os comportamentos no 2.0. Há algumas melhorias que podem exigir a modificação do código em seu aplicativo existente, embora nós acreditamos que, para a maioria dos aplicativos o impacto será baixo, na maioria dos casos que exigem apenas recompilação e mínimas alterações guiadas para substituir as APIs obsoletas.

Atualizando um aplicativo existente para o EF Core 2.0 pode exigir:

1. Atualizando a implementação do .NET de destino do aplicativo para um que dá suporte a .NET Standard 2.0. Ver [suporte para implementações do .NET](../platforms/index.md) para obter mais detalhes.

2. Identifica um provedor para o banco de dados de destino que é compatível com o EF Core 2.0. Ver [EF Core 2.0 requer um provedor de banco de 2.0 dados](#ef-core-20-requires-a-20-database-provider) abaixo.

3. Atualizar todos os pacotes do EF Core (tempo de execução e ferramentas) para 2.0. Consulte a [instalar o EF Core](../get-started/install/index.md) para obter mais detalhes.

4. Faça as alterações de código necessário para compensar as alterações de quebra descritas no restante deste documento.

## <a name="aspnet-core-now-includes-ef-core"></a>O ASP.NET Core agora inclui o EF Core

Aplicativos que têm como destino o ASP.NET Core 2.0 podem usar o EF Core 2.0 sem dependências adicionais além de provedores de banco de dados de terceiros. No entanto, os aplicativos destinados a versões anteriores do ASP.NET Core precisam atualizar para o ASP.NET Core 2.0 para usar o EF Core 2.0. Para obter mais detalhes sobre como atualizar os aplicativos ASP.NET Core 2.0, consulte [documentação do ASP.NET Core sobre o assunto](https://docs.microsoft.com/aspnet/core/migration/1x-to-2x/).

## <a name="new-way-of-getting-application-services-in-aspnet-core"></a>Nova maneira de obter serviços de aplicativo no ASP.NET Core

O padrão recomendado para aplicativos web ASP.NET Core foi atualizado para 2.0, de forma que interrompeu a lógica de tempo de design que do EF Core é usado em 1. x. Anteriormente no tempo de design, o EF Core era capaz de invocar `Startup.ConfigureServices` diretamente para acessar o provedor de serviços do aplicativo. No ASP.NET Core 2.0, a configuração é inicializada fora do `Startup` classe. Aplicativos que usam o EF Core normalmente acessam sua string de conexão de `Configuration`, portanto, `Startup` por si só não é mais suficiente. Se você atualizar um aplicativo do ASP.NET Core 1.x, você pode receber o seguinte erro ao usar as ferramentas do EF Core.

> Nenhum construtor sem parâmetros foi encontrado em 'ApplicationContext'. Adicione um construtor sem parâmetros para 'ApplicationContext' ou adicione uma implementação de ' IDesignTimeDbContextFactory&lt;ApplicationContext&gt;' no mesmo assembly como 'ApplicationContext'

Um gancho de tempo de design novo foi adicionado no modelo de padrão do ASP.NET Core 2.0. Estático `Program.BuildWebHost` método permite que o EF Core acessar o provedor de serviços do aplicativo em tempo de design. Se você estiver atualizando um aplicativo do ASP.NET Core 1.x, você precisará atualizar o `Program` classe para ser semelhante à seguinte.

``` csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;

namespace AspNetCoreDotNetCore2._0App
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
}
```

A adoção desse novo padrão na atualização de aplicativos para o 2.0 é altamente recomendável e é necessária para recursos de produto como as migrações do Entity Framework Core para trabalhar. A outra alternativa comum é [implementar *IDesignTimeDbContextFactory\<TContext >*](xref:core/miscellaneous/cli/dbcontext-creation#from-a-design-time-factory).

## <a name="idbcontextfactory-renamed"></a>IDbContextFactory renomeado

Para dar suporte a padrões de aplicativo diversos e fornecer aos usuários mais controle sobre como suas `DbContext` é usado em tempo de design, nós fornecemos, no passado, a interface `IDbContextFactory<TContext>`. Em tempo de design, as ferramentas do EF Core descobrirá as implementações dessa interface em seu projeto e usará para criar objetos `DbContext`.

Essa interface tinha um nome muito geral que enganava alguns usuários para tentar reutilizá-lo para outros cenários de criação de `DbContext`. Elas eram surpreendidas quando as ferramentas do EF, em seguida, tentou usar sua implementação no tempo de design e causou comandos como `Update-Database` ou `dotnet ef database update` falhe.

Para comunicar-se a semântica de tempo de design de alta segurança dessa interface, foi renomeado para `IDesignTimeDbContextFactory<TContext>`.

Para obter o 2.0 versão o `IDbContextFactory<TContext>` ainda existe, mas está marcado como obsoleto.

## <a name="dbcontextfactoryoptions-removed"></a>DbContextFactoryOptions removido

Devido às alterações de ASP.NET Core 2.0 descritas acima, descobrimos que `DbContextFactoryOptions` não era necessário na nova `IDesignTimeDbContextFactory<TContext>` interface. Aqui estão as alternativas, você deverá usar em vez disso.

| DbContextFactoryOptions | Alternativa                                                  |
|:------------------------|:-------------------------------------------------------------|
| ApplicationBasePath     | AppContext.BaseDirectory                                     |
| ContentRootPath         | Directory.GetCurrentDirectory()                              |
| EnvironmentName         | Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") |

## <a name="design-time-working-directory-changed"></a>Diretório de trabalho de tempo de design alterado

As alterações do ASP.NET Core 2.0 também exigiram que o diretório de trabalho usado pelo `dotnet ef` para alinhar com o diretório de trabalho usado pelo Visual Studio ao executar o aplicativo. Um efeito colateral observável, isso é que SQLite nomes de arquivos agora estão em relação ao diretório do projeto e não no diretório de saída, como elas costumavam ser.

## <a name="ef-core-20-requires-a-20-database-provider"></a>EF Core 2.0 requer um provedor de banco de 2.0 dados

Para o EF Core 2.0, fizemos muitas simplificações e melhorias na maneira como os provedores de banco de dados funcionam. Isso significa que os provedores 1.0.xe 1.1.x não funcionarão com o EF Core 2.0.

Os provedores SQL Server e SQLite são fornecidos pela equipe do EF e as 2.0 versões estarão disponíveis como parte do 2.0 de versão. Os provedores de terceiros do código-fonte aberto para [SQL Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact), [PostgreSQL](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), e [MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) estão sendo atualizadas para 2.0. Todos os outros provedores, entre em contato com o gravador de provedor.

## <a name="logging-and-diagnostics-events-have-changed"></a>Eventos de log e diagnóstico foram alterados

Observação: essas alterações não devem afetar a maioria dos códigos de aplicativo.

As identificações de evento para mensagens enviadas para um [ILogger](https://github.com/aspnet/Logging/blob/dev/src/Microsoft.Extensions.Logging.Abstractions/ILogger.cs) foram alteradas no 2.0. As IDs de evento são exclusivas em todo o código do EF Core. Essas mensagens agora também seguem o padrão para o registro em log estruturado usado pelo MVC, por exemplo.

As categorias de agente também foram alteradas. Há agora um conjunto bem conhecido de categorias acessadas por meio de [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).

[DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) eventos agora usam os mesmos nomes de ID de evento correspondente `ILogger` mensagens. As cargas do evento são todos os tipos nominais derivados [EventData](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/EventData.cs).

Categorias, tipos de carga e IDs de eventos estão documentadas na [CoreEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Diagnostics/CoreEventId.cs) e o [RelationalEventId](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore.Relational/Diagnostics/RelationalEventId.cs) classes.

IDs também foram movidos de Microsoft.EntityFrameworkCore.Infraestructure para o novo namespace Microsoft.EntityFrameworkCore.Diagnostics.

## <a name="ef-core-relational-metadata-api-changes"></a>Alterações de API de metadados relacionais do EF Core

O EF Core 2.0 agora criará um [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) diferente para cada provedor diferente que está sendo usado. Isso normalmente é transparente para o aplicativo. Isso facilitou uma simplificação de APIs de metadados de nível inferior de modo que qualquer acesso a _conceitos de metadados relacionados comuns_ pé feito sempre por meio de uma chamada para `.Relational`, em vez de `.SqlServer`, `.Sqlite` etc. Por exemplo, 1.1.x código como este:

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).SqlServer().TableName;
```

Agora deve ser escrito assim:

``` csharp
var tableName = context.Model.FindEntityType(typeof(User)).Relational().TableName;
```

Em vez de usar métodos como `ForSqlServerToTable`, métodos de extensão agora estão disponíveis para escrever o código condicional com base no provedor atual em uso. Por exemplo:

```C#
modelBuilder.Entity<User>().ToTable(
    Database.IsSqlServer() ? "SqlServerName" : "OtherName");
```

Observe que essa alteração se aplica apenas a APIs/metadados que está definido para _todos os_ provedores relacionais. A API e os metadados permanece o mesmo quando ele é específico para apenas um único provedor. Por exemplo, os índices clusterizados são específicos ao SQL Server, portanto `ForSqlServerIsClustered` e `.SqlServer().IsClustered()` ainda deve ser usado.

## <a name="dont-take-control-of-the-ef-service-provider"></a>Não assuma o controle do provedor de serviços EF

EF Core usa interno `IServiceProvider` (um contêiner de injeção de dependência) para sua implementação interna. Aplicativos devem permitir que o EF Core criar e gerenciar esse provedor, exceto em casos especiais. Considerar a remoção de todas as chamadas para `UseInternalServiceProvider`. Se um aplicativo precisar chamar `UseInternalServiceProvider`, em seguida, considere [registrar um problema](https://github.com/aspnet/EntityFramework/Issues) para que possamos investigar outras maneiras de lidar com seu cenário.

Chamando `AddEntityFramework`, `AddEntityFrameworkSqlServer`, etc. não é necessário pelo código do aplicativo, a menos que `UseInternalServiceProvider` também é chamado. Remover todas as chamadas existentes para `AddEntityFramework` ou `AddEntityFrameworkSqlServer`, etc. `AddDbContext` ainda deve ser usado da mesma forma como antes.

## <a name="in-memory-databases-must-be-named"></a>Bancos de dados na memória devem ser nomeados

O banco de dados na memória global sem nome foi removido e, em vez disso, todos os bancos de dados na memória devem ser nomeados. Por exemplo:

``` csharp
optionsBuilder.UseInMemoryDatabase("MyDatabase");
```

Isso cria/usa um banco de dados com o nome "MyDatabase". Se `UseInMemoryDatabase` é chamado novamente com o mesmo nome, o mesmo banco de dados na memória será usado, permitindo que ele seja compartilhado por várias instâncias de contexto.

## <a name="read-only-api-changes"></a>Alterações na API somente leitura

`IsReadOnlyBeforeSave`, `IsReadOnlyAfterSave`, e `IsStoreGeneratedAlways` obsoleto e substituídos por [BeforeSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L39) e [AfterSaveBehavior](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IProperty.cs#L55). Esses comportamentos se aplicam a qualquer propriedade (não apenas propriedades geradas pelo repositório) e determinar como o valor da propriedade deve ser usado ao inserir em uma linha do banco de dados (`BeforeSaveBehavior`) ou ao atualizar um existente de banco de dados linha (`AfterSaveBehavior`).

As propriedades marcadas como [ValueGenerated.OnAddOrUpdate](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/ValueGenerated.cs) (por exemplo, para colunas computadas) por padrão ignorará qualquer valor atualmente definido na propriedade. Isso significa que um valor gerado pelo repositório sempre será obtido, independentemente se nenhum valor foi definido ou modificado na entidade controlada. Isso pode ser alterado definindo uma opção diferente `Before\AfterSaveBehavior`.

## <a name="new-clientsetnull-delete-behavior"></a>Novo comportamento de exclusão ClientSetNull

Em versões anteriores, [DeleteBehavior.Restrict](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/DeleteBehavior.cs) tinha um comportamento para entidades controladas pelo contexto que mais fechados correspondentes `SetNull` semântica. No EF Core 2.0, um novo `ClientSetNull` comportamento foi introduzido como o padrão para relações opcionais. Esse comportamento tem `SetNull` semântica para entidades controladas e `Restrict` comportamento para bancos de dados criados usando o EF Core. Em nossa experiência, esses são os comportamentos mais esperado/úteis para entidades controladas e o banco de dados. `DeleteBehavior.Restrict` Agora é respeitado para entidades controladas quando definidas para relações opcionais.

## <a name="provider-design-time-packages-removed"></a>Pacotes de tempo de design do provedor removidos

O `Microsoft.EntityFrameworkCore.Relational.Design` pacote foi removido. Conteúdo do arquivo foram consolidado em `Microsoft.EntityFrameworkCore.Relational` e `Microsoft.EntityFrameworkCore.Design`.

Isso é propagada para os pacotes de tempo de design do provedor. Esses pacotes (`Microsoft.EntityFrameworkCore.Sqlite.Design`, `Microsoft.EntityFrameworkCore.SqlServer.Design`, etc.) foram removidos e seus conteúdos consolidados para os pacotes de provedor principal.

Para habilitar `Scaffold-DbContext` ou `dotnet ef dbcontext scaffold` no EF Core 2.0, você só precisa fazer referência ao pacote único provedor:

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer"
    Version="2.0.0" />
<PackageReference Include="Microsoft.EntityFrameworkCore.Tools"
    Version="2.0.0"
    PrivateAssets="All" />
<DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet"
    Version="2.0.0" />
```
