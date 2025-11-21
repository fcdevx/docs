# Manual de Usuario: Sistema de Gestión de Servicios

## Índice
1. [Creación de Servicios](#1-creación-de-servicios)
2. [Generación Automática Diaria de Horarios](#2-generación-automática-diaria-de-horarios)
3. [Limpieza Automática de Horarios Expirados](#3-limpieza-automática-de-horarios-expirados)
4. [Actualización de Servicios](#4-actualización-de-servicios)
5. [Actualización de Precios Base](#5-actualización-de-precios-base)
6. [Actualización de Precios Especiales por Fecha](#6-actualización-de-precios-especiales-por-fecha)
7. [Cambio de Mapa de Asientos](#7-cambio-de-mapa-de-asientos)
8. [Casos Especiales y Conflictos](#8-casos-especiales-y-conflictos)
9. [Servicios Maestros (Master Services)](#9-servicios-maestros-master-services)

---
---

# 1. Creación de Servicios

## ¿Qué pasa cuando creas un nuevo servicio?

Cuando guardas un nuevo servicio en el sistema, se ejecutan **3 operaciones automáticas** que preparan todo para que puedas empezar a vender.

---

## Las 3 operaciones automáticas

### 📊 Operación 1: Registro en Base de Datos de Análisis

**¿Qué hace?**
Guarda todos los datos del servicio en BigQuery (sistema de análisis) para reportes futuros.

**¿Por qué es importante?**
- Crea un historial desde el primer día
- Permite generar estadísticas de servicios
- Facilita auditorías y reportes

**Ejemplo:**
Si creas "Tour Los Roques", se guarda:
- Nombre del servicio
- Precios configurados
- Capacidades
- Fechas de operación
- Origen y destino
- Fecha de creación

---

### 📅 Operación 2: Generación Automática de Horarios

**¿Qué hace?**
Crea automáticamente todos los horarios (schedules) según tu configuración de recurrencia.

**¿Cómo funciona?**

El sistema lee tu configuración y genera horarios para los **próximos 3 meses** (o hasta la fecha final que configuraste, lo que sea menor).

**Ejemplo práctico 1: Servicio diario**

**Tu configuración:**
- Servicio: "Express Caracas-Valencia"
- Frecuencia: Diaria
- Horarios: 8:00am, 2:00pm, 6:00pm
- Fecha inicio: 1 de enero
- Fecha fin: 31 de diciembre

**Lo que hace el sistema automáticamente:**
1. Calcula: 90 días (3 meses) x 3 horarios = 270 horarios
2. Crea 270 schedules con:
   - Hora de salida (departure)
   - Hora de llegada estimada (arrival)
   - Precios calculados para esa fecha
   - Capacidad disponible (suma de todas las quotas)
   - Estado: activo y listo para vender

**Resultado:**
Tienes 270 horarios listos para vender sin crear nada manualmente.

---

**Ejemplo práctico 2: Servicio semanal (fines de semana)**

**Tu configuración:**
- Servicio: "Tour Morrocoy"
- Frecuencia: Semanal
- Días: Sábados y domingos
- Horario: 7:00am
- Fecha inicio: 1 de enero
- Fecha fin: 31 de marzo

**Lo que hace el sistema automáticamente:**
1. Calcula: 13 semanas x 2 días = 26 horarios
2. Crea 26 schedules (solo sábados y domingos)
3. Cada uno con:
   - Hora: 7:00am
   - Precios del día (puede variar si hay reglas de temporada)
   - Capacidad completa disponible

**Resultado:**
Tienes todos los fines de semana del trimestre listos para vender.

---

**Ejemplo práctico 3: Servicio con múltiples horarios**

**Tu configuración:**
- Servicio: "Transporte Aeropuerto"
- Frecuencia: Diaria
- Horarios: Cada 2 horas (6am, 8am, 10am, 12pm, 2pm, 4pm, 6pm, 8pm)
- Fecha inicio: Hoy
- Fecha fin: 6 meses

**Lo que hace el sistema automáticamente:**
1. Calcula: 90 días x 8 horarios = 720 horarios
2. Crea 720 schedules
3. Cada uno con su hora específica

**Resultado:**
Tienes un servicio completo de shuttle con 8 salidas diarias por 3 meses.

---

### 🎯 Operación 3: Cálculo Inteligente de Precios

**¿Qué hace?**
Calcula el precio final de cada horario aplicando todas tus reglas de negocio.

**Reglas que aplica automáticamente:**

#### a) Precio Base
El precio inicial que configuraste en `basePriceTiers`

#### b) Reglas Estacionales (si las configuraste)
Multiplicadores por temporada alta/baja

**Ejemplo:**
- Precio base: $50
- Temporada alta (diciembre 15-31): +30%
- Horarios de diciembre 15-31: $65 (50 x 1.30)
- Resto del año: $50

#### c) Sobrescrituras de Fecha (si las configuraste)
Precios específicos para días especiales

**Ejemplo:**
- Precio base: $50
- 31 de diciembre: $120 (configurado manualmente)
- Ese día específico: $120 (ignora base y temporada)

---

### 🧮 Cálculo de Capacidad Disponible

**¿Qué hace?**
Suma automáticamente todas las quotas de tus tipos de precio para saber cuántos asientos tienes.

**Ejemplo:**

**Tu configuración:**
- Precio Adulto: quota 30
- Precio Niño: quota 10
- Precio Senior: quota 5

**Lo que hace el sistema:**
Para cada horario, calcula:
```
availableSeats = 30 + 10 + 5 = 45 asientos
```

**Resultado:**
Cada horario sabe que tiene 45 asientos disponibles para vender.

---

## Ejemplo Completo: Creación de Servicio Real

### 🚌 Caso: Express Inter-ciudades

**Lo que configuras:**

```
Nombre: "Express Maracaibo-Caracas"
Origen: Maracaibo
Destino: Caracas
Recurrencia:
  - Frecuencia: Diaria
  - Horarios: 6:00am, 10:00am, 3:00pm, 8:00pm
  - Inicio: 15 enero 2025
  - Fin: 31 diciembre 2025

Precios Base:
  - Adulto: $45, quota: 35
  - Niño: $25, quota: 10
  - Senior: $40, quota: 5

Reglas Estacionales:
  - Semana Santa (marzo 20-30): +25%
  - Carnaval (febrero 10-13): +40%
  - Temporada Navidad (dic 15-31): +35%

Sobrescrituras:
  - 31 diciembre: Adulto $100, Niño $60
```

**Lo que hace el sistema automáticamente:**

#### 1. Registra en BigQuery
Guarda todos los datos del servicio para análisis.

#### 2. Genera horarios (primeros 3 meses)
- 15 enero a 15 abril: 90 días
- 90 días x 4 horarios = 360 schedules creados

#### 3. Calcula precios para cada horario

**Horarios de enero-febrero (excepto carnaval):**
- Adulto: $45
- Niño: $25
- Senior: $40
- Capacidad: 50 asientos (35+10+5)

**Horarios de carnaval (feb 10-13):**
- Adulto: $63 (45 x 1.40)
- Niño: $35 (25 x 1.40)
- Senior: $56 (40 x 1.40)
- Capacidad: 50 asientos

**Horarios de Semana Santa (mar 20-30):**
- Adulto: $56.25 (45 x 1.25)
- Niño: $31.25 (25 x 1.25)
- Senior: $50 (40 x 1.25)
- Capacidad: 50 asientos

**31 de diciembre (cuando llegue al 4to mes):**
- Adulto: $100 (sobrescritura)
- Niño: $60 (sobrescritura)
- Senior: $40 (no hay sobrescritura, usa base)
- Capacidad: 50 asientos

#### 4. Resultado final

Tienes **360 horarios listos para vender** con:
- ✅ Precios calculados correctamente según temporada
- ✅ Capacidad configurada (50 asientos cada uno)
- ✅ Fechas y horas exactas
- ✅ Todo registrado en base de datos

**Tiempo total:** Menos de 1 minuto

**Si lo hicieras manual:** Días de trabajo

---

## Límite de 3 Meses

**¿Por qué solo 3 meses?**

El sistema genera horarios para los próximos 3 meses para evitar:
- Crear miles de horarios innecesarios
- Sobrecargar la base de datos
- Horarios muy lejanos que pueden cambiar

**¿Qué pasa después de 3 meses?**

Debes crear más horarios manualmente o esperar a que se cree un proceso automático de extensión (futura funcionalidad).

**Ejemplo:**
- Hoy: 15 enero
- Crea servicio con fin en diciembre
- Sistema genera: 15 enero a 15 abril (3 meses)
- 15 abril: Debes generar los siguientes 3 meses

---

## ¿Qué NO hace al crear?

❌ **No genera horarios infinitos**
- Solo los primeros 3 meses

❌ **No actualiza horarios existentes**
- Solo crea nuevos

❌ **No envía notificaciones**
- Es un proceso interno

---

## Resumen: Creación de Servicios

Cuando creas un servicio, el sistema:

1. 📊 **Registra** todo en base de datos de análisis
2. 📅 **Genera** hasta 720 horarios automáticamente (3 meses)
3. 🎯 **Calcula** precios con reglas de temporada
4. 🧮 **Suma** capacidades de todos los tipos de precio
5. ⚡ **Listo** para vender en menos de 1 minuto

**Tu trabajo:** Configurar el servicio correctamente

**Trabajo del sistema:** Crear todo lo demás automáticamente

---
---

# 2. Generación Automática Diaria de Horarios

## ¿Qué es esta funcionalidad?

Esta es una **tarea programada que se ejecuta automáticamente todos los días a la medianoche** (00:00 hrs) y se encarga de generar nuevos horarios para mantener siempre 3 meses de disponibilidad futura.

---

## ¿Cómo funciona?

### 🕐 Horario de ejecución
**Todos los días a las 12:00 AM (medianoche)**

El sistema ejecuta automáticamente esta tarea sin que tengas que hacer nada.

---

## El proceso diario paso a paso

### 1️⃣ Identificar servicios activos

**¿Qué hace?**
Busca todos los servicios que:
- NO estén expirados (status ≠ 'EXPIRED')
- Tengan fecha de fin de recurrencia mayor o igual a hoy
- Tengan configuración de recurrencia válida

**Ejemplo:**
```
Hoy: 15 enero 2025

Servicios encontrados:
✅ "Express Caracas-Valencia" (fin: 31 dic 2025)
✅ "Tour Los Roques" (fin: 30 jun 2025)
❌ "Tour Navideño" (fin: 31 dic 2024) → Expirado
❌ "Servicio de Prueba" (status: EXPIRED)
```

---

### 2️⃣ Calcular fecha objetivo

**¿Qué hace?**
Para cada servicio activo, calcula la fecha 3 meses hacia adelante desde hoy.

**Ejemplo:**
```
Hoy: 15 enero 2025
Fecha objetivo: 15 abril 2025 (3 meses después)
```

**¿Por qué 3 meses?**
Para mantener siempre una ventana de disponibilidad de 3 meses. Como al crear el servicio solo se generaron 3 meses iniciales, esta tarea se encarga de ir agregando el día que falta cada día.

---

### 3️⃣ Verificar si ya existe horario

**¿Qué hace?**
Verifica si ya existen horarios para esa fecha objetivo.

**Ejemplo:**
```
Servicio: "Express Caracas-Valencia"
Fecha objetivo: 15 abril 2025

¿Ya existen horarios para el 15 abril?
  - Busca en base de datos
  - Si encuentra horarios → ❌ No hace nada
  - Si NO encuentra → ✅ Procede a crear
```

**Importante:** Esto evita duplicados. Si el horario ya fue creado, no lo vuelve a crear.

---

### 4️⃣ Validar patrón de recurrencia

**¿Qué hace?**
Verifica si la fecha objetivo coincide con el patrón de recurrencia configurado del servicio.

**Ejemplos de validación:**

#### Servicio Diario
```
Recurrencia: DIARIA, cada 1 día
Fecha objetivo: 15 abril 2025 (Martes)
✅ Válido: Cualquier día coincide
```

#### Servicio Semanal (solo fines de semana)
```
Recurrencia: SEMANAL, sábados y domingos
Fecha objetivo: 15 abril 2025 (Martes)
❌ No válido: No es sábado ni domingo
→ No crea horarios
```

```
Fecha objetivo: 19 abril 2025 (Sábado)
✅ Válido: Es sábado
→ Sí crea horarios
```

#### Servicio Quincenal
```
Recurrencia: DIARIA, cada 2 días
Inicio: 1 enero 2025
Fecha objetivo: 15 abril 2025
Días desde inicio: 104 días
104 % 2 = 0 ✅ Válido (múltiplo de 2)
```

#### Servicio Mensual (días específicos)
```
Recurrencia: MENSUAL, día 1 y 15 de cada mes
Fecha objetivo: 15 abril 2025
✅ Válido: Es día 15
```

```
Fecha objetivo: 16 abril 2025
❌ No válido: No es día 1 ni 15
```

---

### 5️⃣ Generar horarios del día

**¿Qué hace?**
Si la fecha es válida, genera todos los horarios configurados para ese día.

**Ejemplo:**

**Configuración del servicio:**
```
Horarios: 8:00am, 2:00pm, 6:00pm
Precio Adulto: $50, quota: 30
Precio Niño: $30, quota: 10
Temporada alta (abril): +20%
```

**Horarios generados para el 15 abril:**

```
Horario 1:
  - Salida: 15 abril 2025, 8:00am
  - Adulto: $60 (50 + 20%)
  - Niño: $36 (30 + 20%)
  - Capacidad: 40 asientos

Horario 2:
  - Salida: 15 abril 2025, 2:00pm
  - Adulto: $60
  - Niño: $36
  - Capacidad: 40 asientos

Horario 3:
  - Salida: 15 abril 2025, 6:00pm
  - Adulto: $60
  - Niño: $36
  - Capacidad: 40 asientos
```

**Total: 3 horarios creados**

---

### 6️⃣ Aplicar reglas de precios

**¿Qué hace?**
Al generar cada horario, aplica automáticamente:

#### a) Precios base
Toma los precios configurados en `basePriceTiers`

#### b) Reglas estacionales
Si la fecha cae en temporada alta/baja, aplica el multiplicador

**Ejemplo:**
```
15 abril 2025 está en temporada alta (+20%)
Precio base: $50
Precio final: $60
```

#### c) Sobrescrituras de fecha
Si hay un precio especial configurado para ese día exacto, lo usa en lugar del base

**Ejemplo:**
```
Si 15 abril tiene sobrescritura: $80
Ignora base ($50) y temporada (+20%)
Precio final: $80
```

---

### 7️⃣ Guardar en lotes

**¿Qué hace?**
Guarda todos los horarios generados en la base de datos en lotes de 500 (límite de Firestore).

**¿Por qué en lotes?**
Firestore tiene un límite de 500 operaciones por lote. Si se generan más horarios (múltiples servicios), se dividen en chunks.

---

## Ejemplo completo del proceso diario

### 📅 Hoy: 15 enero 2025 - 12:00 AM

#### Paso 1: Buscar servicios activos
```
✅ Express Caracas-Valencia (fin: 31 dic 2025)
✅ Tour Morrocoy (fin: 31 mar 2025, solo fines de semana)
✅ Shuttle Aeropuerto (fin: 30 jun 2025, cada 2 horas)
❌ Tour Navideño (fin: 31 dic 2024) → Expirado

Total: 3 servicios activos
```

#### Paso 2: Calcular fecha objetivo
```
Fecha objetivo: 15 abril 2025
```

#### Paso 3: Procesar cada servicio

**Servicio 1: Express Caracas-Valencia**
```
Recurrencia: DIARIA, 4 horarios (6am, 10am, 3pm, 8pm)
15 abril 2025 (Martes) ✅ Válido para DIARIA
¿Ya existe? NO
→ Crear 4 horarios
```

**Servicio 2: Tour Morrocoy**
```
Recurrencia: SEMANAL, sábados y domingos, 1 horario (7am)
15 abril 2025 (Martes) ❌ No es fin de semana
→ No crear horarios (0)
```

**Servicio 3: Shuttle Aeropuerto**
```
Recurrencia: DIARIA, 8 horarios (cada 2 horas)
15 abril 2025 (Martes) ✅ Válido para DIARIA
¿Ya existe? NO
→ Crear 8 horarios
```

#### Paso 4: Resultado del día
```
Total horarios creados: 12
  - Express Caracas-Valencia: 4
  - Tour Morrocoy: 0 (no aplica ese día)
  - Shuttle Aeropuerto: 8

Tiempo total: ~10 segundos
```

---

## Ejemplo: Evolución de horarios a lo largo del tiempo

### Día 1: 15 enero (Creación del servicio)
```
Servicio: "Express Caracas-Valencia"
Horarios creados: 15 ene → 15 abril (360 horarios)
```

### Día 2: 16 enero (Primera ejecución automática)
```
Fecha objetivo: 16 abril
¿Ya existe? NO
Horarios creados: 16 abril (4 horarios)

Total disponible ahora: 15 ene → 16 abril (364 horarios)
```

### Día 3: 17 enero
```
Fecha objetivo: 17 abril
¿Ya existe? NO
Horarios creados: 17 abril (4 horarios)

Total disponible ahora: 15 ene → 17 abril (368 horarios)
```

### Día 90: 15 abril
```
Fecha objetivo: 15 julio
¿Ya existe? NO
Horarios creados: 15 julio (4 horarios)

Total disponible ahora: 15 abril → 15 julio (360 horarios)
```

**Resultado:** Siempre tienes exactamente 90 días (3 meses) de disponibilidad hacia adelante.

---

## Casos especiales

### 🎯 Caso 1: Servicio con recurrencia que termina pronto

**Situación:**
```
Hoy: 15 enero 2025
Servicio: "Tour Temporal"
Fecha fin de recurrencia: 28 febrero 2025
Fecha objetivo: 15 abril 2025
```

**Resultado:**
```
15 abril > 28 febrero
❌ La fecha objetivo está fuera del rango de recurrencia
→ No crea horarios
→ El servicio eventualmente se marca como EXPIRED
```

---

### 🎯 Caso 2: Días que no coinciden con patrón

**Situación:**
```
Servicio: "Tour Fines de Semana"
Recurrencia: SEMANAL, solo sábados
Fecha objetivo: 15 abril (Martes)
```

**Resultado:**
```
Martes ≠ Sábado
❌ No coincide con el patrón
→ No crea horarios ese día
→ Esperará al próximo sábado (19 abril)
```

---

### 🎯 Caso 3: Múltiples servicios en paralelo

**Situación:**
```
Tienes 50 servicios activos
Cada uno necesita validación y posible creación
```

**Resultado:**
```
El sistema procesa los 50 servicios en paralelo
→ Mucho más rápido que uno por uno
→ Todos completan en ~1 minuto
```

---

## Beneficios de la generación automática

### ✅ Siempre tienes 3 meses disponibles
No tienes que preocuparte por generar horarios manualmente cada mes.

### ✅ Sin duplicados
El sistema verifica que no existan horarios antes de crearlos.

### ✅ Respeta todas las reglas
Aplica correctamente:
- Patrones de recurrencia
- Reglas estacionales
- Sobrescrituras de fecha
- Precios dinámicos

### ✅ Eficiente
Procesa todos tus servicios en paralelo en segundos.

### ✅ Sin intervención manual
Se ejecuta automáticamente todos los días.

---

## Monitoreo y logs

### ¿Cómo saber si funcionó?

El sistema registra en logs:

```
Starting daily schedule generation for: 2025-01-16T00:00:00.000Z
Found 3 active services to process
Service ABC123: Checking schedule for 2025-04-16
Creating 4 new schedules for service ABC123
Service XYZ789: Schedule already exists for 2025-04-16
Daily schedule generation completed. Total schedules created: 12
```

**Información que registra:**
- Cuántos servicios activos encontró
- Para qué fecha está creando horarios
- Cuántos horarios creó por servicio
- Total de horarios creados en el día

---

## Preguntas frecuentes

### ❓ ¿Qué pasa si el sistema falla un día?

**Respuesta:** No es problema. Al día siguiente, intentará crear los horarios de ambos días (el que faltó + el nuevo). El sistema verifica duplicados, así que no hay riesgo de crear horarios repetidos.

**Ejemplo:**
```
Día 1 (16 ene): Falla el proceso
Día 2 (17 ene): Ejecuta normalmente
  → Intenta crear 16 abril: NO existe → Crea
  → Intenta crear 17 abril: NO existe → Crea
  → Ambos días quedan creados
```

---

### ❓ ¿Puedo forzar la creación de más horarios?

**Respuesta:** La tarea automática solo crea +3 meses desde hoy. Si necesitas más adelante, debes crear un servicio nuevo o extender manualmente usando una función específica (si existe).

---

### ❓ ¿Qué pasa con los horarios viejos?

**Respuesta:** Los horarios pasados se mantienen en la base de datos para historial de ventas y auditoría. No se eliminan automáticamente.

---

### ❓ ¿Afecta los horarios ya existentes?

**Respuesta:** NO. La tarea solo **crea nuevos** horarios. NUNCA modifica horarios existentes. Para modificar horarios, debes actualizar el servicio (ver secciones siguientes).

---

## Resumen

Esta funcionalidad asegura que:

1. 🔄 **Siempre tengas 3 meses de disponibilidad** hacia adelante
2. ⏰ **Se ejecuta automáticamente** todos los días a medianoche
3. ✅ **Respeta todos los patrones** de recurrencia configurados
4. 💰 **Aplica precios correctos** según temporada y sobrescrituras
5. 🚫 **Evita duplicados** verificando antes de crear
6. ⚡ **Procesa todos los servicios** en paralelo eficientemente

**Tu único trabajo:** Asegurarte de que el servicio esté bien configurado inicialmente. El sistema se encarga del resto automáticamente.

---
---

# 3. Limpieza Automática de Horarios Expirados

## ¿Qué es esta funcionalidad?

Esta es una **tarea programada que se ejecuta automáticamente todos los días a la medianoche** (00:00 hrs), igual que la generación de horarios, pero su trabajo es **eliminar horarios que ya pasaron y no tuvieron ventas** para mantener la base de datos limpia y optimizada.

---

## ¿Cómo funciona?

### 🕐 Horario de ejecución
**Todos los días a las 12:00 AM (medianoche)**

Se ejecuta en paralelo con la generación de horarios. Una tarea crea nuevos horarios (+3 meses) y otra elimina horarios viejos que ya expiraron.

---

## El proceso de limpieza paso a paso

### 1️⃣ Buscar horarios expirados sin ventas

**¿Qué hace?**
Busca horarios que cumplan **DOS condiciones simultáneas**:
1. Fecha de expiración (`expireAt`) menor que hoy
2. Sin ventas realizadas (`ordersCount` = 0)

**¿Por qué estas dos condiciones?**
- **Expirados**: Ya pasó el evento, no se pueden vender más
- **Sin ventas**: No hay datos importantes que preservar para historial

**Ejemplo:**
```
Hoy: 15 enero 2025, 00:00 hrs

Horarios encontrados para limpieza:
✅ 10 enero, 8:00am - Expiró: 11 ene - Ventas: 0 → ELIMINAR
✅ 12 enero, 2:00pm - Expiró: 13 ene - Ventas: 0 → ELIMINAR
❌ 13 enero, 6:00pm - Expiró: 14 ene - Ventas: 5 → MANTENER
❌ 20 enero, 8:00am - Expiró: 21 ene - Ventas: 0 → MANTENER (no expiró aún)
```

---

### 2️⃣ Validación de fecha de expiración

**¿Qué es `expireAt`?**
Es la fecha hasta la cual el horario es válido. Normalmente es **24 horas después de la salida**.

**Ejemplo:**
```
Horario:
  - Salida (departure): 10 enero 2025, 8:00am
  - Expiración (expireAt): 11 enero 2025, 8:00am

Si hoy es 15 enero:
  11 enero < 15 enero ✅ Expirado
```

**¿Por qué 24 horas después?**
Da un margen de tiempo después del evento para procesar pagos pendientes, resolver problemas, etc.

---

### 3️⃣ Validación de ventas

**¿Qué es `ordersCount`?**
Es el contador de órdenes/compras realizadas para ese horario específico.

**Ejemplos:**

**Caso 1: Horario sin ventas**
```
Horario: 10 enero, 8:00am
ordersCount: 0
✅ Se puede eliminar (no hay historial de ventas que preservar)
```

**Caso 2: Horario con ventas**
```
Horario: 10 enero, 2:00pm
ordersCount: 15 (15 órdenes/clientes compraron)
❌ NO se elimina (hay historial importante)
```

**Caso 3: Horario con 1 venta**
```
Horario: 10 enero, 6:00pm
ordersCount: 1
❌ NO se elimina (aunque sea 1, es historial importante)
```

---

### 4️⃣ Eliminar en lotes

**¿Qué hace?**
Elimina todos los horarios encontrados en lotes de 500 (límite de Firestore).

**¿Por qué en lotes?**
Firestore tiene un límite de 500 operaciones por transacción. Si hay 2,000 horarios para eliminar, se procesan en 4 lotes.

**Ejemplo:**
```
Horarios a eliminar: 1,847

Lote 1: Elimina 500 horarios (1-500)
Lote 2: Elimina 500 horarios (501-1000)
Lote 3: Elimina 500 horarios (1001-1500)
Lote 4: Elimina 347 horarios (1501-1847)

Total eliminados: 1,847
```

---

## Ejemplo completo del proceso diario

### 📅 Hoy: 15 enero 2025 - 12:00 AM

#### Situación inicial:
```
Base de datos tiene:
- 10,000 horarios totales
- 8,500 horarios futuros (aún no pasan)
- 1,500 horarios pasados
  - 800 con ventas (ordersCount > 0)
  - 700 sin ventas (ordersCount = 0)
```

#### Paso 1: Buscar candidatos
```sql
WHERE expireAt < 15-enero-2025
AND ordersCount <= 0
```

**Resultado:**
```
700 horarios encontrados
```

#### Paso 2: Procesar en lotes
```
Lote 1: Elimina 500 horarios
Lote 2: Elimina 200 horarios

Total eliminado: 700 horarios
```

#### Resultado final:
```
Base de datos ahora tiene:
- 9,300 horarios totales (10,000 - 700)
- 8,500 horarios futuros
- 800 horarios pasados CON ventas (historial preservado)
```

---

## Casos prácticos

### 🎯 Caso 1: Servicio con poca demanda

**Situación:**
```
Servicio: "Tour de Noche" (baja demanda)
Horarios diarios: 1 (9:00pm)
Últimos 30 días:
  - 25 horarios sin ventas
  - 5 horarios con 1-2 ventas
```

**Lo que hace el sistema:**
```
Cada día que pasa:
- Horarios sin ventas → Se eliminan al día siguiente de expirar
- Horarios con ventas → Se mantienen indefinidamente

Resultado después de 30 días:
- 25 horarios eliminados automáticamente
- 5 horarios preservados (con historial de ventas)
- Base de datos limpia y liviana
```

---

### 🎯 Caso 2: Servicio con alta demanda

**Situación:**
```
Servicio: "Express Caracas-Valencia" (alta demanda)
Horarios diarios: 4 (6am, 10am, 3pm, 8pm)
Últimos 30 días:
  - 120 horarios creados (4 x 30)
  - 118 horarios con ventas
  - 2 horarios sin ventas (feriados)
```

**Lo que hace el sistema:**
```
- 118 horarios → Se mantienen (tienen historial)
- 2 horarios → Se eliminan al expirar

Resultado:
- Base de datos tiene casi todos los horarios históricos
- Solo se eliminan los 2 que no vendieron
```

---

### 🎯 Caso 3: Servicio temporal que terminó

**Situación:**
```
Servicio: "Tour Navideño 2024"
Operó: 1 diciembre - 31 diciembre 2024
Horarios creados: 124 (4 horarios x 31 días)
Ventas: 80 horarios vendieron, 44 no vendieron
Hoy: 15 enero 2025
```

**Lo que hace el sistema:**
```
Desde el 2 enero (día después de expirar el último horario):

Día 1 (2 enero):
  - Encuentra horarios del 1 diciembre sin ventas
  - Elimina los que no vendieron

Día 2 (3 enero):
  - Encuentra horarios del 2 diciembre sin ventas
  - Elimina los que no vendieron

... y así cada día

Día 31 (1 febrero):
  - Ya procesó todo diciembre
  - 44 horarios eliminados (los sin ventas)
  - 80 horarios preservados (los con ventas)

Resultado final:
- Historial completo de ventas mantenido
- Horarios sin ventas eliminados
- Base de datos optimizada
```

---

## ¿Por qué es importante esta limpieza?

### 📊 Rendimiento de base de datos
**Sin limpieza:**
```
1 año de operación:
- Servicio con 4 horarios diarios
- 365 días x 4 = 1,460 horarios
- Si solo 50% vende = 730 horarios sin ventas acumulados

10 servicios = 7,300 horarios basura
50 servicios = 36,500 horarios basura
```

**Con limpieza:**
```
- Solo mantiene horarios con ventas
- Base de datos limpia diariamente
- Consultas más rápidas
- Menos costo de almacenamiento
```

---

### 💾 Optimización de costos

**Firebase cobra por:**
- Almacenamiento de documentos
- Lecturas de documentos
- Escrituras de documentos

**Con limpieza automática:**
```
✅ Menos documentos almacenados = Menor costo
✅ Consultas más rápidas = Menos lecturas
✅ Base de datos eficiente = Mejor performance
```

---

### 🔍 Consultas más rápidas

**Escenario:**
```
Buscar horarios disponibles para el próximo mes

Sin limpieza:
- Busca en 10,000 horarios (incluyendo muchos expirados)
- Tiempo: ~2 segundos

Con limpieza:
- Busca en 3,500 horarios (solo actuales y futuros)
- Tiempo: ~0.7 segundos
```

---

## Lo que NO hace esta limpieza

### ❌ NO elimina horarios con ventas

**Aunque estén expirados, si tienen `ordersCount > 0`, se mantienen.**

**Razón:**
- Historial de ventas
- Auditorías
- Reportes financieros
- Soporte al cliente (consultar compras pasadas)

---

### ❌ NO elimina horarios futuros

**Solo elimina horarios que ya expiraron (`expireAt < hoy`).**

**Ejemplo:**
```
Hoy: 15 enero
Horario: 20 enero, 8:00am
expireAt: 21 enero

21 enero > 15 enero
❌ NO se elimina (aún es futuro)
```

---

### ❌ NO afecta las órdenes/tickets

**Los datos de órdenes y tickets están en otras colecciones.**

**Aunque se elimine el horario, las órdenes y tickets se mantienen intactos.**

---

## Monitoreo y logs

### ¿Cómo saber si funcionó?

El sistema registra en logs:

```
Schedules to delete: 847
Batch 1 completed: 500 schedules deleted
Batch 2 completed: 347 schedules deleted
Total schedules deleted: 847
```

**Si no hay nada para eliminar:**
```
Not expired schedules
```

---

## Comparación: Generación vs Limpieza

| Aspecto | Generación de Horarios | Limpieza de Horarios |
|---------|------------------------|----------------------|
| **Horario** | 00:00 diaria | 00:00 diaria |
| **Qué hace** | Crea horarios nuevos | Elimina horarios viejos |
| **Fecha objetivo** | +3 meses (futuro) | Expirados (pasado) |
| **Condición** | No existe + válido | Expirado + sin ventas |
| **Resultado** | Mantiene 3 meses disponibles | Base de datos limpia |

**Trabajan juntas para:**
- ✅ Siempre tener horarios futuros disponibles
- ✅ Nunca acumular horarios viejos sin uso
- ✅ Mantener la base de datos optimizada

---

## Preguntas frecuentes

### ❓ ¿Puedo recuperar un horario eliminado?

**Respuesta:** No. Una vez eliminado, no se puede recuperar. Pero esto solo afecta horarios sin ventas, por lo que no hay pérdida de información importante.

Si tenía ventas, nunca se eliminaría.

---

### ❓ ¿Qué pasa si elimina un horario por error?

**Respuesta:** Esto no puede pasar porque tiene dos validaciones estrictas:
1. Debe estar expirado
2. Debe tener 0 ventas

Si un horario tiene aunque sea 1 venta, NUNCA se eliminará.

---

### ❓ ¿Cada cuánto se ejecuta?

**Respuesta:** Todos los días a medianoche, igual que la generación de horarios.

---

### ❓ ¿Afecta el historial de ventas?

**Respuesta:** NO. Los horarios con ventas (`ordersCount > 0`) NUNCA se eliminan. El historial completo de ventas se preserva indefinidamente.

---

### ❓ ¿Puedo desactivar esta limpieza?

**Respuesta:** Es una función del sistema que se ejecuta automáticamente. No se recomienda desactivarla porque la base de datos crecería indefinidamente y se volvería lenta.

---

## Beneficios de la limpieza automática

### ✅ Base de datos limpia
No acumula datos innecesarios de horarios sin uso.

### ✅ Mejor performance
Consultas más rápidas al tener menos documentos.

### ✅ Menor costo
Menos almacenamiento = Menor factura de Firebase.

### ✅ Preserva historial importante
Mantiene todos los horarios que tuvieron ventas.

### ✅ Sin intervención manual
Se ejecuta automáticamente todos los días.

### ✅ Segura
Nunca elimina datos importantes (ventas, órdenes, tickets).

---

## Resumen

Esta funcionalidad asegura que:

1. 🗑️ **Elimina horarios expirados sin ventas** cada día
2. 🔒 **Preserva horarios con ventas** indefinidamente
3. ⚡ **Mantiene la base de datos optimizada** y rápida
4. 💰 **Reduce costos** de almacenamiento
5. 🔄 **Trabaja en conjunto** con la generación de horarios
6. 🛡️ **Protege datos importantes** (nunca elimina ventas)

**Tu trabajo:** Ninguno. El sistema se encarga automáticamente de mantener todo limpio y optimizado.

---
---

# 4. Actualización de Servicios

## ¿Qué es la función maestra de actualización?

Esta es la **función principal** que se activa automáticamente cada vez que guardas cambios en un servicio existente. Coordina todas las actualizaciones necesarias en tus horarios y asientos.

**Importante:** Esta función solo modifica horarios ya existentes. No crea nuevos horarios (eso lo hace la generación automática diaria).

---

## Las 4 operaciones que realiza automáticamente

Cuando actualizas un servicio, el sistema ejecuta en este orden:

```
1. ¿Hay precios especiales por fecha?
   SÍ → Actualiza esos horarios específicos

2. ¿Cambiaron los precios base?
   SÍ → Actualiza TODOS los horarios

3. ¿Cambió el mapa de asientos?
   SÍ → Crea eventos nuevos para todos los horarios

4. Guardar cambios en base de datos de análisis
```

### Vista general de las operaciones:

| Operación | Cuándo se ejecuta | Horarios afectados |
|-----------|-------------------|-------------------|
| Precios especiales por fecha | Al configurar/modificar dateOverrides | Solo fechas específicas |
| Precios base | Al modificar basePriceTiers | TODOS los horarios |
| Mapa de asientos | Al cambiar seatsConfig.mapId | TODOS los horarios |
| Registro BigQuery | Siempre | N/A (solo registro) |

Ahora veremos cada operación en detalle en las siguientes secciones...

---
---

# 3. Actualización de Precios Base

## ¿Qué hace esta función?

Cuando modificas los **precios base** o características de un servicio, esta función automáticamente **actualiza todos los horarios futuros** que ya fueron creados.

---

## ¿Por qué es importante?

Imagina que tienes un servicio de **Tour a la playa** que opera todos los días del mes. Ya creaste 30 horarios (uno por cada día). Si cambias el precio o alguna característica, NO quieres tener que editar manualmente los 30 horarios uno por uno. Esta función lo hace automáticamente por ti.

---

## Ejemplos prácticos

### 📌 Ejemplo 1: Cambio de precio

**Situación inicial:**
- Servicio: "Tour a la playa"
- Precio Adulto: $50
- Precio Niño: $30
- Horarios creados: 60 (2 meses de operación)

**Lo que haces:**
1. Vas al servicio y cambias el precio de Adulto a $60

**Lo que hace automáticamente el sistema:**
- Busca los 60 horarios existentes
- En cada horario, actualiza el precio de "Adulto" de $50 a $60
- Mantiene todo lo demás igual (asientos vendidos, disponibilidad, etc.)

**Resultado:**
Todos tus horarios ahora muestran el nuevo precio de $60 para adultos.

---

### 📌 Ejemplo 2: Activar asientos numerados

**Situación inicial:**
- Servicio: "Tour en barco"
- Los asientos NO estaban numerados
- Horarios creados: 45

**Lo que haces:**
1. Editas el servicio y activas "Asientos numerados" (isNumbered = true)

**Lo que hace automáticamente el sistema:**
- Busca los 45 horarios
- En cada horario, marca que los asientos están numerados
- Ahora los clientes tendrán que seleccionar su asiento al comprar

**Resultado:**
Todos los horarios futuros ahora requieren selección de asiento.

---

### 📌 Ejemplo 3: Agregar un nuevo tipo de boleto

**Situación inicial:**
- Servicio: "Tour ciudad"
- Tipos de boleto: Adulto ($40), Niño ($20)
- Horarios creados: 30

**Lo que haces:**
1. Agregas un nuevo tipo de boleto "Senior" por $35

**Lo que hace automáticamente el sistema:**
- Busca los 30 horarios existentes
- En cada horario, agrega el nuevo tipo de boleto "Senior" con precio $35
- Los tipos anteriores se mantienen sin cambios

**Resultado:**
Todos los horarios ahora tienen 3 tipos de boletos disponibles para vender.

---

### 📌 Ejemplo 4: Eliminar un tipo de boleto

**Situación inicial:**
- Servicio: "Tour nocturno"
- Tipos de boleto: Adulto, Niño, Estudiante
- Horarios creados: 20

**Lo que haces:**
1. Eliminas el tipo de boleto "Estudiante"

**Lo que hace automáticamente el sistema:**
- Busca los 20 horarios
- En cada horario, marca "Estudiante" como inactivo (no eliminado)
- Si alguien ya compró boletos de estudiante, esos se mantienen
- Los nuevos clientes ya no verán la opción "Estudiante"

**Resultado:**
Los horarios ya no ofrecen boletos de estudiante para nuevas ventas.

---

### 📌 Ejemplo 5: Aumentar la capacidad

**Situación inicial:**
- Servicio: "Paseo en lancha"
- Precio Adulto: quota de 20 personas
- Horarios creados: 15

**Lo que haces:**
1. Cambias la quota de Adulto de 20 a 30 personas

**Lo que hace automáticamente el sistema:**
- Busca los 15 horarios
- En cada horario, actualiza la capacidad de 20 a 30
- Si ya había 5 vendidos, ahora quedan 25 disponibles (antes quedaban 15)

**Resultado:**
Todos los horarios tienen más capacidad para vender.

---

## Escenario completo: Ajuste de inflación

### 💰 Tu situación:
- Enero: Subida de combustible
- Necesitas ajustar precios de todos tus servicios

**Pasos:**
1. Aumentas precios base +15%
2. Guardas cada servicio

**Lo que hace el sistema por cada servicio:**
1. Recalcula precios para TODOS los horarios futuros
2. Respeta precios especiales ya configurados
3. Aplica reglas de temporada sobre el nuevo precio

**Ejemplo numérico:**
- Precio viejo: $40
- Precio nuevo: $46 (+15%)
- Temporada alta (+20%): $55.20 (46 x 1.20)
- Fecha especial Año Nuevo: $100 (NO se toca)

---

## ¿Qué NO hace?

❌ **No modifica boletos ya vendidos**
- Si vendiste 10 boletos a $50, esos se mantienen a $50 aunque cambies el precio a $60

❌ **No afecta horarios pasados**
- La función actualiza TODOS los horarios, pero los que ya pasaron no afectan las ventas

❌ **No crea nuevos horarios**
- Solo actualiza los existentes. Si necesitas más horarios, debes crearlos manualmente

---

## ¿Cuándo se ejecuta?

La función se ejecuta **automáticamente** cada vez que:
- Modificas algún precio en los "Precios Base"
- Cambias características como: asientos numerados, canales de venta, capacidad
- Agregas o eliminas tipos de boletos

**No necesitas hacer nada extra**, el sistema lo hace por ti en segundo plano.

---

## Casos especiales

### 🎯 Precios con reglas estacionales

Si tienes reglas de temporada alta/baja, el sistema las respeta:

**Ejemplo:**
- Precio base Adulto: $50
- Regla de temporada alta (diciembre): +20%
- Cambias el precio base a $60

**Resultado:**
- Horarios de enero-noviembre: $60
- Horarios de diciembre: $72 (60 + 20%)

### 🎯 Precios con sobrescrituras de fecha

Si configuraste un precio especial para una fecha específica (ej: Navidad), ese precio NO se modifica:

**Ejemplo:**
- Precio base: $50
- 25 de diciembre: $100 (sobrescritura)
- Cambias el precio base a $60

**Resultado:**
- Todos los días: $60
- 25 de diciembre: sigue siendo $100 (respeta la sobrescritura)

---

## Resumen

> **En pocas palabras:** Esta función es tu asistente automático. Cada vez que cambias algo en los precios o características de tu servicio, el sistema actualiza inteligentemente todos tus horarios futuros para que reflejen esos cambios, sin que tengas que hacer nada manualmente.

**Beneficios:**
- ⏱️ Ahorra tiempo (no editar horarios uno por uno)
- ✅ Evita errores humanos
- 🔄 Mantiene todo sincronizado
- 🛡️ Protege las ventas ya realizadas

---
---

# 4. Actualización de Precios Especiales por Fecha

## ¿Qué hace?

Cuando configuras precios especiales para fechas específicas (como Navidad, Año Nuevo, conciertos especiales, etc.), esta función actualiza SOLO los horarios de esos días exactos.

---

## Ejemplo práctico

### 🎄 Situación:
- Servicio: "Tour Isla Margarita"
- Precio normal Adulto: $50
- Tienes 90 horarios creados (3 meses)

**Lo que haces:**
1. Configuras un precio especial para el 31 de diciembre:
   - Adulto: $150 (en lugar de $50)
   - Niño: $100 (en lugar de $30)

**Lo que hace el sistema automáticamente:**
1. Busca TODOS los horarios del 31 de diciembre (ej: 8:00am, 10:00am, 2:00pm, 4:00pm)
2. Para cada horario de ese día:
   - Desactiva los precios antiguos ($50 adulto, $30 niño)
   - Los marca como "inactivos" (no los borra, por si alguien ya compró)
   - Agrega los nuevos precios ($150 adulto, $100 niño)
3. Los otros 86 horarios (de otros días) NO se tocan

**Resultado:**
- 31 de diciembre: Todos los horarios muestran $150 adulto / $100 niño
- Resto de días: Mantienen el precio normal de $50 / $30

**Caso importante:**
Si alguien ya había comprado boletos para el 31 a $50, esos boletos siguen siendo válidos y el cliente pagó $50. Solo los nuevos clientes verán y pagarán $150.

---

## Escenario completo: Preparar temporada navideña

### 🎄 Tu situación:
- Servicio: "Tour Los Roques"
- Tienes horarios creados hasta marzo (90 días)
- Quieres cobrar más del 15 al 31 de diciembre

**Pasos:**
1. Configuras 17 fechas especiales (15-31 dic) con precio premium
2. Guardas el servicio

**Lo que hace el sistema:**
1. Encuentra aproximadamente 34 horarios (2 por día x 17 días)
2. Actualiza esos 34 horarios con precios premium
3. Los otros 56 horarios mantienen precio normal

**Tiempo que ahorras:**
En lugar de editar 34 horarios a mano → Automático en segundos

---

## Cómo funciona internamente

1. **Identifica la fecha**: Busca horarios que salgan en esa fecha exacta (desde 00:00 hasta 23:59)
2. **Desactiva precios antiguos**: Marca los precios actuales como `status: false`
3. **Agrega precios nuevos**: Crea nuevos priceTiers con `status: true`
4. **Combina ambos**: El horario tiene ambos sets de precios (viejos inactivos + nuevos activos)

**¿Por qué mantener los viejos?**
Para que los boletos ya vendidos sigan teniendo referencia al precio que pagaron.

---

## Resumen

Esta función es perfecta para:
- 🎉 Eventos especiales (conciertos, festivales)
- 🎄 Temporadas (Navidad, Año Nuevo)
- 🎊 Días feriados (Semana Santa, Carnaval)
- 💰 Promociones de días específicos

**Ventaja principal:** Solo afecta las fechas que configuras, dejando el resto intacto.

---
---

# 5. Cambio de Mapa de Asientos

## ¿Qué hace?

Cuando cambias el mapa de asientos del servicio (distribución física de asientos), crea nuevos eventos en el sistema de mapas para todos los horarios.

---

## Ejemplo práctico

### 🚌 Situación:
- Servicio: "Bus Turístico Premium"
- Tenías un bus de 40 asientos
- Compraste un bus nuevo de 50 asientos con mejor distribución
- 60 horarios programados

**Lo que haces:**
1. Subes el nuevo mapa de asientos (50 asientos)
2. Cambias la configuración del mapa en el servicio (seatsConfig.mapId)

**Lo que hace el sistema automáticamente:**
1. Detecta que el mapa cambió
2. Busca todos los 60 horarios
3. Para cada horario, crea eventos en el sistema de asientos con el nuevo mapa
4. Esto permite que los clientes vean y seleccionen del nuevo layout de 50 asientos

**Resultado:**
Todos tus horarios ahora usan el nuevo mapa de 50 asientos. Los clientes verán la nueva distribución cuando compren.

**Caso especial:**
Si alguien ya había comprado asientos con el mapa viejo, esos asientos se mantienen válidos. El sistema NO mueve a los pasajeros que ya compraron.

---

## Escenario completo: Cambio de flota

### 🚌 Tu situación:
- Servicio: "Express Valencia-Maracay"
- Tenías buses de 42 asientos sin numeración
- Renovaste la flota con buses de 50 asientos numerados
- 200 horarios programados

**Pasos:**
1. Subes el nuevo mapa de 50 asientos
2. Cambias seatsConfig.mapId
3. Activas isNumbered en los precios
4. Guardas

**Lo que hace el sistema:**
1. Actualiza 200 horarios con "asientos numerados"
2. Crea 200 eventos de mapa con el nuevo layout
3. Aumenta la capacidad disponible en todos

**Impacto:**
Los clientes que compren a partir de ahora:
- Verán 50 asientos en lugar de 42
- Deberán elegir su asiento específico
- Verán el nuevo diseño del bus

---

## ¿Qué son los "eventos de mapa"?

Son registros en el sistema de seats.io (o sistema similar) que permiten:
- Mostrar el mapa visual de asientos
- Permitir selección interactiva
- Rastrear qué asientos están ocupados
- Gestionar reservas y bloqueos

---

## Resumen

Esta función es esencial cuando:
- 🚌 Renuevas tu flota de vehículos
- 🎭 Cambias la disposición de asientos
- 📈 Aumentas/reduces capacidad
- 🔄 Mejoras la experiencia de compra

**Nota importante:** Solo aplica si tu servicio usa mapas de asientos (isNumbered: true)

---
---

# 6. Casos Especiales y Conflictos

## ¿Qué pasa si hay conflictos?

### Conflicto 1: Precio especial vs Precio base

**Situación:**
- 25 de diciembre tiene precio especial de $100
- Cambias el precio base de $50 a $60

**Resultado:**
- 25 de diciembre: Mantiene $100 (precio especial gana)
- Otros días: Cambian a $60

**Regla:** Los precios especiales por fecha SIEMPRE tienen prioridad sobre los precios base.

---

### Conflicto 2: Múltiples cambios simultáneos

**Situación:**
Haces 3 cambios al mismo tiempo:
1. Cambias precio base
2. Agregas precio especial
3. Cambias mapa de asientos

**Resultado:**
El sistema ejecuta los 3 en orden:
1. Primero aplica precios especiales
2. Luego actualiza precios base (respetando los especiales)
3. Finalmente actualiza el mapa

**Todo en una sola operación automática.**

---

### Conflicto 3: Actualización con ventas existentes

**Situación:**
- Horario tiene 20/50 asientos vendidos
- Aumentas capacidad a 60

**Resultado:**
- Asientos vendidos: 20 (no se tocan)
- Asientos disponibles: 40 (60 - 20)

**Regla:** Las ventas existentes son intocables. Solo se actualiza la capacidad total.

---

## Limitaciones Importantes

### ⚠️ Lo que NO puedes deshacer automáticamente

❌ **Precios ya cobrados**
- Si vendiste a $50, no puedes cambiar ese precio retroactivamente
- Los boletos vendidos son intocables

❌ **Asientos ya vendidos con mapa viejo**
- Si alguien compró asiento 12A con el mapa viejo
- Ese asiento sigue siendo 12A aunque cambies el mapa
- No puedes reasignar automáticamente

❌ **Horarios ya pasados**
- Si el horario fue ayer, cualquier cambio no afecta nada
- Solo se actualizan horarios futuros

### ✅ Lo que SÍ sucede automáticamente

✅ **Capacidad aumentada**
- Más asientos disponibles para vender

✅ **Nuevas características**
- Asientos numerados, canales de venta, etc.

✅ **Precios actualizados**
- Para todos los horarios sin ventas o con ventas parciales

---
---

# 7. Mejores Prácticas

## 💡 Mejor práctica 1: Planifica con anticipación

**❌ Malo:**
- Crear horarios de diciembre el 30 de noviembre
- Configurar precios especiales el mismo día

**✅ Bueno:**
- Crear horarios de diciembre a inicio de noviembre
- Configurar precios especiales con 1 mes de anticipación
- Da tiempo a que el sistema sincronice todo

---

## 💡 Mejor práctica 2: Cambios en horarios de baja demanda

**❌ Malo:**
- Cambiar mapa de asientos el viernes en la tarde
- Horario de las 5pm con 40/50 asientos vendidos

**✅ Bueno:**
- Cambiar mapa de asientos para horarios nuevos
- O en horarios con pocas ventas (5/50 vendidos)

---

## 💡 Mejor práctica 3: Prueba primero

**❌ Malo:**
- Cambiar 10 servicios al mismo tiempo
- Sin verificar el resultado del primero

**✅ Bueno:**
- Cambiar 1 servicio
- Verificar que los horarios se actualizaron correctamente
- Luego cambiar los demás

---

## 💡 Mejor práctica 4: Entiende el orden de operaciones

**Orden correcto:**
1. Crear servicio con configuración base
2. Esperar generación de horarios (segundos)
3. Configurar precios especiales si es necesario
4. Configurar mapa de asientos si es necesario
5. Hacer ajustes de precios base después

**Por qué:** Cada operación puede afectar la siguiente. Es mejor hacerlas en orden lógico.

---

## 💡 Mejor práctica 5: Documenta cambios importantes

**✅ Recomendado:**
- Anota qué cambiaste y cuándo
- Guarda capturas antes/después de cambios grandes
- Facilita auditorías y resolución de problemas

**Ejemplo:**
```
15 Enero 2025 - Aumenté precio de $50 a $60 por inflación
20 Enero 2025 - Configuré precios especiales Carnaval
1 Febrero 2025 - Cambié mapa de 40 a 50 asientos
```

---
---

# Resumen Final del Sistema

## Flujo completo de vida de un servicio

```
1. CREAR SERVICIO
   ↓
   - Registra en BigQuery
   - Genera 3 meses de horarios iniciales
   - Calcula precios con reglas
   - Suma capacidades
   ↓
   SERVICIO LISTO PARA VENDER

2. GENERACIÓN AUTOMÁTICA DIARIA (00:00 hrs)
   ↓
   Cada día automáticamente:
   - Busca servicios activos
   - Calcula fecha +3 meses
   - Verifica si ya existe
   - Valida patrón de recurrencia
   - Genera horarios del día
   - Aplica reglas de precios
   ↓
   SIEMPRE 3 MESES DISPONIBLES

3. LIMPIEZA AUTOMÁTICA DIARIA (00:00 hrs)
   ↓
   En paralelo con generación:
   - Busca horarios expirados
   - Valida que no tengan ventas
   - Elimina en lotes de 500
   - Preserva horarios con historial
   ↓
   BASE DE DATOS LIMPIA Y OPTIMIZADA

4. ACTUALIZAR SERVICIO (cuando sea necesario)
   ↓
   ¿Qué cambió?

   a) Precios especiales por fecha
      → Actualiza solo esas fechas

   b) Precios base
      → Actualiza TODOS los horarios

   c) Mapa de asientos
      → Crea eventos para todos los horarios

   d) Registra en BigQuery
      → Guarda historial de cambios
   ↓
   HORARIOS ACTUALIZADOS
```

---

## Características del sistema

| Característica | Creación | Generación Diaria | Limpieza Diaria | Actualización |
|----------------|----------|-------------------|-----------------|---------------|
| **Operación principal** | Crear horarios | Crear horarios | Eliminar horarios | Modificar horarios |
| **Horarios afectados** | Hasta 720 (3 meses) | 1 día (+3 meses) | Expirados sin ventas | Todos existentes |
| **Frecuencia** | Una vez | Diaria (00:00) | Diaria (00:00) | Cuando actualices |
| **Precios calculados** | Automático | Automático | N/A | Recalcula todos |
| **Capacidad** | Suma de quotas | Suma de quotas | N/A | Actualiza si cambia |
| **Mapa de asientos** | Crea eventos | Crea eventos | N/A | Actualiza eventos |
| **Registro** | BigQuery | En logs | En logs | BigQuery |
| **Tiempo** | < 1 minuto | ~10 segundos | ~5 segundos | < 1 minuto |
| **Ventas existentes** | N/A | N/A | Protegidas | Protegidas |
| **Validaciones** | Recurrencia | Duplicados + Patrón | 2 condiciones | Cambios detectados |

---

## Ventajas del sistema automatizado

### 🚀 Velocidad
- Crea/actualiza cientos de horarios en segundos
- No más edición manual uno por uno

### 🎯 Precisión
- Aplica reglas correctamente
- No hay errores humanos
- Cálculos exactos siempre

### 🔒 Seguridad
- Protege ventas existentes
- No modifica boletos vendidos
- Mantiene integridad de datos

### 📊 Trazabilidad
- Todo se registra en BigQuery
- Historial completo de cambios
- Auditorías facilitadas

### 🔄 Flexibilidad
- Actualiza cuando quieras
- Cambios globales o específicos
- Reversible (creando nueva configuración)

---

## Tu trabajo vs Trabajo del sistema

### 👤 Tu trabajo:
1. Configurar el servicio correctamente
2. Definir precios y capacidades
3. Establecer reglas de temporada
4. Configurar fechas especiales
5. Decidir cuándo actualizar

### 🤖 Trabajo del sistema:
1. Generar horarios iniciales (3 meses)
2. Generar 1 día nuevo cada medianoche
3. Calcular todos los precios con reglas
4. Aplicar reglas estacionales y sobrescrituras
5. Actualizar automáticamente cuando cambies algo
6. Registrar todo el historial en BigQuery
7. Proteger las ventas existentes
8. Mantener siempre 3 meses disponibles
9. Evitar duplicados y mantener consistencia

---

---
---

# 8. Búsqueda de Horarios (searchSchedules)

## ¿Qué es searchSchedules?

Esta es la función que usan los **clientes finales** (en el frontend de compra) para buscar horarios disponibles cuando quieren comprar boletos. Es la puerta de entrada para que los usuarios vean qué opciones tienen para viajar.

**Importante:** Esta función está en el backend de buyer (frontend de clientes), NO en el backend de administración.

---

## ¿Cómo funciona?

### 🔍 Búsqueda inteligente en 3 pasos

Cuando un cliente busca horarios, el sistema:

1. **Busca horarios reales** guardados en la base de datos
2. **Genera horarios virtuales** basados en la recurrencia del servicio
3. **Combina ambos** y devuelve el resultado final

---

## El proceso paso a paso

### 1️⃣ Recibir criterios de búsqueda

**¿Qué información recibe?**
```javascript
{
  serviceId: "ABC123",        // Opcional: buscar un servicio específico
  plannerId: "XYZ789",        // ID del operador
  dateEpoch: 1762624800,      // Fecha de búsqueda (en epoch)
  origin: "caracas",          // Ciudad origen
  destination: "valencia",    // Ciudad destino
  subdomain: "hugofun"        // Sitio web desde donde buscan
}
```

**Ejemplo real:**
Un cliente busca:
- Viaje de Caracas a Valencia
- Fecha: 15 septiembre 2025
- En el sitio hugofun.com

---

### 2️⃣ Buscar servicios que coincidan

**¿Qué hace?**
Busca en la base de datos todos los servicios que:
- Van del origen al destino especificado
- Pertenecen al operador (plannerId)
- Están activos y disponibles para ese sitio web

**Ejemplo:**
```
Búsqueda: Caracas → Valencia

Servicios encontrados:
✅ "Express Caracas-Valencia" (4 horarios diarios)
✅ "Premium Caracas-Valencia" (2 horarios diarios)
❌ "Tour Los Roques" (no coincide destino)
❌ "Express Valencia-Caracas" (dirección opuesta)

Total servicios encontrados: 2
```

---

### 3️⃣ Filtrar por sitio web (subdomain)

**¿Qué hace?**
Verifica qué servicios están configurados para mostrarse en ese sitio específico.

**Ejemplo:**
```
Sitio: hugofun.com
Servicios configurados en este sitio:
- Express Caracas-Valencia
- Tour Los Roques

De los 2 servicios encontrados en paso anterior:
✅ "Express Caracas-Valencia" (está en hugofun)
❌ "Premium Caracas-Valencia" (solo en otro sitio)

Total después de filtro: 1 servicio
```

**¿Por qué?** Algunos operadores tienen múltiples sitios web y quieren mostrar diferentes servicios en cada uno.

---

### 4️⃣ Buscar horarios reales en base de datos

**¿Qué hace?**
Busca en la colección `schedules` todos los horarios:
- De los servicios encontrados
- Que salgan en la fecha solicitada (00:00 a 23:59)

**Ejemplo:**
```
Fecha: 15 septiembre 2025
Servicio: "Express Caracas-Valencia"

Horarios encontrados en BD:
1. Salida: 15 sept, 6:00am - Adulto: $50, Disponible: 30/40
2. Salida: 15 sept, 10:00am - Adulto: $60, Disponible: 25/40
3. Salida: 15 sept, 3:00pm - Adulto: $50, Disponible: 40/40
4. Salida: 15 sept, 8:00pm - Adulto: $50, Disponible: 35/40

Total horarios reales: 4
```

---

### 5️⃣ Generar horarios virtuales

**¿Qué son los horarios virtuales?**
Son horarios que **no están guardados en la base de datos** pero se generan dinámicamente basándose en la configuración de recurrencia del servicio.

**¿Por qué existen?**
- **Ahorro de espacio:** No se guardan horarios que quizás nunca se vendan
- **Flexibilidad:** Se pueden generar horarios on-demand
- **Backup:** Si por alguna razón no se creó el horario en BD, se genera virtualmente

**¿Cómo funcionan?**

El sistema lee la configuración de recurrencia del servicio:
```javascript
{
  recurrence: {
    frequency: "DAILY",
    times: ["06:00", "10:00", "15:00", "20:00"],
    startDate: 1735689600,
    endDate: 1767225600
  }
}
```

Y genera horarios para esa fecha:
```
Si la fecha 15 sept cae dentro del rango de recurrencia:
✅ Genera horarios virtuales: 6am, 10am, 3pm, 8pm
Con precios y capacidades del servicio base
```

**Ejemplo:**
```
Servicio: "Express Caracas-Valencia"
Fecha solicitada: 15 septiembre 2025

Horarios virtuales generados:
1. Salida: 15 sept, 6:00am - Adulto: $45, Disponible: 40/40
2. Salida: 15 sept, 10:00am - Adulto: $45, Disponible: 40/40
3. Salida: 15 sept, 3:00pm - Adulto: $45, Disponible: 40/40
4. Salida: 15 sept, 8:00pm - Adulto: $45, Disponible: 40/40

Total horarios virtuales: 4
```

---

### 6️⃣ Combinar horarios reales y virtuales

**Regla de prioridad:** Los horarios reales SIEMPRE tienen prioridad sobre los virtuales.

**¿Cómo se combinan?**

1. **Si existe horario real para una hora específica:** Usa el real, descarta el virtual
2. **Si NO existe horario real:** Usa el virtual

**Ejemplo:**

**Horarios reales (de BD):**
- 6:00am → $50, 30/40 disponibles (con ventas)
- 10:00am → $60, 25/40 disponibles (precio especial)

**Horarios virtuales (generados):**
- 6:00am → $45, 40/40 disponibles
- 10:00am → $45, 40/40 disponibles
- 3:00pm → $45, 40/40 disponibles
- 8:00pm → $45, 40/40 disponibles

**Resultado combinado:**
1. ✅ 6:00am → $50, 30/40 (REAL - tiene ventas)
2. ✅ 10:00am → $60, 25/40 (REAL - precio especial)
3. ✅ 3:00pm → $45, 40/40 (VIRTUAL - no hay real)
4. ✅ 8:00pm → $45, 40/40 (VIRTUAL - no hay real)

Total mostrado al cliente: 4 horarios

---

### 7️⃣ Mapear ventas a cada precio

**¿Qué hace?**
Para cada horario, agrega cuántos boletos se han vendido de cada tipo de precio.

**Ejemplo:**

**Servicio tiene registro de ventas:**
```javascript
sold: {
  "precio-adulto-id": 10,  // 10 adultos vendidos
  "precio-nino-id": 5      // 5 niños vendidos
}
```

**Horario antes de mapear:**
```javascript
{
  departure: 1762635600,
  priceTiers: [
    { id: "precio-adulto-id", price: 50, quota: 40 },
    { id: "precio-nino-id", price: 30, quota: 10 }
  ]
}
```

**Horario después de mapear:**
```javascript
{
  departure: 1762635600,
  priceTiers: [
    { id: "precio-adulto-id", price: 50, quota: 40, sold: 10 }, // ← Agregado
    { id: "precio-nino-id", price: 30, quota: 10, sold: 5 }     // ← Agregado
  ]
}
```

**¿Para qué?** El frontend puede calcular:
- Disponibles = quota - sold
- Adultos disponibles = 40 - 10 = 30
- Niños disponibles = 10 - 5 = 5

---

### 8️⃣ Ordenar por hora de salida

**¿Qué hace?**
Ordena todos los horarios de más temprano a más tarde.

**Ejemplo:**
```
Antes de ordenar:
- 3:00pm
- 6:00am
- 8:00pm
- 10:00am

Después de ordenar:
- 6:00am
- 10:00am
- 3:00pm
- 8:00pm
```

---

### 9️⃣ Devolver resultado final

**¿Qué recibe el cliente?**

Un array de horarios listos para mostrar:

```javascript
[
  {
    id: "schedule-123",
    serviceId: "ABC123",
    departure: 1762635600,  // 15 sept, 6:00am
    arrival: 1762646400,     // 15 sept, 9:00am
    priceTiers: [
      {
        id: "tier-adulto",
        name: "Adulto",
        price: 50,
        quota: 40,
        sold: 10,
        // disponibles: 30 (calculado en frontend)
      },
      {
        id: "tier-nino",
        name: "Niño",
        price: 30,
        quota: 10,
        sold: 5,
        // disponibles: 5
      }
    ]
  },
  // ... más horarios
]
```

---

## Casos especiales

### 🎯 Caso 1: No hay horarios reales NI virtuales

**Situación:**
```
Fecha: 15 septiembre 2025
Servicio: "Tour Fines de Semana" (solo sábados/domingos)
15 septiembre es martes
```

**Resultado:**
```javascript
[]  // Array vacío
```

**Mensaje al usuario:** "No hay horarios disponibles para esta fecha"

---

### 🎯 Caso 2: Solo horarios virtuales

**Situación:**
```
Fecha: 15 septiembre 2025
Servicio recién creado, aún no se generaron horarios en BD
Pero tiene recurrencia configurada
```

**Resultado:**
```
Se devuelven solo los horarios virtuales generados dinámicamente
El cliente ve opciones aunque no estén en BD
```

**Ventaja:** El servicio funciona inmediatamente, sin esperar a la generación automática.

---

### 🎯 Caso 3: Solo horarios reales

**Situación:**
```
Servicio NO tiene configuración de recurrencia
Solo tiene horarios creados manualmente en BD
```

**Resultado:**
```
Se devuelven solo los horarios de BD
No se generan virtuales
```

---

### 🎯 Caso 4: Múltiples servicios en la misma ruta

**Situación:**
```
Búsqueda: Caracas → Valencia, 15 sept

Servicios encontrados:
1. "Express Económico" (4 horarios)
2. "Express Premium" (2 horarios)
3. "VIP Ejecutivo" (1 horario)
```

**Resultado:**
```
Total: 7 horarios mezclados de los 3 servicios
Ordenados por hora de salida
El cliente elige el que prefiera
```

---

## Ejemplo completo de búsqueda

### 📱 El cliente busca:
```
Origen: Caracas
Destino: Valencia
Fecha: 15 septiembre 2025
Sitio: hugofun.com
```

### 🔍 Paso 1: Buscar servicios
```
Servicios Caracas → Valencia:
✅ Express Económico
✅ Express Premium
❌ VIP Ejecutivo (no está en hugofun.com)

Total: 2 servicios
```

### 📅 Paso 2: Buscar horarios reales en BD
```
Express Económico (15 sept):
- 6:00am → 35/40 disponibles
- 3:00pm → 20/40 disponibles

Express Premium (15 sept):
- 10:00am → 15/20 disponibles

Total horarios reales: 3
```

### 🔄 Paso 3: Generar horarios virtuales
```
Express Económico tiene configuración:
- Horarios: 6am, 10am, 3pm, 8pm

Horarios virtuales:
- 6:00am → 40/40 disponibles (ignorado, ya existe real)
- 10:00am → 40/40 disponibles (nuevo)
- 3:00pm → 40/40 disponibles (ignorado, ya existe real)
- 8:00pm → 40/40 disponibles (nuevo)

Express Premium tiene configuración:
- Horarios: 10am, 6pm

Horarios virtuales:
- 10:00am → 20/20 disponibles (ignorado, ya existe real)
- 6:00pm → 20/20 disponibles (nuevo)

Total horarios virtuales agregados: 3
```

### 🎯 Paso 4: Resultado combinado
```
Horarios mostrados al cliente (ordenados):

1. 6:00am - Express Económico - $45 - 35 disponibles
2. 10:00am - Express Económico - $45 - 40 disponibles (virtual)
3. 10:00am - Express Premium - $80 - 15 disponibles
4. 3:00pm - Express Económico - $45 - 20 disponibles
5. 6:00pm - Express Premium - $80 - 20 disponibles (virtual)
6. 8:00pm - Express Económico - $45 - 40 disponibles (virtual)

Total: 6 opciones
```

---

## Ventajas de este sistema híbrido (real + virtual)

### ✅ Siempre hay disponibilidad
Aunque no se hayan generado horarios en BD, los virtuales garantizan opciones.

### ✅ Ahorro de espacio
No necesitas guardar TODOS los horarios de TODO el año en BD.

### ✅ Precios actualizados
Los horarios virtuales usan la configuración actual del servicio.

### ✅ Flexibilidad
Puedes tener horarios especiales (reales) y horarios regulares (virtuales).

### ✅ Backup automático
Si falla la generación automática, los virtuales funcionan como respaldo.

---

## Diferencias clave: Real vs Virtual

| Aspecto | Horario Real | Horario Virtual |
|---------|--------------|-----------------|
| **Origen** | Base de datos | Generado on-demand |
| **Precios** | Pueden tener sobrescrituras | Usan precio base |
| **Ventas** | Tienen registro de sold | Siempre sold = 0 |
| **Disponibilidad** | quota - sold | quota completa |
| **Prioridad** | ALTA (se usa siempre) | BAJA (solo si no hay real) |
| **Personalización** | Puede ser único | Sigue patrón de recurrencia |
| **Performance** | Más rápido (ya existe) | Requiere cálculo |

---

## Resumen

La función `searchSchedules`:

1. 🔍 **Busca** servicios que coincidan con origen/destino
2. 🌐 **Filtra** por sitio web (subdomain)
3. 📊 **Obtiene** horarios reales de BD
4. 🔄 **Genera** horarios virtuales según recurrencia
5. 🎯 **Prioriza** horarios reales sobre virtuales
6. 🔢 **Mapea** ventas a cada precio
7. ⬆️ **Ordena** por hora de salida
8. 📤 **Devuelve** lista completa al cliente

**Ventaja principal:** El cliente SIEMPRE ve opciones, incluso si no hay horarios guardados en BD.

**Ubicación:** `/functions/src/entities/services/services.list.js` en el proyecto h4f-backend-buyer-js

---
---

# 9. Servicios Maestros (Master Services)

## ¿Qué son los Servicios Maestros?

Los Servicios Maestros permiten **vincular múltiples servicios que comparten el mismo recurso físico** (como un bus, avión o barco) para que al reservar un asiento en uno, ese asiento quede **automáticamente bloqueado en todos los servicios vinculados**.

---

## Concepto Simplificado

Cuando varios servicios usan el mismo vehículo físico, al reservar un asiento en uno, ese asiento queda bloqueado en TODOS los servicios vinculados.

### 🚌 Ejemplo: Bus con 45 asientos que hace 3 rutas

**Servicios del mismo bus:**
- **SS→LA** (San Salvador → La Antigua)
- **SS→PET** (San Salvador → Peten)
- **SS→GUA** (San Salvador → Guatemala directo)

**Escenario:**
Si alguien reserva el asiento **A5** en el servicio **SS→LA**:

| Servicio | Estado del Asiento A5 |
|----------|----------------------|
| SS→LA | ✅ OCUPADO (comprado) |
| SS→PET | ❌ BLOQUEADO |
| SS→GUA | ❌ BLOQUEADO |

**¿Por qué?** Porque físicamente es el mismo bus. Si la persona A compró A5 para ir de San Salvador a La Antigua, nadie más puede usar ese asiento en ningún tramo del viaje.

---

## ⚠️ Requisitos Importantes

### Los servicios vinculados DEBEN ser idénticos en:

| Requisito | Por qué es necesario |
|-----------|---------------------|
| **Mismas fechas** | El bloqueo aplica por horario/día específico |
| **Mismo mapa de asientos** | Los asientos deben coincidir exactamente |
| **Mismos horarios** | Para que el sistema sepa qué schedules vincular |

### ❌ Si los servicios NO coinciden:

```
Servicio A: Bus de 45 asientos, sale 8:00am
Servicio B: Bus de 50 asientos, sale 9:00am

❌ NO se puede vincular correctamente
❌ Los asientos no coinciden
❌ El aforo no se bloqueará correctamente
```

### ✅ Configuración correcta:

```
Servicio A (SS→LA): Bus de 45 asientos, sale 8:00am
Servicio B (SS→PET): Bus de 45 asientos, sale 8:00am
Servicio C (SS→GUA): Bus de 45 asientos, sale 8:00am

✅ Mismo mapa, mismas fechas
✅ El bloqueo funcionará correctamente
```

---

## Cómo Crear un Servicio Maestro

Para crear un servicio maestro desde el backoffice, debes configurar los siguientes campos:

### Campos a completar:

| Campo | Descripción | Ejemplo |
|-------|-------------|---------|
| **Nombre** | Nombre descriptivo del servicio maestro | "Ruta Centroamérica Express" |
| **Código** | Código único para identificación interna | "CA-EXPRESS-001" |
| **Descripción** | Explicación del servicio (opcional) | "Servicio maestro para la ruta CA" |
| **Estado** | Activo o inactivo | Activo ✅ |

---

### Paso 1: Seleccionar los Servicios Vinculados

En la sección **"Servicios Vinculados"** o **"Master Priority"**, debes seleccionar todos los servicios que comparten el mismo vehículo físico.

**Ejemplo:**
Selecciona los 3 servicios que usa el mismo bus:
1. ✅ San Salvador → La Antigua
2. ✅ La Antigua → Guatemala
3. ✅ San Salvador → Guatemala (directo)

**Importante:** El orden en que los selecciones indica la prioridad. Normalmente es el orden geográfico de la ruta.

---

### Paso 2: Configurar el Mapeo de Precios (Price Map)

El **mapeo de precios** es donde relacionas los precios equivalentes de cada servicio. Esto le dice al sistema: "cuando vendan un boleto de Adulto en el servicio A, bloquea también el Adulto de los servicios B y C".

**¿Cómo se hace?**

Para cada tipo de boleto que tengas, debes:

1. **Dar un nombre al grupo** (ej: "Adulto", "Niño", "VIP")
2. **Seleccionar el precio correspondiente de cada servicio**

**Ejemplo de configuración:**

| Nombre del Grupo | Servicio SS→LA | Servicio LA→GUA | Servicio SS→GUA |
|-----------------|----------------|-----------------|-----------------|
| **Adulto** | Adulto ($50) | Adulto ($35) | Adulto ($75) |
| **Niño** | Niño ($30) | Niño ($20) | Niño ($45) |
| **VIP** | VIP ($100) | VIP ($70) | VIP ($150) |

**¿Por qué es importante?**
Cuando alguien compra un boleto "Adulto" en SS→LA, el sistema automáticamente sabe que debe bloquear el precio "Adulto" de LA→GUA y SS→GUA, porque están mapeados juntos.

---

### Paso 3: Guardar

Al guardar, el sistema:
- Crea el servicio maestro
- Vincula los servicios seleccionados
- Genera IDs automáticos para cada grupo de precios
- Activa el bloqueo automático de asientos

---

## Cómo Actualizar un Servicio Maestro

### Actualización Parcial

Al editar un servicio maestro, **solo se actualizan los campos que modifiques**. Los demás campos mantienen sus valores originales.

**Ejemplos:**

- **Cambiar solo el nombre:** Los servicios vinculados y el mapeo de precios se mantienen igual
- **Agregar un nuevo servicio:** Los demás servicios siguen vinculados, solo se agrega el nuevo
- **Agregar un nuevo tipo de precio:** Los precios existentes se mantienen, solo se agrega el nuevo grupo

---

### Agregar un Nuevo Precio al Mapeo

Si agregas un nuevo tipo de boleto a los servicios (por ejemplo "Tercera Edad"), debes:

1. Ir al servicio maestro
2. En la sección de mapeo de precios, agregar un nuevo grupo
3. Seleccionar el precio "Tercera Edad" de cada servicio vinculado
4. Guardar

El sistema generará automáticamente un ID para el nuevo grupo.

---

### Agregar o Quitar Servicios Vinculados

Si necesitas agregar o quitar un servicio del grupo:

1. Ir al servicio maestro
2. En la sección de servicios vinculados, agregar o quitar el servicio
3. **Importante:** Si agregas un servicio, también debes actualizar el mapeo de precios para incluir los precios del nuevo servicio
4. Guardar

---

## Cómo Ver el Aforo de los Servicios

Al consultar el detalle de un servicio maestro, puedes ver información detallada de cada precio mapeado:

| Información | Descripción |
|-------------|-------------|
| **Nombre del servicio** | De qué servicio viene el precio |
| **Precio** | Valor del boleto |
| **Capacidad total** | Cuántos asientos tiene |
| **Vendidos** | Cuántos se han vendido |
| **Disponibles** | Capacidad - Vendidos |

**Ejemplo de visualización:**

**Grupo: Adulto**
| Servicio | Precio | Capacidad | Vendidos | Disponibles |
|----------|--------|-----------|----------|-------------|
| SS→LA | $50 | 45 | 12 | 33 |
| LA→GUA | $35 | 45 | 8 | 37 |
| SS→GUA | $75 | 45 | 20 | 25 |

Esto te permite ver de un vistazo cuántos asientos quedan en cada tramo.

---

## Comportamiento del Bloqueo de Asientos

### ⚠️ Versión Actual: Bloqueo Completo de Ruta

En la versión actual, cuando se reserva un asiento:
- El asiento queda **BLOQUEADO en TODA la ruta** hasta el destino final
- NO se libera en tramos intermedios

### Ejemplo:

**Ruta completa del bus:** San Salvador → La Antigua → Guatemala

**Escenario:** Pasajero compra SS→LA (primer tramo) en el asiento A5

| Servicio | Estado del Asiento A5 |
|----------|----------------------|
| SS→LA | ✅ OCUPADO (el pasajero viaja aquí) |
| LA→GUA | ❌ BLOQUEADO (aunque el pasajero se bajó) |
| SS→GUA | ❌ BLOQUEADO (ruta completa) |

**¿Por qué no se libera en LA→GUA?**
En esta versión, el sistema bloquea toda la ruta por simplicidad y seguridad. Esto evita problemas de overbooking pero puede reducir el aprovechamiento del bus.

### 🔜 Versión Futura: Liberación por Tramos

En una próxima versión se implementará:
- Cuando un pasajero se baja, el asiento se libera para el siguiente tramo
- Mayor aprovechamiento de capacidad
- Lógica más compleja de reservas

**Ejemplo futuro:** Pasajero compra SS→LA en asiento A5

| Servicio | Estado del Asiento A5 |
|----------|----------------------|
| SS→LA | ✅ OCUPADO |
| LA→GUA | ✅ LIBRE (se libera cuando el pasajero se baja) |
| SS→GUA | ❌ BLOQUEADO (ruta completa sigue bloqueada) |

---

## Casos de Uso Comunes

### 🚌 Caso 1: Ruta de bus con paradas

**Situación:** Bus de 45 asientos que hace:
- Maracaibo → Caracas (directo)
- Maracaibo → Valencia → Caracas

**Configuración:** Vinculas los 3 servicios:
1. Maracaibo → Caracas (directo)
2. Maracaibo → Valencia
3. Valencia → Caracas

**Resultado:** Si alguien compra Maracaibo→Caracas directo en asiento 15B, ese asiento queda bloqueado también en los servicios por tramos.

---

### ✈️ Caso 2: Vuelo con conexión

**Situación:** Avión que hace:
- Caracas → Panamá
- Panamá → Miami
- Caracas → Miami (directo, mismo avión)

**Configuración:** Vinculas los 3 servicios en el orden de la ruta.

**Resultado:** Los asientos se bloquean correctamente entre los tres servicios.

---

### 🚢 Caso 3: Ferry con escalas

**Situación:** Ferry de 200 pasajeros:
- Puerto La Cruz → Margarita
- Margarita → Los Roques
- Puerto La Cruz → Los Roques (directo)

**Configuración:** Vinculas los 3 servicios y mapeas los precios correspondientes.

**Resultado:** Los asientos se bloquean en toda la ruta del ferry.

---

## Mejores Prácticas

### ✅ Qué SÍ hacer

1. **Verificar que los servicios sean idénticos**
   - Mismo número de asientos
   - Mismas fechas de operación
   - Mismos horarios

2. **Usar nombres descriptivos en los grupos de precios**
   - Nombres claros como "Adulto", "Niño", "VIP Ejecutivo"
   - Evitar nombres genéricos como "Precio 1", "Precio 2"

3. **Mantener el orden correcto de los servicios vinculados**
   - El orden indica la prioridad y normalmente sigue la geografía de la ruta
   - Útil para reportes y gestión

4. **Documentar la configuración**
   - Anota qué precios de cada servicio están mapeados juntos
   - Facilita actualizaciones futuras

---

### ❌ Qué NO hacer

1. **No vincular servicios con diferente capacidad**
   - Si un servicio tiene 45 asientos y otro tiene 50, el bloqueo NO funcionará correctamente
   - Todos deben tener el mismo número de asientos

2. **No vincular servicios con diferente mapa de asientos**
   - Si usan mapas diferentes, los asientos no coincidirán
   - Por ejemplo, el asiento "A5" de un mapa puede no existir en otro

3. **No olvidar actualizar el mapeo de precios**
   - Si agregas un nuevo tipo de boleto en los servicios hijos
   - Debes también agregar ese grupo al mapeo de precios del maestro

---

## Preguntas Frecuentes

### ❓ ¿Qué pasa si selecciono un servicio que no existe?

**Respuesta:** El sistema no lo valida estrictamente. Simplemente no encontrará horarios para ese servicio. Asegúrate de seleccionar servicios que existen y están activos.

---

### ❓ ¿Puedo agregar un servicio a múltiples maestros?

**Respuesta:** Técnicamente sí, pero **NO es recomendable**. Un servicio debe pertenecer a un solo servicio maestro para evitar conflictos de bloqueo.

---

### ❓ ¿El bloqueo es en tiempo real?

**Respuesta:** Sí. Cuando se hace una reserva, el asiento se bloquea inmediatamente en todos los servicios vinculados del mismo maestro.

---

### ❓ ¿Cómo sé cuántos asientos quedan disponibles?

**Respuesta:** En el detalle del servicio maestro puedes ver para cada grupo de precios:
- Capacidad total de cada servicio
- Vendidos
- Disponibles (Capacidad - Vendidos)

---

### ❓ ¿Puedo desactivar temporalmente un servicio maestro?

**Respuesta:** Sí, simplemente cambia el estado a "Inactivo" en la edición del servicio maestro.

---

## Resumen

Los Servicios Maestros permiten:

1. 🔗 **Vincular** múltiples servicios que comparten el mismo recurso físico
2. 🪑 **Bloquear** asientos automáticamente en todos los servicios vinculados
3. 💰 **Mapear** precios equivalentes entre servicios
4. 📊 **Consultar** aforo de todos los servicios desde un solo lugar
5. 🔄 **Actualizar** parcialmente solo los campos que necesites

**Requisitos clave:**
- ✅ Mismas fechas de operación
- ✅ Mismo mapa de asientos
- ✅ Mismos horarios

**Limitación actual:**
- ⚠️ El asiento queda bloqueado en toda la ruta (no se libera por tramos)
- 🔜 Liberación por tramos vendrá en una versión futura

---
---

## Soporte y preguntas

Si tienes dudas sobre:
- ❓ Cómo configurar algo específico
- 🐛 Un comportamiento inesperado
- 💡 Una funcionalidad nueva que necesitas

Contacta al equipo técnico con:
- Nombre del servicio
- Qué intentaste hacer
- Qué esperabas que pasara
- Qué pasó realmente

**El sistema está diseñado para hacerte la vida más fácil. Úsalo con confianza.**
