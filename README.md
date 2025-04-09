# larek

Стек: HTML, SCSS, TS, Webpack

Результат:
https://radioacti1ve.github.io/larek/

Структура проекта:
- src/ — исходные файлы проекта
- src/components/ — папка с JS компонентами
- src/components/base/ — папка с базовым кодом

Важные файлы:
- src/pages/index.html — HTML-файл главной страницы
- src/types/index.ts — файл с типами
- src/index.ts — точка входа приложения
- src/styles/styles.scss — корневой файл стилей
- src/utils/constants.ts — файл с константами
- src/utils/utils.ts — файл с утилитами

## Установка и запуск
Для установки и запуска проекта необходимо выполнить команды

```
npm install
npm run start
```

или

```
yarn
yarn start
```
## Сборка

```
npm run build
```

или

```
yarn build
```

## Данные и типы данных, используемые в приложении
Карточка
```
interface ICard {
  id: string;
  description: string;
  image: string;
  title: string;
  category: string;
  price: number;  
}
```

Данные карточки при начальной загрузки страницы
```
type mainCard = Pick<ICard, 'category' | 'title' | 'image' | 'price'>;
```

Данные карточки при открытии модального окна
```
type modalCard = Pick<ICard, 'category' | 'title' | 'image' | 'price' | 'description'>;
```

Данные картоки в корзине
```
type basketCard = Pick<ICard, 'title' | 'price' | 'id'>;
```

Интерфейс корзины с товарами
```
interface IBasketData {
  cards: basketCard[];
  goods: number;
  total: number;
  setTotal(): number;
  getTotal(): number;
  getGoods(): number;
  addItem(card: ICard): void;
  deleteItem(id: string): void;
}
```

Интерфейс для работы с данными карточек
```
interface ICardsData {
  cards: ICard[];
  setCards(cards: ICard[]): void;
  getCards(): ICard[];
  getCard(id: string): ICard;
}
```

## Архитектура приложения

Код приложения разделен на слои согласно парадигме MVP: 
- слой представления, отвечает за отображение данных на странице, 
- слой данных, отвечает за хранение и изменение данных
- презентер, отвечает за связь представления и данных.

### Базовый код

#### Класс `Api`
Содержит в себе базовую логику отправки запросов. В конструктор передается базовый адрес сервера и опциональный объект с заголовками запросов.
Методы: 
- `get` - выполняет GET запрос на переданный в параметрах ендпоинт и возвращает промис с объектом, которым ответил сервер
- `post` - принимает объект с данными, которые будут переданы в JSON в теле запроса, и отправляет эти данные на ендпоинт переданный как параметр при вызове метода. По умолчанию выполняется `POST` запрос, но метод запроса может быть переопределен заданием третьего параметра при вызове.

#### Класс `EventEmitter`
Брокер событий позволяет отправлять события и подписываться на события, происходящие в системе. Класс используется в презентере для обработки событий и в слоях приложения для генерации событий.  
Методы, реализуемые классом:
- `on` - подписка на событие
- `off` - снять обработчик с события
- `onAll` - подписка на все события
- `offAll` - снять обработчик со всех событий
- `emit` - инициализация события
- `trigger` - возвращает функцию, при вызове которой инициализируется требуемое в параметрах событие   

### Слой данных

#### Класс `BasketData`
Класс отвечает за логику и работу данных в корзине. \
Конструктор класса принимает инстант брокера событий. \
В полях класса хранятся следующие данные:
- `cards: basketCard[]` - товары, которые добавил пользователь
- `goods: number` - количество товаров лежащих в корзине
- `total: number` - стоимость всех товаров в корзине
- `events: IEvents` - экземпляр класса `EventEmitter` для инициации событий при изменении данных. 

Так же класс предоставляет набор методов для взаимодействия с данными:
- `getGoods(): number` - получение количества товаров
- `setTotal(): void` - подсчет суммы товаров
- `getTotal(): number` - получение суммы
- `addItem(card: ICard): void` - добавление товара и уведомляет об изменении массива
- `deleteItem(id: string): void ` - удаление товара и уведомляет об изменении массива
- `clearBaket(): void` - очищает корзину

#### Класс `CardsData` 
Класс отвечает за хранение и логику работы с данными карточек, полученных с сервера. \
Конструктор класса принимает инстант брокера событий. \
В полях класса хранятся следующие данные:
- `cards: ICard[]` - массив объектов карточек
- `events: IEvents` - экземпляр класса `EventEmitter` для инициации событий при изменении данных. 

Так же класс предоставляет набор методов для взаимодействия с данными:
-  `setCards(cards: ICard[]): void` - устанавливает массив карточек и уведомляет об изменении массива
-  `getCards(): ICard[]` - возвращает массив карточек
-  `getCard(id: string): ICard` - ищет карточку по переданному в параметрах айди

#### Класс `ContactData` 
Класс отвечает за хранение и установку данных, введенных пользователем в форме. \
В полях класса хранятся следующие данные:
- `payment: string` - способ платежа
- `email: string` - адрес электронной почты
- `phone: string` - номер телефона 
- `address: string` - адрес доставки
- `total: number` - стоимость заказанных товаров
- `items: string[]` - уникальные айди товаров 
Так же класс предоставляет набор методов для взаимодействия с данными:
В полях класса хранятся следующие данные:
- `set _payment(value: string)` - устанавливает метод оплаты
- `set _email(value: string) ` - устанавливает адрес электронной почты
- `set _phone(value: string) ` - устанавливает номер телефона
- `set _address(value: string)` - устанавливает адрес доставки
- `set _total(value: number) ` - устанавливает стоимость заказа
- `set _items(value: string[])` - устанавливает айди товаров


### Классы представления
Все классы представления отвечают за отображение внутри контейнера(Dom элемента) передаваемых в них данных

#### Класс `Page` 

Класс, содержащий основные элементы главного экрана

Свойства класса:
- `_gallery: HTMLElement`: - место для карточек
- `_wrapper: HTMLElement` - обертка всего контента
- `_basket: HTMLButtonElement` - корзина
- `_amount: HTMLSpanElement` - количество товаров в корзине


Методы класса:
- `set catalog(items: HTMLElement[])` - устанавливает количество товаров
- `set locked(value: boolean)` - блокирует прокрутку страницы при открытом модальном окне
- `set amount(value: number)` - устанавливает количество товаров в корзине


#### Класс `Card`
Абстрактный класс для упрощения создания разных представлений карточек. Является расширением класса `Component`
В полях класса хранятся следующие данные:
-  `_title: HTMLHeadingElement` - название
-  `_price: HTMLSpanElement` - цена
-  `_id: string` - айди

Методы класса:
- `set title` - устанавливает название
- `set price` - устанавливает цену
- `set id` - устанавливает айди

#### Класс `CardView`
Класс представляет карточку, которая используется при начальной загрузке страницы 
и является расширением класса `Card`. \
В полях класса хранятся следующие данные:
- `card: HTMLButtonElement` - сама карточка

Методы класса: 
- `set data` - устанавливает необходимые данные

#### Класс `CardCompactView `
Является расширением класса `Card` и представляет карточку, которую пользователь добавил в корзину.

Свойства класса:
- `deleteButton: HTMLButtonElement` - кнопка удаления из корзины
- `_index: HTMLSpanElement` - индекс товара в корзине

Методы класса: 
- `set data` - устанавливает необходимые данные
- `set index` - устанавливает индекс товара в корзине

#### Класс `CardFullView` 
Является расширением класса `Modal` и представляет собой модальное окно карточки.

Свойства класса: 
- `addButton: HTMLButtonElement` - кнопка добавления в корзину

Методы класса: 
- `set data` - устанавливает необходимые данные

#### Класс `Modal` 
Представляет собой класс модального окна. Имеет методы `open()` и `close()`.

Свойства класса: 
- `currentModal: HTMLElement` - текущее модальное окно
- `_content: HTMLElement | undefined` - контент модального окна
- `events: IEvents` - экземпляр класса `EventEmitter` для инициации событий при изменении данных.  
- `buttonClose: HTMLButtonElement` - кнопка закрытия модального окна

Методы класса: 
- `open(content: HTMLElement): void` - открытие окна
- `close(): void` - закрытие
- `set content(container: HTMLElement)` - установка контента модального окна

#### Класс `BasketView` 

Представляет собой корзину с товарами и кнопкой покупки

Свойства класса:
- `_list: HTMLUListElement` - контейнер для карточек
- `buyButton: HTMLButtonElement` - кнопка покупки товаров
- `_total: HTMLSpanElement` - стоимость всех товаров в корзине

Методы класса:
- `set list(cards: HTMLElement[])` - установка товаров
- `set total(value: number)` - установка итоговой цены

#### Класс `ModalForm` 
Является расширением класса `Modal` и представляет собой модальное окно формы.

Свойства класса: 
- `inputList: HTMLInputElement[]` - список инпутов формы

Методы класса: 
- `checkValidation(): boolean` - проверка валидации полей формы
- `setValid(isValid: boolean): void` - установка состояния кнопки

#### Класс `ModalSuccess` 
Является расширением класса `Modal` и представляет собой уведомление об успешной покупке.

Свойства класса: 
- `_total: HTMLParagraphElement` - суммарная цена
- `button: HTMLButtonElement` - кнопка к новым покупкам

### Слой коммуникации

#### Класс `AppApi`
Принимает в конструктор экземпляр класса Api и предоставляет методы реализующие взаимодействие с бэкендом сервиса.

## Взаимодействие компонентов
Код, описывающий взаимодействие представления и данных между собой находится в файле `index.ts`, выполняющем роль презентера.\
Взаимодействие осуществляется за счет событий генерируемых с помощью брокера событий и обработчиков этих событий, описанных в `index.ts`\
В `index.ts` сначала создаются экземпляры всех необходимых классов, а затем настраивается обработка событий.

*Список всех событий, которые могут генерироваться в системе:*\
*События изменения данных (генерируются классами моделями данных)*
- `cards:changed` - изменение массива карточек
- `basket:add` - добавление товара в корзину
- `basket:delete` - удаление товара из корзины

*События, возникающие при взаимодействии пользователя с интерфейсом (генерируются классами, отвечающими за представление)*
- `card:open` - открытие модального окна с картой
- `card:close` - закрытие модального окна с картой
- `basket:open` - открытие модального окна с корзиной
- `basket:close` - закрытие модального окна с корзиной
- `modal:open` - открытие модального окна
- `modal:close` - закрытие модального окна
- `adress:input` - изменение данных в форме с вводом адреса
- `email:input` - изменение данных в форме с вводом почты
- `tel:input` - изменение данных в форме с вводом телефона
- `basket:buy`- уведомление о покупке продолжении покупки пользователем
- `adress:submit` - отправка формы с адресом
- `contacts:submit` - отправка формы с контактами пользователя
- `success:close` - закрытие модального окна об уведомлении об успешных покупках
