# CBAM Gap Analysis & Cost Calculator

**Property of Entrenovu GmbH**

## Overview

This is a comprehensive CBAM (Carbon Border Adjustment Mechanism) analysis tool that combines cost estimation with compliance readiness assessment for German and Austrian importers. The application features real-time cost calculations based on official EU transition data and CN code-specific emission factors.

## Key Technical Components

### 1. Emissions Database

The tool contains a comprehensive database of CN/HS codes with emission factors:

- **Coverage**: 80+ CN codes across all CBAM sectors (Cement, Fertilizers, Aluminium, Hydrogen, Iron & Steel)
- **Data Structure**: Each entry contains direct emissions, indirect emissions, total emissions, and applicable ETS benchmarks
- **Data Source**: Based on EU transitional default values
- **Current Implementation**: **Direct emissions only** - indirect emissions (Scope 2) are disabled and marked as "BETA"

```javascript
cnCodeDatabaseRaw = {
  "7601": { 
    sector:"Aluminium", 
    product:"Unwrought aluminium", 
    direct:2.36,        // Used in calculations
    indirect:8.14,      // Not currently used
    total:10.49, 
    benchmark:1.464     // ETS benchmark for deduction
  }
}
```

### 2. Cost Calculation Engine

#### Core Formula
```
CBAM Cost = (Emission Factor - Benchmark) × Tonnage × CO₂ Price × Phase-in Factor
```

#### Price Projections
- **CO₂ Prices**: €90/tCO₂ (2026) escalating to €178/tCO₂ (2034)
- **Phase-in Factor**: 97.5% (2026), 100% (2027-2034)
- **Time Horizon**: 9-year projection (2026-2034)

#### Benchmark Deductions
Uses official ETS benchmarks where available:
- Cement clinkers: 0.693 tCO₂/t
- Primary aluminium: 1.464 tCO₂/t
- Hot metal: 1.288 tCO₂/t
- EAF steel grades: 0.215-0.268 tCO₂/t
- Fertilizers (ammonia-based): 1.570 tCO₂/t

### 3. Product Categories & Default Values

When CN codes are not specified, the system uses sector-based defaults:

```javascript
cbamDefaultValues = { 
  steel: 2.10,      // Direct emissions only
  aluminium: 2.36,  // Direct emissions only  
  cement: 0.97, 
  fertilizer: 1.90, 
  custom: 2.00 
}
```

**Note**: These defaults represent direct emissions only, significantly lower than total (direct + indirect) values.

### 4. Gap Analysis Scoring

#### Methodology
- **8 Compliance Areas**: Authorization, Data Management, Registry, Organization, Finance, Audit, Legal, IT
- **3-Point Scale**: Have (2 points), Partial (1 point), Need (0 points)
- **Scoring Formula**: `(Have × 2 + Partial × 1) / (8 × 2) × 100`

#### Readiness Levels
- **High (75%+)**: Advanced preparation
- **Medium (50-74%)**: Basic foundation, specialized support needed
- **Low (<50%)**: Comprehensive professional guidance required

### 5. Data Integration

#### Google Apps Script Integration
- Real-time submission to Google Sheets
- Automated email notifications (German/Albanian language support)
- Complete audit trail with timestamps
- Field mapping for all form data and calculated results

#### Data Structure
```javascript
dataToSubmit = {
  language: 'de',                    // Email language selector
  companyName: string,
  sector: string,                    // Derived from products
  exportVolume: number,              // Total tonnage
  readinessScore: number,            // 0-100 percentage
  estimatedCost2026: number,         // Calculated cost
  estimatedCost2030: number,
  estimatedCost2034: number,
  responses: [{question, status}]    // Gap analysis answers
}
```

### 6. Calculation Limitations

#### Current Scope
- **Direct emissions only**: Indirect (Scope 2) emissions are not included in calculations
- **Conservative estimates**: Uses lower direct-only emission factors
- **Simplified methodology**: Does not account for installation-specific data or country-specific electricity grids
- **Benchmark assumptions**: Applies where available, otherwise zero deduction

#### Missing Components
- Scope 2 (indirect) emissions from electricity consumption
- Country-specific electricity emission factors
- Installation-specific emission factors
- Complex product calculations requiring multiple input materials

### 7. Visualization & UX

- **Chart.js Integration**: Interactive cost projection charts
- **Real-time Calculations**: Updates as users input data
- **CN Code Lookup**: Auto-population of emission factors and benchmarks
- **Responsive Design**: Mobile-optimized interface
- **Form Validation**: Required fields with error handling

## Technical Requirements

- Modern browser with ES6 support
- Internet connectivity for Google Apps Script submission
- JavaScript enabled
- Chart.js 4.4.2 (loaded via CDN)

## Data Accuracy Disclaimer

The cost estimates are based on EU transitional default values and represent conservative estimates using **direct emissions only**. Actual CBAM costs will depend on:

- Supplier-specific emission data
- Indirect emissions from electricity consumption  
- Installation-specific factors
- Regulatory developments and benchmark updates
- Verification requirements and methodologies

For production implementation, comprehensive emission factors including Scope 2 emissions should be integrated for more accurate cost projections.

---

*This tool is proprietary software developed by Entrenovu for CBAM compliance assessment and cost estimation.*
