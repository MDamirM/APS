# Описание модулей в SystemVerilog

Основой цифровых схем в SystemVerilog является модуль. Модуль — это блок SystemVerilog-кода описывающий цифровую схему какого-то устройства, например пульта телевизора:

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_00.drawio.jpg](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_00.jpg)

У пульта есть входные сигналы: кнопки, нажатие на которые сообщает о нашем намерении изменить громкость или переключить канал. Кроме того, есть выходной сигнал ИК-светодиода, по которому пульт отправляет информацию телевизору.

Для создания модуля в языке SystemVerilog используются ключевые слова `module` и `endmodule`, которые определяют начало и конец модуля, обрамляя его. Можно сказать, что эти ключевые слова являются корпусом нашего устройства, отделяют его содержимое от внешнего мира.

Определим наш модуль:

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_01.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_01.drawio.png)

```SystemVerilog
module


endmodule
```

У всякого модуля должно быть название. Назовём его `box`. В круглых скобках пишутся имена портов, их направление и типы. Если модуль не имеет ни входов, ни выходов, внутри скобок ничего не пишется. После них всегда ставится точка с запятой.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_02.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_02.drawio.png)

```SystemVerilog
module box();


endmodule
```

Модуль без входов и выходов (портов) — это просто коробка, которая никак не взаимодействует с внешним миром. Подключим к нему два входных сигнала `a, b`  и один выходной `q`. Для объявления портов, необходимо указать направление порта (вход это или выход), и тип используемого сигнала. В рамках данного курса лабораторных работ в качестве типа и входов и выходов будет использоваться тип `logic`, о котором будет рассказано чуть позже.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_03.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_03.drawio.png)

```SystemVerilog
module box(
  input  logic a,
  input  logic b,
  output logic q
);


endmodule
```

Внутри модуля могут быть объявления сигналов, параметров, констант и т.п., о которых другой модуль не узнает. Объявим внутри модуля `box` провод `c`.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_04.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_04.drawio.png)

```SystemVerilog
module box(
  input  logic a,
  input  logic b,

  output logic q
);

  logic c;

endmodule
```

Для объявления провода `c` использовалось ключевое слово (тип) `logic`. Этот тип, подобно стволовым клеткам, может быть в конечном итоге привести к созданию как ячеек памяти (регистров), так и проводов, в зависимости от того, как было описано присваивание объекту этого типа. Поэтому в примере выше говорить о том, что был создан провод не совсем корректно, объект схемы `c` станет проводом, когда будет произведено подключение к этому объекту, соответствующее подключению провода.

Подключим провод `c` ко входу `a`. Для этого используется конструкция `assign c = a;`. Такая конструкция называется **непрерывным присваиванием**. Если очень сильно упростить, то непрерывное присваивание схоже со спайкой двух проводов. После подобного присваивания, провод `c` всегда будет иметь то же значение, что и `a` — как только входной сигнал `a` изменит свое значение, внутренний провод `c` также изменит свое значение (проводу `c` будет **непрерывно присваиваться** значение входа `a`).

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_05.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_05.drawio.png)

```SystemVerilog
module box(
  input  logic a,
  input  logic b,

  output logic q
);

  logic c;

  assign c = a;

endmodule
```

Стоит однако заметить, что аналогия со спайкой проводов имеет свои недостатки: после неё некоторые студенты начинают думать, что расположение "спаиваемых" сигналов относительно знака равно не имеет значения, однако это не так.

В непрерывном присваивании участвует две компоненты: выражение-приемник сигнала и выражение-источник сигнала. Обычно, выражением-приемником является провод (либо группа проводов). Выражение-источник сигнала может быть совершенно различным. В примере приведенном выше выражением-источником так же был провод, но вместо него мог использоваться и регистр и выражение, построенное из цепочки арифметических или логических вентилей.

Важно понять, что при непрерывном присваивании слева от знака равно указывается то **чему мы будем присваивать**, а справа от знака равно указывается то **что мы будем присваивать**.

К примеру, мы можем присвоить проводу `с` значение выхода логического вентиля. Пусть нам нужно, чтобы к сигналу `c` был подключен результат операции `a ИЛИ b`.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_06.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_06.drawio.png)

Такую схему можно реализовать следующим описанием:

```SystemVerilog
module box(
  input  logic a,
  input  logic b,

  output logic q
);

  logic c;

  assign c = a | b;

endmodule
```

Пусть в схеме имеется ещё один логический вентиль - Исключающее ИЛИ. На него подаётся результат операции `a ИЛИ b`, то есть `c`, а также входной сигнал `b`. Результат операции `c ИСКЛЮЧАЮЩЕЕ ИЛИ b` подаётся на выход `q` нашего модуля.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_07.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_07.drawio.png)

```SystemVerilog
module box(
  input  logic a,
  input  logic b,

  output logic q
);

  logic c;

  assign c = a | b;
  assign q = c ^ b;

endmodule
```

Отлично! Мы научились создавать простейшее описание модуля.

Для завершения базового представления о модулях осталось разобраться с таким понятием как **вектор**.

## Векторы

В SystemVerilog **вектором** называют группу проводов или регистров, объединенных общим именем, которая может использоваться как для передачи многоразрядных чисел, так и нескольких сигналов, выполняющих общую задачу.

Синтаксис объявления вектора представлен ниже:

<pre>
<тип> [<старший индекс>:<младший индекс>] <i>имя_вектора</i>
</pre>

Несмотря на то, что может использоваться любой диапазон индексов (даже отрицательный), на практике стараются начинать младший индекс с нуля.

