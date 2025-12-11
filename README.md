


# 📚 Guia de Integração: .NET 8 API + PostgreSQL (Supabase)

Este documento detalha o procedimento padrão ("Golden Path") para conectar uma API .NET 8 a um banco de dados PostgreSQL (via Supabase ou Docker), utilizando Entity Framework Core e isolamento de Schema.

---

## 🛠️ Pré-requisitos
* **.NET SDK 8.0** instalado.
* Projeto criado (`dotnet new webapi`).
* Acesso ao painel do Supabase (ou instância local do Postgres).

---

## 🚀 Passo a Passo

### 1. Saneamento do Ambiente (Terminal)
Garanta que as ferramentas globais do EF Core estejam alinhadas com a versão do projeto (LTS 8) para evitar erros de runtime.

```bash
# Remove ferramentas antigas ou versões "preview" (v10, v9, etc)
dotnet tool uninstall --global dotnet-ef

# Instala a versão estrita 8.x
dotnet tool install --global dotnet-ef --version 8.*
````

### 2\. Instalação de Dependências (NuGet)

Instale apenas os pacotes necessários na versão **8.0.0**. Execute na raiz do projeto:

```bash
# Driver do PostgreSQL para EF Core
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL --version 8.0.0

# Ferramentas de Design (Obrigatório para Migrations)
dotnet add package Microsoft.EntityFrameworkCore.Design --version 8.0.0
```

> **Nota:** Se houver pacotes do `SqlServer` instalados por engano, remova-os com `dotnet remove package Microsoft.EntityFrameworkCore.SqlServer`.

### 3\. Configuração do Contexto (C\#)

Crie ou edite o arquivo de contexto. **Regra:** Utilize sempre um **Schema** personalizado para não poluir o schema `public` do banco.

**Arquivo:** `Context/AgendaContext.cs` (Exemplo)

```csharp
using Microsoft.EntityFrameworkCore;
using api_exemplo.Models; // Ajuste para seu namespace

namespace api_exemplo.Context
{
    public class AgendaContext : DbContext
    {
        public AgendaContext(DbContextOptions<AgendaContext> options) : base(options)
        {
        }

        // Mapeamento das Tabelas
        public DbSet<Contato> Contatos { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            // ISOLAMENTO DE SCHEMA: Define a "pasta" onde as tabelas serão criadas.
            // Ex: "agenda", "financeiro", "rh".
            modelBuilder.HasDefaultSchema("agenda"); 
            
            base.OnModelCreating(modelBuilder);
        }
    }
}
```

### 4\. Connection String (`appsettings.json`)

Configure a conexão. Para Supabase, utilize a porta **5432** (Session Mode) para garantir compatibilidade com Migrations.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=db.SEU-PROJETO.supabase.co;Port=5432;Database=postgres;Username=postgres;Password=SUA_SENHA_REAL;SSL Mode=Require;Trust Server Certificate=true"
  }
}
```

> ⚠️ **Segurança:** Nunca comite este arquivo com a senha real em repositórios públicos. Use *User Secrets* ou Variáveis de Ambiente em produção.

### 5\. Injeção de Dependência (`Program.cs`)

Registre o contexto no container de serviços da aplicação.

```csharp
using Microsoft.EntityFrameworkCore;
using api_exemplo.Context;

var builder = WebApplication.CreateBuilder(args);

// ... 

// Adicionar ANTES de builder.Build()
builder.Services.AddDbContext<AgendaContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();
```

### 6\. Execução das Migrations

Com tudo configurado, execute os comandos para criar o banco de dados.

```bash
# 1. Cria o plano de migração (Snapshot do código)
dotnet ef migrations add InicializacaoBanco

# 2. Aplica o plano no banco de dados (Cria tabelas e schemas)
dotnet ef database update
```

-----

## ✅ Checklist de Validação

Se tudo funcionou corretamente:

1.  [ ] O comando `dotnet build` compila sem erros.
2.  [ ] O terminal exibiu "Done" após o update.
3.  [ ] No Supabase, existe um novo schema (ex: `agenda`).
4.  [ ] A tabela `Contatos` está dentro desse schema.

-----

## 🔗 Links Úteis

  * **Documentação Oficial (MS):** [Entity Framework Core](https://learn.microsoft.com/pt-br/ef/core/)
  * **Driver Npgsql:** [Npgsql Documentation](https://www.npgsql.org/efcore/)
  * **Supabase:** [Connecting to Supabase with .NET](https://www.google.com/search?q=https://supabase.com/docs/guides/database/connecting/c-sharp)
  * **Gestão de Versões:** [.NET Download Archives](https://dotnet.microsoft.com/en-us/download/dotnet)


```

