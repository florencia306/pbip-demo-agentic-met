# ✨ CHECKLIST IMPLEMENTACIÓN + PLANTILLAS

## Parte 1: Archivos a REEMPLAZAR/MODIFICAR

### 1. `.requirements/requirements-01.md` (REEMPLAZAR)

**Acción:** Reemplazar contenido completo

```markdown
# Business Requirements - Life Insurance Analytics 💼

Create a new semantic model named 'LifeInsuranceAnalytics' for comprehensive 
insurance analytics covering policies, claims, and retention metrics.

## Data Warehouse Information 🛢️

- **Server**: your_server.database.windows.net
- **Database**: InsuranceAnalytics

## Requirements by KPI

### INS-001: Issued Policies Overview
**User Story:** As a Sales Manager, I want to see total policies issued 
by product type and period to track business growth.
**Expected:** Create measure [Issued Policies] with breakdown by:
- Product (5 types)
- Period (Year, Quarter, Month)
- Agency

### INS-002: Average Premium Analysis
**User Story:** As an Analyst, I want to understand premium distribution 
by segment and product.
**Expected:** [Average Premium] = AVERAGE(policies[AnnualPremium])

### INS-003: Retention & Persistency Rates
**User Story:** As Executive, I need retention and persistence metrics 
to evaluate business stability.
**Expected:**
- [Retention Rate] = Renewed Policies / Total Eligible
- [Persistency Rate] = 1 - Cancellation Rate

### INS-004: Loss Ratio Analysis
**User Story:** As CFO, I must monitor loss ratio for profitability.
**Expected:** [Loss Ratio] = Claims Amount Paid / Premium Income

### INS-005: Claims Monitoring
**User Story:** As Claims Manager, track all claims activity.
**Expected:** Measures for claims count, paid amounts, approval rates

### INS-006: Product Performance Dashboard
**User Story:** As Product Manager, evaluate each of 5 product lines.
**Expected:** 
- Vida Individual
- Vida Colectivo
- Vida Temporal
- Vida Universal
- Accidentes Personales

## Development Rules 🧑‍💻

- Use import storage mode for all tables
- Reuse Calendar table from .resources/tmdl/Calendar.tmdl
- Create star schema (facts + dimensions)
- All measures in fact tables (no duplication)
- Relationships via date columns
- Enable incremental refresh for large tables
- Apply RLS for agency-based security

## Naming Conventions 🏷️

- Dimensions: singular lowercase (product, customer, agency)
- Facts: plural lowercase (policies, claims, premiums)
- Measures: [Metric Name] or [Metric Name (ly)] or [Metric Name (ytd)]
- No 'dim' or 'fact' prefix
```

---

### 2. `.requirements/AzureSQLSchema.csv` (REEMPLAZAR)

**Acción:** Reemplazar con esquema de seguros

