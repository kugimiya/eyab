epds - experimental [[pissychan]] database syncer. Хочу синкать [[БД]] писсичана, но по-своему. (с)

**Цели проекта:**
- вернуть подписки на треды в [[umechan-opti]]
- "кешировать" треды для umechan-opti
- реализовать модерку для umechan-opti, без конфликтов с origin данными
- чтобы это было максимально оптимально по кол-ву передаваемых данных по сети и тратам CPU у всех участников проекта pissychan
- въебурить в бекенд API'шки функционал, позволяющий получить список изменений на борде после определенного времени (курсоры это, event sourcing, ещё не разобрался)
***
**Про проект:**
#need-todo 
***
**Дневничок разработки сабжа:**

Хост -- [[Рицка (Александр Барышников)]]

- **11.06.2024**
	Пока обкатывал дампилку всех постов с чана, пришёл хост и сказал что я охуел.
	![[Pasted image 20240628140501.png]]

- **11.06.2024**
	Дампилка, кстати, мега-крутая. Реализована так, что всегда будет одновременно обрабатывать N-задач. Вот таким образом загрузил 1866 тредов, по 20 тредов одновременно.
	![[Pasted image 20240628140542.png]]

- **11.06.2024**
	Сижу, вспоминаю как работать с PostgreSQL. Это ж косвенная цель проекта: освежить навык работы с SQL в целом.
	![[Pasted image 20240628140610.png]]

- **12.06.2024**
	Пока блокер: не запускается в docker сам бекенд, чото падоет... Надо бы разобраться, но к такому я слегка не подготовился. Хост тоже планировал разобраться, алсо, но я думаю, что делать это в параллели -- ничо страшного, можно.

- **20.06.2024**
	Запушил в umechan зачатки нового функционала:
		для v1: [https://github.com/U-Me-Chan/umechan/commit/1ebfa1e3a383903e84f709922253de49eff71901](https://github.com/U-Me-Chan/umechan/commit/1ebfa1e3a383903e84f709922253de49eff71901)
		для v2: [https://github.com/U-Me-Chan/umechan/commit/125ab4027db336c9e8fe0c172b5f751c48a4f281](https://github.com/U-Me-Chan/umechan/commit/125ab4027db336c9e8fe0c172b5f751c48a4f281)
	
	Для v1 запилил по приколу, Рицка потом выпилит вместе со всеми v1-эндпоинтами.Что ещё мне осталось в этом репо:
		запилить миграцию
		потестить/подебажить
		запушить фиксы
		пропушить мерж фичи
	И ебанул короче сразу PR'чик: [https://github.com/U-Me-Chan/umechan/pull/38](https://github.com/U-Me-Chan/umechan/pull/38)
	
	По ходу работ засрал прод, мдахех.

- **28.06.2024**
	Ожидаю ревью и мерж PR'чика. Хост сказал, что тоже хочет чото запушить.
