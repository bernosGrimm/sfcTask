# Embedded Software Enginieer Aufgaben zur Vorstellung

## Aufgabe 1

```plantuml
@startuml
database htank [
    
    
    Wasserstoffspeicher 


]
node bzelle [
    Brennstoffzelle
]
node Laderegler [
    Laderegler
    ----
    CC-CV Verfahren
    Überwacht das Laden mit 
    konstanten Strom und 
    konstanter Spannung
]
node Batterie
node Steuereinheit [
    Steuereinheit
    ----
    Mit Notstromversorgung
]


htank = bzelle :Wasserstoff
bzelle - Laderegler :Strom
Laderegler - Batterie :Ladestrom
Laderegler -- Steuereinheit #red;text:red :Überwachung Eingangsstrom\nund Ladeparameter
htank -- Steuereinheit #blue;text:blue : Füllstand
bzelle -- Steuereinheit #green;text:green :Temperatur
Batterie -- Steuereinheit #lightgreen;text:lightgreen :Ladezustandt\nTemperatur

@enduml
```

Das System sollte mindestens aus einem Wasserstoffspeicher, einer Brennstoffzelle, einem Laderegler, einer Steuereinheit und einer Batterieanlage in geeigneter größe bestehen.

Um die Sicherheit im System zu gewährleisten überwacht die Steuereinheit, parameter wie den Füllstand des Wasserstoffspeichers, die Temperatur der Brennstoffzelle, Ladeparameter und Batterietemperatur und Ladestand.
Zudem ist die Steuereinheit mit ihrem eigenem Notstromspeicher ausgestattet um bei Fehlersituationen das System Abschalten zu können.

## Aufgabe 2

```plantuml
@startuml
skinparam componentStyle rectangle

component Zusatzmodul{
    component AnalogPorts{
        component Port1
        component Port2
        component Port3
        component Port4
        Port1 -[hidden]-> Port2
        Port2 -[hidden]-> Port3
        Port3 -[hidden]-> Port4
    }
    component CPU{
    
    }
    Component CAN
    AnalogPorts --> CPU
    CPU <-> CAN
}
@enduml
```

```plantuml
@startuml
package Zusatzmodul {
start 

:load analog port config from NVM;
if(validate config) then (no)
:set config to NOT_ACTIVE;
:send status over CAN: ports NOT_ACTIVE;
else (yes)
endif
:set port config;

repeat :read analog data;
:send data over CAN;
if (Interrupt:\nnew config over CAN) then (yes)

:get new config;

if(validate new config) then (yes)
:save new config to NVM;
:set new port config;
:send new status over CAN;
else (no)
:send error message;
endif
endif

repeat while (True)

}
@enduml
```

```plantuml
@startuml
left to right direction
class PortStructure{
uint8 portID
..
enum PortType
}

enum PortType
{
    NOT_ACTIVE
    ..
    VOLTAGE
    ..
    CURRENT
    ..
    TEMPERATURE
}

PortStructure::PortType *-- PortType

@enduml
```


Dieser Vorschlag gilt für eine baremetal singlecore implementierung. Hierbei ist zu beachten das während der neu Konfiguration keine Daten über den CAN-bus verschickt werden können. Dieser Usecase ist aber sehr unwarscheinlich da die neu Konfiguration eine Hardwareänderung zur Grundlage hat.
