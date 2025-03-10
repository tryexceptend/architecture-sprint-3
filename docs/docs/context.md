```plantuml
@startuml

!include https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

title «Тёплый дом» Context Diagram

top to bottom direction

Person(user, "Владелец дома", "Пользователь системы")
System_Ext(Hous, "Дом", "Дом пользователя")

System(WarmHouseSystem, "Проект «Тёплый дом»", "Управление отоплением, сбор данных с датчиков")

Rel(user, WarmHouseSystem, "Управление отоплением дома")
Rel(WarmHouseSystem,Hous,"Управление отоплением \n\n Опрос датчиков температуры")
@enduml 
```