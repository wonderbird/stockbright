# Stockbright Project Brief

## Project Overview

**Stockbright** is a family household inventory management smartphone application designed to help families track their household stock and plan shopping efficiently.

## Core Mission

"Manage household inventory and plan your shopping. Never forget anything again."

## Target Audience

- Families with children
- People with special dietary requirements
- Households managing domestic inventory

## Key Features

### Current State
1. **CSV Backup/Restore Functionality**
   - Import/export complete inventory from CSV files
   - UTF-8 encoded with semicolon separators
   - Handles 500+ items across 60+ storage locations
   - Atomic operations with error handling

### Planned Features
2. **Inventory Management**
   - Track stock quantities and reorder levels
   - Organize by storage locations
   - Support standardized packaging units
   
3. **Shopping Planning**
   - Generate shopping lists based on reorder levels
   - Never forget essential items

## Technical Specifications

### Data Structure
- **Items**: Name, stock quantity, reorder level, storage location, packaging unit
- **Storage Locations**: Multiple locations per household
- **Packaging Units**: Standardized list (bags, bottles, boxes, cans, cartons, heads, jars, etc.)

### Performance Requirements
- Process 500 items in <15 seconds
- Support 60+ storage locations
- Mobile-optimized interface

### Data Format
```csv
Item;Stock Quantity;Reorder Level;Storage Location;Packaging Unit
```

## Current Development Phase

The project is in the **documentation and specification phase** with detailed:
- Acceptance tests defined
- Sample data available (56 inventory items)
- Interface specifications completed
- CSV import/export fully specified

## Success Metrics

- Complete inventory data migration capability
- Zero data loss for valid records
- Graceful error handling for invalid data
- User-friendly mobile interface
- German/English localization support