# Relatórios Lojão dos Móveis

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

Basta servir os arquivos como conteúdo estático (GitHub Pages, Netlify, Nginx etc.).
Para trocar os endpoints, edite as constantes `WEBHOOK_URL` e `WEBHOOK_URL_SERVICO` no `index.html`.
