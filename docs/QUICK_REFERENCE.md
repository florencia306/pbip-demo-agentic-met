# ⚡ QUICK REFERENCE GUIDE

## En 60 segundos...

### ¿Qué es pbip-demo-agentic?
**Framework agentico** de Power BI que permite GitHub Copilot construir modelos semánticos automáticamente.

### ¿Cómo transformarlo a Seguros?
**3 pasos:**
1. Reemplazar requirements (2 archivos)
2. Crear modelo en `/src` (8 archivos TMDL)
3. Ejecutar GitHub Copilot para implementar

### ¿Cuánto tiempo?
**4-6 semanas** (o 200 horas con Copilot)

### ¿Reutilización?
**95%** del framework es genérico y reutilizable

---

## Tabla Rápida: Retail → Seguros

| Elemento | Retail | Seguros | % Cambio |
|----------|--------|---------|----------|
| **Estructura PBIP** | ✓ | ✓ Igual | 0% |
| **TMDL Rules** | ✓ | ✓ Igual | 0% |
| **BPA Validation** | ✓ | ✓ Igual | 0% |
| **Calendar Table** | ✓ | ✓ +10% | 10% |
| **Star Schema** | ✓ | ✓ Igual | 0% |
| **Time Intelligence** | ✓ | ✓ Igual | 0% |
| **Dimensiones** | Store, Product | Agency, Product*, Customer, Underwriter | 75% nuevo |
| **Hechos** | Sales | Policies, Claims, Premiums | 100% nuevo |
| **Medidas** | Sales Amount, Margin | Premium Income, Loss Ratio | 100% nuevo |

*Product se adapta (Brand → Type), no se reemplaza

---

## Checklist Rápido (3 Fases)

### ✅ BEFORE (Preparación)
- [ ] Acceso a GitHub Copilot Pro (Claude Sonnet 4)
- [ ] Aprobación de stakeholders
- [ ] Datos o CSV simulados disponibles

### ✅ PHASE 1: FOUNDATION (Semana 1-2)
- [ ] Reemplazar requirements-01.md
- [ ] Crear kb-insurance-domain.md
- [ ] Crear carpeta /src + 8 tablas TMDL
- [ ] Ejecutar BPA ✓

### ✅ PHASE 2-4: CORE (Semana 2-4)
- [ ] Implementar medidas (40+ DAX)
- [ ] Crear relaciones (12+ relationships)
- [ ] Validar data con BPA iterativamente

### ✅ PHASE 5-7: FINALIZATION (Semana 4-6)
- [ ] Crear 3-4 roles RLS
- [ ] Optimizar performance
- [ ] Crear 6 reportes Power BI
- [ ] Demo + Publicar

---

## Tablas que vas a Crear

### Dimensiones (5)

| Tabla | Propósito | Filas estimadas |
|-------|-----------|-----------------|
| **product** | 5 tipos de seguros | 5 |
| **customer** | Asegurados | 50,000 |
| **agency** | Canales de venta | 100 |
| **underwriter** | Suscriptores | 50 |
| **calendar** | Tiempo (reutilizado + extendido) | 1,826 (5 años) |

### Hechos (3)

| Tabla | Propósito | Filas estimadas |
|-------|-----------|-----------------|
| **policies** | Pólizas emitidas | 200,000 |
| **claims** | Siniestros | 8,000 (4% ratio) |
| **premiums** | Primas pagadas | 2,400,000 (12 cuotas × activas) |

---

## Medidas Principales (Ejemplos)

```dax
[Issued Policies] = COUNTROWS('policies')
[Premium Income] = SUM('policies'[annualPremium])
[Loss Ratio] = [Claims Amount Paid] / [Premium Income]
[Retention Rate] = Renewed Policies / Eligible for Renewal
[Persistency Rate] = 1 - Cancellation Rate
```

---

## Relaciones Clave

```
policies ─→ product
policies ─→ customer
policies ─→ agency
policies ─→ underwriter
policies ─→ calendar (IssueDate) [PRIMARY]
policies ─→ calendar (ExpiryDate) [INACTIVE]

claims ─→ policies
claims ─→ calendar (ClaimDate)

premiums ─→ policies
premiums ─→ calendar (PremiumDate)
```

---

## KPIs en Dashboard (6 reportes)

| Reporte | Audiencia | Medidas clave |
|---------|-----------|---------------|
| **KPI Executive** | CFO, CEO | Issued Policies, Premium Income, Loss Ratio |
| **Product Mix** | Product Manager | % Premium by Type, Retention by Product |
| **Claims Analysis** | Claims Manager | Claims Reported, Paid, Approval Rate |
| **Agency Rankings** | Sales Manager | Agencies by Premium, Retention, Commissions |
| **Retention Cohort** | Analyst | Cohort retention curves by issue month |
| **Loss Trending** | Actuary | Loss ratio trends, seasonal patterns |

---

## Riesgos Importantes (4)

| # | Riesgo | Solución |
|---|--------|----------|
| 1 | Múltiples fechas en policies | USERELATIONSHIP() + relaciones inactivas |
| 2 | Lógica retención compleja | Pre-calcular o particiones |
| 3 | Estado dinámico de pólizas | Usar Import mode + refresh incremental |
| 4 | Seguridad de datos | RLS por Agency/Region |

