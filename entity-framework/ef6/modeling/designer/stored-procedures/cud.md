---
title: Designer CUD procedimentos armazenados – EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 1e773972-2da5-45e0-85a2-3cf3fbcfa5cf
caps.latest.revision: 3
ms.openlocfilehash: 6b6a1f843142713153fa86309ef55f9d6e804766
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2018
ms.locfileid: "39119735"
---
# <a name="designer-cud-stored-procedures"></a><span data-ttu-id="cbac7-102">Procedimentos armazenados de CUD Designer</span><span class="sxs-lookup"><span data-stu-id="cbac7-102">Designer CUD Stored Procedures</span></span>
<span data-ttu-id="cbac7-103">Este passo a passo mostram como mapear o criar\\inserir, atualizar e excluir operações (CUD) de um tipo de entidade para procedimentos armazenados usando o Entity Framework Designer (Designer de EF).</span><span class="sxs-lookup"><span data-stu-id="cbac7-103">This step-by-step walkthrough show how to map the create\\insert, update, and delete (CUD) operations of an entity type to stored procedures using the Entity Framework Designer (EF Designer).</span></span>  <span data-ttu-id="cbac7-104">Por padrão, o Entity Framework gera automaticamente as instruções SQL para as operações de CUD, mas você também pode mapear procedimentos armazenados para essas operações.</span><span class="sxs-lookup"><span data-stu-id="cbac7-104">By default, the Entity Framework automatically generates the SQL statements for the CUD operations, but you can also map stored procedures to these operations.</span></span>  

<span data-ttu-id="cbac7-105">Observe que o Code First não suporta o mapeamento para procedimentos armazenados ou funções.</span><span class="sxs-lookup"><span data-stu-id="cbac7-105">Note, that Code First does not support mapping to stored procedures or functions.</span></span> <span data-ttu-id="cbac7-106">No entanto, você pode chamar procedimentos armazenados ou funções por meio do método System.Data.Entity.DbSet.SqlQuery.</span><span class="sxs-lookup"><span data-stu-id="cbac7-106">However, you can call stored procedures or functions by using the System.Data.Entity.DbSet.SqlQuery method.</span></span> <span data-ttu-id="cbac7-107">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="cbac7-107">For example:</span></span>
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a><span data-ttu-id="cbac7-108">Considerações ao mapear as operações de CUD de procedimentos armazenados</span><span class="sxs-lookup"><span data-stu-id="cbac7-108">Considerations when Mapping the CUD Operations to Stored Procedures</span></span>

<span data-ttu-id="cbac7-109">Ao mapear as operações de CUD para procedimentos armazenados, as seguintes considerações se aplicam:</span><span class="sxs-lookup"><span data-stu-id="cbac7-109">When mapping the CUD operations to stored procedures, the following considerations apply:</span></span> 

