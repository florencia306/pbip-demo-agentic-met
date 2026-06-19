# Insurance Domain Knowledge Base 📚

## Overview
You are a Life Insurance Analytics Developer responsible for building comprehensive insurance models covering policies, claims, and retention analysis.

## Insurance Products (5 Types)

### 1. Vida Individual (Individual Life)
- Individual coverage
- Typical sum assured: $100K - $500K
- TAC: 4-5.5%
- Age range: 18-75
- Mix: ~45% of total premiums

### 2. Vida Colectivo (Group Life)
- Employer/group coverage
- Higher sum assured: $500K - $1M per employee
- TAC: 3-4%
- Age range: 21-65
- Mix: ~35% of total premiums

### 3. Vida Temporal (Term Life)
- Fixed term coverage (10-20 years)
- Lowest premium
- TAC: 3%
- Age range: 25-70
- Mix: ~12% of total premiums

### 4. Vida Universal (Universal Life)
- Flexible premium & coverage
- Investment component
- TAC: 4.5%
- Age range: 25-75
- Mix: ~5% of total premiums

### 5. Accidentes Personales (Personal Accident)
- Accidental death/disability
- Lower sum assured
- Higher TAC: 6%
- Age range: 16-70
- Mix: ~3% of total premiums

## Key Metrics (KPIs)

### Sales Metrics
- **Issued Policies**: Total policies emitted in period
- **Premium Income**: Total annual premiums collected
- **Average Premium**: Premium Income / Issued Policies
- **New Customers**: First-time policyholders

### Retention Metrics
- **Retention Rate**: (Renewed / Eligible for Renewal) × 100
  - Expected: 80-90%
  - Calculated by cohort (issue month)
- **Persistency Rate**: 1 - Cancellation Rate
  - Expected: 85-95%
- **Cancellation Rate**: (Cancelled / Total) × 100
  - Expected: 5-15%
  - Track reasons: Non-payment, Voluntary, Replacements, etc.

### Loss Metrics
- **Loss Ratio**: Claims Paid / Premium Income
  - Expected: 60-80%
  - Below 60% = Over-pricing opportunity
  - Above 80% = Profitability risk
- **Claims Frequency**: # Claims / # Active Policies
- **Claims Severity**: Average Claim Amount
- **Claim Approval Rate**: Approved / Total Claims × 100
  - Expected: 95%+

### Operational Metrics
- **Premium Payment Rate**: Premiums Paid / Premiums Due
  - Expected: 92-98% on time
- **Overdue Premium**: Premiums not paid by due date (>30 days)
- **Commission Rate**: Commission % of Premium (varies by product & channel)

## Data Quality Rules

1. **Dates**: IssueDate ≤ EffectiveDate < ExpiryDate
2. **Amounts**: AnnualPremium > 0, SumAssured > AnnualPremium
3. **Status**: Must be valid state (Active, Expired, Cancelled, Suspended)
4. **Keys**: No orphaned records (all FK must have corresponding PK)
5. **Calendar**: No gaps in premium payment sequences

## Business Rules

### Policy Lifecycle
```
Issue → Effective → Active → Renewal (due) → Renewed or Lapsed
    Or: Cancelled (voluntary or involuntary)
```

- **IssueDate**: When policy is underwritten
- **EffectiveDate**: When coverage starts
- **ExpiryDate**: When current term ends
- **RenewalDate**: When policy eligible for renewal
- **MaturityDate**: When policy reaches final maturity (if applicable)

### Claims
- Only Active/Suspended policies can have valid claims
- Claim date ≤ Reported date
- Claim amount ≤ Sum assured (minus deductible)
- Status flow: Reported → Approved/Denied → Paid

### Premium Payment
- Frequency: Monthly, Quarterly, Annual
- If payment > 30 days late → Status = "Overdue"
- Sequence number tracks installment (1st, 2nd, 3rd, etc.)

### Retention Cohort
- Measure persistence by issue cohort (month of issue)
- Track renewals at anniversary date
- Use CohortYear/CohortMonth for analysis

## Calculations Reference

### Retention Rate (Cohort-based)
```
Cohort = Issue Month/Year
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

1. **Regional Manager** - See all agencies in assigned region
2. **Agency Manager** - See only own agency data
3. **Underwriter** - See policies underwritten by self
4. **Executive** - See all data (unrestricted)

## Common Analysis Patterns

1. **Cohort Analysis**: Retention by issue month
2. **Product Mix**: % of premium by product type
3. **Agency Performance**: Rankings by premium & retention
4. **Loss Trending**: Loss ratio over time
5. **Cancellation Analysis**: Reasons and seasonality
6. **Payment Performance**: On-time vs. overdue by period

## Power BI Best Practices for Insurance

1. Use Calendar table for ALL date dimensions
2. Multiple relationships to Calendar require USERELATIONSHIP() in measures
3. RLS especially important for sensitive customer data
4. Incremental refresh for large fact tables (policies, claims, premiums)
5. Create separate fact tables for different event types (policies ≠ claims ≠ premiums)
6. Parameterize server/database names for environment flexibility
7. Document all business rules in measures (descriptions)
