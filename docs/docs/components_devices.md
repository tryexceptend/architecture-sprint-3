```plantuml
@startuml
title «Тёплый дом» Микросервисы управления устройствами Component Diagram

' top to bottom direction

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml


Container_Boundary(APIGatewayLayer, "APIGateway") {
  Container(APIGateway, "API Gateway", "Kong, Consul", "Маршрутизатор пользовательских запросов")
}
Container(DevicesServices, "Микросервисы управления устройствами","Для каждого типа устройства лучше реализовать отдельный микросервис") {
  Component(DevicesCommandController, "Devices Command Controller", "Node.js","Контроллер команд управления устройствами")
  Component(DevicesReaderController, "Devices Reader Controller", "Node.js","Чтение данных по устройствам")
  Component(DevicesService, "Devices Service", "Node.js","Сервис обработки логики работы с устройствами")
  
}
ContainerDb(DevicesServiceDb, "Database", "PostgreSQL", "Исторические данные показания устройств"){
    Component(DevicesServiceDbMaster, "Master DB Replic", "PostgreSQL","Мастер реплика БД. Только операции записи")
    Component(DevicesServiceDbSlave, "Slave DB Replic", "ostgreSQL","Слейв реплика БД. Только операции чтения")

    Rel(DevicesServiceDbMaster,DevicesServiceDbSlave,"Репликация данных")
  }
  
  Rel(DevicesService,DevicesServiceDbMaster,"Запись данных, полученных от устройств")
  Rel(DevicesService,DevicesServiceDbSlave,"Чтение данных")
  
  Rel(DevicesCommandController,DevicesServiceDbMaster,"Запись команд управления")
  Rel(DevicesReaderController,DevicesServiceDbSlave,"Чтение данных")
'  Rel(DevicesReaderController,DevicesCommandController,"Чтение данных с устройств")
  Rel(DevicesService,DevicesCommandController,"Чтение данных с устройств")
  
  Rel(APIGateway,DevicesCommandController,"Команды управления устройствами\n[json]")
  Rel(APIGateway,DevicesReaderController,"Запросы состояния усторойств\n[json]")

Container(Observation, "Микросервис управления наблюдением") {
  Component(ObservationCommandController, "Observation Command Controller", "Node.js","Контроллер команд управления видеонаблюдением")
  Component(ObservationReaderController, "Observation Reader Controller", "Node.js","Получение данных по видеонаблюдению\nСтрим с видеокамер")
  Component(ObservationService, "Observation Service", "Node.js","Сервис логики видеонаблюдения\nНастройки подключения к системам видеонаблюдения")
}
ContainerDb(ObservationServiceDb, "Database", "PostgreSQL", "Параметры подключения \nк сервисам видеонаблюдения"){
    Component(ObservationServiceDbMaster, "Master DB Replic", "PostgreSQL","Мастер реплика БД. Только операции записи")
    Component(ObservationServiceDbSlave, "Slave DB Replic", "ostgreSQL","Слейв реплика БД. Тольок операции чтения")

    Rel(ObservationServiceDbMaster,ObservationServiceDbSlave,"Репликация данных")
  }
  Rel(ObservationService,ObservationServiceDbMaster,"Запись данных")
  Rel(ObservationService,ObservationServiceDbSlave,"Чтение данных")
  
  Rel(ObservationCommandController,ObservationServiceDbMaster,"Запись команд управления")
  Rel(ObservationReaderController,ObservationServiceDbSlave,"Чтение данных\nпо сервисам видеонаблюдения")
  
  Rel(APIGateway,ObservationCommandController,"Команды управления видеонаблюдением")
  Rel(APIGateway,ObservationReaderController,"Запросы состояния видеонаблюдения")

System_Ext(house, "Дом пользователя", "API приборов и датчиков, доступные по сети")
System_Ext(ObservationHouse, "Сторонние системы видеонаблюдения", "Стриминговые сервисы видеокамер")

Rel(APIGateway,DevicesService,"Запросы состояния светильников\nКоманды управления освещением\n[json]")
Rel(APIGateway,ObservationService,"Запросы подключения к сервису видеонаблюдения")

Rel(DevicesCommandController,house,"Управление отоплением\nОпрос датчиков\n[Протокол]")

Rel(ObservationCommandController,ObservationHouse,"Получение видео с камер наблюдения\n[Стрим видео]")

@enduml
```