```csv
SchemaName,TableName,ColumnName,DataType,AllowNulls,Description
dbo,product,ProductKey,int,0,Primary Key
dbo,product,ProductName,nvarchar(100),0,Product Name
dbo,product,ProductCode,nvarchar(20),0,Product Code
dbo,product,ProductType,nvarchar(50),0,"Vida Individual, Vida Colectivo, etc"
dbo,product,CoverageType,nvarchar(50),0,Type of coverage
dbo,product,MaxSumAssured,decimal,0,Maximum sum assured
dbo,product,TACPercentage,decimal,0,Commission percentage
dbo,product,MinimumAge,int,0,Minimum underwriting age
dbo,product,MaximumAge,int,0,Maximum underwriting age
dbo,product,IsActive,bit,0,Active flag
dbo,product,CreatedDate,date,0,Creation date
dbo,customer,CustomerKey,int,0,Primary Key
dbo,customer,CustomerCode,nvarchar(50),0,Customer Code
dbo,customer,CustomerName,nvarchar(100),0,Customer Name
dbo,customer,DocumentType,nvarchar(20),0,"RUT, CEDULA, PASAPORTE"
dbo,customer,DocumentNumber,nvarchar(30),0,Document Number
dbo,customer,Age,int,1,Age
dbo,customer,Gender,nvarchar(10),1,"M, F, O"
dbo,customer,MaritalStatus,nvarchar(20),1,Marital Status
dbo,customer,Occupation,nvarchar(50),1,Occupation
dbo,customer,RiskProfileSegment,nvarchar(20),1,"Low, Medium, High"
dbo,customer,CreatedDate,date,0,Creation date
dbo,agency,AgencyKey,int,0,Primary Key
dbo,agency,AgencyName,nvarchar(100),0,Agency Name
dbo,agency,AgencyType,nvarchar(50),0,"Directo, Broker, Empleado, Partner"
dbo,agency,Manager,nvarchar(100),1,Agency Manager Name
dbo,agency,Region,nvarchar(50),0,Region
dbo,agency,Branch,nvarchar(50),1,Branch
dbo,agency,CommissionRate,decimal,0,Commission percentage
dbo,agency,Status,nvarchar(20),0,"Active, Inactive"
dbo,underwriter,UnderwriterKey,int,0,Primary Key
dbo,underwriter,UnderwriterName,nvarchar(100),0,Underwriter Name
dbo,underwriter,Department,nvarchar(50),0,"Individual, Colectivo, Claims"
dbo,underwriter,ExperienceYears,int,0,Years of experience
dbo,underwriter,Specialization,nvarchar(50),1,Specialization
dbo,underwriter,MaxApprovalAmount,decimal,0,Maximum approval limit
dbo,policies,PolicyKey,int,0,Primary Key
dbo,policies,PolicyNumber,nvarchar(50),0,Policy Number
dbo,policies,ProductKey,int,0,Foreign Key to product
dbo,policies,CustomerKey,int,0,Foreign Key to customer
dbo,policies,AgencyKey,int,0,Foreign Key to agency
dbo,policies,UnderwriterKey,int,0,Foreign Key to underwriter
dbo,policies,IssueDate,date,0,Date of issue
dbo,policies,EffectiveDate,date,0,Effective date
dbo,policies,ExpiryDate,date,0,Expiry date
dbo,policies,MaturityDate,date,1,Maturity date
dbo,policies,RenewalDate,date,1,Renewal date
dbo,policies,SumAssured,decimal,0,Sum assured amount
dbo,policies,AnnualPremium,decimal,0,Annual premium
dbo,policies,PremiumFrequency,nvarchar(20),0,"Monthly, Quarterly, Annual"
dbo,policies,PaymentMethod,nvarchar(50),0,Payment method
dbo,policies,Status,nvarchar(20),0,"Active, Expired, Cancelled, Suspended"
dbo,policies,CancellationDate,date,1,Cancellation date if cancelled
dbo,policies,CancellationReason,nvarchar(100),1,Reason for cancellation
dbo,policies,UnderwritingDecision,nvarchar(20),0,"Approved, Conditional, Rejected"
dbo,policies,MedicalRequired,bit,0,Medical exam required flag
dbo,policies,RiderCount,int,0,Number of riders
dbo,policies,CommissionAmount,decimal,0,Commission amount
dbo,claims,ClaimKey,int,0,Primary Key
dbo,claims,ClaimNumber,nvarchar(50),0,Claim Number
dbo,claims,PolicyKey,int,0,Foreign Key to policies
dbo,claims,ClaimDate,date,0,Date of claim occurrence
dbo,claims,ReportedDate,date,0,Date claim reported
dbo,claims,ClaimType,nvarchar(50),0,"Muerte, Invalidez, Accidente, etc"
dbo,claims,ClaimAmount,decimal,0,Claimed amount
dbo,claims,PaidAmount,decimal,0,Amount paid
dbo,claims,Status,nvarchar(20),0,"Reported, Approved, Denied, Paid"
dbo,claims,ApprovalDate,date,1,Date of approval/denial
dbo,claims,PaymentDate,date,1,Date of payment
dbo,claims,Deductible,decimal,0,Deductible applied
dbo,premiums,PremiumKey,int,0,Primary Key
dbo,premiums,PolicyKey,int,0,Foreign Key to policies
dbo,premiums,PremiumDate,date,0,Payment date
dbo,premiums,DueDate,date,0,Due date
dbo,premiums,Amount,decimal,0,Premium amount
dbo,premiums,Status,nvarchar(20),0,"Paid, Overdue, Pending, Partial"
dbo,premiums,PaymentMethod,nvarchar(50),0,Payment method
dbo,premiums,SequenceNumber,int,0,Sequence number (installment #)
```

---

### 3. `.resources/kb-insurance-domain.md` (CREAR NUEVO)

```markdown
# Insurance Domain Knowledge Base 📚

## Overview
You are a Life Insurance Analytics Developer responsible for building 
comprehensive insurance models covering policies, claims, and retention analysis.

## Insurance Products (5 Types)

### 1. Vida Individual (Individual Life)
- Individual coverage
- Typical sum assured: $100K - $500K
- TAC: 4-5.5%
- Age range: 18-75

### 2. Vida Colectivo (Group Life)
- Employer/group coverage
- Higher sum assured: $500K - $1M per employee
- TAC: 3-4%
- Age range: 21-65

### 3. Vida Temporal (Term Life)
- Fixed term coverage (10-20 years)
- Lowest premium
- TAC: 3%
- Age range: 25-70

### 4. Vida Universal (Universal Life)
- Flexible premium & coverage
- Investment component
- TAC: 4.5%
- Age range: 25-75

### 5. Accidentes Personales (Personal Accident)
- Accidental death/disability
- Lower sum assured
- Higher TAC: 6%
- Age range: 16-70

## Key Metrics (KPIs)

### Sales Metrics
- **Issued Policies**: Total policies emitted in period
- **Premium Income**: Total annual premiums
- **Average Premium**: Premium Income / Issued Policies
- **New Customers**: First-time policyholders

### Retention Metrics
- **Retention Rate**: (Renewed / Eligible for Renewal) × 100
  - Expected: 80-90%
- **Persistency Rate**: 1 - Cancellation Rate
  - Expected: 85-95%
- **Cancellation Rate**: (Cancelled / Total) × 100
  - Expected: 5-15%

### Loss Metrics
- **Loss Ratio**: Claims Paid / Premium Income
  - Expected: 60-80%
- **Claims Frequency**: # Claims / # Active Policies
- **Claims Severity**: Avg Claim Amount
- **Claim Approval Rate**: Approved / Total Claims

### Operational Metrics
- **Premium Payment Rate**: Premiums Paid / Premiums Due
- **Overdue Premium**: Premiums not paid by due date
- **Commission Rate**: Commission % of Premium (varies by product & channel)

## Data Quality Rules

1. **Dates**: IssueDate ≤ EffectiveDate < ExpiryDate
2. **Amounts**: AnnualPremium > 0, SumAssured > AnnualPremium
3. **Status**: Must be valid state (Active, Expired, Cancelled, Suspended)
4. **Keys**: No orphaned records (all FK must have corresponding PK)
5. **Calendar**: No gaps in premium payment sequences

## Business Rules

1. **Policy Lifecycle**:
   - Issue → Effective → Active → Renewal (due) → Renewed or Lapsed
   - Or: Cancelled (voluntary or involuntary)

2. **Claims**:
   - Only Active/Suspended policies can have valid claims
   - Claim date ≤ Reported date
   - Claim amount ≤ Sum assured (minus deductible)

3. **Premium Payment**:
   - Frequency: Monthly, Quarterly, Annual
   - If payment > 30 days late → Status = Overdue

4. **Retention Cohort**:
   - Measure persistence by issue cohort (month of issue)
   - Track renewals at anniversary

## Calculations Reference

### Retention Rate (Cohort-based)
```
Cohort = Issue Month
Eligible at Renewal = Policies issued in [Cohort] that reached anniversary
Renewed = Policies in [Cohort] with Status = "Renewed" or new policy issued
Retention Rate = Renewed / Eligible at Renewal
```

### Loss Ratio (Actuarial)
```
Loss Ratio = Total Claims Paid / Total Premium Income
Expected Range: 60-80%
< 60% = Over-pricing (opportunity to reduce premiums)
> 80% = Under-pricing (profitability risk)
```

### Premium Payment Performance
```
On-Time Payments = Premiums with Status = "Paid" before DueDate
Overdue = Premiums with Status = "Overdue"
Payment Rate = (Paid + Partial) / Total Due
Typically: 92-98% on time
```

## RLS Scenarios

1. **Regional Manager** - See all agencies in region
2. **Agency Manager** - See only own agency
3. **Underwriter** - See policies underwritten by self
4. **Executive** - See all data

## Common Analysis Patterns

1. **Cohort Analysis**: Retention by issue month
2. **Product Mix**: % of premium by product type
3. **Agency Performance**: Rankings by premium & retention
4. **Loss Trending**: Loss ratio over time
5. **Cancellation Analysis**: Reasons and seasonality

## Power BI Best Practices for Insurance

1. Use Calendar table for all date dimensions
2. Multiple relationships to Calendar require USERELATIONSHIP()
3. RLS especially important for sensitive customer data
4. Incremental refresh for large fact tables (policies, claims, premiums)
5. Create separate fact tables for different event types
```

---

## Parte 2: Archivos a CREAR (Nuevos)

### Plantilla: `product.tmdl`

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

	measure '# Products' = COUNTROWS('product')
		formatString: #,##0

	measure '# Active Products' = 
		CALCULATE(
			[# Products],
			FILTER('product', [# Products] > 0)
		)
		formatString: #,##0

	partition product-partition = m
		mode: import
		source = 
			let
				Source = Csv.Document(
					Web.Contents(HttpSource, [RelativePath = "RAW-Product.csv"]),
					[Delimiter = ",", Columns = 10, Encoding = 65001]
				),
				#"Promoted Headers" = Table.PromoteHeaders(Source, [PromoteAllScalars = true])
			in
				#"Promoted Headers"
```

### Plantilla: `policies.tmdl` (Parcial - Hecho Principal)

```tmdl
table policies

	measure 'Issued Policies' = COUNTROWS('policies')
		formatString: #,##0

	measure 'Active Policies' = 
		CALCULATE(
			[Issued Policies],
			FILTER('policies', 'policies'[status] IN {"Active", "Suspended"})
		)
		formatString: #,##0

	measure 'Premium Income' = SUM('policies'[annualPremium])
		formatString: $ #,##0

	measure 'Average Premium' = 
		DIVIDE([Premium Income], [Issued Policies], 0)
		formatString: $ #,##0

	// Y más medidas...

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

	// Y más columnas...
```

### Plantilla: `relationships.tmdl`

```tmdl
relationship 'policies-product'
	fromColumn: 'policies'.'productKey'
	toColumn: 'product'.'productKey'

relationship 'policies-customer'
	fromColumn: 'policies'.'customerKey'
	toColumn: 'customer'.'customerKey'

relationship 'policies-agency'
	fromColumn: 'policies'.'agencyKey'
	toColumn: 'agency'.'agencyKey'

relationship 'policies-underwriter'
	fromColumn: 'policies'.'underwriterKey'
	toColumn: 'underwriter'.'underwriterKey'

// Primary date relationship
relationship 'policies-calendar-issue'
	fromColumn: 'policies'.'issueDate'
	toColumn: 'calendar'.'Date'

// Inactive relationships for alternative date analysis
relationship 'policies-calendar-expiry'
	fromColumn: 'policies'.'expiryDate'
	toColumn: 'calendar'.'Date'
	isActive: false

relationship 'claims-policies'
	fromColumn: 'claims'.'policyKey'
	toColumn: 'policies'.'policyKey'

relationship 'claims-calendar'
	fromColumn: 'claims'.'claimDate'
	toColumn: 'calendar'.'Date'

relationship 'premiums-policies'
	fromColumn: 'premiums'.'policyKey'
	toColumn: 'policies'.'policyKey'

relationship 'premiums-calendar'
	fromColumn: 'premiums'.'premiumDate'
	toColumn: 'calendar'.'Date'
```

### Plantilla: `expressions.tmdl`

```tmdl
expression Parameter_SqlServer = "your_server.database.windows.net" 
	meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

expression Parameter_SqlDatabase = "InsuranceAnalytics" 
	meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]

expression Parameter_Environment = "DEV" 
	meta [IsParameterQuery=true, Type="Text", IsParameterQueryRequired=true]
```

### Plantilla: `model.tmdl`

```tmdl
model LifeInsuranceAnalytics
	culture: en-US
	defaultPowerBIDataSourceVersion: powerBI_V3
	discourageImplicitMeasures
	sourceQueryCulture: en-GB
	dataAccessOptions
		legacyRedirects
		returnErrorValuesAsNull

annotation PBI_ProTooling = ["TMDL-Extension"]

ref table product
ref table customer
ref table agency
ref table underwriter
ref table calendar
ref table policies
ref table claims
ref table premiums

ref role 'Regional Manager'
ref role 'Agency Manager'
ref role 'Executive'

ref cultureInfo en-US
```

---

## Parte 3: Estructura de Carpetas (Git)

```
pbip-demo-agentic/
│
├── README.md                          (sin cambios)
├── LICENSE                            (sin cambios)
├── .gitignore                         (sin cambios)
│
├── .requirements/
│   ├── requirements-01.md             ✏️ REEMPLAZAR
│   └── AzureSQLSchema.csv             ✏️ REEMPLAZAR
│
├── .resources/
│   ├── kb-pbip.md                     (sin cambios)
│   ├── kb-insurance-domain.md         ✨ CREAR
│   ├── pbip-sample/                   (sin cambios)
│   ├── tmdl/
│   │   ├── Calendar.tmdl              ✏️ VERIFICAR + EXTENDER
│   │   └── Time Intelligence.tmdl     (sin cambios)
│   └── sql/
│       └── InsuranceSchema.sql        ✨ CREAR
│
├── .bpa/                              (sin cambios)
│   ├── bpa.ps1
│   └── bpa-rules-*.json
│
└── src/
    └── LifeInsuranceAnalytics.SemanticModel/
        ├── definition/
        │   ├── tables/
        │   │   ├── product.tmdl
        │   │   ├── customer.tmdl
        │   │   ├── agency.tmdl
        │   │   ├── underwriter.tmdl
        │   │   ├── calendar.tmdl
        │   │   ├── policies.tmdl
        │   │   ├── claims.tmdl
        │   │   └── premiums.tmdl
        │   ├── relationships.tmdl
        │   ├── model.tmdl
        │   └── expressions.tmdl
        ├── definition.pbism
        └── [Roles RLS] (si son necesarios)
    │
    ├── LifeInsuranceAnalytics.Report/
    │   └── definition.pbir
    │
    └── LifeInsuranceAnalytics.pbip
```

---

## ✅ Checklist de Implementación

### Pre-Implementation
- [ ] Aprobación de stakeholders en esta arquitectura
- [ ] Acceso a BD real (o generar CSV simulados)
- [ ] GitHub Copilot Pro activado (Claude Sonnet 4)

### Phase 1: Foundation
- [ ] Crear carpeta `/src`
- [ ] Reemplazar `requirements-01.md`
- [ ] Reemplazar `AzureSQLSchema.csv`
- [ ] Crear `kb-insurance-domain.md`
- [ ] Copiar/extender `Calendar.tmdl`
- [ ] Crear model.tmdl y expressions.tmdl
- [ ] Ejecutar BPA (debería pasar)

### Phase 2-4: Implementation
- [ ] Crear 5 dimensiones (product, customer, agency, underwriter, calendar)
- [ ] Crear 3 hechos (policies, claims, premiums)
- [ ] Crear relationships.tmdl
- [ ] Crear todas las medidas
- [ ] Ejecutar BPA iterativamente

### Phase 5-7: Finalization
- [ ] Crear roles RLS
- [ ] Optimizar performance
- [ ] Crear reportes Power BI
- [ ] Publicar y demo

---

**Nota:** Todos los archivos TMDL se encuentran documentados en detalle en `TECHNICAL_SPECIFICATIONS.md`