---

## Componentes NO Tocar (Reutilizable 100%)

```
✅ .requirements/requirements-01.md  ← REEMPLAZAR (nuevo contenido)
✅ .resources/kb-pbip.md             ← SIN CAMBIOS
✅ .resources/pbip-sample/           ← SIN CAMBIOS (referencia)
✅ .bpa/                             ← SIN CAMBIOS
✅ .resources/tmdl/Time Intelligence.tmdl  ← SIN CAMBIOS
✅ README.md, LICENSE, .gitignore    ← SIN CAMBIOS

🔄 .resources/tmdl/Calendar.tmdl     ← EXTENDER (agregar cohort dates)
✨ /src/                             ← CREAR (nuevo modelo)
```

---

## Comando Copilot Recomendado

```
Adjunta estos 5 documentos al chat:
1. EXECUTIVE_SUMMARY.md
2. TECHNICAL_SPECIFICATIONS.md
3. VISUAL_MAPPING.md
4. IMPLEMENTATION_CHECKLIST.md
5. Este README.md

Luego ejecuta el prompt:

"Based on the attached documentation, please:
1. Create a comprehensive development specification for Phase 1: Foundation
2. Generate TMDL code for product.tmdl, customer.tmdl, agency.tmdl, underwriter.tmdl
3. Generate the relationships.tmdl file with all 12 relationships
4. Validate using BPA best practices

I want the output ready to save directly into the /src folder."
```

---

## Validación Rápida (BPA Script)

Después de crear el modelo:

```powershell
.\.bpa\bpa.ps1 -src ./src/LifeInsuranceAnalytics.SemanticModel

# Debe retornar: 0 critical errors, 0 warnings
# Si hay errores, Copilot puede fijarlos automáticamente
```

---

## Hitos de Progreso

| Hito | Semana | Status |
|------|--------|--------|
| Foundation (5 dims + structure) | 1-2 | ✓ |
| Core policies table | 2-3 | ✓ |
| Time intelligence measures | 3 | ✓ |
| Claims + Premiums tables | 4 | ✓ |
| Security + Optimization | 4-5 | ✓ |
| Reporting (6 dashboards) | 5-6 | ✓ |
| **Demo + Production** | **6** | **✓** |

---

## Respuestas Rápidas a Preguntas

**P: ¿Cuántas líneas de TMDL?**  
A: ~2,500-3,000 líneas (manejable, bien generado por Copilot)

**P: ¿Compatibilidad con Power BI Desktop?**  
A: Sí, 100% compatible. Abre directamente en Desktop.

**P: ¿Se pueden agregar reportes después?**  
A: Sí, el modelo es extensible. Agregar nuevas medidas es trivial.

**P: ¿Performance con 200K policies?**  
A: Excelente (~50-100ms queries), Import mode + compression

**P: ¿Qué pasa si datos cambian en BD?**  
A: Configura Refresh Schedule automático (diario/semanal)

**P: ¿Puedo usar SQL Server en lugar de CSV?**  
A: Sí, cambiar en expressions.tmdl: Parameter_SqlServer

---

## Diferencias Clave: Retail vs Seguros

```
RETAIL METRICS          →    INSURANCE METRICS
════════════════════════════════════════════════════
Sales Amount            →    Premium Income
Cost                    →    Claims Amount Paid
Margin %                →    Loss Ratio (inverse logic!)
# Customers             →    # Active Policyholders
Avg Sale Price          →    Avg Premium per Policy
(No existe)             →    Retention Rate
(No existe)             →    Persistency Rate
(No existe)             →    Cancellation Rate
(No existe)             →    Loss Ratio Trend
(No existe)             →    Claim Approval Rate
```

---

## Configuración Mínima Power BI Service (Después)

```yaml
Semantic Model:
  Name: LifeInsuranceAnalytics
  Refresh:
    Frequency: Daily
    Time: 2:00 AM
    Type: Full
  
Roles:
  - Regional Manager (por Region)
  - Agency Manager (por Agency)
  - Executive (sin filtro)

Reports:
  - 6 reportes paginados
  - Acceso por Role
  - Push notifications habilitadas
```

---

## Estimaciones Finales

| Métrica | Valor |
|---------|-------|
| **Tiempo estimado** | 4-6 semanas |
| **Horas totales** | 200 horas |
| **% Reutilización** | 95% |
| **Riesgo técnico** | BAJO |
| **Calidad de código** | ALTA (Copilot) |
| **Documentación** | 90 KB |
| **Archivos a crear** | 13 nuevos |
| **Archivos a modificar** | 3 existentes |
| **Archivos sin cambios** | 20+ |

---

## ¿LISTO?

**Próximo paso:**
1. Comparte este análisis (5 documentos) con stakeholders
2. Obtén aprobación ✓
3. Delega a Copilot con IMPLEMENTATION_CHECKLIST.md
4. Comienza Phase 1

**Tiempo hasta Demo:** 4-6 semanas  
**Calidad esperada:** Profesional, listo para producción  
**Valor:** Demo de Agentic Analytics aplicado a Seguros

---

**Análisis completo:** 90 KB documentación  
**Estado:** ✅ Listo para implementación  
**Próximo archivo:** Comienza con `EXECUTIVE_SUMMARY.md`

