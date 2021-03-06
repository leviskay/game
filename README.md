﻿# game 

## Цель: Разработать каркас высокопроизводительного сервера приложений (желательно на  используемых на данный момент технологиях Node.js+javascript/TypeScript) 

##Текущее программно-аппаратное окружение: 

###Backend:

 - Оборудование: VDS (вероятно 5%, SSD, XXX Gb RAM)
 - СУБД: MongoDB на отдельном сервере

 - Сервер прилождений: Node.js
 - Архитектура приложения: 
     - при каждом обращении выполняются запросы к MongoDB с возвращением полноценных объектов и всех с ними связанных обеъктов
     - не уточнено - кэшируется ли пул подключений к MongoDB
 - ORM: Mongoose

 - Серверный кэш: не используется
 - CDN: не используется

 - Среда разработки: Visual Studio Code ????
 - Язык программирования на сервере: JavaScript (возможно немного TypeScript)
 
 - unit-тестирование: используемая технология не обсуждалась
 - отладка приложения: используемая технология не обсуждалась

###Frontend:
  - Браузер (современный, поддержка IE 11 и ниже не требуется)
  - Frontend Framework: пока не анализиурем

##Предлагаемые изменения + Тестовая платформа 1 (Windows): 
----------------------------------------------
 - Оборудование: Core i5, 4 ГГц, SSD, 64GB RAM
 - СУБД: PostgreSQL на отдельном виртуальном сервере Linux (виртуализация Hyper-V)

 - Cервер прилождений: Node.js
 - DB-провайдер для доступа из Node.js: pg-promise
            - TypeScript-библиотека
            - чтение/запись данных PostgreSQL
            - сама управляет пулом подключений (открывает и закрывает при необходимости)
            - предоставляет доступ к параллельному выполнению серии запросов и возвращению разом нескольких результатов одним запросом к БД: array of array
            - есть документация, активно развивается, много примеров использования (много статей на Stack Owerflow)
            - есть инструментарий мониторинга всех запросов - консоль pg-monitor 

 - Архитектура приложения: 
         - Используем DTO-объекты для передачи на клиента 
        
         - Используем модели для получения данных в нужном формате (DTO-объектов и их коллекций): modelUser, modelTournaments, ... - с ними сервер приложений работать будет (так скрываем внутреннюю реализацию)
            - модели представлены интерфейсами, чтобы подменять разные реализации и не переписывать, а уже реализации моделей на разные варианты баз данных, кэшей и др. можно подменять не меняя код программы, т.к. вся программа работает с интернйсами
               Пример: экспортиуем модель пользователей:
                     Код: export const modelUser: IModelUser = new ModelUserPostgre();
                   
                     Расшифровка:
                        - есть интерйес модели пользователей IModelUser - его программа и видит 
                        - также есть класс ModelUserPostgre, реализующий интерфейс IModelUser 
                        - мы экспортируем константу modelUser: IModelUser (везде в прграмме через нее и работаем с моделью пользователей, в то же время неэкспортируемый класс ModelUserPostgre предоставляет реализацию на PostgreSQL)
                     
         - Используем провайдеры для доступа к данным (чтобы можно было подменять реализации простым присвоением реализации модели к интерфейсу)
         - Весь код строго-типизированный на TypeSctipt (объектная модель, желательно избегать pure javascript и объектов типа any)

 - ORM: самописный ОРМ
       Цель использования самописного: 
            - полный контроль над бэкэндом
                - для запроса из БД только нужных значений и заполнения DTO-объектов именно с тем наполнением, которое нам нужно в каждом конкретном случае)
                    - самое гланое: оптимизированные сложные и КОНТРОЛИРУЕМЫЕ разработчиком SQL-запросы из нескольких таблиц одним запросом собирающие данные и возвращающие кортежи данны + возможность их кэширования
                - возможностиь самим выбирать  способ сериализации (обычный json + HTTP - понятно, но больший объем и медлненнее, Google protobuf + WebSocket - бинарный формат и отсуствие накладных расходов на HTTP-протокол и др. )
                - мониторинг производительности

 - серверный кэш: Redis 
         - назначение: кэширование всего на определенное время (от результатов запросов к БД до картинок, любых других ресурсов)
         - взят для примера (можно потом заменить  на аналогичный)
         - тесты производительности запросов к кэшу - 40-50 тыс.запросов в секунду при нагрузке от 50 пользователей - есть утилита тестирования производительности (вместе с самим redis поставляется)
         - как работает: размещается в памяти, может быть на отдельной машине, соединение по tcp или unix-socket'у. 
         - при разрастании кэша сбрасывает его на диск не часто используемые куски и подгружает обратно при запросах...

 - CDN: не используется (пока Redis планиурется протестировать на эту роль => для ускорения доступа к ресурсам)

 - Язык программирования на сервере: TypeScript
 - Среда разработки: Visual Studio 2019 Community 
       + JetBrains Resharper (удобно писать код с ним - много инструментов рефакторинга)(с установленной рабочей нагрузкой [Разработка Node.js])


##Тестовая платформа 2:  
  - пока не развернута






