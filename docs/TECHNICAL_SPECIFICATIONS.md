# 🔧 ESPECIFICACIONES TÉCNICAS DETALLADAS
## Transformación: Retail → Life Insurance Analytics

---

## 1. TABLAS Y ESQUEMA ESPECÍFICO

### 1.1 TABLA DIMENSIÓN: `product`

**Propósito:** Catálogo de productos de seguros de vida

**Definición TMDL:**
```tmdl
table product
    
    column productKey
        dataType: int64
        isHidden
        isKey
        formatString: 0
        summarizeBy: none
        sourceColumn: ProductKey
    
    column productName
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: ProductName
    
    column productCode
        dataType: string
        summarizeBy: none
        sourceColumn: ProductCode
    
    column productType
        dataType: string
        summarizeBy: none
        sourceColumn: ProductType
        // Vida Individual, Vida Colectivo, Vida Temporal, Vida Universal, Accidentes Personales
    
    column coverageType
        dataType: string
        summarizeBy: none
        sourceColumn: CoverageType
    
    column maxSumAssured
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: MaxSumAssured
    
    column tacPercentage
        dataType: decimal
        formatString: 0.00 %
        summarizeBy: none
        sourceColumn: TACPercentage
    
    column minimumAge
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: MinimumAge
    
    column maximumAge
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: MaximumAge
    
    column isActive
        dataType: boolean
        summarizeBy: none
        sourceColumn: IsActive
    
    column createdDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: CreatedDate
    
    measure '# Products' = COUNTROWS('product')
        formatString: #,##0
    
    measure '# Active Products' = 
        CALCULATE(
            [# Products],
            FILTER('product', 'product'[isActive] = TRUE())
        )
        formatString: #,##0
    
    // Partition definition omitted - CSV source
```

**Datos Simulados (CSV):**
```
ProductKey,ProductName,ProductCode,ProductType,CoverageType,MaxSumAssured,TACPercentage,MinimumAge,MaximumAge,IsActive,CreatedDate
1,Vida Individual Standard,VID-001,Vida Individual,Individual,100000000,5.5,18,65,TRUE,2020-01-15
2,Vida Individual Premium,VID-002,Vida Individual,Individual,500000000,4.5,25,75,TRUE,2020-02-01
3,Vida Colectivo Básico,VCO-001,Vida Colectivo,Group,1000000000,3.5,21,65,TRUE,2020-03-01
...
5,Accidentes Personales,AP-001,Accidentes Personales,Individual,50000000,6.0,16,70,TRUE,2020-05-01
```

---

### 1.2 TABLA DIMENSIÓN: `customer`

**Propósito:** Registro de asegurados

```tmdl
table customer
    
    column customerKey
        dataType: int64
        isHidden
        isKey
        formatString: 0
        summarizeBy: none
        sourceColumn: CustomerKey
    
    column customerCode
        dataType: string
        summarizeBy: none
        sourceColumn: CustomerCode
    
    column customerName
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: CustomerName
    
    column documentType
        dataType: string
        summarizeBy: none
        sourceColumn: DocumentType
        // RUT, CEDULA, PASAPORTE
    
    column documentNumber
        dataType: string
        isHidden
        summarizeBy: none
        sourceColumn: DocumentNumber
    
    column age
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: Age
    
    column gender
        dataType: string
        summarizeBy: none
        sourceColumn: Gender
        // M, F, O
    
    column maritalStatus
        dataType: string
        summarizeBy: none
        sourceColumn: MaritalStatus
    
    column occupation
        dataType: string
        summarizeBy: none
        sourceColumn: Occupation
    
    column riskProfileSegment
        dataType: string
        summarizeBy: none
        sourceColumn: RiskProfileSegment
        // Low, Medium, High
    
    column createdDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: CreatedDate
    
    measure '# Customers' = DISTINCTCOUNT('customer'[customerKey])
        formatString: #,##0
    
    measure '# Active Customers' = 
        CALCULATE(
            [# Customers],
            FILTER(
                VALUES('customer'[customerKey]),
                COUNTROWS(
                    FILTER('policies', 'policies'[status] = "Active")
                ) > 0
            )
        )
        formatString: #,##0
```

