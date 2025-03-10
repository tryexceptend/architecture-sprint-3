```plantuml
@startuml
title FitLife Web Application Component Diagram

top to bottom direction

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

Container_Boundary(WLAN, "WLAN") {
  Container(WebApp, "Web Application", "Java", "Пользовательский интерфейс")
  Container(MobileApp, "Mobile Application", "Kotlin", "Пользовательский интерфейс")
}

Container(APIGatewayLayer, "Инфраструктурный слой", "Consul, Kong") {
  Component(ServiceDiscovery, "Service Discovery", "Consul","Автоматическая регистрация микросервисов")
  Component(APIGateway, "API Gateway", "Kong", "Авторизация пользователей.\nМаршрутизация запросов к микросервисам")
}

Container_Boundary(MicroservicesContainer, "Микросервисы системы") {
  Container(HouseControlService, "Микросервис управления домом", "Node.js", "Обработка команд на управление устройствами")
  Container(AutomationMicroservice, "Сервис сценариев автоматизации", "Node.js",  "Конструирование и реализация сценариев автоматизации пользователей")
  
}

Rel(WebApp,APIGateway,"Запросы к системе\n[json,jwt]")
Rel(MobileApp,APIGateway,"Запросы к системе\n[json,jwt]")

Rel(APIGateway,ServiceDiscovery,"Полученее адресов микросервисов")

Rel(APIGateway,HouseControlService,"Команды управления устройствами дома\n[json]")
Rel(APIGateway,AutomationMicroservice,"Конструирование сценариев автоматизации\nУправление сценариями\n[json,xlm]")
@enduml
```