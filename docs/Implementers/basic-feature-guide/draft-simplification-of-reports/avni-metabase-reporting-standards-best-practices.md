---
title: 'Avni Metabase Reporting : Standards & Best Practices'
excerpt: >-
  Based on the Metabase reporting principles you've outlined, here's a
  comprehensive review and elaboration of these best practices for Avni
  reporting:
deprecated: false
hidden: false
metadata:
  robots: index
---
### **1. Dashboard Structure Standards**

#### **Three-Tier Dashboard Layout:**

```
Row 1: Summary & Description
├── Dashboard title and purpose description
├── Explanation of Filters available
└── Key metrics overview

Row 2: Total Count Cards  
├── Aggregate metrics from base query
└── Apply Conditional filters only where appropriate

Row 3+: Filtered Linelists
├── Detailed records for each count card
└── Conditional filters applied
```

#### **Implementation Guidelines:**

* **Base Query Foundation**: All dashboard elements derive from a single, well-defined base query
* **Hierarchical Information Flow**: Summary → Aggregates → Details
* **Consistent Filtering**: Apply same filter logic across all dashboard components
* **User Journey**: Enable logical progression from high-level insights to detailed records

### **2. Dashboard Purity Principle**

#### **Primary Table Focus:**

* **Single Source of Truth**: Each dashboard should have one primary table as its foundation
* **Controlled Joins**: Join additional tables only for supplementary information, not core metrics
* **Avoid Data Mixing**: Don't combine unrelated data sources in a single dashboard

#### **When to Split Dashboards:**

```
❌ Bad: Combined Dashboard
- Subject registrations + Program enrollments + Encounter data
- Multiple unrelated KPIs in one view
- Mixed time periods and contexts

✅ Good: Separate Dashboards  
- Subject Registration Dashboard (primary: individual table)
- Program Performance Dashboard (primary: program_enrolment table)
- Service Delivery Dashboard (primary: encounter tables)
```

#### **Benefits of Pure Dashboards:**

* **Performance**: Faster query execution
* **Maintainability**: Easier to debug and modify
* **User Experience**: Clear, focused insights
* **Data Integrity**: Reduced risk of incorrect joins

### **3. Complex Query Management via PostETLSync**

#### **PostETLSync Overview**

PostETLSync enables custom data transformations after standard ETL completion through configurable SQL transformations. This feature supports incremental processing, ordered execution, and organization-specific configurations while ensuring data integrity through transaction management.

#### **When to Use PostETLSync:**

* **Views Persisted Across ETL Schema Recreates**: Maintain custom views and derived tables that survive ETL schema rebuilds
* **Derived Tables**: Create complex aggregated views from multiple source tables
* **Custom Business Logic**: Implement organization-specific calculations and transformations
* **Performance Optimization**: Pre-compute complex queries for faster dashboard loading
* **Incremental Updates**: Process only changed data since last sync using timestamp filtering

#### **Configuration Structure:**

PostETLSync uses JSON configuration files (`post-etl-sync-processing-config.json`) with two main sections:

* **DDL Operations**: Create tables, indexes, and database objects (executed first)
* **DML Operations**: Insert and update data with ordered execution and parameter substitution

#### **Key Features:**

* **Automatic Parameter Substitution**: `:previousCutoffDateTime` and `:newCutoffDateTime` for incremental processing
* **Schema-Qualified Operations**: All table references must include schema names for proper permissions
* **Timestamp Filtering**: Built-in support for processing only modified records
* **Transaction Safety**: Ensures data consistency during transformation processes

#### **Best Practices:**

* Always use both timestamp parameters in data modification queries
* Include `is_voided = false` checks when applicable
* Use descriptive prefixes for SQL script naming
* Apply timestamp filters to all subqueries and CTEs
* Begin DDL scripts with appropriate role setting for permissions

This approach transforms complex reporting requirements into maintainable, performant solutions that integrate seamlessly with Avni's ETL pipeline while providing the flexibility needed for organization-specific reporting needs.

### **4. Implementation Best Practices**

#### **Base Query Design:**

```sql
-- Standard base query structure
SELECT 
    -- Primary identifiers
    subject.uuid,
    subject.first_name,
    subject.last_name,
    
    -- Core metrics
    program.name as program_name,
    enrolment.enrolment_date_time,
    
    -- Derived fields for filtering
    CASE WHEN enrolment.program_exit_date_time IS NULL 
         THEN 'Active' ELSE 'Exited' END as enrollment_status,
    
    -- Location hierarchy for geographic filtering
    village.title as village,
    block.title as block,
    district.title as district

FROM individual subject
JOIN program_enrolment enrolment ON subject.id = enrolment.individual_id
JOIN program ON enrolment.program_id = program.id
-- Add location joins as needed
WHERE subject.is_voided = false 
  AND enrolment.is_voided = false
```

#### **Dashboard Card Organization:**

1. **Summary Card**: Dashboard description and key insights
2. **Count Cards**: Total enrollments, active cases, completed visits
3. **Linelist Cards**: Detailed records filtered by each count metric

#### **Filter Strategy:**

* **Consistent Parameters**: Same date ranges, locations, programs across all cards
* **Cascading Filters**: Location hierarchy (State → District → Block → Village)
* **User-Friendly Defaults**: Reasonable default values for common use cases

### **5. Quality Assurance Guidelines**

#### **Dashboard Review Checklist:**

* [ ] Single primary table identified
* [ ] All cards use same base query logic
* [ ] Filters work consistently across all cards
* [ ] Performance acceptable (\< 30 seconds load time)
* [ ] Data accuracy verified against source systems
* [ ] User permissions properly configured

#### **Documentation Requirements:**

* Dashboard purpose and target audience
* Base query explanation and assumptions
* Filter definitions and expected behaviors

### **6. Reference Implementation**

Following the <Anchor label="Avni reporting simplification guidelines" target="_blank" href="https://avni.readme.io/docs/draft-simplification-of-reports">Avni reporting simplification guidelines</Anchor>, these principles ensure:

* **Scalable Architecture**: Reports that perform well as data grows
* **Maintainable Codebase**: Clear separation of concerns
* **User-Centric Design**: Intuitive navigation from summary to detail
* **Data Governance**: Consistent metrics across the organization

This structured approach transforms complex organisational data into actionable insights while maintaining system performance and user experience quality.