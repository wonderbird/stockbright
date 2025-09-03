# Stockbright Technical Context

## Technology Stack

### Platform
- **Target**: Smartphone application
- **Primary Platform**: Mobile (iOS/Android implied)
- **Localization**: German/English support

### Data Management

#### CSV Format Specifications
- **Encoding**: UTF-8
- **Separator**: Semicolon (;)
- **Quote Handling**: Optional quotes, required for semicolon-containing values
- **Header**: `Item;Stock Quantity;Reorder Level;Storage Location;Packaging Unit`

#### Data Fields
| Field | Type | Constraints |
|-------|------|-------------|
| Item | Text | Required, unique, max 255 chars |
| Stock Quantity | Decimal | Required, ≥ 0 |
| Reorder Level | Decimal | Optional, ≥ 0 or empty |
| Storage Location | Text | Required, max 100 chars |
| Packaging Unit | Text | Required, from standardized list |

#### Standardized Packaging Units
- bags, bottles, boxes, cans, cartons, heads, jars
- Package (500g), pcs, portions, sachets, tubs, wrapped blocks

### Performance Requirements

#### Scale Targets
- **Items**: Up to 500 household inventory items
- **Locations**: Up to 60 storage locations
- **Import Speed**: Complete CSV restore in <15 seconds
- **File Size**: No specific limits (performance-based)

#### Processing Requirements
- **Atomic Operations**: All-or-nothing data imports
- **Error Handling**: Skip invalid records, process valid ones
- **Data Integrity**: Zero data loss for valid records

### Data Validation

#### CSV Import Validation
- Header row validation (case-sensitive)
- Field type validation (decimals, text)
- Packaging unit validation against standardized list
- Duplicate item name detection
- Storage location constraint enforcement

#### Error Scenarios
- Invalid data in first/last records
- Negative quantities
- Invalid packaging units
- Empty required fields
- Malformed quotation marks
- Unescaped semicolons

### Development Standards

#### Quality Requirements
- Follow established team code quality standards
- Seamless integration with existing application
- Consistent user experience patterns
- Mobile-optimized interface

#### Testing Requirements
- Quantitative verification of import accuracy
- Random sampling validation
- Edge case handling
- Performance testing at scale
- User acceptance testing

### Reference Data

#### Sample Dataset
- **File**: `inventory_export.csv`
- **Records**: 56 sample inventory items
- **Locations**: Multiple storage locations (Vorratsregal, Eckschrank, Gefrierschrank, Küche, Büro, Ölkeller)
- **Variety**: Demonstrates all packaging unit types and data patterns

### Current Implementation Status
- **Documentation**: Complete specifications available
- **Test Data**: Sample CSV files prepared
- **Acceptance Criteria**: Fully defined
- **Code Implementation**: Pending development phase