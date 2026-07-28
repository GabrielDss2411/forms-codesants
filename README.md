# Diagnóstico Estratégico · CodeSants

Experiência de formulário e design system, na identidade visual do site CodeSants
(dark verde-esmeralda, Space Grotesk / Hanken Grotesk / Space Mono, acentos luminosos).

## Arquivos

| Arquivo | O que é |
|---|---|
| [`diagnostico-estrategico.html`](./diagnostico-estrategico.html) | O formulário. Multi-step (estilo typeform), 24 perguntas (identificação + 3 partes) + intro + revisão. 100% autônomo (abra direto no navegador). |
| [`design-system.html`](./design-system.html) | Styleguide visual da CodeSants: cor, tipografia, componentes, forma e movimento. |
| [`forms.md`](./forms.md) | As perguntas originais do diagnóstico (fonte de conteúdo). |
| [`vercel.json`](./vercel.json) | Configuração de hospedagem estática na Vercel (rotas, cache, headers). |

## O formulário

- **Multi-step imersivo**: uma pergunta por tela, barra de progresso, transições com easing expo e telas de capítulo entre as partes.
- **Identificação no início**: nome, empresa e telefone/WhatsApp de quem responde.
- **Tipos de campo**: texto curto, texto longo, telefone, escolha única (avanço automático) e múltipla escolha com “Outro”.
- **Teclado**: `Enter` avança · `Shift+Enter` nova linha · teclas `1–9` selecionam opções.
- **Navegação livre**: nada bloqueia o avanço e nada é salvo entre sessões (fase de testes).
- **Tela de revisão**: clique em qualquer resposta para editar antes de enviar.
- **Recado em vídeo**: a tela final tem uma camada de vídeo de agradecimento. Aponte a constante `THANKYOU_VIDEO` (e, opcional, `THANKYOU_POSTER`) no topo do `<script>` para um arquivo local ou URL; vazio mostra um placeholder na marca.
- **PDF direto**: ao concluir, o botão *Baixar PDF do diagnóstico* gera e baixa o documento na hora (perguntas + respostas, identificação na capa), sem diálogo de impressão. Usa `html2pdf` (via CDN, requer internet); offline, cai automaticamente para o "Salvar como PDF" da impressão. O nome do arquivo usa a empresa/nome do lead.
- **Acessível**: labels, foco gerenciado, `prefers-reduced-motion`, responsivo mobile→desktop.

### Editar as perguntas

Todas as perguntas vivem no array `QUESTIONS` dentro do `<script>` de
`diagnostico-estrategico.html`. Cada item tem `id`, `part`, `type`
(`long` | `text` | `tel` | `single` | `multi`), o texto (`q`, com trechos em `[...]` virando destaque verde),
`hint`, `options` e `other`.

## Deploy (Vercel)

Site 100% estático — **sem build, sem dependências**. A Vercel serve os arquivos direto da raiz do repositório.

Repositório: <https://github.com/GabrielDss2411/forms-codesants>

1. Na Vercel: **Add New → Project → Import** o repositório `forms-codesants`.
2. Framework Preset: **Other** (o `vercel.json` já força `framework: null`, sem build command e `outputDirectory: "."`).
3. Deploy. Cada push na `main` publica em produção; PRs geram preview.

### Rotas

| URL | Arquivo |
|---|---|
| `/` | `diagnostico-estrategico.html` |
| `/diagnostico` | `diagnostico-estrategico.html` |
| `/diagnostico-estrategico` | idem (`cleanUrls`) |
| `/design` · `/design-system` | `design-system.html` |

O HTML é servido com `must-revalidate` (atualização imediata após deploy) e o `logo.svg` com cache longo.

### Rodando local

Basta abrir o `.html` no navegador. Para simular as rotas da Vercel:

```bash
npx vercel dev
```

## Design system

Os tokens espelham `web/app/globals.css` (`@theme`). Ao levar isto para o app Next.js,
reutilize os componentes existentes (`components/ui.tsx`, `cta-button.tsx`): os estilos aqui
foram derivados deles para manter uma linguagem única entre site e formulário.
