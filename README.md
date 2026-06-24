# Painel Gerencial de Materiais

Dashboard de controle gerencial para monitoramento de estoque, contratos/atas, requisições e tramitação de processos. Construído como uma página HTML única (sem frameworks, sem build step), com tema claro/escuro.

## Estrutura do projeto

```
painel-materiais/
├── public/
│   └── index.html    ← painel completo (HTML + CSS + JS inline)
├── vercel.json        ← configuração de deploy do Vercel
└── README.md          ← este arquivo
```

Tudo vive em um único arquivo `public/index.html`. Não há dependências, não há `package.json`, não há etapa de build — é só abrir no navegador ou publicar como site estático.

## Como abrir localmente
Basta abrir o arquivo diretamente no navegador:
```bash
open public/index.html
```

## Como publicar no Vercel
```bash
npm i -g vercel   # se ainda não tiver
cd painel-materiais
vercel            # primeiro deploy (vai pedir login)
vercel --prod     # deploy de produção
```
Ou: suba esta pasta para um repositório no GitHub e importe o repositório em vercel.com → "Add New Project".

## Arquitetura interna do `index.html`

O arquivo é dividido em três blocos:

1. **`<style>`** — todo o CSS, incluindo variáveis de tema (`:root` para claro, `html[data-theme="dark"]` para escuro). Os componentes usam variáveis CSS (`var(--text)`, `var(--bg)`, etc.) em vez de cores fixas, para que o tema escuro funcione automaticamente.
2. **HTML do corpo** — estrutura de abas: Visão Geral, Estoque, Contratos, Requisições, Tramitação. Cada aba é uma `<div class="tab-content">` que é mostrada/escondida via JS.
3. **`<script>`** — dados e lógica de renderização:
   - **Dados em arrays JS no topo do script**: `estoque`, `contratos`, `requisicoes`, `processosConcluidos`. Esses arrays são a fonte de verdade — vêm de uma planilha Excel ("BASE_DE_DADOS_POWER_BI_-_Contratos_e_ATAS.xlsx") que o usuário fornece periodicamente para atualização.
   - **Funções `render*()`**: cada seção do painel tem sua função de renderização (`renderKPI`, `renderHighlights`, `renderEstoqueTable`, `renderContratos`, `renderRequisicoes`, `renderTramitacao`, etc.) que gera o HTML dinamicamente a partir dos arrays de dados.
   - **Tema**: `toggleTheme()` alterna entre claro/escuro e persiste a escolha em `localStorage`.
   - Tudo é renderizado client-side, sem nenhum backend.

## Atualizando os dados

Quando uma nova planilha Excel for fornecida, as atualizações tipicamente envolvem:
1. Abrir a planilha e ler as abas: `Estoque Produto`, `Contratos e Atas Vigentes`, `Requisições Feitas`, `Processos Concluídos`.
2. Localizar os arrays correspondentes no `<script>` do `index.html` (`estoque`, `contratos`, `requisicoes`, `processosConcluidos`).
3. Aplicar as mudanças pontuais via edição direta no array (adicionar/remover/atualizar objetos).
4. Atualizar a constante `TODAY` no topo do script para a data de referência da planilha (usada para calcular dias até ruptura/vencimento).

## Convenções de dados

- **`estoque`**: `{cod, nome, saldo, consumo, ruptura (Date), instrumento, vencimento (Date|null)}`
- **`contratos`**: `{instr, venc (Date|null), cod, nome, qtdContratada, preco, qtdRequisitada, qtdRestante}` — agrupados por `instr` (nome do instrumento/ata/fornecedor) na renderização.
- **`requisicoes`**: `{memo, cod, nome, qtd, processo, situacao: "pendente"|"entregue", pagamento}`
- **`processosConcluidos`**: `{objeto, avg (dias), count}`

## Funcionalidades já implementadas
- 5 abas: Visão Geral (KPIs + destaques + gráfico de status + ruptura iminente + tramitação), Estoque (tabela filtrável), Contratos (cards expansíveis agrupados por instrumento, ordenados por vencimento, com busca), Requisições (sub-abas Pendentes/Entregues/Requisitar), Tramitação (tabela com tempos médios)
- Tema claro/escuro com toggle no header (persiste em localStorage)
- Navegação inferior fixa no mobile, abas no topo no desktop
- Cálculos automáticos de dias até ruptura, status (Ruptura/Crítico/Alerta/OK), % de consumo de cota contratual
- Aba "Requisitar" cruza itens críticos do estoque com requisições pendentes para identificar o que falta requisitar
- Barra de progresso em Contratos mostra visualmente a fração "requisitada mas ainda não entregue" (hachurado) dentro do total já requisitado

## Notas de design
- Sem frameworks (React, Vue etc.) — tudo é DOM vanilla + template strings.
- Sem build step — qualquer editor + navegador é suficiente para iterar.
- Cores semânticas via variáveis CSS: `--danger`, `--crit`, `--warn`, `--ok` (e suas variantes `-bg` para fundos suaves e `-kpi-bg` para fundos mais vivos usados nos cards de KPI).
