# 📊 ANÁLISIS COMPLETO DE ARQUITECTURA
## Repositorio: pbip-demo-agentic
**Objetivo**: Transformar un modelo de ventas MinorAuto → Analytics Agentic para Seguros de Vida

---

## 1. ARQUITECTURA ACTUAL

### 1.1 Visión General
Este es un **template/framework agentico** basado en Power BI Project (PBIP) diseñado para demostrar cómo GitHub Copilot puede construir modelos semánticos automáticamente.

**Características clave:**
- Paradigma **Agentic Development**: Define requirements → Agent genera spec → Agent implementa
- Formato **Power BI Project (PBIP)** con archivos TMDL (Tabular Model Definition Language)
- Sistema de **validación automática** mediante Best Practice Analysis (BPA)
- **Knowledge base** para guiar los agentes de IA
- Modelo de ejemplo: Análisis de ventas retail (AdventureWorks)

### 1.2 Estructura de Carpetas

```
/pbip-demo-agentic
├── .requirements/              ← Especificaciones de negocio
│   ├── requirements-01.md      ← User stories y requisitos funcionales
│   └── AzureSQLSchema.csv      ← Esquema de datos referencia
│
├── .resources/                 ← Assets reutilizables
│   ├── kb-pbip.md             ← Knowledge base para agentes
│   ├── pbip-sample/           ← Ejemplo de estructura PBIP
│   ├── tmdl/
│   │   ├── Time Intelligence.tmdl    ← Métricas de tiempo genéricas
│   │   └── Calendar.tmdl             ← Tabla de calendario reutilizable
│   └── bpa-rules*.json        ← Reglas de validación
│
├── .bpa/                       ← Best Practice Analysis
│   ├── bpa.ps1               ← Script de validación
│   └── bpa-rules-*.json      ← Reglas semantic model y report
│
└── (src/)                      ← Donde va el modelo desarrollado
```

### 1.3 Componentes del Modelo de Ventas (Actual)

**Tablas Dimensionales:**
- `Product` (Productos: nombre, código, categoría, subcategoría, marca, costo, precio)
- `Store` (Locales: ubicación, información regional)
- `Calendar` (Tiempo: fecha, mes, trimestre, año, día semana)

**Tablas de Hechos:**
- `Sales` (Ventas: orden, línea, cantidad, precio neto, costo, fecha pedido/entrega)

**Medidas (Métrica Retail):**
- Sales Amount, Margin, Margin %, Cost
- Sales Amount (LY), Margin (LY)
- Sales Amount (12M average), Sales Amount (6M average)
- # Customers, # Products, # Sales

**Relaciones:**
- Sales → Product (ProductKey)
- Sales → Store (StoreKey)
- Sales → Calendar (Order Date)

**Roles de Seguridad:**
- Store - Canada, Store - United States

---

## 2. ANÁLISIS DE COMPONENTES: GENÉRICOS vs. ESPECÍFICOS

### 2.1 Componentes GENÉRICOS (100% Reutilizable)

#### ✅ **Infraestructura PBIP**
| Componente | Ubicación | Reutilización |
|-----------|-----------|---------------|
| Estructura PBIP | .resources/pbip-sample | Copiar estructura exacta para nuevo modelo |
| Naming conventions | kb-pbip.md | Aplicar reglas de nombres (singular dim, plural fact, lowercase) |
| TMDL formatting rules | kb-pbip.md | Reglas syntax relaciones, comentarios, medidas multi-línea |
| BPA validation script | .bpa/ | Reutilizar directamente sin cambios |

#### ✅ **Tabla Calendar (con ajustes mínimos)**
```tmdl
table calendar
  column date (dateTime) - Clave primaria
  column year, quarter, month, week, day of week
  column day name, month name, etc.
```
**Reutilizable:** SÍ
**Cambios necesarios:** Solo agregar columnas específicas para seguros (ej: cohort de póliza, año de suscripción)

#### ✅ **Patrón de Medidas Time Intelligence**
Uso de SAMEPERIODLASTYEAR, PREVIOUSMONTH, DATESINPERIOD → Transferible a cualquier dominio

