# Painel Gerencial de Materiais

Dashboard de controle gerencial de materiais, contratos e requisições.

## Como publicar no Vercel

### Opção 1 — Interface Web (sem instalar nada)
1. Acesse [vercel.com](https://vercel.com) e crie uma conta gratuita
2. Clique em **"Add New Project"**
3. Escolha **"Upload"** (arraste a pasta `painel-materiais`)
4. Clique em **Deploy**
5. Em segundos você terá uma URL pública como `painel-materiais.vercel.app`

### Opção 2 — Via GitHub (recomendado para atualizações)
1. Crie um repositório no [github.com](https://github.com)
2. Faça upload dos arquivos desta pasta
3. No Vercel, importe o repositório do GitHub
4. A cada atualização no GitHub, o Vercel republica automaticamente

### Opção 3 — Via CLI
```bash
npm i -g vercel
cd painel-materiais
vercel
```

## Estrutura
```
painel-materiais/
├── public/
│   └── index.html    ← painel completo
├── vercel.json       ← configuração do Vercel
└── README.md
```

## Como atualizar os dados
Edite o arquivo `public/index.html` e altere os arrays `estoque`, `contratos` e `requisicoes` no início do bloco `<script>`.
