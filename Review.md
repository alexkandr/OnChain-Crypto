# Обзор способов получения исторических данных о ценах токенов в DEX на solana:
Для обучения и анализа либо искать аггрегированные OHLC данные, либо брать сырые данные и аггрегировать самостоятельно:

### Готовые OHLC
1. [Bitquery](https://docs.bitquery.io/docs/examples/Solana/historical-aggregate-data/) - "Enterprise Grade Blockchain Data Products"
    - Аггрегирует данные только по DEX prices
    - Можно достаточно гибко настроить фильтры, по которым будут строиться свечи
    - данные "from May 2024 to now "
    - интервал как минимум 10 минут
    - есть реал-тайм OHLC
    - прайсинг спрятан за TalkToSales страницей...
    - Полезные ссылки на доки [1](https://docs.bitquery.io/docs/examples/Solana/historical-aggregate-data/), [2](https://docs.bitquery.io/docs/usecases/tradingview-subscription-realtime/historical_OHLC/), [3](https://docs.bitquery.io/docs/cubes/dextradesbyTokens/)
1. [SimpleHash](https://docs.simplehash.com/reference/fungible-historical-ohlc) - Multi-chain Token API
    - нужно выбрать DEX биржу с которой будут браться данные
    - нижняя граница дат не указана
    - интервал времени >= 1 минуты
    - 
    - прайсинг скрыт за Let's Talk страницей
1. [birdeye data service](https://docs.birdeye.so/docs/ohlcv-pair) - Multi-chain API
    - Название биржи нельзя указать
    - интервал времени >= 1 минуты
    - возвращает ещё и volume
    - [прайсинг](https://docs.birdeye.so/docs/pricing), [внутренние у.е.](https://docs.birdeye.so/docs/compute-unit-cost)

### Аггрегируем сами:
1.  Любая RPC нода.     
Если собирать все транзакции запросами в блокчейн, то подойдёт любая RPC node будь то [Helius](https://docs.helius.dev/solana-apis/enhanced-transactions-api/parsed-transaction-history) или [Alchemy](https://docs.alchemy.com/reference/gettransaction-sdk-v3), потому что они все обязаны реализовывать [solana API](https://solana.com/docs/rpc) (Хотя Alchemy вроде хранит ещё [исторические данные](https://docs.alchemy.com/reference/get-historical-token-prices)).  Список RPC нод можно смотреть [тут](https://www.alchemy.com/list-of/rpc-node-providers-on-solana)  
    - При аггрегировании нужно однозначно разобраться в используемых протоколах. Потому что Jupiter при совершении свопа может перегонять деньги по нескольким кошелькам и биржам, что может завышать истинный volume
2. Dune     
В dune есть уже две готовые и предобработанные таблицы со всеми досутпными транзакциями: [jupiter_solana.aggregator_swaps](https://docs.dune.com/data-catalog/curated/solana/trading/jupiter-aggregator-trades) и [dex_solana.trades](https://docs.dune.com/data-catalog/curated/solana/trading/solana-dex-trades). Работа с dune осуществляется через sql запросы.
    - За 2024 год jupiter совершил ~ 1,564,266,635 свопов (данные из dune). В dune транзакция занимает ~400 байт памяти, так что бд со всей информацией про транзакций jupiter из dune за год окажется примерно ~625 Гб.
    - Можно предобработать данные о транзакциях в OHLC на стороне dune и выкачать уже их, [типо такого](https://dune.com/queries/3581970). Или брать таблицу [prices.minute](https://docs.dune.com/data-catalog/curated/prices/overview) основанную на dex_solana.trades
    - [прайсинг](https://docs.dune.com/learning/how-tos/credit-system-on-dune) зависит от [количества выгружаемой информации](https://docs.dune.com/learning/how-tos/credit-system-on-dune): 