**Ejemplos genéricos:**
```dax
[Measure (LY)] = CALCULATE([Measure], SAMEPERIODLASTYEAR('Calendar'[Date]))
[Measure YoY Growth %] = DIVIDE([Measure] - [Measure (LY)], [Measure (LY)])
```

#### ✅ **Data Source Patterns**
- Uso de **parámetros** para server/database (expressions.tmdl)
- Importación de CSV desde Web.Contents
- Power Query transformations reutilizables (promoción de headers, cambio de tipos, filtros)

#### ✅ **Relaciones y Modelado Star Schema**
- Patrón relación muchos-uno clara
- Uso de surrogate keys (ProductKey, StoreKey, CustomerKey) → Aplicable a Pólizas
- Distinción entre relaciones activas/inactivas

#### ✅ **Roles de Seguridad**
Estructura de Row-Level Security aplicable a cualquier segmentación de datos

---

### 2.2 Componentes ESPECÍFICOS DEL CASO RETAIL (Requieren Rediseño)

#### ❌ **Tablas Dimensionales**
| Tabla | Razón específica | Reemplazo necesario |
|-------|-----------------|-------------------|
| `Product` | Categorías de retail (Brand, Color, Weight) | `LifeInsuranceProduct` (Tipo, Suma asegurada, TAC) |
| `Store` | Ubicación geográfica de locales | `Agency`, `Region`, `Branch` (Sucursal aseguradora) |

#### ❌ **Tabla de Hechos**
| Campo | Razón específica | Reemplazo |
|-------|-----------------|-----------|
| `Sales[Order Number]` | Transacción de venta | `Policies[PolicyNumber]` |
| `Sales[Order Date/Delivery Date]` | Ciclo compra | `Policies[IssueDate, EffectiveDate, MaturityDate]` |
| `Sales[Quantity/Unit Price]` | Unidades × precio | `Policies[Premium, Frequency, Coverage]` |

#### ❌ **Medidas de Negocio**
| Métrica Retail | Métrica Seguros (Nueva) |
|----------------|----------------------|
| Sales Amount | Premium Income |
| Margin % | Loss Ratio |
| # Customers | # Active Policies |
| Cost | Claims Amount |

---

## 3. PROPUESTA DE ARQUITECTURA NUEVA (Seguros de Vida)

### 3.1 Diagrama Conceptual

```
┌─────────────────────────────────────────────────────┐
│           INSURANCE ANALYTICS AGENTIC               │
│        Base: pbip-demo-agentic (reutilizado)       │
└─────────────────────────────────────────────────────┘

CAPA DE DATOS
├── Fact Tables (Eventos de póliza)
│   ├── policies (Pólizas emitidas)
│   ├── claims (Siniestros)
│   └── premiums (Primas pagadas)
│
└── Dimension Tables (Contexto)
    ├── product (Tipos: Individual, Colectivo, Temporal, Universal, AP)
    ├── customer (Asegurados)
    ├── agency (Canales de venta)
    ├── underwriter (Suscriptores)
    └── calendar (Tiempo)

CAPA DE INTELIGENCIA
├── Time Intelligence Measures
│   ├── Premium Income (Mensual, Anual, Acumulado)
│   ├── Claims Amount
│   └── Loss Ratio
│
├── Retention & Persistency Measures
│   ├── Retention Rate (Políticas renovadas/total)
│   ├── Persistency Rate (Políticas sin cancelación)
│   └── Cancellation Rate
│
└── Performance Measures
    ├── Issued Policies Count
    ├── Average Premium
    ├── Customer Lifetime Value
    └── KPI Dashboard Metrics

CAPA DE PRESENTACIÓN
├── Executive Dashboard
│   ├── KPI Cards (Pólizas, Prima promedio, Siniestros)
│   ├── Premium Income Trend
│   ├── Product Mix Breakdown
│   └── Retention Cohort Analysis
│
└── Operational Views
    ├── Underwriting Performance
    ├── Claims Analysis
    └── Agency Rankings
```

### 3.2 Nuevas Tablas

#### **Dimensiones**

**product** (Productos de Seguros)
```tmdl
table product
  column productKey (int) [Key]
  column productName (string): Vida Individual, Vida Colectivo, etc.
  column productType (string)
  column coverageType (string): Individual, Group, etc.
  column description (string)
  column isActive (boolean)
```