---

### 1.3 TABLA DIMENSIÓN: `agency`

**Propósito:** Canales de distribución y agencias

```tmdl
table agency
    
    column agencyKey
        dataType: int64
        isHidden
        isKey
        formatString: 0
        summarizeBy: none
        sourceColumn: AgencyKey
    
    column agencyName
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: AgencyName
    
    column agencyType
        dataType: string
        summarizeBy: none
        sourceColumn: AgencyType
        // Directo, Broker, Empleado, Partner
    
    column manager
        dataType: string
        summarizeBy: none
        sourceColumn: Manager
    
    column region
        dataType: string
        summarizeBy: none
        sourceColumn: Region
    
    column branch
        dataType: string
        summarizeBy: none
        sourceColumn: Branch
    
    column commissionRate
        dataType: decimal
        formatString: 0.00 %
        summarizeBy: none
        sourceColumn: CommissionRate
    
    column status
        dataType: string
        summarizeBy: none
        sourceColumn: Status
    
    measure '# Agencies' = COUNTROWS('agency')
        formatString: #,##0
    
    measure '# Active Agencies' = 
        CALCULATE([# Agencies], FILTER('agency', 'agency'[status] = "Active"))
        formatString: #,##0
```

---

### 1.4 TABLA DIMENSIÓN: `underwriter`

**Propósito:** Suscriptores (equipo de riesgos)

```tmdl
table underwriter
    
    column underwriterKey
        dataType: int64
        isHidden
        isKey
        formatString: 0
        summarizeBy: none
        sourceColumn: UnderwriterKey
    
    column underwriterName
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: UnderwriterName
    
    column department
        dataType: string
        summarizeBy: none
        sourceColumn: Department
        // Individual, Colectivo, Claims
    
    column experience
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: ExperienceYears
    
    column specialization
        dataType: string
        summarizeBy: none
        sourceColumn: Specialization
    
    column maxApprovalAmount
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: MaxApprovalAmount
    
    measure '# Underwriters' = COUNTROWS('underwriter')
        formatString: #,##0
```

---

### 1.5 TABLA HECHO: `policies` ⭐ (Principal)

**Propósito:** Registro maestro de pólizas emitidas

