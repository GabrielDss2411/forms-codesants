# Diagnóstico Inicial da Empresa · CodeSants

Experiência de formulário e design system, na identidade visual do site CodeSants
(dark verde-esmeralda, Space Grotesk / Hanken Grotesk / Space Mono, acentos luminosos).

## Arquivos

| Arquivo | O que é |
|---|---|
| [`index.html`](./index.html) | O formulário. Multi-step (estilo typeform), 26 telas (4 de identificação + 22 perguntas em 7 seções) + intro e revisão. 100% autônomo (abra direto no navegador). |
| [`design-system.html`](./design-system.html) | Styleguide visual da CodeSants: cor, tipografia, componentes, forma e movimento. |
| [`forms.md`](./forms.md) | As perguntas do diagnóstico (fonte de conteúdo). O `index.html` deve espelhar este arquivo. |
| [`vercel.json`](./vercel.json) | Configuração de hospedagem estática na Vercel (rotas, cache, headers). |

## O formulário

- **Multi-step imersivo**: uma pergunta por tela, barra de progresso, transições com easing expo e telas de capítulo entre as partes.
- **Identificação no início**: nome, telefone/WhatsApp, empresa e e-mail de quem responde.
- **Tipos de campo**: texto curto, texto longo, telefone, e-mail, escolha única (avanço automático) e múltipla escolha com “Outro”.
- **Teclado**: `Enter` avança · `Shift+Enter` nova linha · teclas `1–9` selecionam opções.
- **Navegação livre**: nada bloqueia o avanço e nada é salvo entre sessões (fase de testes).
- **Tela de revisão**: clique em qualquer resposta para editar antes de enviar.
- **Recado em vídeo**: a tela final tem uma camada de vídeo de agradecimento. Aponte a constante `THANKYOU_VIDEO` (e, opcional, `THANKYOU_POSTER`) no topo do `<script>` para um arquivo local ou URL; vazio mostra um placeholder na marca.
- **PDF direto**: ao concluir, o botão *Baixar PDF do diagnóstico* gera e baixa o documento na hora (perguntas + respostas, identificação na capa), sem diálogo de impressão. Usa `html2pdf` (via CDN, requer internet); offline, cai automaticamente para o "Salvar como PDF" da impressão. O nome do arquivo usa a empresa/nome do lead.
- **Acessível**: labels, foco gerenciado, `prefers-reduced-motion`, responsivo mobile→desktop.
- **Envio ao CRM**: ao concluir, faz `POST` para `CRM_ENDPOINT` (constante no topo do `<script>`). Vazio = não envia. Falha em silêncio de propósito — o formulário não pode depender do CRM estar no ar.

### Editar as perguntas

Todas as perguntas vivem no array `QUESTIONS` dentro do `<script>` de
`index.html`. Cada item tem `id`, `part`, `type`
(`long` | `text` | `tel` | `email` | `single` | `multi`), o texto (`q`, com trechos em `[...]` virando destaque verde),
`hint`, `options` e `other`.

> Ao mexer nas perguntas, espelhe o mesmo `id` em `lib/questions.ts` do
> [CRM](https://github.com/GabrielDss2411/crm-codesants) — os ids são o
> contrato entre os dois projetos.

## Deploy (Vercel)

Site 100% estático — **sem build, sem dependências**. A Vercel serve os arquivos direto da raiz do repositório.

Repositório: <https://github.com/GabrielDss2411/forms-codesants>

1. Na Vercel: **Add New → Project → Import** o repositório `forms-codesants`.
2. **Framework Preset: `Other`**. Root Directory `./`. Build Command, Output Directory e Install Command ficam **vazios / desligados** — não há build.
3. Deploy. Cada push na `main` publica em produção; PRs geram preview.

> Se aparecer **404: NOT_FOUND**, o Preset está errado (geralmente detectado como um framework com build) ou o Root Directory aponta para uma subpasta. Em *Settings → General*, volte para `Other` + `./` e faça **Redeploy**.

### Rotas

| URL | Arquivo |
|---|---|
| `/` | `index.html` (o formulário) |
| `/diagnostico` · `/diagnostico-estrategico` | redirect → `/` |
| `/design-system` | `design-system.html` (`cleanUrls`) |
| `/design` | redirect → `/design-system` |

O `logo.svg` é servido com cache longo; o HTML fica sob o cache padrão da Vercel para estáticos, revalidado a cada deploy.

### Rodando local

Basta abrir o `index.html` no navegador. Para simular as rotas da Vercel:

```bash
npx vercel dev
```

## Design system

Os tokens espelham `web/app/globals.css` (`@theme`). Ao levar isto para o app Next.js,
reutilize os componentes existentes (`components/ui.tsx`, `cta-button.tsx`): os estilos aqui
foram derivados deles para manter uma linguagem única entre site e formulário.