-   <span data-ttu-id="cbac7-110">Se uma das operações CUD estiver mapeando para um procedimento armazenado, mapeie todos eles.</span><span class="sxs-lookup"><span data-stu-id="cbac7-110">If you are mapping one of the CUD operations to a stored procedure, map all of them.</span></span> <span data-ttu-id="cbac7-111">Se você não mapear todos os três, as operações não mapeadas falhará se executado e uma **UpdateException** será lançada.</span><span class="sxs-lookup"><span data-stu-id="cbac7-111">If you do not map all three, the unmapped operations will fail if executed and an **UpdateException** will be thrown.</span></span>
-   <span data-ttu-id="cbac7-112">Você deve mapear cada parâmetro do procedimento armazenado para as propriedades da entidade.</span><span class="sxs-lookup"><span data-stu-id="cbac7-112">You must map every parameter of the stored procedure to entity properties.</span></span>
-   <span data-ttu-id="cbac7-113">Se o servidor gera o valor de chave primária para a linha inserida, você deve mapear esse valor para a propriedade de chave da entidade.</span><span class="sxs-lookup"><span data-stu-id="cbac7-113">If the server generates the primary key value for the inserted row, you must map this value back to the entity's key property.</span></span> <span data-ttu-id="cbac7-114">No exemplo a seguir, o **InsertPerson** procedimento armazenado retorna a chave primária recentemente criada como parte do conjunto de resultados do procedimento armazenado.</span><span class="sxs-lookup"><span data-stu-id="cbac7-114">In the example that follows, the **InsertPerson** stored procedure returns the newly created primary key as part of the stored procedure's result set.</span></span> <span data-ttu-id="cbac7-115">A chave primária é mapeada para a chave de entidade (**PersonID**) usando o **&lt;adicionar associações de resultado&gt;** recurso do EF Designer.</span><span class="sxs-lookup"><span data-stu-id="cbac7-115">The primary key is mapped to the entity key (**PersonID**) using the **&lt;Add Result Bindings&gt;** feature of the EF Designer.</span></span>
-   <span data-ttu-id="cbac7-116">As chamadas de procedimento armazenado são mapeados 1:1 com as entidades no modelo conceitual.</span><span class="sxs-lookup"><span data-stu-id="cbac7-116">The stored procedure calls are mapped 1:1 with the entities in the conceptual model.</span></span> <span data-ttu-id="cbac7-117">Por exemplo, se você implementar uma hierarquia de herança no modelo conceitual e mapeie o CUD procedimentos armazenados para o **pai** (base) e o **filho** entidades (derivadas), salvando o **Filho** alterações chamará somente o **filho**de procedimentos armazenados, ele não disparará a **pai**do armazenados chamadas de procedimentos.</span><span class="sxs-lookup"><span data-stu-id="cbac7-117">For example, if you implement an inheritance hierarchy in your conceptual model and then map the CUD stored procedures for the **Parent** (base) and the **Child** (derived) entities, saving the **Child** changes will only call the **Child**’s stored procedures, it will not trigger the **Parent**’s stored procedures calls.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cbac7-118">Pré-requisitos</span><span class="sxs-lookup"><span data-stu-id="cbac7-118">Prerequisites</span></span>

<span data-ttu-id="cbac7-119">Para concluir esta explicação passo a passo, será necessário:</span><span class="sxs-lookup"><span data-stu-id="cbac7-119">To complete this walkthrough, you will need:</span></span>

- <span data-ttu-id="cbac7-120">Uma versão recente do Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cbac7-120">A recent version of Visual Studio.</span></span>
- <span data-ttu-id="cbac7-121">O [banco de dados de exemplo School](~/ef6/resources/school-database.md).</span><span class="sxs-lookup"><span data-stu-id="cbac7-121">The [School sample database](~/ef6/resources/school-database.md).</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="cbac7-122">Configurar o projeto</span><span class="sxs-lookup"><span data-stu-id="cbac7-122">Set up the Project</span></span>

-   <span data-ttu-id="cbac7-123">Abra o Visual Studio 2012.</span><span class="sxs-lookup"><span data-stu-id="cbac7-123">Open Visual Studio 2012.</span></span>
-   <span data-ttu-id="cbac7-124">Selecione **arquivo -&gt; New -&gt; projeto**</span><span class="sxs-lookup"><span data-stu-id="cbac7-124">Select **File-&gt; New -&gt; Project**</span></span>
-   <span data-ttu-id="cbac7-125">No painel esquerdo, clique em **Visual C#\#** e, em seguida, selecione o **Console** modelo.</span><span class="sxs-lookup"><span data-stu-id="cbac7-125">In the left pane, click **Visual C\#**, and then select the **Console** template.</span></span>
-   <span data-ttu-id="cbac7-126">Insira **CUDSProcsSample** como o nome.</span><span class="sxs-lookup"><span data-stu-id="cbac7-126">Enter **CUDSProcsSample** as the name.</span></span>
-   <span data-ttu-id="cbac7-127">Selecione **OK**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-127">Select **OK**.</span></span>

## <a name="create-a-model"></a><span data-ttu-id="cbac7-128">Criar um modelo</span><span class="sxs-lookup"><span data-stu-id="cbac7-128">Create a Model</span></span>

