# Sistema Gestión Robots de Servicio v1.2

> Recuperatorio del Primer Parcial de **Programación II**, desarrollado en **Java** con Programación Orientada a Objetos.
> Tecnicatura Universitaria en Programación – UTN Avellaneda.

![Java](https://img.shields.io/badge/Java-17%2B-orange)
![POO](https://img.shields.io/badge/Paradigma-POO-blue)
![Estado](https://img.shields.io/badge/Estado-Entregado-success)

---

## Datos académicos

| | |
|---|---|
| **Materia** | Programación II |
| **Instancia** | Recuperatorio – Primer Parcial |
| **Cuatrimestre** | 2º Cuatrimestre 2026 |
| **División** | 322-2 |
| **Docentes** | Baus / Bustos |
| **Alumno** | Papetti, Fabricio |

---

## Descripción

La empresa **TecnoAsistencia** administra una flota de robots que realiza distintas tareas en edificios
inteligentes. El sistema registra robots de **limpieza**, **vigilancia** y **reparto**, calcula el consumo
de batería de cada uno, ejecuta servicios y genera diagnósticos.

Todos los robots tienen número de serie, fabricante, prioridad de servicio (`BAJA`, `MEDIA`, `ALTA`) y
nivel de batería (entero entre 0 y 100). Cada tipo agrega un atributo propio, siempre entero y mayor que cero.

---

## Reglas de negocio

| Tipo de robot | Atributo propio | Consumo por operación (puntos porcentuales) |
|---|---|---|
| Limpieza | superficie (m²) | `superficie / 10 + 5` (división entera) |
| Vigilancia | cantidad de cámaras | `cámaras × 4 + 6` |
| Reparto | capacidad de carga (kg) | `capacidad × 3 + 4` |

- Calcular el consumo **no modifica** la batería.
- **Limpieza y reparto** pueden ejecutar servicios. Si la batería es insuficiente, se lanza
  `IllegalStateException` y la batería **no cambia**.
- **Vigilancia y reparto** pueden generar diagnósticos sin modificar su estado.
- Dos robots son **iguales** si coinciden en número de serie y fabricante, sin importar tipo, prioridad
  ni otros atributos.

---

## Funcionalidades

- [x] Agregar un robot a la flota; si ya existe uno igual, lanza `RobotDuplicadoException`.
- [x] Obtener los robots registrados sin exponer la colección interna (se devuelve una copia).
- [x] Filtrar por prioridad de servicio.
- [x] Obtener los robots cuyo consumo por operación sea menor o igual a un máximo indicado.
- [x] Mostrar consumos, ejecutar servicios y generar diagnósticos desde el programa principal.
- [x] Manejo de falta de batería por robot, sin interrumpir el recorrido.

---

## Diseño

### Diagrama de clases

El diagrama completo, realizado con **UMLetino**, se encuentra en [`/docs`](docs/).

![Diagrama de clases](docs/diagrama-clases.png)

### Jerarquía

```
                 «interface»              «interface»
                 Ejecutable               Diagnosticable
                     ▲  ▲                    ▲  ▲
                     ┆  ┆                    ┆  ┆
        ┌────────────┘  └──────┐      ┌──────┘  └────────────┐
        ┆                      ┆      ┆                      ┆
 RobotLimpieza            RobotReparto               RobotVigilancia
        │                      │                           │
        └──────────────────────┼───────────────────────────┘
                               ▼
                       Robot (abstracta)
```

### Conceptos de POO aplicados

| Concepto | Aplicación |
|---|---|
| **Herencia** | `Robot` abstracta con los atributos comunes; una subclase por tipo. |
| **Polimorfismo** | `calcularConsumo()` se implementa en cada subclase y se usa desde `Robot`. |
| **Interfaces** | `Ejecutable` y `Diagnosticable` modelan capacidades repartidas entre distintos tipos. |
| **Enumerado** | `Prioridad` (`BAJA`, `MEDIA`, `ALTA`). |
| **Excepciones** | `RobotDuplicadoException` (chequeada) e `IllegalStateException` por batería insuficiente. |
| **Encapsulamiento** | Atributos `private`, getters y validaciones centralizadas. |
| **Composición** | `FlotaRobots` es dueña de su lista de robots. |

### Decisiones de diseño

- `Robot` es **abstracta** porque el consumo depende del tipo y no tiene sentido crear un robot genérico.
- **Interfaces** para las capacidades, porque se reparten de forma cruzada (reparto ejecuta y diagnostica).
- **Constantes `static final`** en lugar de números sueltos en los cálculos de consumo.
- **Validaciones en métodos privados** (`validarTexto`, `validarPositivo`, `validarBateria`,
  `validarPrioridad`, `validarNombre`) para mantener los constructores limpios.
- **`consumirBateria()`** verifica antes de restar, así la batería queda intacta si el servicio falla.
- **Copia defensiva** en `obtenerRobots()` y verificación con `contains` antes de agregar.
- Se utilizó únicamente código visto en la cátedra.

---

## Estructura del proyecto

```
Recuperatorio-1erParcialProgramacion-II-Papetti-Fabricio/
├── src/
│   └── sistemagestionrobotsdeservicio/
│       ├── SistemaGestionRobotsDeServicio.java
│       ├── Robot.java
│       ├── RobotLimpieza.java
│       ├── RobotVigilancia.java
│       ├── RobotReparto.java
│       ├── Ejecutable.java
│       ├── Diagnosticable.java
│       ├── Prioridad.java
│       ├── FlotaRobots.java
│       └── RobotDuplicadoException.java
└── README.md
```

---

## Cómo ejecutar

### Requisitos

- JDK 17 o superior (compatible con 11+)
- NetBeans, IntelliJ IDEA, Eclipse o cualquier terminal

### Desde NetBeans

1. **File → Open Project** y seleccionar la carpeta del proyecto.
2. Clic derecho sobre el proyecto → **Clean and Build**.
3. Ejecutar `Main.java` con **Run File** (`Shift + F6`).

### Desde la terminal

```bash
# Compilar
javac -d out src/tecnoasistencia/*.java

# Ejecutar
java -cp out tecnoasistencia.Main
```

---

## Salida esperada

```
No se pudo agregar: Ya existe un robot con serie RL-10 y fabricante Nova.
Robots registrados:
RobotLimpieza [serie=RL-10, fabricante=Nova, prioridad=ALTA, bateria=60, superficie=80]
RobotVigilancia [serie=RV-20, fabricante=Orbis, prioridad=MEDIA, bateria=75, camaras=3]
RobotReparto [serie=RR-30, fabricante=Nova, prioridad=BAJA, bateria=40, carga=5]
RobotReparto [serie=RR-40, fabricante=Orbis, prioridad=ALTA, bateria=15, carga=4]

Ejecución de servicios:
Servicio de limpieza RL-10 ejecutado. Bateria restante: 47%.
El robot RV-20 no ejecuta servicios.
Servicio de reparto RR-30 ejecutado. Bateria restante: 21%.
No se pudo ejecutar RR-40: Bateria insuficiente.

Diagnósticos:
El robot RL-10 no genera diagnostico.
Diagnostico de vigilancia RV-20: 3 camaras operativas.
Diagnostico de reparto RR-30: capacidad de 5 kg.
Diagnostico de reparto RR-40: capacidad de 4 kg.

Consumos actuales:
RL-10: 13 puntos de bateria por operacion.
RV-20: 18 puntos de bateria por operacion.
RR-30: 19 puntos de bateria por operacion.
RR-40: 16 puntos de bateria por operacion.
```

---

## Documentación

El código está documentado con **Javadoc** en las clases y los métodos principales.
Para generar el HTML:

```bash
javadoc -d docs/javadoc -sourcepath src tecnoasistencia
```

---

## Autor

**Fabricio Papetti**
Tecnicatura Universitaria en Programación – UTN Avellaneda
