```plantuml
@startuml
title «Тёплый дом» Container Diagram

top to bottom direction
' left to right direction

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

Person(user, "User", "Пользователь, подключивший дом к системе")

System(WarmHouseSystem, "Проект «Тёплый дом»", "System managing memberships, schedules, and payments")

Container_Boundary(WarmHouseSystem, "Проект «Тёплый дом»") {
  Container(WebApp, "Web Application", "Java, Spring", "Handles user interactions")
  Container(MobileApp, "Mobile Application", "Kotlin, Swift", "Allows users to manage their membership")
  
  Container(APIGateway, "API Gateway", "Kong, Consul", "Авторизация пользователей, распределение нагрузки, маршрутизация, регистрация сервисов")
rectangle {
 Container_Boundary(HouseControlMicroservice, "Сервис управления домом") {
    Container(HouseControlService, "HouseControl Service", "Node.js", "Обработка команд на управление устройствами")
    ContainerDb(HouseControlDatabase, "Database", "PostgreSQL", "Данные пользователей, настройки комнат, панель управления")
    Rel(HouseControlService,HouseControlDatabase,"Чтение/запись данных пользователей")
  }

  Container_Boundary(AutomationMicroservice, "Сервис сценариев автоматизации") {
    Container(AutomationService, "Automation Service", "Node.js", "Сценарии автоматизации пользователей")
    ContainerDb(AutomationDatabase, "Database", "MongoDb", "Параметры и конфигурации датчиков \n Исторические данные")
    Rel(AutomationService,AutomationDatabase,"Чтение/Запись данных сценариев")
  }

  Container_Boundary(DeviceMicroservices, "Сервисы работы с устройствами") { 
    Container(DeviceMicroservice, "Devices Services", "Node.js", "Микросервисы работы с устройствами")
    ContainerDb(DeviceDatabase, "Database", "MongoDB", "Параметры и конфигурации датчиков, исторические данные")
   Rel(DeviceMicroservice,DeviceDatabase,"Чтение/Запись параметров устройств и показания датчиков")
  }
}
}

System_Ext(house, "Дом пользователя", "API приборов и датчиков, доступные по сети")

Rel(user, WebApp, "Использование веб сайта")
Rel(user, MobileApp, "Использование приложения")

Rel(WebApp,APIGateway,"Запросы к API")
Rel(MobileApp,APIGateway,"Запросы к API")

Rel(DeviceMicroservice,APIGateway,"Регистрация микросервисов")
Rel(HouseControlService,APIGateway,"Регистрация микросервисов")
Rel(AutomationService,APIGateway,"Регистрация микросервисов")

Rel(APIGateway,HouseControlService,"Запросы управления домом")
Rel(APIGateway,AutomationService,"Запросы управления сценариями автоматизации")

Rel(AutomationService,DeviceMicroservice,"Команды на управление устройствами")
Rel(HouseControlService,DeviceMicroservice,"Команды на управление устройствами")

Rel(DeviceMicroservice,house,"Управление датчиками \n Сбор показаний датчиков")

@enduml
```