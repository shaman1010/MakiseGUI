Система ввода.

Система ввода организует доставку событий с устройств ввода до нужных элементов графического интерфейса.

Устройствами ввода могут быть:

1. Внешние физические кнопки
2. Виртуальные события(из любого места программы). Например виртуальная экранная клавиатура
3. Мышь/сенсорный экран
4. Любое другое устройство

Т.к. система может быть асинхронной, то события сначала складываются в FIFO буффер, а потом по возможности вызываются. Длинна буффера этого задаётся в makise\_config.h при помощи MAKISE\_GUI\_INPUT\_BUFFER\_LEN.

Информация о событии хранится в структуре MInputData:

- event определяет, какое событие было совершено: нажата кнопка, введён символ и тд
- [опционально] key говорит о том, какая кнопка была нажата
- [опционально] time - сколько времени была нажата кнопка или нажимается. В миллисекундах
- [опционально] c - символ, который был введён
- [опционально] cursor - текущие координаты курсора и их изменение с прошлого подобного события

Послать событие системе можно в любой момент при помощи методов:

* makise\_gui\_input\_send(MHost*, MInputData) - посылает событие(добавляет его в буффер
* makise\_gui\_input\_send\_button(MHost*, MInputKeyEnum кнопка, MInputEventEnum тип события, uint32_t время) - создаёт событие нажатия кнопки и добавляет его в буффер

Как станет возможным(пройдут все методы отрисовки и прочие действия с графическим интерфейсом) для того чтобы события дошли до графического интерфейса необходимо вызвать makise\_gui\_input\_perform. Этот метод передаст все события из буфера графическому интерфейсу и очистит буфер.

Обработка событий графическим интерфейсом происходит так:

1. Событие передаются базовому контейнеру MHost.
2. Если контейнер уже имеет элемент в фокусе, то событие передаётся тому элементу. Он уже обрабатывает событие сам и возвращает результат.
3. Когда результат получен, то если задан пользовательский обработчик событий, то результат передаётся туда.