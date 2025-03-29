# Бектестинг торгових стратегій на основі 1-хвилинних даних Binance Data Vision

Cистема для бектестингу алгоритмічних торгових стратегій на 1-хвилинних даних

## Зміст

- [Бектестинг торгових стратегій на основі 1-хвилинних даних Binance Data Vision](#бектестинг-торгових-стратегій-на-основі-1-хвилинних-даних-binance-data-vision)
  - [Зміст](#зміст)
  - [Про проект](#про-проект)
  - [Можливості проекту](#можливості-проекту)
  - [Встановлення](#встановлення)
- [Використання](#використання)
  - [Опис стратегій](#опис-стратегій)
    - [Стратегія 1: `ATR Average Breakout`](#стратегія-1-atr-average-breakout)
    - [Стратегія 2: `SMA Crossover`](#стратегія-2-sma-crossover)
    - [Стратегія 3: `VWAP Reversion Intraday`](#стратегія-3-vwap-reversion-intraday)
  - [Висновки](#висновки)
  - [Контакти](#контакти)

## Про проект

Проєкт розроблено для тестування та аналізу алгоритмічних торгових стратегій на 1-хвилинних історичних ринкових даних Binance.<br>
Ця система бектестингу дозволяє:

- Виконувати бектест на високочастотних даних із адаптивними умовами (комісії, сліпейдж, часовий дрейф);
- Порівнювати ефективність різних стратегій за ключовими фінансовими метриками;
- Візуалізувати результати для подальшого аналізу.<br>

## Можливості проекту

- Завантаження та обробка даних:
  - Отримання 1-хвилинних OHLCV-даних із Binance Data Vision;
  - Вибір найбільш ліквідних пар
- Реалізація торгових стратегій
  - SMA Crossover – перетин ковзних середніх із фільтрами волатильності;
  - VWAP Reversion Intraday - відкриття позиції при значному відхиленні від VWAP з поверненням до нього;
  - ATR-based breakout - стратегія прориву з використанням ATR та трейлінг-стопів
- Бектестинг через VectorBT
  - Урахування торгових комісій, сліпейджу та можливих затримок у виконанні ордерів;
  - Генерація equity curve, heatmap ефективності по всіх парах;
- Розрахунок фінансових метрик
  - Total Return, Sharpe Ratio, Max Drawdown, Winrate, Expectancy, Exposure Time;
  - Формування підсумкової таблиці .csv з метриками всіх стратегій

## Встановлення

Кроки для встановлення проекту локально.

1. Клонуйте репозиторій:
   ```bash
   git clone https://github.com/DimaFedor/Test_task/tree/main
   ```
2. Перейдіть до директорії проекту:
   ```bash
   cd project
   ```
3. Встановіть залежності:
   ```bash
   pip install -r requirements.txt
   ```

# Використання

<span style="color: red;">ATTENTION</span>
Файли з Binance Data Vision будуть викачуватись +- 40хв, тому краще папку <b>data</b> не видаляти, та працювати з нею

1. Першочергово потрібно викачати дані з Binance Data Vision та запустити стратегії
   ```bash
   python3 main.py
   ```
2. Коли в терміналі ви побачите `Зведені метрики по стратегіях:` це буде означати, що все пройшло успішно і результати збережені
3. Перейти в директорію `results` та спостерігати за результатами по кожній стратегії

   <details>
     <summary>Переглянути вміст папки results</summary>
     
     ```plaintext
     results/
     ├── atr_breakout #результати по стратегії ATR-based breakout
     ├── sma_cross #результати по стратегії SMA Crossover
     ├── vwap_reversion #результати по стратегії VWAP Reversion Intraday
     └── metrics_comparison.csv #Зручний csv файл для порівняння стратегій

4. В кожній з цих папок будуть результати в відповідних форматах

   <details>
     <summary>Переглянути вміст папок</summary>
     
     ```plaintext
     atr_breakout/
     ├── equity_curve.png #Відображення зміни вартості портфеля з часом
     ├── heatmap.png #відображає Total Return для кожного активу в рамках стратегії.
     └── vwap_reversion #Метрики допомагають оцінити ефективність стратегії з різних аспектів

## Опис стратегій

### Стратегія 1: `ATR Average Breakout`

`ATR Average Breakout`Strategy

- Ця стратегія використовує два індикатори: `ATR` (Середній справжній діапазон) та `SMA` (Просте ковзаюче середнє) для визначення консолідації та прориву ринку, а також для здійснення торгів. Основна ідея стратегії: коли ціна пробиває верхній або нижній канал ATR, це вважається проривом, і відкривається позиція; коли ціна повертається в канал ATR, це вважається консолідацією, і позиція закривається. Стратегія також враховує управління ризиками та розміром позиції.
- Нажаль результати по цій стратегії не є позитивними виходячи з цих метрик:
  - `Total Return`: -66.99%, це нам говорить про те, що капітал зменшився на ~70% від початкових вкладень(
  - `Sharpe Ratio`: -19.35 говорить нам про те, що стратегія не тільки не принесла прибутку, а й створила великі втрати
  - `Total Return`: 67.75% показник, який вказує на максимальні втрати від піку до мінімуму під час тестування. З цього можна зробити висновок, що дана стратегія з цими параметрами та вхідними даними дуже резикована
  - `Win Rate`: 17.09% показує нам, що ця стратегія вигравала лише в ~17% випадків
  - `Expectancy`: -4.93 показує скільки в середньому було доходу/збитку приносила стратегія, в середньому кожна угода йшла в -4.93
- <b>Загальні висновки</b> по стратегії не втішні і є дуже не ефективною в рамках реального ринку `(2025-02-01/2025-02-28)` через `Total Return`,`Max Drawdown`,`Win Rate`,`Expectancy`

### Стратегія 2: `SMA Crossover`

- Ця стратегія базується на перетині двох простих рухомих середніх (SMA) різних періодів, короткострокового`(fast)` та `(slow)`. Ідея полягає в тому, що коли `fast` SMA перетинає `long` знизу вгору, це є сигналом до покупки, і навпаки, коли `fast` SMA перетинає `long` зверху вниз, це є сигналом до продажу.
- Нажаль результати по цій стратегії не є позитивними, але кращими за `ATR Average Breakout` виходячи з цих метрик:
  - `Total Return`: -34.96% також дуже негативний результат і свідчить нам про те, що стартовий капітал зменшився на ~35%
  - `Sharpe Ratio`: -5.5% вказує на те, що стратегія була дуже ризикованою, і не приесла очікуваного прибутку
  - `Max Drawdown`: 42.5% вказує на те, що початковий портфель на певному етапі втратив майже половину свого капіталу
  - `Win Rate`: 19% було дуже мало угод, з яких стратегія вийшла з прибутком
  - `Expectancy`: ~(-6) показує скільки в середньому було доходу/збитку приносила стратегія, в середньому кожна угода йшла в ~(-6), що є дуже негативним результатом для торгів
- <b>Загальні висновки</b> по стратегії не втішні і є дуже не ефективною в рамках реального ринку `(2025-02-01/2025-02-28)`, але кращими за `ATR Average Breakout` через `Total Return`,`Max Drawdown`,`Win Rate`,`Expectancy`

### Стратегія 3: `VWAP Reversion Intraday`

- Ця стратегія базується на ідеї, що ціна після відхилення від VWAP (Volume Weighted Average Price) має тенденцію повернутися до цього рівня протягом торгового дня. Ця стратегія добре працює на ринках, де ціна може тимчасово відходити від середнього рівня, щоб повернутися назад, що дає можливість здійснити вигідні угоди. Ідея цієї стратегії доволі проста та зрозуміла. Коли ціна значно відходить від VWAP, ми очікуємо, що вона повернеться до цього рівня. Стратегія відкриває позицію на повернення ціни до VWAP.
- Результати цієї стратегії є досить сумнівними та в деяких аспектах аномальними:
  - `Total Return`: 37,351,728.12% аномальний прибутковий відсоток це звʼязано з тим, що на певних позиціях не правильно обраховуються параметри<br>![RVNBTC](https://live.staticflickr.com/65535/54417815654_ff66eb1096_t.jpg),![ONE](https://live.staticflickr.com/65535/54416756722_f9c8d62bab_t.jpg), ![ZIL](https://live.staticflickr.com/65535/54417816194_0f0538da1c_t.jpg)
- Якщо прибрати аномальні позиції виходить ось так
  - `Total Return`: 1072% що також є завеликим значенням, але вже більш реалістичним
  - `Sharpe Ratio`: 0.24% цей показник доволі низький, що вказує на те, що ця стратегія доволі ризикована
  - `Max Drawdown`: 34.36% вказує що в певний момет часу ціна портфелю просіла на 34.36%, що не є дуже хорошим результатом
  - `Win Rate`: 68.96% доволі високий показник виграшних угод, який вказує, що в більшій частині угод стратегія приносила прибуток
  - `Expectancy`: 53.71 показує скільки в середньому було доходу/збитку приносила стратегія, в середньому кожна угода йшла в +53.71, що є дуже позитивним результатом для торгів

## Висновки

- Стратегія `ATR Average Breakout` виявилася надзвичайно неефективною в даних умовах. Великий рівень втрат, низький відсоток виграшних угод і величезні просадки вказують на те, що ця стратегія не є життєздатною для реального застосування в умовах ринку 2025/02.
- Стратегія `SMA Crossover` демонструє дещо кращі результати, ніж ATR Average Breakout, але все одно є неефективною для реального ринку. Відсоток виграшних угод дуже низький, а просадки і негативний Expectancy вказують на ризикованість стратегії.
- Стратегія `VWAP Reversion Intraday` показала вражаючий результат в загальному обсязі прибутку (після корекції Total Return 1072%), але цей результат не зовсім стабільний і має низький Sharpe Ratio. Також стратегія зазнавала великих просадок (Max Drawdown 34.36%), що вказує на високий ризик. Однак, високий Win Rate та Expectancy свідчать про потенціал стратегії, хоча треба бути обережним з великою волатильністю.

## Контакти

Контакти для зворотного зв'язку та підтримки.

- Email: diamfedorenkoznO@gmail.com
- GitHub: [DimaFedor](https://github.com/DimaFedor)
