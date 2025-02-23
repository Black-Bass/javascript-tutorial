
# Set, Map, WeakSet и WeakMap

В ES-2015 появились новые типы коллекций в JavaScript: `Set`, `Map`, `WeakSet` и `WeakMap`.

## Map

`Map` -- коллекция для хранения записей вида `ключ:значение`.

В отличие от объектов, в которых ключами могут быть только строки, в `Map` ключом может быть произвольное значение, например:

```js run
'use strict';

let map = new Map();

map.set('1', 'str1');   // ключ-строка
map.set(1, 'num1');     // число
map.set(true, 'bool1'); // булевое значение

// в обычном объекте это было бы одно и то же,
// map сохраняет тип ключа
alert( map.get(1)   ); // 'num1'
alert( map.get('1') ); // 'str1'

alert( map.size ); // 3
```

Как видно из примера выше, для сохранения и чтения значений используются методы `get` и `set`. И ключи и значения сохраняются "как есть", без преобразований типов.

Свойство `map.size` хранит общее количество записей в `map`.

Метод `set` можно чейнить:

```js
map
  .set('1', 'str1')
  .set(1, 'num1')
  .set(true, 'bool1');
```

При создании `Map` можно сразу инициализировать списком значений.

Объект `map` с тремя ключами, как и в примере выше:

```js
let map = new Map([
  ['1',  'str1'],
  [1,    'num1'],
  [true, 'bool1']
]);
```

Аргументом `new Map` должен быть итерируемый объект (не обязательно именно массив). Везде утиная типизация, максимальная гибкость.

**В качестве ключей `map` можно использовать и объекты:**

```js run
'use strict';

let user = { name: "Вася" };

// для каждого пользователя будем хранить количество посещений
let visitsCountMap = new Map();

*!*
// объект user является ключом в visitsCountMap
visitsCountMap.set(user, 123);
*/!*

alert( visitsCountMap.get(user) ); // 123
```

Использование объектов в качестве ключей -- как раз тот случай, когда `Map` сложно заменить обычными объектами `Object`. Ведь для обычных объектов ключ может быть только строкой.

```smart header="Как map сравнивает ключи"
Для проверки значений на эквивалентность используется алгоритм [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/index.html#sec-samevaluezero). Он аналогичен строгому равенству `===`, отличие -- в том, что `NaN` считается равным `NaN`. Поэтому значение `NaN` также может быть использовано в качестве ключа.

Этот алгоритм нельзя изменять или задавать свою функцию сравнения.
```

Методы для удаления записей:

- `map.delete(key)` удаляет запись с ключом `key`, возвращает `true`, если такая запись была, иначе `false`.
- `map.clear()` -- удаляет все записи, очищает `map`.

Для проверки существования ключа:

- `map.has(key)` -- возвращает `true`, если ключ есть, иначе `false`.

### Итерация

Для итерации по `map` используется один из трёх методов:

- `map.keys()` -- возвращает итерируемый объект для ключей,
- `map.values()` -- возвращает итерируемый объект для значений,
- `map.entries()` -- возвращает итерируемый объект для записей `[ключ, значение]`, он используется по умолчанию в `for..of`.

Например:

```js run
'use strict';

let recipeMap = new Map([
  ['огурцов',   '500 гр'],
  ['помидоров', '350 гр'],
  ['сметаны',   '50 гр']
]);

// цикл по ключам
for(let fruit of recipeMap.keys()) {
  alert(fruit); // огурцов, помидоров, сметаны
}

// цикл по значениям [ключ,значение]
for(let amount of recipeMap.values()) {
  alert(amount); // 500 гр, 350 гр, 50 гр
}

// цикл по записям
for(let entry of recipeMap) { // то же что и recipeMap.entries()
  alert(entry); // огурцов,500 гр , и т.д., массивы по 2 значения
}
```

```smart header="Перебор идёт в том же порядке, что и вставка"
Перебор осуществляется в порядке вставки. Объекты `Map` гарантируют это, в отличие от обычных объектов `Object`.
```

Кроме того, у `Map` есть стандартный метод `forEach`, аналогичный массиву:

```js run
'use strict';

let recipeMap = new Map([
  ['огурцов',   '500 гр'],
  ['помидоров', '350 гр'],
  ['сметаны',   '50 гр']
]);

recipeMap.forEach( (value, key, map) => {
  alert(`${key}: ${value}`); // огурцов: 500 гр, и т.д.
});
```

## Set

`Set` -- коллекция для хранения множества значений, причём каждое значение может встречаться лишь один раз.

Например, к нам приходят посетители, и мы хотели бы сохранять всех, кто пришёл. При этом повторные визиты не должны приводить к дубликатам, то есть каждого посетителя нужно "посчитать" ровно один раз.

`Set` для этого отлично подходит:

```js run
'use strict';

let set = new Set();

let vasya = {name: "Вася"};
let petya = {name: "Петя"};
let dasha = {name: "Даша"};

// посещения, некоторые пользователи заходят много раз
set.add(vasya);
set.add(petya);
set.add(dasha);
set.add(vasya);
set.add(petya);

// set сохраняет только уникальные значения
alert( set.size ); // 3

set.forEach( user => alert(user.name ) ); // Вася, Петя, Даша
```

В примере выше многократные добавления одного и того же объекта в `set` не создают лишних копий.

Альтернатива `Set` -- это массивы с поиском дубликата при каждом добавлении, но они гораздо хуже по производительности. Или можно использовать обычные объекты, где в качестве ключа выступает какой-нибудь уникальный идентификатор посетителя. Но это менее удобно, чем простой и наглядный `Set`.

