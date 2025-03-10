```plantuml
@startuml
title «Тёплый дом» Микросервис управления домом Component Diagram

top to bottom direction

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

Container_Boundary(APIGAtewayLayer, "Инфраструктурный слой") {
  Container(APIGateway, "API Gateway", "Kong", "Маршрутизатор пользовательских запросов")
}
Container_Boundary(DevicesMicroservices, "Микросервисы управления устройствами") {
  Container(DevicesService, "Devices Service", "Node.js","Сервисы управления устройствами")
  Container(ObservationService, "Observation Service", "Node.js","Сервис наблюдения\nНастройки подключения к системам видеонаблюдения")
}
Container(HouseMicroservice, "Микросервис управления домом", "Node.js") {
  Component(UserController, "User Controller", "Node.js","Контроллер управляет пользовательскими данными")
  Component(RoomsController, "Rooms Controller", "Node.js","Контроллер отдает состояние устройств, распределенные по комнатам\nУправление устройствами")
}
ContainerDb(HouseMicroserviceDb, "Database", "PostgreSQL", "Данные пользователей\nПараметры комнат"){
  Component(HouseMicroserviceDbMaster, "Master DB Replic", "PostgreSQL","Мастер реплика БД. Только операции записи")
    Component(HouseMicroserviceDbSlave, "Slave DB Replic", "ostgreSQL","Слейв реплика БД. Тольок операции чтения")

    Rel(HouseMicroserviceDbMaster,HouseMicroserviceDbSlave,"Репликация данных")
  }
Rel(APIGateway,UserController,"Запросы управление данными пользователей\n[json]")
Rel(APIGateway,RoomsController,"Запросы управления устройствами в комнате от пользователей\n[json]")
Rel(RoomsController,DevicesService,"Команды управления устройствами\n[json]")
Rel(APIGateway,ObservationService,"Стрим с видеокамер сторонних систем видеонаблюдения\n[Стрим видео]")

Rel(RoomsController,HouseMicroserviceDbMaster,"Запись данных")
Rel(RoomsController,HouseMicroserviceDbSlave,"Чтение данных")
Rel(UserController,HouseMicroserviceDbMaster,"Запись данных")
Rel(UserController,HouseMicroserviceDbSlave,"Чтение данных")
@enduml
```