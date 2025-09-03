# CSV Backup Restore - Test Specification

## Test Specification Document
**Feature:** CSV Backup Restore for Stock Management App  
**Document Version:** 1  
**Created:** August 31, 2025  
**Target Application:** Stock Management Smartphone App (Family Household Use)

---

## Feature File: csv_backup_restore.feature

```gherkin
Feature: CSV Backup Restore
  As a family household user of the stock management app
  I want to restore my complete inventory data from a CSV backup file
  So that I can transfer data from another device or recover from a backup

  Background:
    Given the stock management app is installed and running

  # =================================================================
  # SECTION 1: Navigation and Access Tests (No data changes)
  # =================================================================

  Rule: Users can access restore functionality through settings

    Scenario: Navigate to CSV restore function
      Given the app is freshly installed with no inventory data
      And I am on the main screen
      When I navigate to the Settings menu
      And I go to the "Backup and Restore" section
      Then I should see a "Restore from CSV" option

    Scenario: Open file picker for CSV restore
      Given I am in the "Backup and Restore" section of Settings
      When I tap on "Restore from CSV"
      Then a standard file picker should be displayed
      And the file picker should allow selection of CSV files

  # =================================================================
  # SECTION 2: Format Validation Tests (No successful imports)
  # =================================================================

  Rule: CSV format validation ensures data integrity

    Scenario: Invalid CSV header format
      Given I am in the file picker for CSV restore
      And I have a CSV file "invalid_headers.csv" with incorrect headers "Product;Qty;Level;Location;Unit"
      When I select the CSV file and confirm the restore
      Then the completion message should indicate the file format is invalid
      And no data should be imported
      And the app should still contain 0 inventory items

    Scenario: Empty CSV file handling
      Given I am in the file picker for CSV restore
      And the app currently contains 0 inventory items
      And I have an empty CSV file "empty_file.csv" with headers only
      When I select the CSV file and confirm the restore
      Then the completion message should show "0 records imported successfully, 0 records could not be imported"
      And the app should still contain 0 inventory items

  # =================================================================
  # SECTION 3: Basic Successful Import Tests (Building up data)
  # =================================================================

  Rule: Valid CSV files are processed successfully

    Scenario: First successful restore with 3 basic items
      Given I am in the file picker for CSV restore
      And the app currently contains 0 inventory items
      And I have a valid CSV file "basic_3items.csv" with these items:
        | Item        | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Milk        | 2              | 1             | Küche           | bottles        |
        | Bread       | 1              | 1             | Küche           | pcs            |
        | Apples      | 5              | 3             | Küche           | pcs            |
      When I select the CSV file and confirm the restore
      Then the restore process should complete within 15 seconds
      And I should see a completion message showing "3 records imported successfully, 0 records could not be imported"
      And the app should contain exactly 3 inventory items

    Scenario: Replace existing data with 5 different items
      Given I am in the file picker for CSV restore
      And the app currently contains 3 inventory items from the previous test
      And I have a valid CSV file "household_5items.csv" with these items:
        | Item          | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Orange Juice  | 2              | 1             | Küche           | cartons        |
        | Pasta         | 4              | 2             | Pantry          | boxes          |
        | Olive Oil     | 1              | 1             | Pantry          | bottles        |
        | Detergent     | 3              | 1             | Bathroom        | bottles        |
        | Toilet Paper  | 12             | 6             | Bathroom        | wrapped blocks |
      When I select the CSV file and confirm the restore
      Then the completion message should show "5 records imported successfully, 0 records could not be imported"
      And the app should contain exactly 5 items (the new ones)
      And none of the original 3 items should exist anymore

  # =================================================================
  # SECTION 4: Data Field Validation Tests (Using current 5-item state)
  # =================================================================

  Rule: Data field validation follows business rules

    Scenario: Import with mixed valid packaging units
      Given I am in the file picker for CSV restore
      And the app currently contains 5 inventory items
      And I have a CSV file "packaging_test.csv" with these 6 items testing different units:
        | Item             | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Canned Beans     | 8              | 4             | Pantry          | cans           |
        | Fresh Lettuce    | 2              | 1             | Küche           | heads          |
        | Jam              | 3              | 1             | Pantry          | jars           |
        | Butter           | 2              | 1             | Küche           | wrapped blocks |
        | Soup Mix         | 10             | 5             | Pantry          | sachets        |
        | Ice Cream        | 1              | 1             | Küche           | tubs           |
      When I select the CSV file and confirm the restore
      Then the completion message should show "6 records imported successfully, 0 records could not be imported"
      And the app should contain exactly 6 items with correct packaging unit translations

  # =================================================================
  # SECTION 5: Error Handling with Mixed Valid/Invalid Data
  # =================================================================

  Rule: Invalid records are skipped with detailed error reporting

    Scenario: Mixed valid and invalid records with detailed error reporting
      Given I am in the file picker for CSV restore
      And the app currently contains 6 inventory items from packaging tests
      And I have a CSV file "mixed_validity.csv" with these 8 records:
        | Row | Item Name       | Stock Quantity | Reorder Level | Storage Location | Packaging Unit | Validity |
        | 2   | Coffee          | 3              | 2             | Küche           | bags           | valid    |
        | 3   | Sugar           | 1              | 1             | Küche           | boxes          | valid    |
        | 4   | Expired Milk    | -2             | 1             | Küche           | bottles        | invalid  |
        | 5   | Tea             | 4              | 2             | Küche           | boxes          | valid    |
        | 6   | Special Sauce   | 1              | 1             | Garage          | invalid_unit   | invalid  |
        | 7   | Cookies         | 0              | 2             | Pantry          | boxes          | valid    |
        | 8   | Premium Coffee  |                | 1             | Pantry          | bags           | invalid  |
        | 9   | Rice            | 2              | 1             | Pantry          | bags           | valid    |
      When I select the CSV file and confirm the restore
      Then the completion message should show "5 records imported successfully, 3 records could not be imported"
      And the error details should include "Row 4: Expired Milk (Küche)"
      And the error details should include "Row 6: Special Sauce (Garage)"
      And the error details should include "Row 8: Premium Coffee (Pantry)"
      And the app should contain exactly 5 valid items

    Scenario: Duplicate item names handling
      Given I am in the file picker for CSV restore
      And the app currently contains 5 inventory items
      And I have a CSV file "duplicates.csv" with duplicate "Coffee" items at rows 2 and 5:
        | Row | Item Name | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | 2   | Coffee    | 3              | 2             | Küche           | bags           |
        | 3   | Sugar     | 1              | 1             | Küche           | boxes          |
        | 4   | Tea       | 4              | 2             | Küche           | boxes          |
        | 5   | Coffee    | 2              | 1             | Pantry          | jars           |
      When I select the CSV file and confirm the restore
      Then the completion message should show "3 records imported successfully, 1 records could not be imported"
      And the error details should include "Row 5: Coffee (Pantry) - duplicate item name"
      And the app should contain exactly 3 items
      And the Coffee item should have the values from row 2

  # =================================================================
  # SECTION 6: CSV Formatting and Encoding Tests
  # =================================================================

  Rule: CSV formatting and encoding requirements

    Scenario: UTF-8 encoding and quoted values with special characters
      Given I am in the file picker for CSV restore
      And the app currently contains 3 inventory items
      And I have a UTF-8 CSV file "special_chars.csv" with these items:
        | Item Name               | Stock Quantity | Reorder Level | Storage Location | Packaging Unit |
        | Würstchen               | 4              | 2             | Kühlschrank     | packages       |
        | Ben's "Special" Sauce   | 1              | 1             | Küche           | bottles        |
        | Müsli                   | 2              | 1             | Küche           | boxes          |
      When I select the CSV file and confirm the restore
      Then the completion message should show "3 records imported successfully, 0 records could not be imported"
      And all German characters should display correctly as "Kühlschrank"
      And the quoted item name should display as "Ben's \"Special\" Sauce"

  # =================================================================
  # SECTION 7: Edge Cases and Boundary Testing
  # =================================================================

  Rule: Edge cases are handled appropriately

    Scenario: All records invalid - no data imported
      Given I am in the file picker for CSV restore
      And the app currently contains 3 inventory items from the previous test
      And I have a CSV file "all_invalid.csv" where all 4 records have invalid data:
        | Row | Item Name    | Stock Quantity | Storage Location | Issue                |
        | 2   | Bad Item 1   | -5             | Küche           | negative quantity    |
        | 3   | Bad Item 2   | 2              | Küche           | invalid_packaging    |
        | 4   |              | 1              | Küche           | empty item name      |
        | 5   | Bad Item 4   | 1              |                 | empty storage        |
      When I select the CSV file and confirm the restore
      Then the completion message should show "0 records imported successfully, 4 records could not be imported"
      And the app should still contain the same 3 items as before (no changes)
      And error details should list all 4 failed rows with specific reasons

    Scenario: Field length validation
      Given I am in the file picker for CSV restore
      And the app currently contains 3 inventory items
      And I have a CSV file "field_length.csv" with one item having a 300-character name
      When I select the CSV file and confirm the restore
      Then the record with the long name should be rejected
      And the error should mention "field length violation" and the row number

  # =================================================================
  # SECTION 8: Performance Testing (Uses large dataset)
  # =================================================================

  Rule: Performance requirements are met

    Scenario: Performance test with target scale (500 items)
      # Note: This test requires fresh app state due to the large dataset
      Given I reset the app to have 0 inventory items
      And I am in the file picker for CSV restore
      And I have a CSV file "performance_500items.csv" with 500 items across 60 storage locations
      When I select the CSV file and confirm the restore
      Then the restore operation should complete within 15 seconds
      And the completion message should show "500 records imported successfully, 0 records could not be imported"
      And the app should contain exactly 500 items

  # =================================================================
  # SECTION 9: Data Verification (Uses the 500-item dataset from performance test)
  # =================================================================

  Rule: Data verification after successful restore

    Scenario: Random sampling verification of large dataset
      Given the app contains 500 inventory items from the performance test
      When I navigate to storage location "Küche" (which should contain 25 items from the CSV)
      Then all items and their values should match the CSV data exactly
      And German translations should display correctly for packaging units
      And all numerical values should be preserved with correct precision

    Scenario: Complete data count verification
      Given the app contains inventory from the 500-item performance test
      When I count the total items across all storage locations
      Then the count should exactly match 500 items
      And items should be distributed across 60 different storage locations

  # =================================================================
  # SECTION 10: User Experience and Atomic Operations
  # =================================================================

  Rule: User experience during restore operation

    Scenario: Restore confirmation and progress (using medium dataset)
      # Note: Reset to known state for consistent UX testing
      Given I reset the app to contain exactly 10 test items
      And I am in the file picker for CSV restore  
      And I have selected a valid CSV file with 50 items
      When I confirm the file selection
      Then I should see a confirmation dialog warning that all existing data will be replaced
      When I proceed with the restore
      Then I should see some indication that the operation is running
      And the UI should remain responsive
      And upon completion, I should see "50 records imported successfully, 0 records could not be imported"

  Rule: Error recovery and rollback behavior

    Scenario: File access error handling
      Given I reset the app to contain exactly 5 test items
      And I am in the file picker for CSV restore
      When I attempt to select a CSV file that becomes inaccessible during the operation
      Then I should receive an appropriate error message
      And the app should still contain the original 5 inventory items unchanged
```