**customer** (Asegurados)
```tmdl
table customer
  column customerKey (int) [Key]
  column customerCode (string): ID único asegurado
  column customerName (string)
  column documentType (string): RUT, CÉDULA, PASAPORTE
  column documentNumber (string)
  column age (int)
  column gender (string)
  column maritalStatus (string)
  column occupation (string)
  column createdDate (date)
  column tier (string): Premium, Standard, Basic
```

**agency** (Canales de Venta)
```tmdl
table agency
  column agencyKey (int) [Key]
  column agencyName (string)
  column agencyType (string): Directo, Broker, Empleado, Partner
  column region (string)
  column branch (string)
  column manager (string)
  column commissionRate (decimal)
  column status (string): Active, Inactive
```

**underwriter** (Suscriptores)
```tmdl
table underwriter
  column underwriterKey (int) [Key]
  column underwriterName (string)
  column department (string): Individual, Colectivo, Claims
  column experience (int): años en compañía
  column activeRiskAmount (decimal): monto en riesgo bajo su responsabilidad
```

#### **Hechos**

**policies** (Pólizas - Principal)
```tmdl
table policies
  column policyKey (int) [Fact]
  column policyNumber (string): ID transaccional
  column productKey (int): FK → product
  column customerKey (int): FK → customer
  column agencyKey (int): FK → agency
  column underwriterKey (int): FK → underwriter
  column issueDate (date): Fecha emisión
  column effectiveDate (date): Fecha vigencia
  column expiryDate (date): Fecha vencimiento
  column maturityDate (date): Fecha plazo
  column renewalDate (date): Fecha próxima renovación
  column sumAssured (decimal): Suma asegurada
  column annualPremium (decimal): Prima anual
  column premiumFrequency (string): Mensual, Trimestral, Anual
  column paymentMethod (string): Débito automático, Boleta, etc.
  column status (string): Active, Expired, Cancelled, Suspended
  column cancellationDate (date): Si está cancelada
  column cancellationReason (string): Por qué se canceló
  column underwritingDecision (string): Approved, Conditional, Rejected
  column medicalRequired (boolean)
  column commissionAmount (decimal)
```

**claims** (Siniestros)
```tmdl
table claims
  column claimKey (int) [Fact]
  column claimNumber (string)
  column policyKey (int): FK → policies
  column claimDate (date): Fecha de ocurrencia
  column reportedDate (date): Fecha de reporte
  column claimType (string): Muerte, Invalidez, Accidente, etc.
  column claimAmount (decimal): Monto reclamado
  column paidAmount (decimal): Monto pagado
  column status (string): Reported, Under Review, Approved, Denied, Paid
  column approvalDate (date)
  column paymentDate (date)
  column deductible (decimal)
```

**premiums** (Primas Pagadas - Movimientos)
```tmdl
table premiums
  column premiumKey (int) [Fact]
  column policyKey (int): FK → policies
  column premiumDate (date): Fecha pago
  column dueDate (date)
  column amount (decimal)
  column status (string): Paid, Overdue, Pending, Partial
  column paymentMethod (string)
  column frequency (string): Mensual, Anual, etc.
  column sequenceNumber (int): Número de cuota
```

---

## 4. CAMBIOS POR ARCHIVO/CARPETA

### 4.1 `.requirements/` → Especificaciones Nuevas

#### Archivo: `requirements-01.md` (REEMPLAZAR)
**Cambios:**
```markdown
# Business Requirements for Life Insurance Analytics

## Requirement IDs:

INS-001: Issued Policies Overview
- User Story: Como gerente de seguros, quiero ver pólizas emitidas 
  por producto y período
- Expected: Measure [Issued Policies] con desglose por tipo

INS-002: Average Premium Analysis
- User Story: Como analista, quiero conocer prima promedio por 
  producto y segmento
- Expected: [Average Premium] = AVERAGE(policies[AnnualPremium])

INS-003: Retention & Persistency
- User Story: Como director, quiero medir retención y persistencia
- Expected: 
  - [Retention Rate] = Pólizas renovadas / Total pólizas
  - [Persistency Rate] = Pólizas activas sin cancelación

INS-004: Loss Ratio Analysis
- User Story: Como CFO, quiero monitorear ratio de pérdida
- Expected: [Loss Ratio] = Claims Amount / Premium Income

INS-005: Claims Monitoring
- User Story: Como analista de siniestros, ver actividad reciente
- Expected: Count claims, sum paid amounts, status breakdown

INS-006: Product Performance Dashboard
- Products: Vida Individual, Vida Colectivo, Vida Temporal, 
  Vida Universal, Accidentes Personales
```

