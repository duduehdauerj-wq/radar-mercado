# 🛒 Radar de Mercado — Freguesia/Jacarepaguá

Bot pessoal de preços e promoções para **Freguesia (JPA) e bairros próximos**, com comparação histórica, lojas online e listas de compras pessoais independentes.

## O que já está implementado

- Guanabara, Mundial, Prezunic, Supermarket, Vianense e Rede Economia como fontes web iniciais.
- Base de lojas locais com muitas unidades de Freguesia, Pechincha, Taquara, Curicica, Tanque, Itanhangá e Recreio.
- Assaí, Dom Atacadista, Armazém Urbano, Intercontinental e mercados menores cadastrados para expansão.
- Amazon Brasil e Mercado Livre como fontes online.
- Histórico de até 365 dias.
- Preço-alvo + média + mediana + percentis históricos.
- Classificação: normal / bom / muito bom / excepcional.
- Distância estimada a partir do centro da Freguesia via geocodificação OpenStreetMap/Nominatim com cache.
- O sistema **não calcula Uber nem desconta custo de deslocamento**. A decisão presencial usa uma lista configurável de lojas que fazem parte do seu caminho.
- Dashboard HTML em `data/dashboard.html`.
- Listas de compras pessoais independentes (por exemplo, `Dudu` e `Mãe`) sem alterar o radar global de produtos.
- GitHub Actions a cada ~6 horas.
- Telegram para alertas.

## Importante sobre lojas

O cadastro inclui lojas que estão fisicamente na região, mas uma loja só entra em alerta de preço quando existe uma fonte de preço automatizável. Isso evita inventar preços. As demais ficam no cadastro para futuras fontes/encartes.

Para preços de rede que não são explicitamente amarrados a uma filial, o alerta diz **“preço da rede; confirmar na filial”**.

## Como usar

1. Crie um bot no Telegram.
2. Crie um repositório privado no GitHub chamado `radar-mercado`.
3. Envie todos os arquivos deste projeto.
4. Crie os Secrets `TELEGRAM_BOT_TOKEN` e `TELEGRAM_CHAT_ID`.
5. Vá em Actions → Radar de Mercado → Run workflow.
6. Depois o GitHub roda sozinho.

Você normalmente só editará `config/products.json`.

## Produtos

Cada produto pode ter:

- `keywords`: termos de identificação;
- `target_price`: preço-alvo;
- `alert_below_average_percent`: desconto mínimo contra a média histórica;
- `max_online_price`: teto para resultados Amazon/Mercado Livre.

## Rota e lojas presenciais

O radar pode guardar distância apenas como informação. **Não existe custo de Uber/deslocamento na decisão de compra.**

As lojas presenciais que entram como rota preferencial são configuradas em `config/settings.json`, em `route_stores`. A configuração inicial prioriza Supermarket Freguesia, Mundial Freguesia, Armazém Urbano Freguesia, Hortifruti Freguesia e Rede Economia Freguesia.

## Compras online

Amazon e Mercado Livre usam frete padrão de R$ 0,00 no comparador, porque o usuário normalmente dispõe de frete grátis/Full. O sistema ainda recomenda confirmar o frete no anúncio.

## Listas de compras

As listas ficam em `config/shopping_lists.json` e são independentes. Exemplo:

```json
{
  "Dudu": [{"product_name": "Café Pilão 500g", "quantity": 1}],
  "Mãe": [{"product_name": "Arroz 5kg", "quantity": 2}]
}
```

Adicionar um produto à lista da Mãe **não adiciona esse produto ao radar global**. O radar continua monitorando somente os produtos definidos em `config/products.json`; a lista apenas consulta as ofertas coletadas para montar a cesta daquela pessoa.
