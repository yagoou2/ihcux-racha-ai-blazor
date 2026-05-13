# 💸 RachaAí — Dashboard Blazor

> Projeto desenvolvido para a disciplina de **Interação Humano-Computador e UX**
> Centro Universitário UNA · Prof. Daniel Henrique Matos de Paiva


---

Feito por: Yago Soares Costa

## 📱 Wireframes (Miro) — Visão Geral

O projeto foi baseado em **6 telas** prototipadas no Miro, cobrindo o fluxo completo do usuário:

| Tela | Nome | Descrição |
|------|------|-----------|
| Tela 1 | **Dashboard Principal** | Lista de grupos ativos com status de pagamento (Pago / a pagar) |
| Tela 2 | **Detalhes do Grupo** | Itens do grupo (Carne, Bebida, Carvão...), total contribuído e botão Contribuir |
| Tela 3 | **Registrar Gasto** | Formulário com valor, descrição, pagantes e método de divisão (Igual / Percentual / Por quanto) |
| Tela 4 | **Enviar Comprovante** | Upload de foto do comprovante + botão "Digitar o código" |
| Tela 5 | **Confirmar Pagamento** | Dados bancários (Agência: NUNUBANK), data de envio e total a pagar |
| Tela 6 | **Pagamento Bem Sucedido** | Tela de confirmação com causa, valor pago e campo de mensagem |

---

## 🎨 Design System — Paleta de Cores

Baseado na identidade visual do logo RachaAí (roxo + verde) e nos wireframes do Miro:

| Token | Valor | Uso |
|-------|-------|-----|
| `--color-bg` | `#0d0d0d` | Fundo geral da aplicação |
| `--color-surface` | `#1a1a1a` | Cards e superfícies elevadas |
| `--color-surface2` | `#222222` | Ícones dentro dos cards |
| `--color-border` | `#2e2e2e` | Bordas e separadores |
| `--color-success` | `#00c853` | **Verde** — crédito, pago, positivo |
| `--color-danger` | `#ff3d3d` | **Vermelho** — débito, a pagar, negativo |
| `--color-purple` | `#7c3aed` | **Roxo** — logo, destaque de marca |
| `--color-text` | `#f0f0f0` | Texto principal |
| `--color-muted` | `#888888` | Texto secundário e labels |

**Tipografia:**
- Display / Títulos: **Sora** (700–800)
- Corpo / Labels: **DM Sans** (400–600)

---

## 🗂️ Estrutura de Arquivos

```
RachaAi/
├── Models/
│   └── Grupo.cs                  # Modelo de dados do grupo
├── Pages/
│   └── Dashboard.razor           # Tela 1 — Dashboard (@page "/dashboard")
├── Shared/
│   ├── GrupoCard.razor           # Componente reutilizável de grupo
│   └── NavMenu.razor             # Sidebar com link para o Dashboard
└── wwwroot/
    └── css/
        └── dashboard.css         # Design System completo do RachaAí
```

---

## 🔄 Implementação Blazor: Do Wireframe ao Código

### Tela 1 → Dashboard.razor

O wireframe da Tela 1 mostrava uma lista de grupos com nome, ícone, custo, número de pessoas e badge de status "Pago" ou checkbox de pendente. No Blazor, isso foi transposto assim:

```
Wireframe (Miro)                  →   Componente Blazor
──────────────────────────────────────────────────────────
Header com logo + sino + lua      →   <header class="dashboard-header">
Lista de grupos                   →   @foreach + <GrupoCard>
Badge "Pago" / checkbox pendente  →   text-success / text-danger + seta ▲▼
Tagline "sem dor de cabeça..."    →   .brand-tagline no header
```

### Hierarquia Visual aplicada

A hierarquia do wireframe foi mantida em três níveis:

1. **Nível 1 — Identidade** → Header com logo roxo e nome da marca
2. **Nível 2 — Status Financeiro** → Cards de resumo (a receber / a pagar / saldo)
3. **Nível 3 — Ação** → Lista de grupos + FAB verde de destaque

---

## 🧩 Componente GrupoCard.razor

Baseado na Tela 1 do Miro, o card exibe:

- Ícone derivado da categoria (propriedade calculada no model)
- Nome em negrito com truncamento via CSS
- Badge de categoria
- Contador de membros
- Valor em R$ com cor verde (crédito) ou vermelha (débito)
- Barra lateral colorida como reforço visual do status
- `EventCallback<Grupo>` para comunicação com a página pai

---

## ⚙️ Maior Dificuldade Técnica: Componentização do GrupoCard

### Formatação de moeda

O maior desafio foi garantir que o valor sempre fosse exibido em Real Brasileiro, independente do locale do servidor:

```csharp
// ❌ Pode exibir "$" dependendo do servidor
@Grupo.Valor.ToString("C")

// ✅ Sempre exibe em R$
@Grupo.Valor.ToString("C", new System.Globalization.CultureInfo("pt-BR"))
```

### Comunicação filho → pai

```razor
@* GrupoCard.razor (filho) *@
[Parameter] public EventCallback<Grupo> OnClick { get; set; }
await OnClick.InvokeAsync(Grupo);

@* Dashboard.razor (pai) *@
<GrupoCard Grupo="grupo" OnClick="AbrirGrupo" />
```

---

## ⭐ Desafio Extra: Busca Reativa

```razor
<input @bind-value="TextoBusca" @bind-value:event="oninput" />
```

```csharp
private IEnumerable<Grupo> GruposFiltrados =>
    string.IsNullOrWhiteSpace(TextoBusca)
        ? Grupos
        : Grupos.Where(g =>
            g.Nome.Contains(TextoBusca, StringComparison.OrdinalIgnoreCase) ||
            g.Categoria.Contains(TextoBusca, StringComparison.OrdinalIgnoreCase));
```

---

## 🖼️ Heurísticas de Nielsen Aplicadas

| Heurística | Implementação |
|---|---|
| **Visibilidade do status** | Verde = crédito, Vermelho = débito em todos os componentes |
| **Consistência** | Mesma paleta e tipografia em todos os componentes |
| **Affordance** | FAB verde com sombra e hover indica clicabilidade |
| **Tratamento de erros** | Estado vazio com mensagem amigável e sugestão de ação |
| **Feedback** | Toast de confirmação ao clicar em cards ou no FAB |

---

## 🚀 Como Executar

```bash
git clone https://github.com/seu-usuario/ihcux-racha-ai-blazor.git
cd ihcux-racha-ai-blazor
dotnet run
```

Acesse: `http://localhost:PORT/dashboard`  
**Pré-requisito:** .NET 8 SDK ou superior

---

*Entrega: Atividade Prática — Do Wireframe ao Código com Blazor*