#### Archivo: `AzureSQLSchema.csv` (REEMPLAZAR)
**Cambios:**
- Agregar esquema de tablas seguros: policies, claims, premiums, 
  product, customer, agency, underwriter
- Mapeos de columnas específicas del dominio

### 4.2 `.resources/` → Assets Reutilizables

#### Mantenerse igual (100% reutilizable):
✅ `kb-pbip.md` - No requiere cambios
✅ `.bpa/` - Scripts de validación genéricos
✅ `tmdl/Time Intelligence.tmdl` - Patrón genérico (adaptar si necesario)
✅ `tmdl/Calendar.tmdl` - Tabla base (agregar columnas seguros si es necesario)

#### Archivos nuevos a agregar:
📝 `pbip-templates/LifeInsuranceModel-Template.md` - Descripción de modelo nuevo
📝 `sql/LifeInsuranceSchema.sql` - Script DDL tablas seguros
📝 `kb-insurance-domain.md` - Knowledge base específico seguros

### 4.3 `/src/` → Modelo Semántico

#### Estructura de carpetas:

```
/src
├── /LifeInsuranceAnalytics.SemanticModel
│   ├── /definition
│   │   ├── /tables
│   │   │   ├── product.tmdl          [Dimensión]
│   │   │   ├── customer.tmdl         [Dimensión]
│   │   │   ├── agency.tmdl           [Dimensión]
│   │   │   ├── underwriter.tmdl      [Dimensión]
│   │   │   ├── calendar.tmdl         [Dimensión - Reutilizada]
│   │   │   ├── policies.tmdl         [Hecho - Principal]
│   │   │   ├── claims.tmdl           [Hecho - Secundaria]
│   │   │   └── premiums.tmdl         [Hecho - Secundaria]
│   │   ├── relationships.tmdl        [Todas las relaciones]
│   │   ├── model.tmdl                [Definición modelo]
│   │   └── expressions.tmdl          [Parámetros]
│   ├── definition.pbism              [Metadatos PBIP]
│   └── [Roles de seguridad]
│
├── /LifeInsuranceAnalytics.Report
│   └── definition.pbir               [Reporte vacío]
│
└── LifeInsuranceAnalytics.pbip       [Entrada PBIP]
```

---

## 5. MAPEO ESPECÍFICO DE CAMBIOS

### 5.1 De Retail a Seguros: Transformación de Medidas

| Métrica Retail | DAX Retail | Nueva Métrica Seguros | DAX Seguros |
|---|---|---|---|
| Sales Amount | SUM(Sales[Net Price]) | Premium Income | SUM(policies[AnnualPremium]) |
| # Customers | DISTINCTCOUNT(Sales[CustomerKey]) | # Customers | DISTINCTCOUNT(policies[CustomerKey]) |
| # Products (w/ Sales) | DISTINCTCOUNT(Sales[ProductKey]) | # Active Products | COUNTROWS(FILTER(product, [Issued Policies]>0)) |
| Cost | SUM(Sales[Unit Cost]) | Claims Amount | SUM(claims[PaidAmount]) |
| Margin % | [Margin]/[Sales Amount] | Loss Ratio | [Claims Amount]/[Premium Income] |
| - | - | Retention Rate | DIVIDE(COUNTROWS(FILTER(policies, DATEDIFF([ExpiryDate], TODAY(), DAY) <= 0 AND [Status]="Renewed")), COUNTROWS(FILTER(policies, [RenewalDate]<=TODAY()))) |

### 5.2 Relaciones Estructurales

**Antes (Retail):**
```
Sales → Product (muchos-uno)
Sales → Store (muchos-uno)
Sales → Customer (implícita)
Sales → Calendar (Order Date)
```

