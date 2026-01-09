DESAFIOS DE LINQ

Aqui está o código de preparação (Setup). Copie e cole no seu LINQPad (modo **C# Program**).

### 1. O Setup de Dados (Copie para o LINQPad)

```csharp
void Main()
{
	// Dados Mockados
	var categorias = new List<Categoria>
	{
		new Categoria { Id = 1, Nome = "Eletrônicos" },
		new Categoria { Id = 2, Nome = "Móveis" },
		new Categoria { Id = 3, Nome = "Vestuário" },
		new Categoria { Id = 4, Nome = "Alimentos" } // Categoria sem produtos para teste
	};

	var produtos = new List<Produto>
	{
		new Produto { Id = 1, CategoriaId = 1, Nome = "Notebook Dell", Preco = 3500m, Ativo = true },
		new Produto { Id = 2, CategoriaId = 1, Nome = "Mouse Logitech", Preco = 120m, Ativo = true },
		new Produto { Id = 3, CategoriaId = 2, Nome = "Cadeira Gamer", Preco = 1200m, Ativo = true },
		new Produto { Id = 4, CategoriaId = 2, Nome = "Mesa de Escritório", Preco = 800m, Ativo = false }, // Inativo
		new Produto { Id = 5, CategoriaId = 3, Nome = "Camiseta Dev", Preco = 50m, Ativo = true },
		new Produto { Id = 6, CategoriaId = 1, Nome = "Monitor 24pol", Preco = 900m, Ativo = true }
	};

	var clientes = new List<Cliente>
	{
		new Cliente { Id = 1, Nome = "João Silva", Estado = "SP", Email = "joao@email.com" },
		new Cliente { Id = 2, Nome = "Maria Souza", Estado = "RJ", Email = "maria@email.com" },
		new Cliente { Id = 3, Nome = "Pedro Santos", Estado = "MG", Email = "pedro@email.com" },
		new Cliente { Id = 4, Nome = "Ana Costa", Estado = "SP", Email = "ana@email.com" } // Cliente sem pedidos
	};

	var pedidos = new List<Pedido>
	{
		new Pedido { Id = 101, ClienteId = 1, Data = new DateTime(2023, 1, 15), Status = "Entregue", Itens = new List<ItemPedido>
			{
				new ItemPedido { ProdutoId = 1, Quantidade = 1, PrecoUnitario = 3500m },
				new ItemPedido { ProdutoId = 2, Quantidade = 2, PrecoUnitario = 120m }
			} 
		},
		new Pedido { Id = 102, ClienteId = 2, Data = new DateTime(2023, 2, 20), Status = "Entregue", Itens = new List<ItemPedido>
			{
				new ItemPedido { ProdutoId = 3, Quantidade = 1, PrecoUnitario = 1200m }
			}
		},
		new Pedido { Id = 103, ClienteId = 1, Data = new DateTime(2023, 3, 10), Status = "Cancelado", Itens = new List<ItemPedido>
			{
				new ItemPedido { ProdutoId = 5, Quantidade = 5, PrecoUnitario = 50m }
			}
		},
		new Pedido { Id = 104, ClienteId = 3, Data = new DateTime(2023, 3, 15), Status = "Processando", Itens = new List<ItemPedido>
			{
				new ItemPedido { ProdutoId = 1, Quantidade = 1, PrecoUnitario = 3600m }, // Preço variou
				new ItemPedido { ProdutoId = 6, Quantidade = 1, PrecoUnitario = 900m }
			}
		},
		new Pedido { Id = 105, ClienteId = 2, Data = new DateTime(2023, 4, 05), Status = "Entregue", Itens = new List<ItemPedido>
			{
				new ItemPedido { ProdutoId = 2, Quantidade = 1, PrecoUnitario = 120m },
				new ItemPedido { ProdutoId = 5, Quantidade = 2, PrecoUnitario = 50m }
			}
		}
	};
	
	// SEU CÓDIGO COMEÇA AQUI
	// Exemplo: pedidos.Dump();
}

// Classes de Domínio
public class Categoria
{
	public int Id { get; set; }
	public string Nome { get; set; }
}

public class Produto
{
	public int Id { get; set; }
	public int CategoriaId { get; set; }
	public string Nome { get; set; }
	public decimal Preco { get; set; } // Preço atual de tabela
	public bool Ativo { get; set; }
}

public class Cliente
{
	public int Id { get; set; }
	public string Nome { get; set; }
	public string Estado { get; set; }
	public string Email { get; set; }
}

public class Pedido
{
	public int Id { get; set; }
	public int ClienteId { get; set; }
	public DateTime Data { get; set; }
	public string Status { get; set; } // Entregue, Processando, Cancelado
	public List<ItemPedido> Itens { get; set; }
}

public class ItemPedido
{
	public int ProdutoId { get; set; }
	public int Quantidade { get; set; }
	public decimal PrecoUnitario { get; set; } // Preço histórico no momento da venda
}

```

---

### Os Desafios

Não pule etapas. Se você realmente quer evoluir para um nível de engenharia sênior, a base precisa ser inabalável.

#### Nível 1: Iniciante (Filtros Compostos e Projeções Simples)

O foco aqui é limpar os dados. Se você não consegue filtrar o lixo, não consegue analisar nada.

1. **Produtos Ativos de Categoria Específica:** Liste o nome e o preço de todos os produtos ativos da categoria "Eletrônicos" (Use o ID 1 para filtrar).
2. **Pedidos Recentes:** Liste todos os pedidos feitos a partir de Março de 2023, ordenados pela data (do mais recente para o mais antigo).
3. **Verificação de Estoque:** Verifique se existe algum produto na lista com preço menor que 100 reais (Retorne apenas um booleano).
4. **Busca por Estado:** Retorne os nomes dos clientes que moram em "SP".
5. **Cálculo Simples:** Qual é o valor total do pedido de ID 101? (Dica: Sum nos itens, multiplicando quantidade por preço).

#### Nível 2: Intermediário (Flattening, Tipos Anônimos e DTOs)

Aqui começamos a transformar os dados para o que o Front-end ou a API precisa, não o que o banco devolve.

6. **Itens Vendidos (Flattening):** Crie uma lista plana (flat) contendo todos os `ItemPedido` de todos os pedidos entregues. (Dica: `SelectMany`).
7. **Projeção de DTO:** Crie uma projeção (Select) que retorne um objeto anônimo com: `IdPedido`, `Data`, `NomeCliente` (Vai precisar cruzar com a lista de clientes aqui ou usar o ID se não quiser fazer Join agora) e `ValorTotalPedido`.
8. **Produtos Caros:** Liste os nomes dos produtos cujo preço seja superior à média de preços de todos os produtos.
9. **Vendas de um Produto:** Quantas unidades do produto "Mouse Logitech" (Id 2) foram vendidas no total, considerando apenas pedidos "Entregue"?
10. **Clientes sem Pedidos:** Liste o nome dos clientes que **não** fizeram nenhum pedido até hoje.

#### Nível 3: Avançado (Group, Join e Relacionamentos Complexos)

É aqui que o estagiário trava e o sênior resolve. Agrupamentos e cruzamento de dados heterogêneos.

11. **Agrupamento por Estado:** Agrupe os clientes por Estado e conte quantos clientes existem em cada estado.
12. **Relatório de Categorias (Join):** Liste o Nome da Categoria e o Nome do Produto. Devem aparecer apenas categorias que possuem produtos.
13. **Vendas por Mês:** Agrupe os pedidos pelo mês/ano e retorne quanto foi faturado em cada mês (Considere apenas status "Entregue").
14. **Top Cliente:** Quem é o cliente que mais gastou dinheiro na loja (somando todos os pedidos entregues)? Retorne o objeto Cliente.
15. **Inner Join Manual:** Sem usar a navegação de objetos (imagine que `Itens` não estivesse aninhado em `Pedido`), cruze a lista de `Pedidos` com `Clientes` usando `Join` para listar: Data do Pedido e Nome do Cliente.

#### Nível 4: Expert (Analytics, GroupJoin e Lógica de Negócio)

Cenários de BI e relatórios que exigem manipulação profunda da coleção.

16. **Ticket Médio por Cliente:** Calcule o ticket médio (Valor Total Gasto / Número de Pedidos) de cada cliente. Retorne: Nome, QtdPedidos, TicketMedio.
17. **Produtos Nunca Vendidos:** Liste os produtos que nunca apareceram em nenhum pedido (Dica: Use `Except` ou `!Any` com `SelectMany`).
18. **Categoria Mais Valiosa:** Qual categoria possui o maior valor total de produtos em estoque (soma dos preços de tabela dos produtos ativos)?
19. **Análise de Portfólio (GroupJoin):** Liste **todas** as categorias e, para cada uma, mostre a lista de seus produtos. Se a categoria não tiver produtos, a lista deve estar vazia (não nula).
20. **O Dashboard (Single Shot):** Em uma única consulta LINQ, retorne um objeto anônimo contendo:
* `TotalVendasGeral`: Soma de todos os pedidos entregues.
* `ClienteMaisAtivo`: Nome do cliente com mais pedidos.
* `ProdutoMaisVendido`: Nome do produto com maior quantidade vendida.



---
