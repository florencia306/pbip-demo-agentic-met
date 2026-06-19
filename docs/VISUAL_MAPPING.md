# 🎯 VISUAL MAPPING: RETAIL → SEGUROS

## Diagrama de Transformación Completa

```
╔════════════════════════════════════════════════════════════════════════════════╗
║                     REUTILIZACIÓN DE ARQUITECTURA                             ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  PBIP FRAMEWORK (100% Reutilizable)                                          ║
║  ├── Estructura PBIP (.resources/pbip-sample/)                               ║
║  ├── TMDL Language Rules (kb-pbip.md)                                        ║
║  ├── BPA Validation Scripts (.bpa/)                                          ║
║  ├── Time Intelligence Patterns                                              ║
║  └── Agentic Development Workflow                                            ║
║                                                                                ║
║  ✅ MANTENER INTACTO - NO REQUIERE CAMBIOS                                   ║
║                                                                                ║
╚════════════════════════════════════════════════════════════════════════════════╝

╔════════════════════════════════════════════════════════════════════════════════╗
║                        TRANSFORMACIÓN DE DATOS                                ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  MODELO RETAIL (AdventureWorks)         →    MODELO SEGUROS (Vida)          ║
║  ═══════════════════════════════════════      ════════════════════════════   ║
║                                                                                ║
║  DIMENSIONES:                                 DIMENSIONES:                    ║
║  ├─ Store (Tienda física)         ──→        ├─ Agency (Canal de venta)     ║
║  │  • Location, Region                       │  • Type, Region, Branch       ║
║  │  • Manager, Status                        │  • Manager, Commission        ║
║  │                                           │                                ║
║  ├─ Product (Artículos retail)    ──→        ├─ Product (Productos seguros) ║
║  │  • Name, Category, Subcategory            │  • Type (5), Coverage         ║
║  │  • Brand, Color, Weight                   │  • SumAssured, TAC %          ║
║  │  • UnitPrice, UnitCost                    │  • AgeRange, MaxSumAssured    ║
║  │                                           │                                ║
║  ├─ DimCustomer (Clientes)       ──→        ├─ Customer (Asegurados)       ║
║  │  • Name, Country, City                    │  • Name, Age, Document        ║
║  │  • (Implícito en Sales)                   │  • Gender, Occupation         ║
║  │                                           │  • RiskProfile, Tier          ║
║  │                                           │                                ║
║  └─ Calendar (Tiempo)             ──→        ├─ Calendar (Tiempo)           ║
║     • Date, Month, Quarter, Year             │  • Date, Month, Quarter, Year ║
║     • DayOfWeek, etc.                        │  • DayOfWeek (igual)          ║
║     (Reutilizable, igual schema)             │  • CohortKey (nuevo)          ║
║                                              │                                ║
║                                              └─ Underwriter (Suscriptores)  ║
║                                                 • Name, Department           ║
║                                                 • Specialization             ║
║                                                 • MaxApprovalAmount          ║
║                                                                                ║
║  HECHOS:                                      HECHOS:                         ║
║  ├─ Sales (Transacciones)         ──→        ├─ Policies (Pólizas)          ║
║  │  • OrderNumber, OrderDate                  │  • PolicyNumber, IssueDate   ║
║  │  • Quantity, NetPrice, UnitCost            │  • SumAssured, AnnualPremium  ║
║  │  • Keys: ProductKey, StoreKey              │  • Status, Dates (Múltiples) ║
║  │                                            │  • Keys: ProductKey, CustKey  ║
║  │  TRANSFORMACIÓN:                           │                                ║
║  │  ✓ OrderNumber → PolicyNumber              │  NEW MEASURES:               ║
║  │  ✓ OrderDate → IssueDate                   │  ✓ Issued Policies          ║
║  │  ✓ Quantity → (desaparecer)                │  ✓ Premium Income           ║
║  │  ✓ NetPrice → AnnualPremium                │  ✓ Persistency Rate         ║
║  │  ✓ StoreKey → AgencyKey (nueva dim)        │  ✓ Retention Rate           ║
║  │  ✓ Margin % → Loss Ratio                   │  ✓ Cancellation Rate        ║
║  │                                            │                                ║
║  └─ (Ninguno)                     ──→        ├─ Claims (Siniestros)        ║
║                                              │  • ClaimNumber, ClaimDate   ║
║                                              │  • ClaimAmount, PaidAmount  ║
║                                              │  • Status, ApprovalDate     ║
║                                              │  • NEW MEASURES:             ║
║                                              │    - Claims Amount Paid     ║
║                                              │    - Loss Ratio             ║
║                                              │    - Claim Approval Rate    ║
║                                              │                              ║
║                                              └─ Premiums (Cobranza)       ║
║                                                 • PremiumDate, DueDate    ║
║                                                 • Amount, Status          ║
║                                                 • SequenceNumber          ║
║                                                 • NEW MEASURES:            ║
║                                                   - Premium Payment Rate   ║
║                                                   - Premium Overdue        ║
║                                                                              ║
╚════════════════════════════════════════════════════════════════════════════════╝

╔════════════════════════════════════════════════════════════════════════════════╗
║                      MEDIDAS: RETAIL → SEGUROS                                ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  RETAIL METRICS              │  SEGUROS METRICS                              ║
║  ════════════════════════════╪══════════════════════════════════════════    ║
║  Sales Amount                │  Premium Income ✓                             ║
║  Sales Amount (LY)           │  Premium Income (LY) ✓                        ║
║  Sales YoY Growth %          │  Premium Income YoY Growth % ✓                ║
║  Sales MoM Growth %          │  Premium Income MoM Growth % ✓                ║
║  # Customers (w/ Sales)      │  # Customers (active) ✓                       ║
║  # Products (w/ Sales)       │  # Products (with policies) ✓                 ║
║  Cost                        │  Claims Amount Paid ✗ (Nuevo concepto)       ║
║  Margin %                    │  Loss Ratio ✗ (Nuevo concepto)               ║
║  Avg Sales per Day           │  Avg Premium per Policy ✓                     ║
║  ═════════════════════════════════════════════════════════════════════════  ║
║  (No existe)                 │  Retention Rate ✓ (Nueva métrica)             ║
║  (No existe)                 │  Persistency Rate ✓ (Nueva métrica)           ║
║  (No existe)                 │  Cancellation Rate ✓ (Nueva métrica)          ║
║  (No existe)                 │  Claims Reported ✓ (Nueva métrica)            ║
║  (No existe)                 │  Claim Approval Rate ✓ (Nueva métrica)        ║
║  (No existe)                 │  Premium Payment Rate ✓ (Nueva métrica)       ║
║                                                                                ║
╚════════════════════════════════════════════════════════════════════════════════╝

╔════════════════════════════════════════════════════════════════════════════════╗
║                         MODELO ESTRELLA (STAR SCHEMA)                         ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║                                  CALENDAR                                     ║
║                                    │                                          ║
║                                    │ Date                                     ║
║                                    │                                          ║
║           ┌──────────────────────────┼──────────────────────────┐            ║
║           │                          │                          │            ║
║      IssueDate                ExpiryDate                  RenewalDate        ║
║           │                          │                          │            ║
║           │                          │ (inactive)              │ (inactive)  ║
║      ┌────▼──────────┐        ┌──────┴────────┐      ┌────────┴──────┐     ║
║      │   POLICIES    │        │ POLICIES      │      │ POLICIES      │     ║
║      │   (FACT)      │        │ (FACT)        │      │ (FACT)        │     ║
║      │               │        │               │      │               │     ║
║      │ PolicyKey     │        │ PolicyKey     │      │ PolicyKey     │     ║
║      │ PolicyNumber  │        │ ExpiryDate    │      │ RenewalDate   │     ║
║      │ IssueDate     │        │ Status        │      │ Status        │     ║
║      │ SumAssured    │        │ Measures:     │      │ Measures:     │     ║
║      │ AnnualPremium │        │               │      │               │     ║
║      │ Status        │        └───────────────┘      └───────────────┘     ║
║      │ Measures      │                                                      ║
║      │ ✓ Premium     │                                                      ║
║      │   Income      │                    ┌─ Premium Date                   ║
║      │ ✓ Issued      │                    │                                ║
║      │   Policies    │              ┌─────┴──────┐                         ║
║      └────┬──────────┘              │  PREMIUMS  │                         ║
║           │                          │  (FACT)    │                         ║
║      ProductKey                      │            │                         ║
║      CustomerKey    ─┐               │ PremiumKey │                         ║
║      AgencyKey       ├─────┐         │ PolicyKey  │                         ║
║      UnderwriterKey  ┘     │         │ Amount     │                         ║
║                            │         │ Status     │                         ║
║                            │         └────┬───────┘                         ║
║                            │              │                                 ║
║                      ┌─────▼─────┐   ┌────┴────┐                           ║
║                      │  PRODUCT  │   │ CALENDAR │                           ║
║                      │ (DIM)     │   │          │                           ║
║                      │           │   │ Date     │                           ║
║                      │ ProductKey│   │ Month    │                           ║
║                      │ Name      │   │ Year     │                           ║
║                      │ Type (5)  │   └──────────┘                           ║
║                      │ TAC %     │                                          ║
║                      └─────┬─────┘                                          ║
║                            │                                                ║
║                      ┌─────▼─────────┐                                      ║
║                      │   CUSTOMER    │                                      ║
║                      │  (DIM)        │                                      ║
║                      │               │                                      ║
║                      │ CustomerKey   │                                      ║
║                      │ Name          │                                      ║
║                      │ Age, Gender   │                                      ║
║                      │ RiskProfile   │                                      ║
║                      └───────────────┘                                      ║
║                                                                              ║
║                      ┌─────────────┐                                        ║
║                      │   AGENCY    │                                        ║
║                      │  (DIM)      │                                        ║
║                      │             │                                        ║
║                      │ AgencyKey   │                                        ║
║                      │ Name        │                                        ║
║                      │ Region      │                                        ║
║                      │ Type        │                                        ║
║                      └─────────────┘                                        ║
║                                                                              ║
║                      ┌─────────────┐       ┌────────────┐                   ║
║                      │ UNDERWRITER │       │   CLAIMS   │                   ║
║                      │   (DIM)     │       │   (FACT)   │                   ║
║                      │             │       │            │                   ║
║                      │ UWKey       │       │ ClaimKey   │                   ║
║                      │ Name        │       │ ClaimNumber│                   ║
║                      │ Department  │       │ PolicyKey  │ ◄────────┐        ║
║                      │ Experience  │       │ ClaimAmount│         │        ║
║                      └─────────────┘       │ PaidAmount │     FK  │        ║
║                                            │ Status     │         │        ║
║                                            └────┬───────┘         │        ║
║                                                 │                 │        ║
║                                            ┌────┴──────┐   ┌──────┴───┐   ║
║                                            │  CALENDAR  │   │ POLICIES  │   ║
║                                            │ ClaimDate  │   │ PolicyKey │   ║
║                                            └────────────┘   └───────────┘   ║
║                                                                              ║
╚════════════════════════════════════════════════════════════════════════════════╝

╔════════════════════════════════════════════════════════════════════════════════╗
║                    ARCHIVOS: ANTES vs DESPUÉS                                 ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  ARCHIVO                              │  ACCIÓN                              ║
║  ═════════════════════════════════════╪═══════════════════════════════════  ║
║  .gitignore                            │  ✅ Sin cambios                      ║
║  README.md                             │  ✅ Sin cambios (descripción genérica║
║  LICENSE                               │  ✅ Sin cambios                      ║
║  ═════════════════════════════════════════════════════════════════════════  ║
║  .requirements/requirements-01.md      │  🔄 REEMPLAZAR (nuevos requisitos)  ║
║  .requirements/AzureSQLSchema.csv      │  🔄 REEMPLAZAR (esquema seguros)    ║
║  ═════════════════════════════════════════════════════════════════════════  ║
║  .resources/kb-pbip.md                 │  ✅ Sin cambios (genérico)          ║
║  .resources/bpa-rules-*.json           │  ✅ Sin cambios (genérico)          ║
║  .bpa/bpa.ps1                          │  ✅ Sin cambios (genérico)          ║
║  .resources/tmdl/Calendar.tmdl         │  ➕ EXTENDER (agregar cohort cols)  ║
║  .resources/tmdl/Time Intelligence.tmdl│  ✅ Sin cambios (genérico)          ║
║  .resources/pbip-sample/              │  ✅ Sin cambios (referencia)        ║
║  ═════════════════════════════════════════════════════════════════════════  ║
║  /src/ (NUEVO - NO EXISTE EN RETAIL)                                        ║
║  └─ /LifeInsuranceAnalytics.SemanticModel/                                   ║
║     ├─ /definition/                                                          ║
║     │  ├─ /tables/                                                           ║
║     │  │  ├─ product.tmdl              │  ✨ CREAR (renombrar de Product)    ║
║     │  │  ├─ customer.tmdl             │  ✨ CREAR (nueva tabla)             ║
║     │  │  ├─ agency.tmdl               │  ✨ CREAR (cambiar de Store)        ║
║     │  │  ├─ underwriter.tmdl          │  ✨ CREAR (nueva tabla)             ║
║     │  │  ├─ calendar.tmdl             │  ✨ CREAR (copiar y extender)      ║
║     │  │  ├─ policies.tmdl             │  ✨ CREAR (transformar de Sales)    ║
║     │  │  ├─ claims.tmdl               │  ✨ CREAR (tabla nueva)             ║
║     │  │  └─ premiums.tmdl             │  ✨ CREAR (tabla nueva)             ║
║     │  ├─ relationships.tmdl           │  ✨ CREAR (todas nuevas relaciones) ║
║     │  ├─ model.tmdl                   │  ✨ CREAR (referencias modelo)      ║
║     │  └─ expressions.tmdl             │  ✨ CREAR (parámetros)              ║
║     ├─ definition.pbism                │  ✨ CREAR (metadata PBIP)           ║
║     └─ [Roles de seguridad]            │  ✨ CREAR (3-4 roles RLS)          ║
║  │                                                                            ║
║  ├─ /LifeInsuranceAnalytics.Report/                                         ║
║  │  └─ definition.pbir                 │  ✨ CREAR (reporte vacío)          ║
║  │                                                                            ║
║  └─ LifeInsuranceAnalytics.pbip        │  ✨ CREAR (entrada PBIP)           ║
║                                                                              ║
╚════════════════════════════════════════════════════════════════════════════════╝

╔════════════════════════════════════════════════════════════════════════════════╗
║                    DOCUMENTACIÓN NUEVA A CREAR                                ║
╠════════════════════════════════════════════════════════════════════════════════╣
║                                                                                ║
║  ARCHIVO                                    │  PROPÓSITO                     ║
║  ════════════════════════════════════════════╪═══════════════════════════════ ║
║  .resources/kb-insurance-domain.md          │  Domain knowledge específico   ║
║                                             │  - Terminología seguros       ║
║                                             │  - Ratios actuariales         ║
║                                             │  - Reglas de negocio          ║
║  ═══════════════════════════════════════════════════════════════════════════ ║
║  .resources/sql/InsuranceSchema.sql         │  Script DDL para BD real      ║
║  ═══════════════════════════════════════════════════════════════════════════ ║
║  .resources/samples/README.md               │  Guía datos simulados        ║
║  .resources/samples/sample-policies.csv     │  CSV ejemplo 1000 policies   ║
║  .resources/samples/sample-claims.csv       │  CSV ejemplo 400 claims      ║
║  ═══════════════════════════════════════════════════════════════════════════ ║
║  docs/DATA_DICTIONARY.md                    │  Diccionario de datos        ║
║  docs/DASHBOARD_GUIDE.md                    │  Guía de dashboards          ║
║  docs/SETUP_INSTRUCTIONS.md                 │  Cómo usar el modelo         ║
║                                                                              ║
╚════════════════════════════════════════════════════════════════════════════════╝
