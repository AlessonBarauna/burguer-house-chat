# 🍔 BurgerBot — Burguer House Chat

Assistente virtual com IA para a **Burguer House**, lanchonete artesanal em Botujuru, Mogi das Cruzes/SP.

O cliente conversa com o BurgerBot, monta o pedido pelo carrinho integrado e finaliza direto no WhatsApp — sem precisar sair do chat.

---

## Funcionalidades

- **Chat com IA** — BurgerBot responde dúvidas sobre cardápio, preços, horários e formas de pagamento usando LLaMA 3.3 70B via Groq
- **Carrinho integrado** — painel de cardápio com controle de quantidade por item
- **Checkout completo** — nome, telefone, tipo de entrega (delivery/retirada), seleção de bairro, endereço e observações
- **Taxa de entrega por zona** — calculada automaticamente conforme o bairro selecionado
- **Frete grátis** — pedidos acima de R$ 80,00
- **Envio via WhatsApp** — deep-link com resumo formatado do pedido (itens, total, endereço, bairro, taxa)

---

## Fluxo completo

```
Usuário abre o chat
       │
       ▼
 BurgerBot responde dúvidas (IA)
       │
       ▼
 Clica em 🛒 → Painel do cardápio
       │
       ▼
 Adiciona itens (+/−)
       │
       ▼
 "Finalizar Pedido" → Modal de checkout
       │
       ├─ Preenche nome e telefone
       ├─ Escolhe Delivery ou Retirada
       ├─ Se Delivery: seleciona bairro → taxa exibida na hora
       ├─ Preenche endereço e observações
       │
       ▼
 "Confirmar via WhatsApp" → abre wa.me com pedido formatado
       │
       ▼
 Confirmação no chat + carrinho limpo
```

---

## Zonas de entrega

Partindo de **Rua Santos, 58 — Botujuru, Mogi das Cruzes/SP**.

| Região | Bairros | Taxa |
|--------|---------|------|
| Botujuru e entorno | Botujuru, Vila Suíssa, Jd. Botujuru, Nova Botujuru | R$ 4,00 |
| Mogi — Centro e próximos | Centro, Brás Cubas, Vila Industrial, Vila Mogilar, Jundiapeba, Jd. Armênia | R$ 8,00 |
| Mogi — bairros afastados | Taiaçupeba, Biritiba Ussu, César de Souza, Cocuera, Sabaúna, Quatinga | R$ 12,00 |
| Cidades vizinhas | Suzano, Poá, Ferraz de Vasconcelos, Itaquaquecetuba | R$ 18,00 |
| Fora da área | — | Consultar pelo WhatsApp |

> Frete grátis em qualquer zona para pedidos acima de **R$ 80,00**.

---

## Stack

| Camada | Tecnologia |
|--------|-----------|
| Backend | .NET 10 — ASP.NET Core Minimal API |
| Frontend | HTML + CSS + Vanilla JS (single file em `wwwroot/index.html`) |
| IA | [Groq](https://console.groq.com) — LLaMA 3.3 70B Versatile (gratuito) |
| Entrega do pedido | WhatsApp deep-link (`wa.me`) |

A chave de API **nunca vai ao browser**. O frontend chama `/api/chat` no próprio backend, que repassa ao Groq server-side.

```
Browser → POST /api/chat → .NET → api.groq.com
```

---

## Como rodar

### 1. Obter chave Groq (gratuito)

1. Acesse [console.groq.com](https://console.groq.com)
2. Crie uma conta → **API Keys** → **Create API Key**
3. Copie a chave gerada (`gsk_...`)

### 2. Configurar a chave

Crie o arquivo `appsettings.Development.json` na raiz do projeto (já está no `.gitignore`):

```json
{
  "Groq": {
    "ApiKey": "gsk_SUA_CHAVE_AQUI"
  }
}
```

Ou via variável de ambiente (Windows cmd):

```cmd
set Groq__ApiKey=gsk_SUA_CHAVE_AQUI
```

### 3. Rodar

```bash
# Requer .NET 10 SDK
set ASPNETCORE_ENVIRONMENT=Development && dotnet run
```

Acesse: [http://localhost:5080](http://localhost:5080)

---

## Estrutura

```
burguer-house-chat/
├── Program.cs              # Proxy /api/chat → Groq
├── BurguerHouse.Chat.csproj
├── appsettings.json        # Porta 5080, ApiKey vazia
├── appsettings.Development.json  # (gitignored) ApiKey real
└── wwwroot/
    └── index.html          # Chat + carrinho + checkout (single file)
```
