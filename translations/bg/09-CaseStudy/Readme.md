<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "b6b1bc868efed4cf02c52f8deada559d",
  "translation_date": "2025-05-17T17:35:40+00:00",
  "source_file": "09-CaseStudy/Readme.md",
  "language_code": "bg"
}
-->
# Проучване на случай: Azure AI туристически агенти – референтна реализация

## Общ преглед

[Azure AI туристически агенти](https://github.com/Azure-Samples/azure-ai-travel-agents) е цялостно референтно решение, разработено от Microsoft, което демонстрира как да се изгради многоагентно приложение за планиране на пътувания, захранвано от AI, използвайки Model Context Protocol (MCP), Azure OpenAI и Azure AI Search. Този проект показва най-добрите практики за оркестриране на множество AI агенти, интегриране на корпоративни данни и предоставяне на сигурна, разширяема платформа за реални сценарии.

## Основни функции
- **Оркестрация на много агенти:** Използва MCP за координиране на специализирани агенти (напр. агенти за полети, хотели и маршрути), които си сътрудничат за изпълнение на сложни задачи по планиране на пътувания.
- **Интеграция на корпоративни данни:** Свързва се с Azure AI Search и други корпоративни източници на данни, за да предостави актуална, релевантна информация за препоръки за пътуване.
- **Сигурна, мащабируема архитектура:** Използва Azure услуги за удостоверяване, разрешаване и мащабируемо разгръщане, следвайки най-добрите практики за корпоративна сигурност.
- **Разширяеми инструменти:** Реализира повторно използваеми MCP инструменти и шаблони за подканване, позволяващи бърза адаптация към нови домейни или бизнес изисквания.
- **Потребителско изживяване:** Предоставя разговорен интерфейс за потребителите, за да взаимодействат с туристическите агенти, захранван от Azure OpenAI и MCP.

## Архитектура
![Архитектура](https://github.com/Azure-Samples/azure-ai-travel-agents/blob/main/docs/ai-travel-agents-architecture-diagram.png)

### Описание на диаграмата на архитектурата

Решението Azure AI туристически агенти е проектирано за модулност, мащабируемост и сигурна интеграция на множество AI агенти и корпоративни източници на данни. Основните компоненти и потока на данни са както следва:

- **Потребителски интерфейс:** Потребителите взаимодействат със системата чрез разговорен UI (като уеб чат или бот в Teams), който изпраща потребителски запитвания и получава препоръки за пътуване.
- **MCP сървър:** Служи като централен оркестратор, получава потребителски вход, управлява контекста и координира действията на специализирани агенти (напр. FlightAgent, HotelAgent, ItineraryAgent) чрез Model Context Protocol.
- **AI агенти:** Всеки агент отговаря за конкретен домейн (полетите, хотелите, маршрутите) и е реализиран като MCP инструмент. Агенти използват шаблони за подканване и логика, за да обработват заявки и генерират отговори.
- **Azure OpenAI услуга:** Предоставя напреднало разбиране и генериране на естествен език, позволявайки на агентите да интерпретират намерението на потребителя и да генерират разговорни отговори.
- **Azure AI Search и корпоративни данни:** Агенти правят запитвания към Azure AI Search и други корпоративни източници на данни, за да извлекат актуална информация за полети, хотели и опции за пътуване.
- **Удостоверяване и сигурност:** Интегрира се с Microsoft Entra ID за сигурно удостоверяване и прилага контрол на достъпа с най-малко привилегии за всички ресурси.
- **Разгръщане:** Проектирано за разгръщане на Azure Container Apps, осигуряващо мащабируемост, мониторинг и оперативна ефективност.

Тази архитектура позволява безпроблемно оркестриране на множество AI агенти, сигурна интеграция с корпоративни данни и надеждна, разширяема платформа за изграждане на специфични за домейна AI решения.

## Обяснение стъпка по стъпка на диаграмата на архитектурата
Представете си, че планирате голямо пътуване и имате екип от експертни асистенти, които ви помагат с всеки детайл. Системата Azure AI туристически агенти работи по подобен начин, използвайки различни части (като членове на екипа), които всеки има специална задача. Ето как всичко се съчетава:

### Потребителски интерфейс (UI):
Мислете за това като за фронт офис на вашия туристически агент. Това е мястото, където вие (потребителят) задавате въпроси или правите заявки, като например „Намери ми полет до Париж“. Това може да бъде чат прозорец на уебсайт или приложение за съобщения.

### MCP сървър (Координаторът):
MCP сървърът е като мениджър, който слуша вашата заявка на фронт офиса и решава кой специалист трябва да се справи с всяка част. Той следи вашия разговор и гарантира, че всичко върви гладко.

### AI агенти (Специалисти асистенти):
Всеки агент е експерт в конкретна област—един знае всичко за полети, друг за хотели, а друг за планиране на вашия маршрут. Когато поискате пътуване, MCP сървърът изпраща вашата заявка до правилния агент(и). Тези агенти използват своите знания и инструменти, за да намерят най-добрите опции за вас.

### Azure OpenAI услуга (Езиков експерт):
Това е като да имате езиков експерт, който разбира точно какво искате, независимо как го формулирате. Той помага на агентите да разберат вашите заявки и да отговорят на естествен, разговорен език.

### Azure AI Search и корпоративни данни (Информационна библиотека):
Представете си огромна, актуална библиотека с цялата последна информация за пътуване—разписания на полети, наличност на хотели и др. Агенти търсят в тази библиотека, за да получат най-точните отговори за вас.

### Удостоверяване и сигурност (Охрана):
Точно както охрана проверява кой може да влезе в определени области, тази част гарантира, че само упълномощени хора и агенти могат да получат достъп до чувствителна информация. Той пази вашите данни безопасни и поверителни.

### Разгръщане на Azure Container Apps (Сградата):
Всички тези асистенти и инструменти работят заедно вътре в сигурна, мащабируема сграда (облака). Това означава, че системата може да се справи с много потребители наведнъж и винаги е налична, когато ви е нужна.

## Как всичко работи заедно:

Започвате, като задавате въпрос на фронт офиса (UI).
Мениджърът (MCP сървър) разбира кой специалист (агент) трябва да ви помогне.
Специалистът използва езиковия експерт (OpenAI), за да разбере вашата заявка и библиотеката (AI Search), за да намери най-добрия отговор.
Охраната (Удостоверяване) гарантира, че всичко е безопасно.
Всичко това се случва вътре в надеждна, мащабируема сграда (Azure Container Apps), така че вашето изживяване е гладко и безопасно.
Тази работа в екип позволява на системата бързо и безопасно да ви помогне да планирате вашето пътуване, точно като екип от експертни туристически агенти, работещи заедно в модерен офис!

## Техническа реализация
- **MCP сървър:** Хоства основната оркестрационна логика, предоставя инструменти за агенти и управлява контекста за многоетапни работни потоци за планиране на пътувания.
- **Агенти:** Всеки агент (напр. FlightAgent, HotelAgent) е реализиран като MCP инструмент със свои собствени шаблони за подканване и логика.
- **Azure интеграция:** Използва Azure OpenAI за разбиране на естествен език и Azure AI Search за извличане на данни.
- **Сигурност:** Интегрира се с Microsoft Entra ID за удостоверяване и прилага контрол на достъпа с най-малко привилегии за всички ресурси.
- **Разгръщане:** Поддържа разгръщане на Azure Container Apps за мащабируемост и оперативна ефективност.

## Резултати и въздействие
- Демонстрира как MCP може да се използва за оркестриране на множество AI агенти в реален, производствен сценарий.
- Ускорява разработването на решения, като предоставя повторно използваеми модели за координация на агенти, интеграция на данни и сигурно разгръщане.
- Служи като план за изграждане на специфични за домейна, AI-захранвани приложения, използвайки MCP и Azure услуги.

## Препратки
- [Azure AI туристически агенти GitHub хранилище](https://github.com/Azure-Samples/azure-ai-travel-agents)
- [Azure OpenAI услуга](https://azure.microsoft.com/en-us/products/ai-services/openai-service/)
- [Azure AI Search](https://azure.microsoft.com/en-us/products/ai-services/ai-search/)
- [Model Context Protocol (MCP)](https://modelcontextprotocol.io/)

**Отказ от отговорност**: 
Този документ е преведен с помощта на AI услуга за превод [Co-op Translator](https://github.com/Azure/co-op-translator). Въпреки че се стремим към точност, моля, имайте предвид, че автоматизираните преводи може да съдържат грешки или неточности. Оригиналният документ на неговия роден език трябва да се счита за авторитетен източник. За критична информация се препоръчва професионален човешки превод. Не носим отговорност за каквито и да е недоразумения или погрешни интерпретации, произтичащи от използването на този превод.