-   <span data-ttu-id="cbac7-129">O nome do projeto no Gerenciador de soluções e selecione **Add -&gt; Novo Item**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-129">Right-click the project name in Solution Explorer, and select **Add -&gt; New Item**.</span></span>
-   <span data-ttu-id="cbac7-130">Selecione **dados** no menu esquerdo e selecione **modelo de dados de entidade ADO.NET** no painel de modelos.</span><span class="sxs-lookup"><span data-stu-id="cbac7-130">Select **Data** from the left menu and then select **ADO.NET Entity Data Model** in the Templates pane.</span></span>
-   <span data-ttu-id="cbac7-131">Insira **CUDSProcs.edmx** para o nome de arquivo e clique **Add**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-131">Enter **CUDSProcs.edmx** for the file name, and then click **Add**.</span></span>
-   <span data-ttu-id="cbac7-132">Na caixa de diálogo Escolher conteúdo do modelo, selecione **gerar a partir do banco de dados**e, em seguida, clique em **próxima**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-132">In the Choose Model Contents dialog box, select **Generate from database**, and then click **Next**.</span></span>
-   <span data-ttu-id="cbac7-133">Clique em **nova Conexão**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-133">Click **New Connection**.</span></span> <span data-ttu-id="cbac7-134">Na caixa de diálogo Propriedades de Conexão, insira o nome do servidor (por exemplo, **(localdb)\\mssqllocaldb**), selecione o método de autenticação, digite **School** para o nome do banco de dados e, em seguida, Clique em **Okey**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-134">In the Connection Properties dialog box, enter the server name (for example, **(localdb)\\mssqllocaldb**), select the authentication method, type **School** for the database name, and then click **OK**.</span></span>
    <span data-ttu-id="cbac7-135">A caixa de diálogo Escolher sua Conexão de dados é atualizada com sua configuração de conexão de banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cbac7-135">The Choose Your Data Connection dialog box is updated with your database connection setting.</span></span>
-   <span data-ttu-id="cbac7-136">Na escolha seu objetos de banco de dados caixa de diálogo do **tabelas** nó, selecione o **pessoa** tabela.</span><span class="sxs-lookup"><span data-stu-id="cbac7-136">In the Choose Your Database Objects dialog box, under the **Tables** node, select the **Person** table.</span></span>
-   <span data-ttu-id="cbac7-137">Além disso, selecione os seguintes procedimentos armazenados sob a **procedimentos armazenados e funções** nó: **DeletePerson**, **InsertPerson**, e **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="cbac7-137">Also, select the following stored procedures under the **Stored Procedures and Functions** node: **DeletePerson**, **InsertPerson**, and **UpdatePerson**.</span></span> 
-   <span data-ttu-id="cbac7-138">Começando com o Visual Studio 2012, o EF Designer oferece suporte a importação em massa de procedimentos armazenados.</span><span class="sxs-lookup"><span data-stu-id="cbac7-138">Starting with Visual Studio 2012 the EF Designer supports bulk import of stored procedures.</span></span> <span data-ttu-id="cbac7-139">O **importação selecionado procedimentos armazenados e funções no modelo de entidade** é marcada por padrão.</span><span class="sxs-lookup"><span data-stu-id="cbac7-139">The **Import selected stored procedures and functions into the entity model** is checked by default.</span></span> <span data-ttu-id="cbac7-140">Como neste exemplo estamos têm procedimentos armazenados que inserir, atualizarem e excluir tipos de entidade, vamos não quiser importá-los e será desmarque essa caixa de seleção.</span><span class="sxs-lookup"><span data-stu-id="cbac7-140">Since in this example we have stored procedures that insert, update, and delete entity types, we do not want to import them and will uncheck this checkbox.</span></span> 

    ![ImportSProcs](~/ef6/media/importsprocs.jpg)

-   <span data-ttu-id="cbac7-142">Clique em **Finalizar**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-142">Click **Finish**.</span></span>
    <span data-ttu-id="cbac7-143">O Designer de EF, que fornece uma superfície de design para editar seu modelo, é exibido.</span><span class="sxs-lookup"><span data-stu-id="cbac7-143">The EF Designer, which provides a design surface for editing your model, is displayed.</span></span>

## <a name="map-the-person-entity-to-stored-procedures"></a><span data-ttu-id="cbac7-144">Mapear a entidade de pessoa para procedimentos armazenados</span><span class="sxs-lookup"><span data-stu-id="cbac7-144">Map the Person Entity to Stored Procedures</span></span>

