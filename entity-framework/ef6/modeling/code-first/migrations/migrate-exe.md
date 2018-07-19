---
title: Usando migrate.exe - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 989ea862-e936-4c85-926a-8cfbef5df5b8
caps.latest.revision: 3
ms.openlocfilehash: 3a9bc47d2c18abe6ca70b92a8758b4d95010a464
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39119720"
---
# <a name="using-migrateexe"></a><span data-ttu-id="ffcc2-102">Usando migrate.exe</span><span class="sxs-lookup"><span data-stu-id="ffcc2-102">Using migrate.exe</span></span>
<span data-ttu-id="ffcc2-103">Migrações do Code First pode ser usadas para atualizar um banco de dados de dentro do visual studio, mas também podem ser executadas por meio de linha de comando ferramenta migrate.exe.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-103">Code First Migrations can be used to update a database from inside visual studio, but can also be executed via the command line tool migrate.exe.</span></span> <span data-ttu-id="ffcc2-104">Esta página fornecerá uma visão geral rápida sobre como usar migrate.exe para executar migrações em relação a um banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-104">This page will give a quick overview on how to use migrate.exe to execute migrations against a database.</span></span>

> [!NOTE]
> <span data-ttu-id="ffcc2-105">Este artigo pressupõe que você sabe como usar as migrações Code First em cenários básicos.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-105">This article assumes you know how to use Code First Migrations in basic scenarios.</span></span> <span data-ttu-id="ffcc2-106">Se você não fizer isso, você precisará ler [migrações do Code First](~/ef6/modeling/code-first/migrations/index.md) antes de continuar.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-106">If you don’t, then you’ll need to read [Code First Migrations](~/ef6/modeling/code-first/migrations/index.md) before continuing.</span></span>

## <a name="copy-migrateexe"></a><span data-ttu-id="ffcc2-107">Copiar migrate.exe</span><span class="sxs-lookup"><span data-stu-id="ffcc2-107">Copy migrate.exe</span></span>

<span data-ttu-id="ffcc2-108">Quando você instala o Entity Framework usando o NuGet migrate.exe estará dentro da pasta de ferramentas do pacote baixado.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-108">When you install Entity Framework using NuGet migrate.exe will be inside the tools folder of the downloaded package.</span></span> <span data-ttu-id="ffcc2-109">Na &lt;pasta do projeto&gt;\\pacotes\\EntityFramework.&lt; versão&gt;\\ferramentas</span><span class="sxs-lookup"><span data-stu-id="ffcc2-109">In &lt;project folder&gt;\\packages\\EntityFramework.&lt;version&gt;\\tools</span></span>

<span data-ttu-id="ffcc2-110">Quando você tiver migrate.exe, em seguida, você precisará copiá-lo para o local do assembly que contém suas migrações.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-110">Once you have migrate.exe then you need to copy it to the location of the assembly that contains your migrations.</span></span>

<span data-ttu-id="ffcc2-111">Se seu aplicativo tem como alvo o .NET 4, e não 4.5, em seguida, você precisará copiar o **Redirect.config** no local bem e renomeá-lo **migrate.exe.config**. Isso é para que migrate.exe obtém os redirecionamentos de associação correto para ser capaz de localizar o assembly do Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-111">If your application targets .NET 4, and not 4.5, then you will need to copy the **Redirect.config** into the location as well and rename it **migrate.exe.config**. This is so that migrate.exe gets the correct binding redirects to be able to locate the Entity Framework assembly.</span></span>

| <span data-ttu-id="ffcc2-112">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="ffcc2-112">.NET 4.5</span></span>                                   | <span data-ttu-id="ffcc2-113">.NET 4.0</span><span class="sxs-lookup"><span data-stu-id="ffcc2-113">.NET 4.0</span></span>                                   |
|:-------------------------------------------|:-------------------------------------------|
| ![Net45Files](~/ef6/media/net45files.png)  | ![Net40Files](~/ef6/media/net40files.png)  |

> [!NOTE]
> <span data-ttu-id="ffcc2-116">Migrate.exe não dá suporte a x64 assemblies.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-116">migrate.exe doesn't support x64 assemblies.</span></span>

## <a name="using-migrateexe"></a><span data-ttu-id="ffcc2-117">Usando Migrate.exe</span><span class="sxs-lookup"><span data-stu-id="ffcc2-117">Using Migrate.exe</span></span>

<span data-ttu-id="ffcc2-118">Depois de mover migrate.exe para a pasta correta, em seguida, você poderá usá-lo para executar migrações no banco de dados.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-118">Once you have moved migrate.exe to the correct folder then you should be able to use it to execute migrations against the database.</span></span> <span data-ttu-id="ffcc2-119">Tudo o que o utilitário é projetado para fazer é executar as migrações.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-119">All the utility is designed to do is execute migrations.</span></span> <span data-ttu-id="ffcc2-120">Ele não é possível gerar migrações ou criar um script SQL.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-120">It cannot generate migrations or create a SQL script.</span></span>

