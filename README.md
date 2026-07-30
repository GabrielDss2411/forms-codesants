# Diagnóstico Estratégico · CodeSants

Experiência de formulário e design system, na identidade visual do site CodeSants
(dark verde-esmeralda, Space Grotesk / Hanken Grotesk / Space Mono, acentos luminosos).

## Arquivos

| Arquivo | O que é |
|---|---|
| [`index.html`](./index.html) | O formulário. Multi-step (estilo typeform), 30 telas (4 de identificação + 26 perguntas em 4 partes) + intro e revisão. 100% autônomo (abra direto no navegador). |
| [`design-system.html`](./design-system.html) | Styleguide visual da CodeSants: cor, tipografia, componentes, forma e movimento. |
| [`forms.md`](./forms.md) | As perguntas do diagnóstico (fonte de conteúdo). O `index.html` deve espelhar este arquivo. |
| [`vercel.json`](./vercel.json) | Configuração de hospedagem estática na Vercel (rotas, cache, headers). |

## O formulário

- **Multi-step imersivo**: uma pergunta por tela, barra de progresso, transições com easing expo e telas de capítulo entre as partes.
- **Identificação no início**: nome, telefone/WhatsApp, empresa e e-mail de quem responde.
- **Tipos de campo**: texto curto, texto longo, telefone, e-mail, escolha única, múltipla escolha com “Outro” e **lista de prioridade** (`rank`). Marcar uma opção **não** avança sozinho: quem passa de tela é a pessoa, com `Enter` ou *Continuar*. Uma pergunta de múltipla escolha pode ter `detail`: um campo de texto abaixo das opções, guardado em `<id>__detalhe` (usado na 21, onde marcar a área não responde — o relato responde).
- **Teclado**: `Enter` avança · `Shift+Enter` nova linha · teclas `1–9` selecionam opções.
- **Todas as perguntas obrigatórias**: não se avança sem responder, e o botão diz o que falta. Voltar é sempre livre; quem edita pela revisão é devolvido para lá. E-mail precisa ter formato válido; telefone, ao menos 10 dígitos — contato inválido é um lead que não dá para alcançar.
- **Nada é salvo entre sessões** (fase de testes).
- **Transição de parte**: cada parte abre com uma tela de capítulo. As que têm texto em `PART_INTROS` (hoje a Parte 4) **não somem sozinhas** — existem para serem lidas e esperam a pessoa avançar.
- **Tela de revisão**: clique em qualquer resposta para editar antes de enviar.
- **Duas telas de fecho**: o *encerramento* explica o que acontece a seguir (texto do `forms.md`) e coleta a disponibilidade; enviada a agenda, a *confirmação* avisa que o envio terminou e recapitula o que foi registrado. Falha no envio mantém a pessoa no encerramento, com o e-mail de contato — nunca confirma o que não foi gravado. O lead não baixa cópia das respostas: o registro fica no CRM.
- **Acessível**: labels, foco gerenciado, `prefers-reduced-motion`, responsivo mobile→desktop.
- **Disponibilidade para a call**: a tela de encerramento coleta dias, períodos e uma observação, e anexa ao diagnóstico já gravado (`PATCH`).
- **Envio ao CRM**: ao concluir, faz `POST` para `CRM_ENDPOINT` (constante no topo do `<script>`) e guarda o `id` devolvido — é ele que permite o `PATCH` da disponibilidade. Vazio = não envia. O `POST` falha em silêncio de propósito (o formulário não pode depender do CRM estar no ar); já o `PATCH` avisa a pessoa e oferece o e-mail de contato, porque ela está esperando um retorno.

### Lista de prioridade (`rank`)

Usada na pergunta 3. Um input por item, botão para acrescentar, remoção
individual e reordenação **arrastando pelo punho** à esquerda. A resposta é um
array de strings onde a **posição é a informação** (`1º` = mais importante) —
diferente de `multi`, em que a ordem não significa nada.

Arrastar usa Pointer Events, não a API de drag do HTML5, porque aquela não
funciona em toque. As setas ↑/↓ com o punho em foco fazem o mesmo que o
arrasto, para quem usa teclado ou leitor de tela.

### Editar as perguntas

Todas as perguntas vivem no array `QUESTIONS` dentro do `<script>` de
`index.html`. Cada item tem `id`, `part`, `type`
(`long` | `text` | `tel` | `email` | `single` | `multi` | `rank`), o texto (`q`, com trechos em `[...]` virando destaque verde),
`hint`, `options`, `other` e `detail`.

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