-   <span data-ttu-id="cbac7-145">Clique com botão direito do **pessoa** tipo de entidade e selecione **mapeamento de procedimento armazenado**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-145">Right-click the **Person** entity type and select **Stored Procedure Mapping**.</span></span>
-   <span data-ttu-id="cbac7-146">Os mapeamentos de procedimento armazenado são exibidos na **Mapping Details** janela.</span><span class="sxs-lookup"><span data-stu-id="cbac7-146">The stored procedure mappings appear in the **Mapping Details** window.</span></span>
-   <span data-ttu-id="cbac7-147">Clique em  **&lt;selecione Inserir função&gt;**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-147">Click **&lt;Select Insert Function&gt;**.</span></span>
    <span data-ttu-id="cbac7-148">O campo se torna uma lista suspensa dos procedimentos armazenados no modelo de armazenamento que podem ser mapeados para tipos de entidade no modelo conceitual.</span><span class="sxs-lookup"><span data-stu-id="cbac7-148">The field becomes a drop-down list of the stored procedures in the storage model that can be mapped to entity types in the conceptual model.</span></span>
    <span data-ttu-id="cbac7-149">Selecione **InsertPerson** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="cbac7-149">Select **InsertPerson** from the drop-down list.</span></span>
-   <span data-ttu-id="cbac7-150">Mapeamentos padrão entre os parâmetros de procedimento armazenado e as propriedades da entidade são exibidos.</span><span class="sxs-lookup"><span data-stu-id="cbac7-150">Default mappings between stored procedure parameters and entity properties appear.</span></span> <span data-ttu-id="cbac7-151">Observe que as setas indicam a direção de mapeamento: valores de propriedade são fornecidos para os parâmetros de procedimento armazenado.</span><span class="sxs-lookup"><span data-stu-id="cbac7-151">Note that arrows indicate the mapping direction: Property values are supplied to stored procedure parameters.</span></span>
-   <span data-ttu-id="cbac7-152">Clique em  **&lt;Adicionar associação de resultado&gt;**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-152">Click **&lt;Add Result Binding&gt;**.</span></span>
-   <span data-ttu-id="cbac7-153">Tipo de **NewPersonID**, o nome do parâmetro retornado pelo **InsertPerson** procedimento armazenado.</span><span class="sxs-lookup"><span data-stu-id="cbac7-153">Type **NewPersonID**, the name of the parameter returned by the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="cbac7-154">Certifique-se de não digite espaços à esquerda ou à direita.</span><span class="sxs-lookup"><span data-stu-id="cbac7-154">Make sure not to type leading or trailing spaces.</span></span>
-   <span data-ttu-id="cbac7-155">Pressione **ENTER**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-155">Press **Enter**.</span></span>
-   <span data-ttu-id="cbac7-156">Por padrão, **NewPersonID** é mapeado para a chave de entidade **PersonID**.</span><span class="sxs-lookup"><span data-stu-id="cbac7-156">By default, **NewPersonID** is mapped to the entity key **PersonID**.</span></span> <span data-ttu-id="cbac7-157">Observe que uma seta indica a direção do mapeamento: O valor da coluna de resultado é fornecido para a propriedade.</span><span class="sxs-lookup"><span data-stu-id="cbac7-157">Note that an arrow indicates the direction of the mapping: The value of the result column is supplied to the property.</span></span>

    ![MappingDetails](~/ef6/media/mappingdetails.png)

-   <span data-ttu-id="cbac7-159">Clique em **&lt;Selecionar função de atualização&gt;** e selecione **UpdatePerson** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="cbac7-159">Click **&lt;Select Update Function&gt;** and select **UpdatePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="cbac7-160">Mapeamentos padrão entre os parâmetros de procedimento armazenado e as propriedades da entidade são exibidos.</span><span class="sxs-lookup"><span data-stu-id="cbac7-160">Default mappings between stored procedure parameters and entity properties appear.</span></span>
-   <span data-ttu-id="cbac7-161">Clique em **&lt;Selecionar função de exclusão&gt;** e selecione **DeletePerson** na lista suspensa.</span><span class="sxs-lookup"><span data-stu-id="cbac7-161">Click **&lt;Select Delete Function&gt;** and select **DeletePerson** from the resulting drop-down list.</span></span>
-   <span data-ttu-id="cbac7-162">Mapeamentos padrão entre os parâmetros de procedimento armazenado e as propriedades da entidade são exibidos.</span><span class="sxs-lookup"><span data-stu-id="cbac7-162">Default mappings between stored procedure parameters and entity properties appear.</span></span>

<span data-ttu-id="cbac7-163">Inserir, atualizar e excluir operações do **pessoa** tipo de entidade agora são mapeados para procedimentos armazenados.</span><span class="sxs-lookup"><span data-stu-id="cbac7-163">The insert, update, and delete operations of the **Person** entity type are now mapped to stored procedures.</span></span>

