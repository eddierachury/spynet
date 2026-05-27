# Diagrama UML de Clases — Spynet (estado actual del repositorio)

Este archivo está listo para **copiar y pegar** en un generador compatible con **PlantUML** (por ejemplo, Lucidchart usando importación de texto/PlantUML).

## Código UML (copia desde `@startuml` hasta `@enduml`)

```plantuml
@startuml
skinparam classAttributeIconSize 0
title Spynet - Diagrama UML de Clases (actual)

package "spynet" {

  class Analyzer {
    - whois: WhoisService
    - dns: DnsRecordService
    - geo: GeoService
    - wayback: WaybackService
    + __init__()
    + analyze(url: str, depth_data: bool = false): dict
  }

  package "services" {

    class WhoisService {
      + get_whois(url: str, all_data: bool = false): dict
    }

    class DnsRecordService {
      + get_dns_records(url: str): dict
    }

    class GeoService {
      - api_endpoint: str
      + __init__()
      + get_geoinfo(url: str, all_data: bool = false): dict
    }

    class WaybackService {
      - cdx_api: str
      - headers: dict
      + __init__()
      + get_count(domain: str): int
      + get_snapshots(domain: str): list
      + get_wayback(url: str): dict
    }
  }
}

' ==== Dependencias externas (librerías/servicios) ====
class "whois (python-whois)" as WhoIsLib <<library>>
class "dns.resolver (dnspython)" as DnsResolver <<library>>
class "requests" as Requests <<library>>
class "datetime/timezone" as DateTime <<library>>
class "ip-api.com" as IpApi <<external API>>
class "web.archive.org CDX API" as WaybackApi <<external API>>

' ==== Relaciones principales ====
Analyzer *-- WhoisService : compone
Analyzer *-- DnsRecordService : compone
Analyzer *-- GeoService : compone
Analyzer *-- WaybackService : compone

WhoisService ..> WhoIsLib : usa
WhoisService ..> DateTime : usa
DnsRecordService ..> DnsResolver : usa
DnsRecordService ..> WhoIsLib : extract_domain()
GeoService ..> Requests : usa
GeoService ..> WhoIsLib : extract_domain()
WaybackService ..> Requests : usa
WaybackService ..> WhoIsLib : extract_domain()

GeoService ..> IpApi : GET /json/{domain}
WaybackService ..> WaybackApi : GET /cdx/search/cdx

note right of Analyzer
  Patrón: Facade
  Orquesta los servicios de análisis.
end note

note bottom of GeoService
  Si all_data = true, retorna
  todo el JSON del proveedor.
end note

note bottom of WhoisService
  Calcula edad de dominio en años
  con creation_date.
end note

note bottom of WaybackService
  Devuelve total de snapshots
  y últimos snapshots (límite 5).
end note

@enduml
```

## Cobertura incluida en este diagrama

- Clases implementadas actualmente en el código Python.
- Atributos y métodos públicos/privados observables en el repositorio.
- Dependencias de librerías (`python-whois`, `requests`, `dnspython`).
- Dependencias de APIs externas (IP-API y Wayback CDX API).
- Relaciones de composición y uso.

## Sugerencia de uso en Lucidchart

1. Crear un diagrama nuevo.
2. Usar la opción de importar/pegar PlantUML (si está habilitada en tu plan/integración).
3. Pegar exactamente el bloque entre `@startuml` y `@enduml`.
4. Ajustar estilo visual (colores, fuentes y distribución) sin cambiar estructura.