### <a name="see-options"></a><span data-ttu-id="ffcc2-121">Consulte as opções</span><span class="sxs-lookup"><span data-stu-id="ffcc2-121">See options</span></span>

``` console
Migrate.exe /?
```

<span data-ttu-id="ffcc2-122">As opções acima exibirá a página de ajuda associada com esse utilitário, observe que você precisará ter o EntityFramework no mesmo local que você está executando migrate.exe para que isso funcione.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-122">The above will display the help page associated with this utility, note that you will need to have the EntityFramework.dll in the same location that you are running migrate.exe in order for this to work.</span></span>

### <a name="migrate-to-the-latest-migration"></a><span data-ttu-id="ffcc2-123">Migrar para a migração mais recente</span><span class="sxs-lookup"><span data-stu-id="ffcc2-123">Migrate to the latest migration</span></span>

``` console
Migrate.exe MyMvcApplication.dll /startupConfigurationFile=”..\\web.config”
```

<span data-ttu-id="ffcc2-124">Quando executar migrate.exe o parâmetro obrigatório somente é o assembly, que é o assembly que contém as migrações que você está tentando executar, mas ele usará a convenção de todas as configurações baseadas no se você não especificar o arquivo de configuração.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-124">When running migrate.exe the only mandatory parameter is the assembly, which is the assembly that contains the migrations that you are trying to run, but it will use all convention based settings if you do not specify the configuration file.</span></span>