```tmdl
table policies
    
    // Keys
    column policyKey
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: PolicyKey
    
    column policyNumber
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: PolicyNumber
    
    // Foreign Keys
    column productKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: ProductKey
    
    column customerKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: CustomerKey
    
    column agencyKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: AgencyKey
    
    column underwriterKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: UnderwriterKey
    
    // Key Dates
    column issueDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: IssueDate
    
    column effectiveDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: EffectiveDate
    
    column expiryDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: ExpiryDate
    
    column maturityDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: MaturityDate
    
    column renewalDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: RenewalDate
    
    // Coverage Terms
    column sumAssured
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: SumAssured
    
    column annualPremium
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: sum
        sourceColumn: AnnualPremium
    
    column premiumFrequency
        dataType: string
        summarizeBy: none
        sourceColumn: PremiumFrequency
        // Mensual, Trimestral, Semestral, Anual
    
    column paymentMethod
        dataType: string
        summarizeBy: none
        sourceColumn: PaymentMethod
    
    // Status
    column status
        dataType: string
        summarizeBy: none
        sourceColumn: Status
        // Active, Expired, Cancelled, Suspended, Lapsed
    
    column cancellationDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: CancellationDate
    
    column cancellationReason
        dataType: string
        summarizeBy: none
        sourceColumn: CancellationReason
    
    // Underwriting
    column underwritingDecision
        dataType: string
        summarizeBy: none
        sourceColumn: UnderwritingDecision
        // Approved, Conditional, Rejected, Pending
    
    column medicalRequired
        dataType: boolean
        summarizeBy: none
        sourceColumn: MedicalRequired
    
    column riderCount
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: RiderCount
    
    column commissionAmount
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: CommissionAmount
    
    // Measures
    
    /// Total number of policies issued in the period
    measure 'Issued Policies' = COUNTROWS('policies')
        formatString: #,##0
    
    /// Number of active policies (not cancelled, not expired)
    measure 'Active Policies' = 
        CALCULATE(
            [Issued Policies],
            FILTER('policies', 'policies'[status] IN {"Active", "Suspended"})
        )
        formatString: #,##0
    
    /// Number of cancelled policies
    measure 'Cancelled Policies' = 
        CALCULATE(
            [Issued Policies],
            FILTER('policies', 'policies'[status] = "Cancelled")
        )
        formatString: #,##0
    
    /// Total premium income (annual basis)
    measure 'Premium Income' = SUM('policies'[annualPremium])
        formatString: $ #,##0
    
    /// Average premium per policy
    measure 'Average Premium' = 
        DIVIDE([Premium Income], [Issued Policies], 0)
        formatString: $ #,##0
    
    /// Average sum assured
    measure 'Average Sum Assured' = 
        DIVIDE(SUMX('policies', 'policies'[sumAssured]), [Issued Policies], 0)
        formatString: $ #,##0
    
    /// Premium Income compared to Last Year
    measure 'Premium Income (LY)' = 
        CALCULATE([Premium Income], SAMEPERIODLASTYEAR('calendar'[Date]))
        formatString: $ #,##0
    
    /// Year-over-Year Premium Growth
    measure 'Premium Income YoY Growth %' = 
        DIVIDE(
            [Premium Income] - [Premium Income (LY)],
            [Premium Income (LY)],
            0
        )
        formatString: 0.00 %
    
    /// Premium Income Month-over-Month Growth
    measure 'Premium Income MoM Growth %' = 
        DIVIDE(
            [Premium Income] - 
            CALCULATE([Premium Income], PREVIOUSMONTH('calendar'[Date])),
            CALCULATE([Premium Income], PREVIOUSMONTH('calendar'[Date])),
            0
        )
        formatString: 0.00 %
    
    /// Cancellation Rate = Cancelled / Issued
    measure 'Cancellation Rate' = 
        DIVIDE([Cancelled Policies], [Issued Policies], 0)
        formatString: 0.00 %
    
    /// Persistence Rate: policies remaining active (no cancellation)
    measure 'Persistency Rate' = 
        1 - [Cancellation Rate]
        formatString: 0.00 %
    
    /// Policies renewed in the period
    measure 'Renewed Policies' = 
        CALCULATE(
            [Issued Policies],
            FILTER('policies', 'policies'[renewalDate] <= TODAY())
        )
        formatString: #,##0
    
    /// Retention Rate: Renewed / Up for Renewal
    measure 'Retention Rate' = 
        VAR v_policyDueToRenew = CALCULATE([Issued Policies], 
            FILTER('policies', 'policies'[expiryDate] <= TODAY()))
        VAR v_policyRenewed = [Renewed Policies]
        RETURN
            DIVIDE(v_policyRenewed, v_policyDueToRenew, 0)
        formatString: 0.00 %
    
    /// Total commissions paid to agencies
    measure 'Total Commissions' = SUM('policies'[commissionAmount])
        formatString: $ #,##0
    
    /// Average commission per policy
    measure 'Average Commission' = 
        DIVIDE([Total Commissions], [Issued Policies], 0)
        formatString: $ #,##0
    
    /// Percentage of policies with medical requirement
    measure 'Medical Required Rate' = 
        CALCULATE(
            COUNTROWS(FILTER('policies', 'policies'[medicalRequired] = TRUE())),
            ALL('policies')
        ) / CALCULATE(COUNTROWS('policies'), ALL('policies'))
        formatString: 0.00 %
```

