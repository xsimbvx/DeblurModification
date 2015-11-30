# DeblurModification
Программа модификации алгоритма восстановления изображения, а также демонстрация сравнения модифицированного алгоритма с инверсной фильтрацией.


## Оглавление

- [Описание программы](#Описание-программы)
- [Описание метода](#Описание-метода)
- [Оценка качества восстановления](#Оценка-качества-восстановления)
- [Классы](#Классы)
- [Примеры работы](#Примеры-работы)
- [Загрузка](#Загрузка)
- [Публикации](#Публикации)

## Описание программы

Представленный в работе алгоритм является *улучшением существующих алгоритмов восстановления изображения*. Он заключается в минимизации ошибки округления при дискретизации изображения. Эта модификация может использоваться в паре с любым из алгоритмов восстановления изображения (инверсный фильтр, фильтр Винера, фильтр Тихонова). В данном примере используется как дополнение к **инверсному фильтру**, при восстановления размытия по Гауссу, размером 3х3. В среднем алгоритм уменьшает среднеквадратичное отклонение восстановленного изображения от исходного от 3% до 20% в зависимости от типа изображения и размера фильтра. 

Программа была разработана как часть дипломной работы. Она включает в себя ещё три части:
[Библиотека работы с изображениями](http://github.com/Kovnir/ImageEditor), [Библиотека восстановления расфокуссированных изображений](http://github.com/xsimbvx/ImageRecovery) и [Пример интеграции этой библиотеки](http://github.com/xsimbvx/IRIntegration).
Проект был собран в Microsoft Visual Studio 2015.

## Описание метода

Значение каждого канала каждого пиксела обычно занимает 1 байт (число от 0 до 255), так как такой точности более чем достаточно, чтобы человеческий глаз не заметил ошибки округления. Процесс дискретизации даёт небольшую погрешность. Фактически, операция восстановление изображение в любом случае имеет прямое и обратное преобразований Фурье. Для вычисления каждого пиксела спектра Фурье задействуются значения всех пикселов (если речь не идёт о алгоритме быстрого преобразования Фурье), и в каждом есть погрешность. Перед нами стояла задача восстановить потерянную при округлении точность изображения. 

Выбор метода восстановления дробной части может быть совершенно любым. Алгоритм стоит подбирать исходя из конкретной задачи, балансируя между необходимым качеством восстановлении и скорости работы алгоритма. Мы создали алгоритм, исходя из задачи, получить улучшенный результат при минимальном времени работы алгоритма.

Было выдвинуто предположение, что несколько соседних пикселов размытого изображения с большой вероятностью можно представить полиномиальной функцией. Для упрощения вычислений мы заменили полином н-ной степени на линейную функцию, предположив, что из-за малого расстояния между пикселами в реальном мире (расстояния между точками, которыми стали пикселами при их фотографировании) погрешность будет несущественна.

Зная, как работает округление, а также значения соседних к текущему пикселу пикселов мы интерполируем их значения и получаем значение текущего. Если число попало в диапазон округления *(x+0.5; x-0.5]*, где *х* – текущий пиксел – запоминаем его значение. Если оно выходит за рамки округления – мы предполагаем, что округления произошло из крайней точки диапазона округления и присваиваем новому пикселу соответствующее значение. Крайние пикселы изображения мы берём «как есть». Временная сложность данного алгоритма для линейного ряда чисел:

*O(S-2),*

где *S* – размер ряда.

Предложенный метод даёт улучшение восстановление даже с таким приближенным быстрым алгоритмом. Более сложные продуманные алгоритмы восстановления потерянной будут давать лучший результат. Подобные алгоритмы стоит строить исходя из природы изображения и допустимой скорости их работы.

## Оценка качества восстановления

Для определения качества восстановления изображения следует использовать три критерия:
* среднеквадратичное отклонение цвета;
* среднеквадратичное отклонение резкости переходов;
* метод «на глаз».

Первый критерий популярный, однако совсем не отображает качество восстановления изображений. Более того, чаще всего среднеквадратичное отклонение цвета исходного и восстановленного изображения выше, чем исходного и испорченного.

Второй метод более показателен. Для его реализации мы сначала создаём для каждого изображения маску, в котором каждый элемент равен разнице значений текущего и следующего пикселей, а потом считаем их среднеквадратичное отклонение.

Несмотря на всю мощь численных методов, только визуально можно правильно оценить качество восстановленного изображения.

## Классы

Исходный код включает в себя несколько классов:

Имя | Описание
------------ | -------------
***Program.cs*** | Точка входа в программу.
***ImageComparator.cs*** | Класс, содержащий методы разширения для класса Image, добавляющий методы сразвение изображений.
***InterpolationExtention.cs*** | Класс, реализующий восстановление дробной части, потерянной при округлении.
***MainForm.cs*** | Главная форма программы.
***StartForm.cs*** | Форма выбора обрабатываемого изображения.

## Примеры работы

Несколько скриншотов работы программы.

![](/Samples/Guitarist.png)

![](/Samples/Cat.png)

![](/Samples/Cofee.png)

![](/Samples/Goat.png)

![](/Samples/Bible.png)

## Загрузка

* [Исполнительный файл](https://github.com/Kovnir/DeblurModification/blob/master/bin/RecoveryModification.zip?raw=true)
* [Исходные коды](https://github.com/Kovnir/DeblurModification/archive/master.zip)

## Публикации

Данная работа была опубликована в качестве научной публикации.
