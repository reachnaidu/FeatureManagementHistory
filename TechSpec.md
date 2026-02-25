
### Components

#### 1. **FeatureManagementHistory Table**
Custom table that stores feature management history records with the following key fields:
- `productName` - Name of the Dynamics 365 product
- `platformName` - Platform version identifier
- `productVersion` - Product semantic version
- `productBuildVersion` - Product build number
- `platformBuildVersion` - Platform build number
- `FeatureLabel` - Localized feature display name
- `ModuleName` - Feature module/category
- `FeatureState` - Current feature state
- `Summary` - Feature description
- `deployedDate` - UTC deployment timestamp
- `CanDisable` - Flag indicating if feature can be disabled
- `IsEnabledByDefault` - Flag for default enabled state
- `Type` - Feature type classification
- `LifecycleStage` - Current lifecycle stage (Preview, Public, Deprecated, etc.)
- `LearnMoreUrl` - Documentation reference URL

#### 2. **FeatureManagementHistoryForm**
Main form that provides user interface and business logic:

**Key Methods:**

- `init()` - Form initialization entry point
  - Calls `populateFeatureManagementHistory()` to update table data
  - Calls `populateVersionComboBox()` to populate filter dropdown
  
- `populateFeatureManagementHistory()` - Data synchronization method
  - Retrieves current product and platform version information from `ProductInfoProvider`
  - Queries `FEATUREMANAGEMENTMETADATA` table for features modified since last deployment
  - Inserts new records into `FeatureManagementHistory` with complete metadata
  - Implements duplicate detection to prevent re-insertion
  
- `populateVersionComboBox()` - Combo box initialization
  - Queries distinct product/platform version combinations
  - Formats as composite keys: `productName - productVersion - platformName - productBuildVersion - platformBuildVersion`
  - Populates dropdown with sorted results
  
- `applyVersionFilter(str _selectedKey)` - Dynamic filtering
  - Parses composite key from selected dropdown value
  - Applies query ranges to datasource
  - Refreshes grid with filtered results

**Color Coding in Display (displayOption method):**

| Condition | Color | RGB Values |
|-----------|-------|-----------|
| CanDisable = 1 | Green | 198, 239, 206 |
| CanDisable = 0 | Red | 255, 199, 206 |
| IsEnabledByDefault = 1 | Yellow/Amber | 255, 214, 153 |

#### 3. **Security Privilege**
`FeatureManagementHistoryFormView` privilege controls access to the form.

## Installation

### Prerequisites
- Dynamics 365 Finance and Operations environment (version 10.0+)
- Development access to Visual Studio or Web-based IDE
- Appropriate privileges to deploy custom objects

### Steps

1. **Import the Model/Package**
   - Export the `CustomXppMetadata10046` project
   - Import into your Dynamics 365 environment using Visual Studio or Package Deployment

2. **Deploy Objects**
   - Compile the table: `FeatureManagementHistory`
   - Compile the form: `FeatureManagementHistoryForm`
   - Sync the database schema

3. **Assign Privileges**
   - Assign the `FeatureManagementHistoryFormView` privilege to relevant security roles
   - Ensure users have table access through Data Security Policies if applicable

4. **Verify Installation**
   - Navigate to the form in your Dynamics 365 environment
   - Form should auto-populate on first load
   - Verify dropdown appears with version combinations

## Usage

### Initial Load
When the form opens for the first time:
1. `populateFeatureManagementHistory()` executes automatically
2. New feature data is captured from `FEATUREMANAGEMENTMETADATA`
3. Records are inserted into `FeatureManagementHistory`
4. Dropdown is populated with distinct version keys
5. Grid displays all captured features

### Filtering
1. Click the **Version Filter** dropdown
2. Select a specific product/platform version combination
3. Grid updates automatically to show only matching records
4. Select blank entry to show all records

### Interpreting Results
- **Green rows**: Features you can toggle on/off
- **Red rows**: Mandatory features that cannot be disabled
- **Yellow/Amber rows**: Features enabled by default (may require explicit disabling if needed)

## Key Design Decisions

### Duplicate Prevention
The form implements two-level duplicate detection:
1. Checks for any record with `CreatedDateTime > utcDeployedDate`
2. Checks for specific product/platform version combination
- Prevents unnecessary data duplication on repeated form loads

### Composite Key Filtering
Uses hyphen-separated composite keys (`-` delimiter) to:
- Combine five dimension fields into a single filter value
- Enable user-friendly dropdown display
- Support multi-field filtering with a single selection

### Localization
- Feature labels and summaries are automatically localized using `SysLabel::labelId2String()`
- System language ID is obtained from `SystemParameters::getSystemLanguageId()`

### UTC DateTime Handling
- Uses UTC timestamps to ensure consistency across time zones
- Deployment date is set to UTC midnight + 1 second for accurate cutoff calculation

## Development Notes

### Adding New Fields
To capture additional feature metadata:

1. Add field to `FeatureManagementHistory` table
2. Update `populateFeatureManagementHistory()` method to map the field
3. Add grid column if visibility is needed

### Modifying Color Coding
Edit the `displayOption()` method in the datasource class:
