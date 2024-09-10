# Проект Яндекс Прилавка (API)
Яндекс Прилавок - это сервис, который предоставляет предпринимателям и компаниям возможность создавать собственные
виртуальные магазины в онлайн-пространстве. Этот инструмент облегчает процесс создания и настройки интернет-магазинов, 
управления продажами и обработки заказов. Он также обеспечивает интеграцию с онлайн-платежами, 
что делает его полезным для развития электронной коммерции и совершения успешных онлайн-сделок.

![image](https://github.com/user-attachments/assets/cde3b4af-4b5f-4ada-a55c-83006374d03f)

<details>
  <summary>Задача</summary> 
  
**Работа с наборами:**

возможность добавлять продукты в набор — ручка > POST /api/v1/kits/{id}/products.
Работа с курьерами: возможность проверить, есть ли доставка курьерской службой «Привезём быстро» и сколько она стоит. 
Ручка> POST /fast-delivery/v3.1.1/calculate-delivery.xml

**Работа с корзиной:**

возможность получить список продуктов, которые добавили в корзину. > Ручка GET /api/v1/orders/id;
возможность добавлять продукты в корзину.> Ручка PUT /api/v1/orders/:id;
возможность удалять корзину.> Ручка DELETE/api/v1/orders/:id.

**Постановка задачи** 

1.Проанализируй требования к новой функциональности бэкенда Яндекс.Прилавка.
Изучи документацию к API в Apidoc.

2.Спроектируй тесты в виде чек-листа, чтобы покрыть функциональность, 
которую тебе передали на тестирование: она описана выше. Авторизацию проверять не нужно.

3.Протестируй API через Postman и заведи баг-репорты в YouTrack, если это понадобится.

4.Напиши отчёт о тестировании (*). Что ты можешь рассказать команде о статусе протестированной части продукта?
(*) — задача со звёздочкой — задание, которое можно не сдавать. Оно не повлияет на результат итогового проекта.
</details>

<details>
  <summary>Требования к бэкенду</summary> 
  
# Требования к бэкенду приложения

## Описание общей логики

**Авторизация и данные для заказа**

Пользователь может зарегистрироваться. Если пользователь не 
зарегистрировался, то форма заполнения: имя, e-mail, телефон, адрес, 
комментарий — появляется, когда пользователь уже сформировал корзину и хочет 
оформить заказ. Пользователь не может сделать заказ, если не ввёл 
обязательные поля. Если пользователь зарегистрировался, то ему не нужно вновь 
вводить данные, однако он может их изменить. Пользователь может оформить 
несколько заказов.

 Для заказа нужно ввести:
- имя;
- телефон;
- адрес;
- e-mail (необязательно);
- комментарий к заказу (необязательно)

**Ограничение полей**
![image](https://github.com/user-attachments/assets/d6ef9a98-a1d9-4fa0-8b77-e8df4aff4c5e)

## Главное меню заказа
 На выбор даётся 3 карточки:
 - «Под ситуацию» (вечер кино, на дачу и пикник, вкусы Парижа);
 - «Приготовь блюдо» (сырники, борщ, карбонара, штрудель);
 - «Создать свой набор» (пользователь сам называет и добавляет туда 
   продукты).
 Переходишь в карточку — видишь варианты наборов. 
Переходишь в набор — видишь перечень возможных продуктов. Каждый продукт 
относится к определённой категории (например, «Напитки»). В перечне 
пользователь видит название продукта, его массу, цену. Когда клиент нажимает на 
продукт, ему даётся возможность выбрать количество продуктов. При этом 
появляется кнопка «Корзина», которая отображает сумму выбранных товаров и 
время доставки.
 При нажатии на кнопку пользователь может посмотреть свою корзину.

## Корзина

 Отображает наименование продукта, его количество, цену для этого продукта с 
учётом количества, итог. Если доставка платная, то отображает сумму доставки и 
итоговую сумму заказа. Пользователь может удалить корзину, добавить новые 
продукты, убрать выбранные продукты.

**Создание корзины:** 

 - При создании корзины должна быть возможность указать время доставки 
   продуктов
 - При создании корзины проверять, что все службы доставки могут обработать 
   заказ в указанное время
 - Если время доставки не указано - брать, как текущее время с сервера.
При удалении и просмотре корзины время доставки не учитывается.

## Создание своего набора

 Пользователь может создать свой набор и выбрать продукты. Он обязательно 
даёт имя набору и выбирает продукты. Пользователь может изменить название 
набора, удалить набор, удалить выбранные продукты, добавить новые. Если 
данные при создании или изменении набора введены неверно — выводится 
сообщение об ошибке.

## Ограничения создания набора
![image](https://github.com/user-attachments/assets/d5bc7d53-704b-476f-9250-07e32052d347)

## Работа с курьерами
 Работа с курьерами предполагает два режима работы:
 1. При оформлении заказа: URL 
> POST /api/v1/orders
 Логика выбора курьерской службы при оформлении заказа пользователем: служба 
должна работать в указанное в заказе время и должна быть самой дешёвой. 
Пользователю отображается время доставки в зависимости от выбранной службы.
 В заказе доставка становится платной, если соблюдается хотя бы одно условие:

- превышено максимальное количество товаров;
- превышен максимальный вес;
- сумма заказа меньше 150 рублей.
- 
Логика расчёта стоимости доставки заказа для пользователя:
- Если вес или количество превысили максимальное значение, стоимость 
доставки для пользователя становится 99 рублей.
- Если вес или количество в заказе не превышают максимального, берётся 
> price этих продуктов, и если их сумма меньше 150 рублей, то стоимость 
доставки для пользователя также становится 99 рублей.
 Стоимость доставки прибавляется в итоговую сумму заказа.
 Если ни одно из обозначенных условий не соблюдается, то цена доставки 
 курьерской службы не включается в итоговую сумму заказа.


  


  
  