---

### 1.6 TABLA HECHO: `claims` (Secundaria)

**Propósito:** Registro de siniestros reportados

```tmdl
table claims
    
    column claimKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: ClaimKey
    
    column claimNumber
        dataType: string
        isDefaultLabel
        summarizeBy: none
        sourceColumn: ClaimNumber
    
    column policyKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: PolicyKey
    
    column claimDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: ClaimDate
    
    column reportedDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: ReportedDate
    
    column claimType
        dataType: string
        summarizeBy: none
        sourceColumn: ClaimType
        // Muerte, Invalidez, Accidente, Enfermedad, Otra
    
    column claimAmount
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: ClaimAmount
    
    column paidAmount
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: sum
        sourceColumn: PaidAmount
    
    column status
        dataType: string
        summarizeBy: none
        sourceColumn: Status
        // Reported, Under Review, Approved, Denied, Paid, Partial
    
    column approvalDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: ApprovalDate
    
    column paymentDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: PaymentDate
    
    column deductible
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: none
        sourceColumn: Deductible
    
    // Measures
    
    /// Total number of claims reported
    measure 'Claims Reported' = COUNTROWS('claims')
        formatString: #,##0
    
    /// Total amount claimed (requested)
    measure 'Claims Amount Requested' = SUM('claims'[claimAmount])
        formatString: $ #,##0
    
    /// Total amount paid (approved + disbursed)
    measure 'Claims Amount Paid' = SUM('claims'[paidAmount])
        formatString: $ #,##0
    
    /// Number of paid claims
    measure 'Claims Paid' = 
        CALCULATE(
            [Claims Reported],
            FILTER('claims', 'claims'[status] IN {"Paid", "Partial"})
        )
        formatString: #,##0
    
    /// Number of denied claims
    measure 'Claims Denied' = 
        CALCULATE(
            [Claims Reported],
            FILTER('claims', 'claims'[status] = "Denied")
        )
        formatString: #,##0
    
    /// Approval Rate
    measure 'Claim Approval Rate' = 
        DIVIDE([Claims Paid], [Claims Reported], 0)
        formatString: 0.00 %
    
    /// Total deductibles applied
    measure 'Total Deductible' = SUM('claims'[deductible])
        formatString: $ #,##0
    
    /// Average payout per claim
    measure 'Average Payout' = 
        DIVIDE([Claims Amount Paid], [Claims Paid], 0)
        formatString: $ #,##0
```

---

### 1.7 TABLA HECHO: `premiums` (Secundaria)

**Propósito:** Movimientos de prima pagada (para análisis de cobranza)

```tmdl
table premiums
    
    column premiumKey
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: PremiumKey
    
    column policyKey
        dataType: int64
        isHidden
        formatString: 0
        summarizeBy: none
        sourceColumn: PolicyKey
    
    column premiumDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: PremiumDate
    
    column dueDate
        dataType: date
        formatString: Long Date
        summarizeBy: none
        sourceColumn: DueDate
    
    column amount
        dataType: decimal
        formatString: $ #,##0
        summarizeBy: sum
        sourceColumn: Amount
    
    column status
        dataType: string
        summarizeBy: none
        sourceColumn: Status
        // Paid, Overdue, Pending, Partial, Cancelled
    
    column paymentMethod
        dataType: string
        summarizeBy: none
        sourceColumn: PaymentMethod
    
    column sequenceNumber
        dataType: int64
        formatString: 0
        summarizeBy: none
        sourceColumn: SequenceNumber
    
    // Measures
    
    /// Total premiums paid
    measure 'Premium Paid' = 
        CALCULATE(
            SUM('premiums'[amount]),
            FILTER('premiums', 'premiums'[status] = "Paid")
        )
        formatString: $ #,##0
    
    /// Total overdue premiums
    measure 'Premium Overdue' = 
        CALCULATE(
            SUM('premiums'[amount]),
            FILTER('premiums', 'premiums'[status] = "Overdue")
        )
        formatString: $ #,##0
    
    /// Premium Payment Rate
    measure 'Premium Payment Rate' = 
        DIVIDE(
            CALCULATE(SUM('premiums'[amount]), FILTER('premiums', 'premiums'[status] = "Paid")),
            SUM('premiums'[amount]),
            0
        )
        formatString: 0.00 %
```

