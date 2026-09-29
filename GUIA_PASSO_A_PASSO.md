# GUIA PASSO A PASSO — Radar de Mercado Freguesia/JPA

Você **não precisa saber programar** para usar a versão em nuvem.

## O que o projeto monitora

A base já inclui dezenas de pontos de compra em Freguesia/Jacarepaguá e arredores: Mundial, Assaí, Supermarket, Prezunic, Guanabara, Vianense, Rede Economia/Superpax, Supermarket, Unidos, Super Compras, Multi Market, Dom Atacadista, Intercontinental, Extra, Armazém Urbano e mercados menores. Amazon Brasil e Mercado Livre ficam em uma camada online separada.

Nem todo estabelecimento tem preço automatizável. **Cadastrar uma loja não significa inventar preço para ela.** Quando não existe fonte digital confiável, a loja permanece na base para expansão.

## 1. Telegram

Abra `@BotFather` → `/newbot` → crie seu bot.

Guarde o token.

Abra o bot, clique em START e mande `teste`.

No navegador, abra:

`https://api.telegram.org/botSEU_TOKEN/getUpdates`

Procure o número em `chat.id`.

## 2. GitHub

Crie um repositório privado chamado:

`radar-mercado`

Depois envie todos os arquivos deste ZIP mantendo a estrutura de pastas.

O arquivo mais importante é:

`.github/workflows/radar.yml`

## 3. Secrets

No repositório:

`Settings → Secrets and variables → Actions → New repository secret`

Crie:

- `TELEGRAM_BOT_TOKEN`
- `TELEGRAM_CHAT_ID`

Nunca coloque o token dentro de `products.json`.

## 4. Primeiro teste

Vá em:

`Actions → Radar de Mercado → Run workflow`

A execução instala Python, Playwright e Chromium, geocodifica as lojas uma única vez e coleta os preços.

A primeira execução pode demorar mais porque cria o cache de localização. Depois o cache fica salvo em `data/geocoded.json`.

## 5. O que você edita

Normalmente só:

`config/products.json`

Exemplo:

```json
{
  "name": "Café Pilão 500g",
  "keywords": ["cafe", "pilao", "500g"],
  "target_price": 22.0,
  "alert_below_average_percent": 15,
  "max_online_price": 25.0
}
```

## 6. Distância

O centro configurado é **Freguesia (Jacarepaguá)**, não seu endereço.

O robô calcula uma distância aproximada em linha reta para as lojas. Isso serve para filtrar estabelecimentos muito distantes.

O raio inicial é 12 km e pode ser alterado em `config/settings.json`.

### Por que não uso o Uber diretamente?

Porque o preço do Uber varia por horário, trânsito, demanda e modalidade. O sistema usa uma **estimativa conservadora configurável**, não finge ter uma cotação real.

Configuração inicial:

- R$ 8 de custo fixo;
- R$ 2,50 por km;
- ida e volta.

Você pode alterar esses números depois de observar quanto realmente paga.

## 7. A lógica que resolve seu caso

O bot não considera apenas:

> “Onde está o menor preço?”

Ele considera:

> “Onde está um preço suficientemente bom para justificar o deslocamento?”

Então uma loja 8 km distante pode aparecer com preço menor, mas o alerta mostra o custo estimado do deslocamento.

## 8. Amazon e Mercado Livre

O robô pesquisa os produtos cadastrados nas duas plataformas.

Para compras online, distância física não é aplicada.

O preço alertado **não inclui frete**, porque o frete depende de CEP, vendedor, assinatura, carrinho e outros fatores. O alerta manda você abrir o anúncio e conferir o valor final.

## 9. Histórico

O sistema guarda até 365 dias.

Para cada produto/loja ele calcula:

- menor preço;
- maior preço;
- média;
- mediana;
- faixa dos 25% mais baratos;
- faixa dos 10% mais baratos.

Isso permite diferenciar:

- ⚪ preço normal;
- 🟢 bom;
- 🔥 muito bom;
- 💥 excepcional.

## 10. Dashboard

Depois de uma execução, abra:

`data/dashboard.html`

Ele contém uma tabela com os preços observados.

## 11. Lista de compras

Você pode editar:

`config/shopping_list.json`

Exemplo:

```json
[
  {"product_name":"Café Pilão 500g","quantity":1},
  {"product_name":"Leite Italac Integral 1L","quantity":2}
]
```

A estrutura já possui o otimizador de cesta. A evolução natural é transformar isso em um comando de Telegram e mostrar:

> “Para esta cesta, estas são as lojas/combinações com menor custo efetivo.”

## 12. O que acontece se um site mudar?

Supermercados mudam HTML, catálogo e regras de localização.

O projeto separa as fontes. Se uma falhar, as outras continuam.

Você verá o erro no log do GitHub Actions.

## 13. O que é realmente garantido?

Não existe garantia de estoque, preço na prateleira ou disponibilidade de uma promoção até você confirmar na loja.

Preços de rede podem não estar amarrados a uma filial específica. Quando isso acontece, o alerta informa explicitamente que é preço da rede e pede confirmação.

## 14. Depois da instalação

Você não precisa mexer no Python.

A manutenção normal será:

1. adicionar/remover produtos;
2. ajustar preços-alvo;
3. ajustar o raio;
4. ajustar a estimativa de deslocamento.

Quando quiser adicionar uma rede, aí sim o código precisa ser alterado — e podemos fazer essa alteração sem você precisar aprender programação.