Пример:

<pre>
<b>logic</b> [7:0] <i>sum</i>; // Объявляется 8-битный вектор с именем sum типа logic.
                 // Старший индекс равен 7, младший — 0.
</pre>

Используя индекс, можно обратиться к отдельным битам вектора. С помощью диапазона индексов можно получить доступ к диапазону соответствующих битов.

|фрагмент кода|описание|
|-------------|--------|
|sum[0];      | Обращение к младшему биту вектора sum, объявленного выше|
|sum[7:4];    | Обращение к старшим четырем битам 8-битного вектора sum, объявленного выше|

Важно понимать, что векторы могут быть использованы и при описании портов модуля:

```SystemVerilog
module vector_ex(
  input  logic [3:0] a, // У данного модуля четырехразрядный вход 'a'
  output logic [7:0] b  // и восьмиразрядный выход 'b'.
);

assign b[7:4] = a;      // К старшим четырем битам выхода b подключен вход a
assign b[3:1] = a[2:0]; // К битам с третьего по первый выхода b подключены
                        // биты со второго по нулевой входа a
assign b[0]   = a[3];   // к младшему биту b подключен старший бит a;

endmodule
```

## Иерархия модулей

Модули могут содержать другие модули. Реализуя модуль "Пульт ДУ" можно использовать такие цифровые схемы как "Передатчик ИК-сигнала" и "Контроллер нажатия клавиш". Обе эти цифровые схемы могут быть независимыми модулями, которые объединяются в модуле верхнего уровня.

Допустим, у нас есть модуль `inv`, который подает на выход инверсию входа и мы хотим реализовать модуль `top`, который хочет использовать функционал модуля `inv` следующим образом:

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_08.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_08.drawio.png)

Опишем `inv`:

```SystemVerilog
module inv(
  input  logic a,
  output logic d
);

  assign d = ~a;
endmodule
```

Опишем `top`:

```SystemVerilog
module top(
  input  logic a,
  input  logic b,
  output logic q
);
  // подключение модуля
  inv invertor_1( // подключаем модуль inv и
                  // даём экземпляру этого модуля
                  // имя invertor_1

    .a(a),        // вход а модуля inv подключаем ко
                  //входу a модуля top

    .d(c)         // выход d модуля inv подключаем к
                  // проводу с модуля top
  );

endmodule
```

Обратите внимание на то, как подключаются сигналы к вложенному модулю: при подключении после `.` пишется имя сигнала подключаемого модуля, затем в скобках пишется имя сигнала подключающего модуля. Для лучшего понимания, посмотрите внимательно на схеме на провод `c` и выход `d` модуля `inv`, а так же на SystemVerilog-описание этой схемы.

Мы можем подключить сколько угодно экземпляров одного модуля, поэтому у каждого из экземпляра должно быть свое уникальное имя. Пусть `c` подаётся на логический вентиль И вместе со входом `b`. Результат операции И тоже пойдет на инвертор, а затем на выход `q` модуля top.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_09.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_09.drawio.png)

Тогда в нашем описании добавится подключение второго модуля `inv` и провод `c`.

```SystemVerilog
module inv(
  input  logic a,
  output logic d
);

  assign d = ~a;
endmodule
```

```SystemVerilog
module top(
  input  logic a,
  input  logic b,
  output logic q
);

  logic c;

  // подключение модуля 1
  inv invertor_1( // подключаем модуль inv и даём ему
                  // имя invertor_1

    .a(a),        // подключаем вход 'а' модуля inv ко
                  // входу 'a' модуля top

    .d(c)         // подключаем выход 'd' модуля inv к
                  // проводу 'с' модуля top
  );

  // подключение модуля 2
  inv invertor_2( // подключаем модуль inv и даём ему
                  // имя invertor_2

    .a(c & b),    // на вход 'а' модуля inv подаём
                  // результат логической операции
                  // "с И b"

    .d(q)         // подключаем выход 'd' модуля inv
                  // к выходу q модуля top
  );

endmodule
```

___

## Итоги

1. Ключевым блоком в иерархии цифровой схемы, описанной на языке SystemVerilog является **модуль**. Модули позволяют выносить части сложной цифровой схемы в отдельные блоки, из которых потом и будет составлена итоговая схема, что сильно упрощает разработку.
2. Условно, модуль можно разделить на следующие части:
   1. Объявление модуля:
      1. Ключевые слова `module` / `endmodule` определяющие границы описания модуля.
      2. Название модуля, следующее за ключевым словом `module`. Описанный модуль представляет собой отдельный тип, имя которого совпадает с названием модуля.
      3. Указание входов и выходов (портов) модуля, идущих в круглых скобках после названия модуля. Для указания направления порта модуля используются ключевые слова `input` и `output`. После указание направления порта следует указать тип порта (в рамках данного курса типом портов всегда будет logic), его разрядность, а затем имя.
   2. Функциональное описание модуля:
      1. Объявление внутренних сигналов модуля (будь то проводов или регистров) с помощью ключевого слова `logic`.
      2. Создание при необходимости объектов других модулей.
      3. Описание функциональной связи между различными сигналами и объектами внутри описываемого модуля.

## Проверь себя

Как по-вашему описать нижеприведенную схему на языке описания аппаратуры SystemVerilog?

Обратите внимание, что вход `a` модуля `top` является двухразрядным: нулевой его бит идет на вход `a` модуля `or`, первый бит идет на вход `b` модуля `or`.

![../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_10.drawio.png](../../../technical/Other/Basic%20Verilog%20structures/Pic/modules/fig_10.drawio.png)