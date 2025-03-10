```plantuml
@startuml

title FitLife Membership Management Code Diagram

top to bottom direction

!includeurl https://raw.githubusercontent.com/RicardoNiepel/C4-PlantUML/master/C4_Component.puml

class User {
  +String name
  +String email
  +List<Hous> houses
  +void register()
  +void login()
}

class Hous {
  +String address
  +List<Room> rooms
}

class Room {
  +String name
  +List<Device> devices
}

class Device {
  +String name
  +String state
  +void init()
  +Byte[] getValue()
  +Integer getValueAsInt()
  +Float getValueAsFloat()
}

User "1" -- "0..*" Hous : владеет
Hous "1" -- "0..*" Room : имеет
Room "1" -- "0..*" Device : установлены

class Script {
  +String name
  +List<Device> devices
  +List<ScriptStep> steps
  +void apply(List<Device> devices)
}

Script "1" -- "0..*" Device : использует
Script "1" -- "0..*" ScriptStep : содержит

class ScriptStep {
  +String name
  +String[] stepCode
  +void applyStep(List<Device> devices)
}

@enduml
``` 