---

## 2. RELACIONES ENTRE TABLAS

### 2.1 Archivo `relationships.tmdl`

```tmdl
// Policy - Product relationship
relationship 'policies-product'
    fromColumn: 'policies'.'productKey'
    toColumn: 'product'.'productKey'

// Policy - Customer relationship
relationship 'policies-customer'
    fromColumn: 'policies'.'customerKey'
    toColumn: 'customer'.'customerKey'

// Policy - Agency relationship
relationship 'policies-agency'
    fromColumn: 'policies'.'agencyKey'
    toColumn: 'agency'.'agencyKey'

// Policy - Underwriter relationship
relationship 'policies-underwriter'
    fromColumn: 'policies'.'underwriterKey'
    toColumn: 'underwriter'.'underwriterKey'

// Policy - Calendar (Issue Date)
relationship 'policies-calendar-issue'
    fromColumn: 'policies'.'issueDate'
    toColumn: 'calendar'.'Date'

// Policy - Calendar (Expiry Date) - INACTIVE
relationship 'policies-calendar-expiry'
    fromColumn: 'policies'.'expiryDate'
    toColumn: 'calendar'.'Date'
    isActive: false

// Policy - Calendar (Renewal Date) - INACTIVE
relationship 'policies-calendar-renewal'
    fromColumn: 'policies'.'renewalDate'
    toColumn: 'calendar'.'Date'
    isActive: false

// Claims - Policy relationship
relationship 'claims-policies'
    fromColumn: 'claims'.'policyKey'
    toColumn: 'policies'.'policyKey'

// Claims - Calendar (Claim Date) - PRIMARY
relationship 'claims-calendar-claim'
    fromColumn: 'claims'.'claimDate'
    toColumn: 'calendar'.'Date'

// Claims - Calendar (Reported Date) - INACTIVE
relationship 'claims-calendar-reported'
    fromColumn: 'claims'.'reportedDate'
    toColumn: 'calendar'.'Date'
    isActive: false

// Premiums - Policy relationship
relationship 'premiums-policies'
    fromColumn: 'premiums'.'policyKey'
    toColumn: 'policies'.'policyKey'

// Premiums - Calendar (Premium Date)
relationship 'premiums-calendar-date'
    fromColumn: 'premiums'.'premiumDate'
    toColumn: 'calendar'.'Date'

// Premiums - Calendar (Due Date) - INACTIVE
relationship 'premiums-calendar-due'
    fromColumn: 'premiums'.'dueDate'
    toColumn: 'calendar'.'Date'
    isActive: false
```

**Notas importantes:**
- Múltiples relaciones a Calendar requieren `USERELATIONSHIP()` en medidas específicas
- Las relaciones inactivas se activan dinámicamente cuando es necesario

---

## 3. PARÁMETROS (expressions.tmdl)

```tmdl
expression Parameter_SqlServer = "your_server.database.windows.net" 
    meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

expression Parameter_SqlDatabase = "InsuranceAnalytics" 
    meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

expression Parameter_UserId = "default_user" 
    meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

expression Parameter_Environment = "DEV" 
    meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]
```

---

## 4. ESTRATEGIA DE DATOS SIMULADOS

### 4.1 Generación de CSV