**Después (Seguros):**
```
policies → product (muchos-uno)
policies → customer (muchos-uno)
policies → agency (muchos-uno)
policies → underwriter (muchos-uno)
policies → calendar (issue date, effective, expiry)

claims → policies (muchos-uno)
claims → calendar (claim date, reported date)

premiums → policies (muchos-uno)
premiums → calendar (premium date, due date)
```

---

## 6. RIESGOS TÉCNICOS

### 6.1 Riesgos Críticos ⚠️

| # | Riesgo | Impacto | Mitigación |
|---|--------|--------|-----------|
| **R1** | **Múltiples relaciones calendario** - policies tiene 3+ fechas | Ambigüedad en filtro temporal | Usar `USERELATIONSHIP()` en medidas específicas o crear relaciones inactivas |
| **R2** | **Grain de hechos no consistente** - policies vs premiums | Sobre-conteos en agregaciones | Documentar grain explícitamente; usar `DISTINCT` si es necesario |
| **R3** | **Fórmulas complejas de retención** - lógica multicohort | DAX lento, errores de lógica | Pre-calcular en Power Query o usar particiones RefreshPolicy |
| **R4** | **Actualizaciones frecuentes de pólizas** - estado dinámico | Inconsistencia temporal | Usar import con particiones incrementales por fecha |

### 6.2 Riesgos Moderados

| # | Riesgo | Mitigación |
|---|--------|-----------|
| **R5** | Tamaño de datos: millions de registros | Agregación en Power Query, particiones, compression |
| **R6** | Seguridad: datos sensibles asegurados/siniestros | RLS por Agency/Region; auditoría; encriptación |
| **R7** | Cambios de reglas de negocio | Usar parámetros DAX; documentar decisiones en descripciones |

---

## 7. ROADMAP DE IMPLEMENTACIÓN INCREMENTAL

### **FASE 1: Foundation (Semana 1-2)**
**Objetivo:** Establecer estructura base reutilizable

- [ ] **P1.1** Crear carpetas `/src` con estructura PBIP
- [ ] **P1.2** Copiar `calendar.tmdl` y ajustar con columnas seguros
- [ ] **P1.3** Crear `model.tmdl` y `expressions.tmdl` (parámetros SQL)
- [ ] **P1.4** Crear tablas dimensionales: `product.tmdl`, `customer.tmdl`, `agency.tmdl`
- [ ] **P1.5** Crear archivo `relationships.tmdl` (vacío, se completará luego)
- [ ] **P1.6** Ejecutar BPA para validar estructura

**Deliverable:** ✅ Modelo vacío con dimensiones, pasando BPA

---

### **FASE 2: Core Fact Table & Relationships (Semana 2-3)**
**Objetivo:** Implementar tabla principal de pólizas y lógica relacional

- [ ] **P2.1** Crear `policies.tmdl` con todas las columnas
- [ ] **P2.2** Crear relaciones principales policies → dimensiones
- [ ] **P2.3** Crear medidas base: `[Issued Policies]`, `[Active Policies]`
- [ ] **P2.4** Crear relaciones de tiempo (Order Date, Expiry Date)
- [ ] **P2.5** Documentar múltiples relaciones al Calendar
- [ ] **P2.6** Ejecutar BPA y resolver errores

**Deliverable:** ✅ Tabla policies con medidas básicas funcionando

---

### **FASE 3: Time Intelligence & Analysis Measures (Semana 3)**
**Objetivo:** Implementar análisis temporal y métricas de seguros

- [ ] **P3.1** Crear medidas YoY/MoM: `[Premium Income]`, `[Premium Income (LY)]`
- [ ] **P3.2** Crear medidas de agregación: `[Average Premium]`, `[Total Customers]`
- [ ] **P3.3** Crear medidas de retención: `[Retention Rate]`, `[Persistency Rate]`
- [ ] **P3.4** Crear medidas de comparación: `[Retention Rate (LY)]`
- [ ] **P3.5** Validar con BPA
- [ ] **P3.6** Documento internal de fórmulas DAX

**Deliverable:** ✅ Medidas de análisis temporal operativas

---

### **FASE 4: Secondary Fact Tables & Claims (Semana 4)**
**Objetivo:** Agregar análisis de siniestros