Основные методы:

- `set.add(item)` -- добавляет в коллекцию `item`, возвращает `set` (чейнится).
- `set.delete(item)` -- удаляет `item` из коллекции, возвращает `true`, если он там был, иначе `false`.
- `set.has(item)` -- возвращает `true`, если `item` есть в коллекции, иначе `false`.
- `set.clear()` -- очищает `set`.

Перебор `Set` осуществляется через `forEach` или `for..of` аналогично `Map`:

```js run
'use strict';

let set = new Set(["апельсины", "яблоки", "бананы"]);

// то же, что: for(let value of set)
set.forEach((value, valueAgain, set) => {
  alert(value); // апельсины, затем яблоки, затем бананы
});
```

Заметим, что в `Set` у функции в `.forEach` три аргумента: значение, ещё раз значение, и затем сам перебираемый объект `set`. При этом значение повторяется в аргументах два раза.

Так сделано для совместимости с `Map`, где у `.forEach`-функции также три аргумента. Но в `Set` первые два всегда совпадают и содержат очередное значение множества.

## WeakMap и WeakSet

`WeakSet` -- особый вид `Set` не препятствующий сборщику мусора удалять свои элементы. То же самое -- `WeakMap` для `Map`.

То есть, если некий объект присутствует только в `WeakSet/WeakMap` -- он удаляется из памяти.

Это нужно для тех ситуаций, когда основное место для хранения и использования объектов находится где-то в другом месте кода, а здесь мы хотим хранить для них "вспомогательные" данные, существующие лишь пока жив объект.

Например, у нас есть элементы на странице или, к примеру, пользователи, и мы хотим хранить для них вспомогательную информацию, например обработчики событий или просто данные, но действительные лишь пока объект, к которому они относятся, существует.

Если поместить такие данные в `WeakMap`, а объект сделать ключом, то они будут автоматически удалены из памяти, когда удалится элемент.

Например:

```js
// текущие активные пользователи
let activeUsers = [
  {name: "Вася"},
  {name: "Петя"},
  {name: "Маша"}
];

// вспомогательная информация о них,
// которая напрямую не входит в объект юзера,
// и потому хранится отдельно
let weakMap = new WeakMap();

weakMap[activeUsers[0]] = 1;
weakMap[activeUsers[1]] = 2;
weakMap[activeUsers[2]] = 3;

alert( weakMap[activeUsers[0]] ); // 1

activeUsers.splice(0, 1); // Вася более не активный пользователь

// weakMap теперь содержит только 2 элемента

activeUsers.splice(0, 1); // Петя более не активный пользователь

// weakMap теперь содержит только 1 элемент
```

Таким образом, `WeakMap` избавляет нас от необходимости вручную удалять вспомогательные данные, когда удалён основной объект.

У WeakMap есть ряд ограничений:

- Нет свойства `size`.
- Нельзя перебрать элементы итератором или `forEach`.
- Нет метода `clear()`.

Иными словами, `WeakMap` работает только на запись (`set`, `delete`) и чтение (`get`, `has`) элементов по конкретному ключу, а не как полноценная коллекция. Нельзя вывести всё содержимое `WeakMap`, нет соответствующих методов.

Это связано с тем, что содержимое `WeakMap` может быть модифицировано сборщиком мусора в любой момент, независимо от программиста. Сборщик мусора работает сам по себе. Он не гарантирует, что очистит объект сразу же, когда это стало возможным. В равной степени он не гарантирует и обратное. Нет какого-то конкретного момента, когда такая очистка точно произойдёт -- это определяется внутренними алгоритмами сборщика и его сведениями о системе.

Поэтому содержимое `WeakMap` в произвольный момент, строго говоря, не определено. Может быть, сборщик мусора уже удалил какие-то записи, а может и нет. С этим, а также с требованиями к эффективной реализации `WeakMap`, и связано отсутствие методов, осуществляющих доступ ко всем записям.

То же самое относится и к `WeakSet`: можно добавлять элементы, проверять их наличие, но нельзя получить их список и даже узнать количество.

Эти ограничения могут показаться неудобными, но по сути они не мешают `WeakMap/WeakSet` выполнять свою основную задачу -- быть "вторичным" хранилищем данных для объектов, актуальный список которых (и сами они) хранятся в каком-то другом месте.

## Итого

- `Map` -- коллекция записей вида `ключ: значение`, лучше `Object` тем, что перебирает всегда в порядке вставки и допускает любые ключи.
- `Set` -- коллекция уникальных элементов, также допускает любые ключи.

Основная область применения `Map` -- ситуации, когда строковых ключей не хватает (нужно хранить соответствия для ключей-объектов), либо когда строковый ключ может быть совершенно произвольным.

К примеру, в обычном объекте `Object` нельзя использовать "совершенно любые" ключи. Есть встроенные методы, и уж точно есть свойство с названием `__proto__`, которое зарезервировано системой. Если название ключа даётся посетителем сайта, то он может попытаться использовать такое свойство, заменить прототип, а это, при запуске JavaScript на сервере, уже может привести к серьёзным ошибкам.
 
- `WeakMap` и `WeakSet` -- "урезанные" по функционалу варианты `Map/Set`, которые позволяют только "точечно" обращаться элементам (по конкретному ключу или значению). Они не препятствуют сборке мусора, то есть если ссылка на объект осталась только в `WeakSet/WeakMap` -- он будет удалён.


