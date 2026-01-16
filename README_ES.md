# ZoqueLabs —  Snapshots de infraestructura de amenazas de LATAM

Este repositorio contiene artefactos generados a partir de snapshots periódicos 
de observación de actividad de servidores de comando y control (C2) e infraestructura maliciosa
en América Latina.

El objetivo es **mapeo de infraestructura**, no atribución:
recopilación de bajo ruido, análisis basado en snapshots y deltas reproducibles
con el tiempo.

---

## Qué es esto

- Una **vista basada en snapshots** de infraestructura de red maliciosa / sospechosa
- Centrado en **América Latina**
- Construido para soportar:
  - investigación en inteligencia de amenazas
  - análisis longitudinal (infra reutilización, agitación, deriva)
  - futura ingestión en plataformas TI (MISP / STIX / Colander)

Cada snapshot se trata como un *momento en el tiempo*, no como telemetría en vivo.

---

## Datos de entrada (snapshot JSON)

Cada snapshot es un mapeo JSON de direcciones IP a metadatos observados:

```json
{
  "XXXX": {
    "threat_names": ["Cobalt Strike", "Havoc"],
    "puertos": [443, 31337],
    "asn": "AS16509",
    "org": "Servicios de datos de Amazon",
    "isp": "Amazon.com, Inc.",
    "país": "Brasil",
    "ciudad": "São Paulo",
    "last_scan": "2026-01-02_16-19-23",
    "fuente": "censys"
  }
}
````

Notas:

* Una IP puede estar asociada con **múltiples amenazas**
* Los puertos representan **puertos abiertos observados en el momento del escaneo**
* Las cadenas Geo, ASN, ISP y org se conservan *como se observa* (sin normalización forzada)

---

## Salidas generadas

La ejecución del script de informe produce los siguientes archivos:

### `report.md`

Informe técnico legible por humanos, que incluye:

* Metadatos de snapshots
* Métricas globales (IP, puertos, amenazas, países, ASN…)
* **Listas Top-N** 
* **Gráficos Mermaid**:

  * Distribución de amenazas
  * Distribuciones de país / ASN/ISP
  * País → Amenaza y ASN → Relaciones de amenaza
* **Sección delta** (si se proporcionó un snapshot anterior):

  * IP nuevas / eliminadas
  * Marcos de malware nuevos / eliminados
  * Nuevos países, ASN, ISP, puertos
  * Reutilización de IP y deriva de amenazas
  * Gráficos Sankey exclusivos de Delta
* **Tabla completa de todas las IP** en el snapshot actual (en la parte inferior)

El informe está diseñado para poder verse directamente en
Editores de GitHub, GitLab o Markdown compatibles con Mermaid.

---

### `summary.json`

Resumen legible por máquinas:

* Marca de tiempo de snapshot
* Métricas básicas
* Valores Top-N para amenazas, países, ASN, ISP, puertos
* Información delta (cuando se proporciona la snapshot anterior)
* Reutilización de IP / detalles de deriva de amenazas

Este archivo es adecuado para:

* paneles de control
* procesamiento automatizado adicional
* scripts posteriores

---

### `conjunto de datos.csv`

Conjunto de datos plano (una fila por IP), adecuado para hojas de cálculo o filtrado rápido:

Columnas:

* `ip`
* `país`
* `ciudad`
* `asn`
* `isp`
* `org`
* `puertos` (separados por punto y coma)
* `threat_names` (separados por punto y coma)
* `fuente`
* `último_escaneo`

---

### `stix2_bundle.json`

Exportación mínima del paquete **STIX 2.1**:

* Un objeto `identity` (organización)
* Un `indicador` por IP

  * Patrón: `[ipv4-addr:value = 'XXXX']`
  * Las etiquetas incluyen `c2`, `threat-infra` y `malware:<name>`

---

### `misp_event.json`

Exportación mínima **EVento MISP**:

* Evento único
* Un atributo `ip-dst` por IP
* Atributos `port` opcionales
* Nombres de malware expuestos como etiquetas (`malware:<name>`)
* Metadatos contextuales (país, ASN, ISP, fuente) almacenados como comentarios

---

## Comparación de snapshots (lógica delta)

Cuando se proporciona una snapshot anterior, el informe calcula:

* **Nuevas IP** y **IP eliminadas**
* **IP persistentes**
* **Marcos de amenazas nuevos y eliminados**
* **Nuevos países, ASN, ISP, puertos**
* **Reutilización de IP / deriva de amenazas**

### Definición de reutilización de IP

Una IP se considera reutilizada cuando:

> La misma IP aparece en ambos snapshots **pero con un conjunto diferente de frameworks de amenazas asociados**.

Esto captura:

* reutilización de infraestructura
* rotación de herramientas
* cambios en la puesta en escena del operador

**no** implica atribución.

---

## Lo que esto *no* es

* No es un sistema de monitoreo en tiempo real
* No es un escáner
* No es un motor de atribución

---

## Uso previsto

* Informes técnicos
* Análisis longitudinal de TI
* Publicaciones de investigación
* Laboratorios de amenazas de la sociedad civil
* Fortalecimiento de capacidades en la región

---

## Licencia / uso

A menos que se indique lo contrario:

* Los datos son observacionales
* Interpretar cuidadosamente
* Evite reclamar en exceso
* Citar responsablemente

ZoqueLabs
