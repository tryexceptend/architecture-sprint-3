```plantuml
@startuml
title «Тёплый дом» Микросервис сценариев автоматизации Component Diagram

top to bottom direction

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

Container_Boundary(APIGAtewayLayer, "Инфраструктурный слой") {
  Container(APIGateway, "API Gateway", "Kong,Consul", "Маршрутизатор пользовательских запросов")
}
Container_Boundary(DevicesServices, "Микросервисы управления устройствами") {
  Container(DevicesService, "Devices Service", "Node.js","Сервис управления устройствами")
}
Container(AutomationServices, "Микросервисы", "Node.js") {
  Component(AutomationEditorController, "Automation Editor Controller", "Node.js","Редактор сценариев автоматизации")
  Component(AutomationService, "Automation Service", "Node.js","Сервис выполнения сценариев автоматизации")
}

ContainerDb(AutomationServicesDb, "Database", "MongoDB", "Сценарии автоматизации пользователей\nКоманды управления в рамках сценариев"){
  Component(AutomationServicesDbMaster, "Master DB Replic", "PostgreSQL","Мастер реплика БД. Только операции записи")
    Component(AutomationServicesDbSlave, "Slave DB Replic", "ostgreSQL","Слейв реплика БД. Тольок операции чтения")

    Rel(AutomationServicesDbMaster,AutomationServicesDbSlave,"Репликация данных")
  }

Rel(APIGateway,AutomationEditorController,"Запросы редактирования сценариев пользователей\n[json,xml]")
Rel(AutomationService,DevicesService,"Команды управления устройствами\n[json]")

Rel(AutomationEditorController,AutomationServicesDbSlave,"Чтение данных")
Rel(AutomationEditorController,AutomationServicesDbMaster,"Запись данных")
Rel(AutomationService,AutomationServicesDbSlave,"Чтение данных")
Rel(AutomationService,AutomationServicesDbMaster,"Запись данных")

@enduml
```