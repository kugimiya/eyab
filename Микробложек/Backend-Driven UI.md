Эта статья имеет релиз на [[Habr]]: https://habr.com/ru/articles/753718/

**Зачем? Потому что хотим избежать боли при:**
- Значительно поменять Layout фронтенд-приложения
- Или же структуру какого-то экрана в приложении
- Внедрение A/B-тестирования
- Срочно добавить дополнительно промо от отдела маркетинга
- Новая страничка с каким-то контентом

**А в чем же боль? Для всех членов команды она своя:**
- Для ПМа нужно будет учитывать/представлять сложность изменения структуры страницы. Конечно, есть тимлид который подскажет. Но что, если он тимлид-джун, без опыта? То-то же.
- Тестировщики. Изменение глобальных частей, с захардкоженной структурой практически всегда триггерит полный регресс. Нет уверенности в том, что полный ручной регресс не вызывает боль.
- Для прогеров можно выделить следующие боли кратко:
- - изменения в структуре нужно зафиксировать в коде, то есть сесть и заверстать
- - интегрировать внешние источники данных для новых блоков
- - контроллировать удаление источника данных при удалении блока
- - встроить новый блок в существую верстку, или же адаптировать верстку при удалении блока

**Как насчет сроков?** 
Давайте рассмотрим кейс, в котором у нас заказывают небольшой сервис бронирования койко-места. У нас есть дизайн, по которому мы верстаем; есть договор, определяющий функционал и сроки. Есть реальная действительность, где перед дедлайном просят поменять промо-блок и компонент-селектор-койки местами.

Казалось бы, в верстке меняешь два компонента местами, готово. Но, есть вероятные технические блокеры:

- фронтендер мог верстать страницу изначально с этими блоками, делая хардкод в размерах, отсупах и так далее
- источники данных могут требовать какой-то определенный порядок запроса данных
- код может быть "грязным", и понимающий его синиор в отпуске
- ещё есть минус от хардкоженной структуры UI, не очень очевидный: у страницы нет контракта с точки зрения его структуры и данных. Мы можем понять какие данные придут, но только после изучения кода. Узнать раскладку блоков в верстке -- тоже только после полного изучения кода.

**А добавление новой страницы с текстовым контентом?**
- нажно залезть в код и скопировать типовую страничку, с её layout; по сути копипаст бойлерплейта
- зарегистрировать её в роутере приложения, закрыть за авторизацией если надо
- подрубить источник данных

**С такой ментальной моделью приложения сложно будет делать следующее:**
- A/B-тестирование
- Добавлять новый контент на сайт без привлечения отдела разработки

Иными словами, фронтенд превращается в программу, внешний вид который не поддается изменению извне, он не зависит от какой-то схемы.

По-сути, вся техника Backend-Driven UI сводится к получению схемы экрана от бекенда. Часто даже с получением карты сайта. 

**Какие проблемы нам нужно решить, чтобы получить удобный backend-driven ui?**
- контракт от бекенда; нужно понимать, в каком формате будет приходить лейаут экрана
- начальное состояние компонент, оно вообще нужно? если да, то как лучше инкапсулировать?
- до какой степени bdui описывает layout? это просто расположение блоков? а вложенность блоков? а горизонтально или вертикально? а размеры блока посередине? а мобильная/десктоп версии layout?
- если у страницы есть параметры (например номер текущей страницы, 2я например), их тоже надо учесть при проектировании

Как это сделать? Лучше всего, как я выяснил, выделять это в отдельный микросервис, BFF, Backend For Frontend. Не стоит запихивать в бекенд проекта логику для полиморфного отображения.  

Интересный пример реализации -- Strapi, headless cms. Она даёт интерфейс для редактирования контента, и не формирует отображения, не генерирует сайт. Так, можно создать сущность "макет страницы" и в него накидывать блоки страницы. Но, это совсем неудобно. И ещё нет возможности удобно впилить другие источники данных.

Есть другой пример реализации, block manager в cms cs-cart. По сути, это упрощенный визуальный редактор макета страницы. Минусов несколько:

- оно формирует сайт по заранее заданным бизнес-требованиям, то есть это ad-hoc
- оно формирует сайт, есть это не bff
- php в 2022 не торт для фронтендера  

Хорошо, что можем сделать такой BFF для backend-driven ui самостоятельно. Есть рецепт от меня, но он слишком большой для этого сумбурного мини-ресерча.