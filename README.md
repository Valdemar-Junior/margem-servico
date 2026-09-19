# Relatórios Lojão dos Móveis

**Em produção:** https://margem-servico.vercel.app/

PWA (aplicativo web instalável) com dois relatórios operacionais:

- **Margem** — margem bruta por vendedor, agrupada por departamento (Móveis e Colchões / Eletros), com filtro de período e seleção múltipla de vendedores.
- **Serviço** — ranking de garantia estendida, com geração de mensagem pronta para WhatsApp.

## Como funciona

É um app estático de arquivo único (`index.html`) — sem build, sem dependências.
Os dados vêm de dois webhooks do n8n, consultados via `fetch` no navegador:

| Aba | Webhook |
| --- | --- |
| Margem | `/webhook/relatorio-margem` |
| Serviço | `/webhook/relatorio-servico` |

O `sw.js` (service worker) faz cache apenas dos arquivos do próprio app;
as chamadas ao n8n nunca são interceptadas, então os dados sempre vêm frescos da rede.

## Arquivos

| Arquivo | Função |
| --- | --- |
| `index.html` | App completo: HTML, CSS e JS |
| `manifest.json` | Metadados do PWA (nome, ícones, cores) |
| `sw.js` | Service worker / cache do app shell |
| `icon-192.png`, `icon-512.png` | Ícones do PWA |

## Rodando localmente

O service worker exige um servidor HTTP (não funciona abrindo o arquivo direto):

```bash
python3 -m http.server 8000
```

Depois acesse `http://localhost:8000`.

## Deploy

Hospedado na **Vercel** como site estático, em https://margem-servico.vercel.app/.
A Vercel está conectada a este repositório: todo push na branch `main` publica sozinho,
sem passo de build (não há dependências nem bundler).

Os arquivos são servidos com `cache-control: max-age=0, must-revalidate`, então uma nova
versão do `sw.js` é detectada no próximo acesso e o app se atualiza sem reinstalação.

Para trocar os endpoints, edite as constantes `WEBHOOK_URL` e `WEBHOOK_URL_SERVICO` no `index.html`.

## Instalando no celular

O app é um PWA — instala pelo próprio navegador, sem APK e sem loja.

- **Android (Chrome):** abrir o link → menu **⋮** → **Adicionar à tela inicial**
- **iPhone (Safari):** abrir o link → **Compartilhar** → **Adicionar à Tela de Início**

Vira ícone na tela inicial e abre em tela cheia, sem barra de endereço
(`"display": "standalone"` no manifest). No iOS é obrigatório usar o Safari — o
Chrome do iPhone não instala PWA.
