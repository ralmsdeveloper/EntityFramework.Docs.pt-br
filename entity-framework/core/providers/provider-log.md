---
title: Log de alterações que afetam o provedor – EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: a87eca72aa58487415eea11e4f83de1a19e73506
ms.sourcegitcommit: 5e11125c9b838ce356d673ef5504aec477321724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50022331"
---
# <a name="provider-impacting-changes"></a>Alterações que afetam o provedor

Esta página contém links para efetuar pull de solicitações feitas no repositório do EF Core que pode exigir que os autores de outros provedores de banco de dados para reagir. A intenção é fornecer um ponto de partida para autores de provedores de banco de dados de terceiros existente ao atualizar o seu provedor para uma nova versão.

Esse log estamos começando com alterações da 2.1 para 2.2. Antes de 2.1 usamos a [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) e [ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) rótulos em nossos problemas e solicitações de pull.

## <a name="21-----22"></a>2.1---> 2.2

### <a name="test-only-changes"></a>Alterações de teste

* [https://github.com/aspnet/EntityFrameworkCore/pull/12057](https://github.com/aspnet/EntityFrameworkCore/pull/12057) -Permitir delimitadores personalizáveis do SQL nos testes
  * Testar as alterações que permitem que comparações de ponto flutuante não restrito em BuiltInDataTypesTestBase
  * Alterações de teste que permitem testes de consulta ser reutilizado com diferentes dos delimitadores SQL
* [https://github.com/aspnet/EntityFrameworkCore/pull/12072](https://github.com/aspnet/EntityFrameworkCore/pull/12072) -Adicionar testes de DbFunction para os testes de especificação relacional
  * De modo que esses testes podem ser executados em relação a todos os provedores de banco de dados
* [https://github.com/aspnet/EntityFrameworkCore/pull/12362](https://github.com/aspnet/EntityFrameworkCore/pull/12362) -Limpeza de teste Async
  * Remover `Wait` chamadas, desnecessários async e renomeado alguns métodos de teste
* [https://github.com/aspnet/EntityFrameworkCore/pull/12666](https://github.com/aspnet/EntityFrameworkCore/pull/12666) -Unificar a infraestrutura de teste do log
  * Adicionado `CreateListLoggerFactory` e removidos alguns infraestrutura de log anterior, o que exigirá provedores que usam esses testes para reagir
* [https://github.com/aspnet/EntityFrameworkCore/pull/12500](https://github.com/aspnet/EntityFrameworkCore/pull/12500) -Executar mais testes de consulta de forma síncrona e assíncrona
  * Nomes de teste e a fatoração foi alterado, que exigirá provedores que usam esses testes para reagir
* [https://github.com/aspnet/EntityFrameworkCore/pull/12766](https://github.com/aspnet/EntityFrameworkCore/pull/12766) -Renomeação navegações no modelo ComplexNavigations
  * Talvez seja necessário reagir provedores que usam esses testes
* [https://github.com/aspnet/EntityFrameworkCore/pull/12141](https://github.com/aspnet/EntityFrameworkCore/pull/12141) -Retornar o contexto para o pool em vez de descartar em testes funcionais
  * Essa alteração inclui certa refatoração do teste que podem exigir provedores reagir


### <a name="test-and-product-code-changes"></a>Alterações de código do produto e de teste

* [https://github.com/aspnet/EntityFrameworkCore/pull/12109](https://github.com/aspnet/EntityFrameworkCore/pull/12109) -Consolidar RelationalTypeMapping.Clone métodos
  * As alterações no 2.1 para a RelationalTypeMapping permitidas para uma simplificação em classes derivadas. Não acreditamos que isso estava interrompendo aos provedores, mas provedores podem tirar proveito dessa alteração no seu tipo derivado classes de mapeamento.
* [https://github.com/aspnet/EntityFrameworkCore/pull/12069](https://github.com/aspnet/EntityFrameworkCore/pull/12069) -Consultas nomeadas ou marcadas
  * Adiciona a infraestrutura para marcação de consultas LINQ e ter essas marcas que aparecem como comentários no SQL. Isso pode exigir provedores reagir na geração de SQL.
* [https://github.com/aspnet/EntityFrameworkCore/pull/13115](https://github.com/aspnet/EntityFrameworkCore/pull/13115) -Suporte a dados espaciais via NTS
  * Permite que os mapeamentos de tipo e membro tradutores a serem registrados fora do provedor
    * Provedores devem chamar base. FindMapping() em sua implementação ITypeMappingSource para funcionar
  * Siga esse padrão para adicionar suporte espacial ao seu provedor que é consistente com provedores.
* [https://github.com/aspnet/EntityFrameworkCore/pull/13199](https://github.com/aspnet/EntityFrameworkCore/pull/13199) -Adicionar depuração aprimorada para criação de provedor de serviços
  * Permite que DbContextOptionsExtensions implementar uma nova interface que pode ajudar as pessoas a entender por que está sendo compilado novamente o provedor de serviço interno
* [https://github.com/aspnet/EntityFrameworkCore/pull/13289](https://github.com/aspnet/EntityFrameworkCore/pull/13289) -Adiciona CanConnect API para uso por verificações de integridade
  * Essa solicitação de pull adiciona o conceito de `CanConnect` que será usada pela integridade do ASP.NET Core verifica para determinar se o banco de dados está disponível. Por padrão, a implementação relacional apenas chama `Exist`, mas os provedores podem implementar algo diferente se necessário. Provedores não relacionais precisará implementar a nova API para que a verificação de integridade ser usado.
* [https://github.com/aspnet/EntityFrameworkCore/pull/13306](https://github.com/aspnet/EntityFrameworkCore/pull/13306) -Atualizar RelationalTypeMapping base para definir o tamanho de DbParameter
  * Pare definindo o tamanho por padrão, pois isso pode causar truncamento. Provedores talvez seja necessário adicionar sua própria lógica, se o tamanho deve ser definida.
* [https://github.com/aspnet/EntityFrameworkCore/pull/13372](https://github.com/aspnet/EntityFrameworkCore/pull/13372) -RevEng: Sempre especificar tipo de coluna para colunas decimais
  * Sempre configure tipo de coluna para colunas decimais no código gerado por scaffolding em vez de configurar por convenção.
  * Provedores não devem exigir qualquer alteração em seu lado.
* [https://github.com/aspnet/EntityFrameworkCore/pull/13469](https://github.com/aspnet/EntityFrameworkCore/pull/13469) -Adiciona CaseExpression para gerar as expressões de caso de SQL
* [https://github.com/aspnet/EntityFrameworkCore/pull/13648](https://github.com/aspnet/EntityFrameworkCore/pull/13648) – Adiciona a capacidade de especificar mapeamentos de tipo em SqlFunctionExpression para melhorar a inferência de tipo de repositório de argumentos e os resultados.