<span data-ttu-id="cbac7-164">Se você quiser habilitar a verificação de concorrência ao atualizar ou excluir uma entidade com procedimentos armazenados, use uma das seguintes opções:</span><span class="sxs-lookup"><span data-stu-id="cbac7-164">If you want to enable concurrency checking when updating or deleting an entity with stored procedures, use one of the following options:</span></span>

-   <span data-ttu-id="cbac7-165">Use uma **saída** afetados de parâmetro para retornar o número de linhas do procedimento armazenado e verifique se o **linhas afetadas parâmetro** caixa de seleção ao lado do nome do parâmetro.</span><span class="sxs-lookup"><span data-stu-id="cbac7-165">Use an **OUTPUT** parameter to return the number of affected rows from the stored procedure and check the **Rows Affected Parameter** checkbox next to the parameter name.</span></span> <span data-ttu-id="cbac7-166">Se o valor retornado é zero quando a operação é chamada, uma [ **OptimisticConcurrencyException** ](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) será lançada.</span><span class="sxs-lookup"><span data-stu-id="cbac7-166">If the value returned is zero when the operation is called, an  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) will be thrown.</span></span>
-   <span data-ttu-id="cbac7-167">Verifique as **usar o valor Original** caixa de seleção ao lado de uma propriedade que você deseja usar para verificação de simultaneidade.</span><span class="sxs-lookup"><span data-stu-id="cbac7-167">Check the **Use Original Value** checkbox next to a property that you want to use for concurrency checking.</span></span> <span data-ttu-id="cbac7-168">Quando uma tentativa de atualização, o valor da propriedade que foi originalmente leitura do banco de dados será usado ao gravar dados de volta para o banco de dados.</span><span class="sxs-lookup"><span data-stu-id="cbac7-168">When an update is attempted, the value of the property that was originally read from the database will be used when writing data back to the database.</span></span> <span data-ttu-id="cbac7-169">Se o valor não coincide com o valor no banco de dados, uma **OptimisticConcurrencyException** será lançada.</span><span class="sxs-lookup"><span data-stu-id="cbac7-169">If the value does not match the value in the database, an **OptimisticConcurrencyException** will be thrown.</span></span>

## <a name="use-the-model"></a><span data-ttu-id="cbac7-170">Usar o modelo</span><span class="sxs-lookup"><span data-stu-id="cbac7-170">Use the Model</span></span>

<span data-ttu-id="cbac7-171">Abra o **Program.cs** do arquivo onde o **Main** método é definido.</span><span class="sxs-lookup"><span data-stu-id="cbac7-171">Open the **Program.cs** file where the **Main** method is defined.</span></span> <span data-ttu-id="cbac7-172">Adicione o seguinte código na função principal.</span><span class="sxs-lookup"><span data-stu-id="cbac7-172">Add the following code into the Main function.</span></span>

<span data-ttu-id="cbac7-173">O código cria um novo **pessoa** objeto, em seguida, atualiza o objeto e, por fim, exclui o objeto.</span><span class="sxs-lookup"><span data-stu-id="cbac7-173">The code creates a new **Person** object, then updates the object, and finally deletes the object.</span></span>         

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

-   <span data-ttu-id="cbac7-174">Compile e execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="cbac7-174">Compile and run the application.</span></span> <span data-ttu-id="cbac7-175">O programa produz a saída a seguir \*</span><span class="sxs-lookup"><span data-stu-id="cbac7-175">The program produces the following output \*</span></span>
    >[!NOTE]
> <span data-ttu-id="cbac7-176">PersonID é gerado automaticamente pelo servidor, portanto, você provavelmente verá um número diferente \*</span><span class="sxs-lookup"><span data-stu-id="cbac7-176">PersonID is auto-generated by the server, so you will most likely see a different number\*</span></span>

```
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

<span data-ttu-id="cbac7-177">Se você estiver trabalhando com a versão Ultimate do Visual Studio, você pode usar o Intellitrace com o depurador para ver as instruções SQL que são executadas.</span><span class="sxs-lookup"><span data-stu-id="cbac7-177">If you are working with the Ultimate version of Visual Studio, you can use Intellitrace with the debugger to see the SQL statements that get executed.</span></span>

![IntelliTrace](~/ef6/media/intellitrace.png)