## Test Execution Sequence Notes

### Sequential Execution Benefits:
1. **Sections 1-2**: Start with navigation and validation tests that don't import data
2. **Section 3**: Build up from 0→3→5 items, establishing a known baseline
3. **Section 4**: Use the 5-item state for packaging validation, growing to 6 items
4. **Section 5**: Test error handling with mixed data, using current state as baseline
5. **Section 6**: Test special formatting while maintaining manageable dataset size
6. **Section 7**: Edge cases that may leave app in various states
7. **Section 8**: Performance test requiring reset due to large dataset size
8. **Section 9**: Verification tests that leverage the 500-item performance test data
9. **Section 10**: UX tests requiring specific controlled states

### Required Test Data Files:
1. **invalid_headers.csv** - Wrong header format
2. **empty_file.csv** - Headers only
3. **basic_3items.csv** - 3 simple valid items  
4. **household_5items.csv** - 5 household items
5. **packaging_test.csv** - 6 items testing all packaging units
6. **mixed_validity.csv** - 8 items (5 valid, 3 invalid with specific row numbers)
7. **duplicates.csv** - 4 items with duplicate names at specific rows
8. **special_chars.csv** - 3 items with UTF-8 and quoted values
9. **all_invalid.csv** - 4 completely invalid items
10. **field_length.csv** - Item with 300-character name
11. **performance_500items.csv** - 500 items across 60 locations

### State Reset Points:
- **Performance test** (Section 8): Requires reset due to dataset size
- **UX tests** (Section 10): Require controlled states for consistent testing

This organization minimizes setup overhead while ensuring comprehensive coverage and realistic test flow.