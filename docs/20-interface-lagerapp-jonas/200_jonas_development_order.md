# Development Order - CSV Backup Restore

**Date:** August 30, 2025 14:00 CET  
**Document Version:** 1  
**Requested By:** Stefan Boos  
**Target System:** Stock Management Smartphone App (Family Household Use)  

## 1. Commission Overview

The development team is commissioned to implement a **CSV backup restore functionality** for the existing stock management smartphone application. This feature enables users to restore their complete inventory data from a CSV backup file, typically when transferring data from another device or application.

**Target User Group:** Family households managing domestic inventory

## 2. Functional Requirements

### 2.1 Core Functionality

**Restore Operation:**
- User can select a CSV backup file from their device
- System replaces ALL existing inventory data with data from the CSV file
- Operation is atomic: either all valid data is restored, or no changes are made
- Invalid records are skipped; valid records are processed

**Use Case:**
- Loading inventory data from another device or application

## 3. Data Format Specification

### 3.1 CSV File Requirements

**File Format:** UTF-8 encoded CSV with semicolon separators  
**Header Row:** Exactly as specified (case-sensitive):

```csv
Item;Stock Quantity;Reorder Level;Storage Location;Packaging Unit
```

**CSV Formatting Rules:**
- Quotation marks are optional for values (single word or multi-word)
- Quotation marks are required only if the value contains a semicolon
- Quotation marks within values must be escaped with backslash
- Example: `Ben's "Special" Sauce;1;2;Küche;bottles` or `"Product; with semicolon";3;5;Storage;jars`

### 3.2 Data Field Specifications

| Field Name | Data Type | Required | Constraints |
|------------|-----------|----------|-------------|
| **Item** | Text | Yes | Unique within file, max 255 characters |
| **Stock Quantity** | Decimal | Yes | ≥ 0 |
| **Reorder Level** | Decimal | No | ≥ 0 or empty |
| **Storage Location** | Text | Yes | Max 100 characters |
| **Packaging Unit** | Text | Yes | Must be from standardized list |

### 3.3 Valid Packaging Units

Only these standardized packaging units are accepted:
- bags, bottles, boxes, cans, cartons, heads, jars, Package (500g), pcs, portions, sachets, tubs, wrapped blocks

### 3.4 Sample Data

Reference file `inventory_export.csv` contains 56 sample inventory items demonstrating the expected format and data structure.

## 4. Performance Requirements

- **Processing Time:** Restore operation must complete within 15 seconds for importing 500 items distributed across 60 storage locations
- **Target Scale:** System optimized for family household inventory management (expected maximum: 500 items, 60 storage locations)
- **File Size:** No specific limits defined (if performance issues arise at target scale, discussion required)

## 5. Acceptance Criteria

### 5.1 Successful Restore Verification

**Quantitative Verification:**
- Number of items in the application after restore matches the number of valid items in the CSV file
- Zero data loss for valid records

**Sample Testing:**
- Random storage location selected from CSV → verify all items and values display correctly in UI
- Random storage location selected from UI → verify all items and values match CSV data
- German translations display correctly in UI (packaging units, storage locations)

### 5.2 Error Handling Verification

**Invalid Data Handling:**
The system must gracefully handle and skip invalid records while processing valid ones.

**Test Scenarios:**
- CSV with invalid data in first record → remaining valid records processed
- CSV with invalid data in last record → preceding valid records processed  
- CSV with negative quantities → record skipped, others processed
- CSV with invalid packaging units → record skipped, others processed
- CSV with empty required fields → record skipped, others processed
- CSV with malformed quotation marks or values with unescaped semicolons → appropriate error handling

**Expected Behavior:**
- Invalid records are skipped without stopping the entire operation
- User receives feedback about the restore operation results
- Valid records are successfully restored regardless of invalid records present

### 5.3 Edge Cases

- Empty CSV file (headers only)
- CSV file with no valid records
- CSV file with duplicate item names
- Large CSV files at target scale (500 items, 60 storage locations)
- CSV files exceeding target scale (performance validation)

## 6. Quality Standards

- Implementation must follow team's established code quality standards
- Feature must integrate seamlessly with existing application
- User experience should be consistent with existing app patterns

## 7. Deliverables

### 7.1 Required Deliverables

- **Functional Feature:** Working CSV backup restore functionality

### 7.2 Acceptance Process

**User Acceptance Testing:**
- Customer will perform acceptance testing using the criteria defined in Section 5
- Any deviations from specified requirements must be discussed and approved

**Sign-off Criteria:**
- All acceptance criteria pass verification
- Performance requirements met
- Error handling functions as expected

## 8. Reference Materials

### 8.1 Data Files

- `inventory_export.csv` - Sample data file with 56 inventory items

### 8.2 Packaging Units Reference

| English Term | German Translation | Description |
|--------------|-------------------|-------------|
| **bags** | Beutel / Tüten | Flexible containers made of plastic, paper, or fabric |
| **bottles** | Flaschen | Rigid containers with narrow necks for liquids |
| **boxes** | Schachteln / Kartons | Rigid rectangular containers, usually cardboard |
| **cans** | Dosen | Metal cylindrical containers for preserved foods |
| **cartons** | Kartons | Paperboard containers for liquids (milk, juice) |
| **heads** | Köpfe | Whole vegetable heads (lettuce, cabbage, etc.) |
| **jars** | Gläser | Glass containers with wide mouths and lids |
| **Package (500g)** | 500g-Paket | Specific weight-based packaging unit |
| **pcs** | Stück | Individual countable pieces (ISO standard abbreviation) |
| **portions** | Portionen | Pre-divided serving-sized quantities |
| **sachets** | Beutelchen / Portionsbeutel | Small sealed packets containing single servings |
| **tubs** | Becher / Dosen | Wide, shallow containers with lids (margarine, ice cream) |
| **wrapped blocks** | Blockpackungen | Items wrapped in foil or paper (butter, cheese blocks) |

**Translation Notes:**
- **bags** covers various German terms like "Beutel" (plastic bags), "Tüten" (paper bags)
- **cartons** specifically refers to liquid containers ("Getränkekartons") 
- **boxes** refers to rigid cardboard packaging ("Schachteln" for small boxes, "Kartons" for larger ones)
- **pcs** follows ISO 80000-1 standard for "pieces"
- **sachets** are small portion packets, distinct from larger bags
- **tubs** and **wrapped blocks** provide specific clarity for different dairy product packaging

### 8.3 Context

This restore functionality enables data migration between devices and applications, supporting user workflows where inventory data needs to be transferred or recovered from backups.