### <a name="migrate-to-a-specific-migration"></a><span data-ttu-id="ffcc2-125">Migrar para uma migração específica</span><span class="sxs-lookup"><span data-stu-id="ffcc2-125">Migrate to a specific migration</span></span>

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /targetMigration=”AddTitle”
```

<span data-ttu-id="ffcc2-126">Se você quiser executar as migrações até uma migração específica, você pode especificar o nome da migração.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-126">If you want to run migrations up to a specific migration, then you can specify the name of the migration.</span></span> <span data-ttu-id="ffcc2-127">Isso executará todas as migrações anteriores conforme necessário até que a introdução à migração especificada.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-127">This will run all previous migrations as required until getting to the migration specified.</span></span>

### <a name="specify-working-directory"></a><span data-ttu-id="ffcc2-128">Especifique o diretório de trabalho</span><span class="sxs-lookup"><span data-stu-id="ffcc2-128">Specify working directory</span></span>

``` console
Migrate.exe MyApp.exe /startupConfigurationFile=”MyApp.exe.config” /startupDirectory=”c:\\MyApp”
```

<span data-ttu-id="ffcc2-129">Se você o assembly tem dependências ou lê arquivos em relação ao diretório de trabalho, em seguida, você precisará definir startupDirectory.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-129">If you assembly has dependencies or reads files relative to the working directory then you will need to set startupDirectory.</span></span>

### <a name="specify-migration-configuration-to-use"></a><span data-ttu-id="ffcc2-130">Especifique a configuração da migração para usar</span><span class="sxs-lookup"><span data-stu-id="ffcc2-130">Specify migration configuration to use</span></span>

``` console
Migrate.exe MyAssembly CustomConfig /startupConfigurationFile=”..\\web.config”
```

<span data-ttu-id="ffcc2-131">Se você tiver várias classes de configuração de migração, classes herdadas de DbMigrationConfiguration, em seguida, você precisa especificar qual deve ser usado para essa execução.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-131">If you have multiple migration configuration classes, classes inheriting from DbMigrationConfiguration, then you need to specify which is to be used for this execution.</span></span> <span data-ttu-id="ffcc2-132">Isso é especificado, fornecendo o segundo parâmetro opcional sem um comutador como acima.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-132">This is specified by providing the optional second parameter without a switch as above.</span></span>

### <a name="provide-connection-string"></a><span data-ttu-id="ffcc2-133">Forneça a cadeia de caracteres de conexão</span><span class="sxs-lookup"><span data-stu-id="ffcc2-133">Provide connection string</span></span>

``` console
Migrate.exe BlogDemo.dll /connectionString=”Data Source=localhost;Initial Catalog=BlogDemo;Integrated Security=SSPI” /connectionProviderName=”System.Data.SqlClient”
```

<span data-ttu-id="ffcc2-134">Se você quiser especificar uma cadeia de caracteres de conexão na linha de comando, em seguida, você também deve fornecer o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-134">If you wish to specify a connection string at the command line then you must also provide the provider name.</span></span> <span data-ttu-id="ffcc2-135">Não especificando o nome do provedor fará com que uma exceção.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-135">Not specifying the provider name will cause an exception.</span></span>

## <a name="common-problems"></a><span data-ttu-id="ffcc2-136">Problemas comuns</span><span class="sxs-lookup"><span data-stu-id="ffcc2-136">Common Problems</span></span>

| <span data-ttu-id="ffcc2-137">Mensagem de erro</span><span class="sxs-lookup"><span data-stu-id="ffcc2-137">Error Message</span></span>                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="ffcc2-138">Solução</span><span class="sxs-lookup"><span data-stu-id="ffcc2-138">Solution</span></span>                                                                                                                                                                                                                                                                                             |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="ffcc2-139">Exceção sem tratamento: System.IO.FileLoadException: não foi possível carregar arquivo ou assembly ' EntityFramework, versão = Version=5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089' ou uma de suas dependências.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-139">Unhandled Exception: System.IO.FileLoadException:  Could not load file or assembly 'EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089' or one of its dependencies.</span></span> <span data-ttu-id="ffcc2-140">Definição do manifesto do assembly localizado não coincide com a referência de assembly.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-140">The located assembly's manifest definition does not match the assembly reference.</span></span> <span data-ttu-id="ffcc2-141">(Exceção de HRESULT: 0x80131040)</span><span class="sxs-lookup"><span data-stu-id="ffcc2-141">(Exception from HRESULT: 0x80131040)</span></span>         | <span data-ttu-id="ffcc2-142">Normalmente, isso significa que você está executando um aplicativo .NET 4 sem o arquivo Redirect.config.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-142">This typically means that you are running a .NET 4 application without the Redirect.config file.</span></span> <span data-ttu-id="ffcc2-143">Você precisa copiar o Redirect.config no mesmo local como migrate.exe e renomeie-o para migrate.exe.config.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-143">You need to copy the Redirect.config to the same location as migrate.exe and rename it to migrate.exe.config.</span></span>                                                                                       |
| <span data-ttu-id="ffcc2-144">Exceção sem tratamento: System.IO.FileLoadException: não foi possível carregar arquivo ou assembly ' EntityFramework, versão = 4.4.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089' ou uma de suas dependências.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-144">Unhandled Exception: System.IO.FileLoadException: Could not load file or assembly 'EntityFramework, Version=4.4.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089' or one of its dependencies.</span></span> <span data-ttu-id="ffcc2-145">Definição do manifesto do assembly localizado não coincide com a referência de assembly.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-145">The located assembly's manifest definition does not match the assembly reference.</span></span> <span data-ttu-id="ffcc2-146">(Exceção de HRESULT: 0x80131040)</span><span class="sxs-lookup"><span data-stu-id="ffcc2-146">(Exception from HRESULT: 0x80131040)</span></span>          | <span data-ttu-id="ffcc2-147">Essa exceção significa que você está executando um aplicativo com o Redirect.config copiado para o local de migrate.exe do .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-147">This exception means that you are running a .NET 4.5 application with the Redirect.config copied to the migrate.exe location.</span></span> <span data-ttu-id="ffcc2-148">Se seu aplicativo for .NET 4.5, em seguida, você não precisará ter o arquivo de configuração com os redirecionamentos dentro.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-148">If your app is .NET 4.5 then you do not need to have the config file with the redirects inside.</span></span> <span data-ttu-id="ffcc2-149">Exclua o arquivo migrate.exe.config.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-149">Delete the migrate.exe.config file.</span></span>                                    |
| <span data-ttu-id="ffcc2-150">Erro: Não é possível atualizar o banco de dados de acordo com o modelo atual porque existem alterações pendentes e a migração automática está desabilitada.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-150">ERROR: Unable to update database to match the current model because there are pending changes and automatic migration is disabled.</span></span> <span data-ttu-id="ffcc2-151">Gravar as alterações do modelo pendentes em uma migração baseada em código ou habilitar a migração automática.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-151">Either write the pending model changes to a code-based migration or enable automatic migration.</span></span> <span data-ttu-id="ffcc2-152">Defina DbMigrationsConfiguration.AutomaticMigrationsEnabled para verdadeiro para habilitar a migração automática.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-152">Set DbMigrationsConfiguration.AutomaticMigrationsEnabled to true to enable automatic migration.</span></span> | <span data-ttu-id="ffcc2-153">Esse erro ocorre se migrar de execução quando você não tiver criado uma migração para lidar com as alterações feitas no modelo e o banco de dados não coincide com o modelo.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-153">This error occurs if running migrate when you haven’t created a migration to cope with changes made to the model, and the database does not match the model.</span></span> <span data-ttu-id="ffcc2-154">Adicionar uma propriedade a uma classe de modelo, em seguida, executando migrate.exe sem criar uma migração para atualizar o banco de dados é um exemplo disso.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-154">Adding a property to a model class then running migrate.exe without creating a migration to upgrade the database is an example of this.</span></span> |
| <span data-ttu-id="ffcc2-155">Erro: Tipo não for resolvido para o membro ' System.Data.Entity.Migrations.Design.ToolingFacade+UpdateRunner,EntityFramework, versão = Version=5.0.0.0, Culture = neutral, PublicKeyToken = b77a5c561934e089'.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-155">ERROR: Type is not resolved for member 'System.Data.Entity.Migrations.Design.ToolingFacade+UpdateRunner,EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089'.</span></span>                                                                                                                                       | <span data-ttu-id="ffcc2-156">Esse erro pode ser causado pela especificação de um diretório de inicialização incorretos.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-156">This error can be caused by specifying an incorrect startup directory.</span></span> <span data-ttu-id="ffcc2-157">Isso deve ser o local de migrate.exe</span><span class="sxs-lookup"><span data-stu-id="ffcc2-157">This must be the location of migrate.exe</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="ffcc2-158">Exceção sem tratamento: System. NullReferenceException: referência de objeto não definida para uma instância de um objeto.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-158">Unhandled Exception: System.NullReferenceException: Object reference not set to an instance of an object.</span></span> <br/>   <span data-ttu-id="ffcc2-159">no System.Data.Entity.Migrations.Console.Program.Main (String [] args)</span><span class="sxs-lookup"><span data-stu-id="ffcc2-159">at System.Data.Entity.Migrations.Console.Program.Main(String[] args)</span></span>                                                                                                                                             | <span data-ttu-id="ffcc2-160">Isso pode ser causado por não especificar um parâmetro obrigatório para um cenário que você está usando.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-160">This can be caused by not specifying a required parameter for a scenario that you are using.</span></span> <span data-ttu-id="ffcc2-161">Por exemplo, especificando uma cadeia de caracteres de conexão sem especificar o nome do provedor.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-161">For example specifying a connection string without specifying the provider name.</span></span>                                                                                                                        |
| <span data-ttu-id="ffcc2-162">Erro: mais de um tipo de configuração de migrações foi encontrado no assembly 'ClassLibrary1'.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-162">ERROR: More than one migrations configuration type was found in the assembly 'ClassLibrary1'.</span></span> <span data-ttu-id="ffcc2-163">Especifique o nome de um para usar.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-163">Specify the name of the one to use.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="ffcc2-164">Como o erro afirma, há mais de uma classe de configuração no assembly fornecido.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-164">As the error states, there is more than one configuration class in the given assembly.</span></span> <span data-ttu-id="ffcc2-165">Você deve usar a opção de /configurationType para especificar qual deles usar.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-165">You must use the /configurationType switch to specify which to use.</span></span>                                                                                                                                           |
| <span data-ttu-id="ffcc2-166">Erro: Não foi possível carregar arquivo ou assembly '&lt;assemblyName&gt;' ou uma de suas dependências.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-166">ERROR: Could not load file or assembly ‘&lt;assemblyName&gt;’ or one of its dependencies.</span></span> <span data-ttu-id="ffcc2-167">O assembly determinado nome ou a codebase era inválido.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-167">The given assembly name or codebase was invalid.</span></span> <span data-ttu-id="ffcc2-168">(Exceção de HRESULT: 0x80131047)</span><span class="sxs-lookup"><span data-stu-id="ffcc2-168">(Exception from HRESULT: 0x80131047)</span></span>                                                                                                                                                    | <span data-ttu-id="ffcc2-169">Isso pode ser causado por especificar um nome de assembly incorretamente ou não ter</span><span class="sxs-lookup"><span data-stu-id="ffcc2-169">This can be caused by specifying an assembly name incorrectly or not having</span></span>                                                                                                                                                                                                                          |
| <span data-ttu-id="ffcc2-170">Erro: Não foi possível carregar arquivo ou assembly '&lt;assemblyName&gt;' ou uma de suas dependências.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-170">ERROR: Could not load file or assembly ‘&lt;assemblyName&gt;' or one of its dependencies.</span></span> <span data-ttu-id="ffcc2-171">Foi feita uma tentativa de carregar um programa com um formato incorreto.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-171">An attempt was made to load a program with an incorrect format.</span></span>                                                                                                                                                                          | <span data-ttu-id="ffcc2-172">Isso acontece se você está tentando executar migrate.exe em x64 aplicativo.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-172">This happens if you are trying to run migrate.exe against an x64 application.</span></span> <span data-ttu-id="ffcc2-173">EF 5.0 e abaixo funcionará somente em x86.</span><span class="sxs-lookup"><span data-stu-id="ffcc2-173">EF 5.0 and below will only work on x86.</span></span>                                                                                                                                                                                |