```powerquery
// En Power Query, cuando no hay acceso a BD real
let
    // 1. Seed products (5 tipos)
    Products = #table(
        {"ProductKey", "ProductName", "ProductCode", "ProductType", ...},
        {
            {1, "Vida Individual Standard", "VID-001", "Vida Individual", ...},
            {2, "Vida Individual Premium", "VID-002", "Vida Individual", ...},
            ...
        }
    ),
    
    // 2. Generate customers (50,000)
    Customers = List.Generate(
        () => 1,
        each _ <= 50000,
        each _ + 1,
        each {_, "CUST" & Text.PadStart(Text.From(_), 6, "0"), ...}
    ),
    
    // 3. Generate policies (200,000)
    // Random dates: 2022-2026
    // Random products, customers, agencies
    // Random premiums: $100-$5,000 annual
    
    // 4. Generate claims (5-10% of policies)
    // Random claim types, amounts
    
    // 5. Generate premium payments
    // Monthly/quarterly/annual sequences
    
in
    Policies
```

### 4.2 Volúmenes Realistas

| Tabla | Registros | Crecimiento |
|-------|-----------|-----------|
| product | 5 | Estático |
| customer | 50,000 | +20% anual |
| agency | 100 | +5% anual |
| underwriter | 50 | Estático |
| policies | 200,000 | +30% anual (nuevas emisiones) |
| claims | 8,000 (4% ratio) | Proporcional a policies |
| premiums | 2,400,000 | 12 cuotas × policies activas |

---

## 5. ROLES DE SEGURIDAD (RLS)

### 5.1 Rol: `Regional Manager`
- Acceso a pólizas y siniestros de su región
- DAX filter: `'agency'[region] = USERNAME()`

### 5.2 Rol: `Agency Manager`
- Acceso solo a su agencia
- DAX filter: `'agency'[agencyKey] = [CurrentAgencyKey]`

### 5.3 Rol: `Underwriter`
- Acceso a pólizas que suscribió
- DAX filter: `'policies'[underwriterKey] = [CurrentUnderwriterKey]`

### 5.4 Rol: `Executive`
- Acceso total sin restricciones

---

## 6. MEDIDAS DERIVADAS (Análisis Profundo)

### 6.1 Análisis de Cohorte (Cohort Retention)

```dax
[Cohort Retention Month 1] = 
    VAR v_cohort = CALCULATE(MAX('policies'[issueDate]))
    VAR v_monthsAgo = 1
    VAR v_targetMonth = DATE(YEAR(v_cohort), MONTH(v_cohort) - v_monthsAgo, 1)
    RETURN
    DIVIDE(
        CALCULATE(
            [Active Policies],
            FILTER('policies', 
                'policies'[status] IN {"Active", "Suspended"} &&
                'policies'[issueDate] >= v_targetMonth &&
                'policies'[issueDate] < EDATE(v_targetMonth, 1)
            )
        ),
        CALCULATE(
            [Issued Policies],
            FILTER('policies',
                'policies'[issueDate] >= v_targetMonth &&
                'policies'[issueDate] < EDATE(v_targetMonth, 1)
            )
        ),
        0
    )
```

### 6.2 Loss Ratio (Métrica Actuarial)

```dax
[Loss Ratio] = 
    DIVIDE(
        [Claims Amount Paid],
        [Premium Income],
        0
    )
    // Formato: 0.00 (Expected range: 0.60-0.80)

[Loss Ratio Trended] =
    VAR v_currentLR = [Loss Ratio]
    VAR v_targetLR = 0.75
    VAR v_trend = v_currentLR - v_targetLR
    RETURN
    IF(ABS(v_trend) > 0.05, "⚠️ Elevated", "✓ Normal")
```

### 6.3 Premium Income per Product

```dax
[Premium Income by Product] = 
    SUMX(
        VALUES('product'[productKey]),
        CALCULATE(
            [Premium Income],
            'policies'[productKey] = EARLIER('product'[productKey])
        )
    )
```

---

## CONCLUSIÓN

Esta especificación técnica proporciona el blueprint completo para la transformación del modelo agentico de Retail a Seguros de Vida. Cada tabla, relación y medida está documentada para ser implementada de forma incremental.

**Próximo paso:** Crear requirements-01.md específico para Seguros basado en esta especificación.