- [ ] **P4.1** Crear `claims.tmdl` con datos de siniestros
- [ ] **P4.2** Crear `premiums.tmdl` para movimientos
- [ ] **P4.3** Crear relaciones claims → policies, premiums → policies
- [ ] **P4.4** Crear medidas: `[Claims Amount]`, `[Loss Ratio]`, `[Paid Claims]`
- [ ] **P4.5** Crear medidas derivadas: `[Claim Frequency]`, `[Avg Claim Amount]`
- [ ] **P4.6** BPA y resolución de problemas

**Deliverable:** ✅ Modelo completo con 3 fact tables

---

### **FASE 5: Seguridad & Refinamiento (Semana 4-5)**
**Objetivo:** Roles, seguridad de datos, optimización

- [ ] **P5.1** Crear roles RLS: `Agency Manager`, `Regional Manager`, `Underwriter`
- [ ] **P5.2** Implementar permisos por agency/region
- [ ] **P5.3** Optimizar tamaños: eliminar columnas no usadas, comprimir
- [ ] **P5.4** Crear reglas BPA adicionales para seguros
- [ ] **P5.5** Documentación técnica del modelo
- [ ] **P5.6** Test de performance: # records, query speed

**Deliverable:** ✅ Modelo asegurado con roles aplicados

---

### **FASE 6: Reporting & Dashboards (Semana 5-6)**
**Objetivo:** Crear reportes ejecutivos

- [ ] **P6.1** Crear dashboard KPI executivo (emitidas, prima, retención)
- [ ] **P6.2** Crear análisis por producto (producto mix, premium por tipo)
- [ ] **P6.3** Crear dashboard siniestros (claims, ratio, frequency)
- [ ] **P6.4** Crear análisis de agencia (performance, comisiones)
- [ ] **P6.5** Pruebas de UI/UX
- [ ] **P6.6** Documentación para usuarios finales

**Deliverable:** ✅ Suite de reportes lista para demo

---

### **FASE 7: Demo & Entrega (Semana 6)**
**Objetivo:** Publicar e iterar

- [ ] **P7.1** Publicar modelo a Power BI Service
- [ ] **P7.2** Configurar refresh schedule
- [ ] **P7.3** Presentación ejecutiva
- [ ] **P7.4** Feedback loop y ajustes

**Deliverable:** ✅ Solución en producción

---

## 8. RIESGOS Y CONSIDERACIONES

### 8.1 Decisiones Arquitectónicas

| Decisión | Alternativa | Justificación |
|----------|-----------|---------------|
| **1 tabla policies** vs múltiples | Separar "nuevas" y "renovadas" | Simplifica relaciones; soportar renovaciones con columna status |
| **Import mode** para todas | DirectQuery / Dual | Mejor performance, seguridad, datos simulados |
| **Calendar centralizado** | Local en cada tabla | Reutilización; patrón PBIP |
| **Medidas en fact tables** | Medidas centralizadas | Mejor performance, mantenibilidad |

### 8.2 Datos Simulados vs. Reales

El modelo debe ser capaz de:
- Leer CSV simulados (como el ejemplo actual)
- Conectarse a SQL Server real (cuando esté disponible)
- Mantener coherencia: 1M policies, 10M premiums, 100K claims realista

---

## 9. COMPONENTES PARA REUTILIZAR EN SIGUIENTE VERSIÓN

Una vez completada esta solución de Seguros, la arquitectura puede extenderse a:

- **Otros sectores:** Salud, Pensiones, Fianzas, Seguros Generales
- **Componentes genéricos nuevos a crear:**
  - Librería de medidas de retención/persistencia
  - Patrones de cohorte analytics
  - Frameworks de Loss Ratio (actuarial)
  - Modelos de churn prediction preparación para ML

---

## CONCLUSIÓN

**La arquitectura pbip-demo-agentic es 100% reutilizable como base.** 

**Lo que cambia:**
- Tablas dimensionales (dimensiones comerciales)
- Tabla de hechos principal (eventos de negocio)
- Medidas de negocio (KPIs de dominio)

**Lo que se mantiene:**
- Estructura PBIP y TMDL
- Patrones de relaciones
- Time intelligence patterns
- Validación BPA
- Workflow agentico

**Esfuerzo estimado:** 4-6 semanas para modelo completo + reportes

