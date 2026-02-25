
***

# **Feature Management History – Quick Start**

## **What this project includes**

*   A table (**FeatureManagementHistory**) that stores feature metadata.
*   A form (**FeatureManagementHistoryForm**) that loads the data and shows it in a grid.
*   Basic filtering using a version dropdown.
*   Color‑coded rows (Green = can disable, Red = mandatory, Yellow = enabled by default).

***

# **How to Install**

### **1. Import the project**

*   Import the **FMHProjectPackage.axpp** Project /package into your D365 FO development environment.

### **2. Build & Sync**

*   Compile:
    *   **FeatureManagementHistory** table
    *   **FeatureManagementHistoryForm** form
*   Run a **DB Synchronization**.

### **3. Assign Security**

*   Give users the **FeatureManagementHistoryFormView** privilege so they can open the form.

***

# **How to Use**

### **Open the Form**

*   Navigate to **Feature Management History** in the menu.
*   The form will:
    *   Load feature metadata automatically
    *   Insert records into the history table
    *   Populate the version filter dropdown

### **Filter by Version**

*   Open the **Version Filter** dropdown.
*   Pick a version combination to filter the grid.
*   Choose blank to view all records again.



***

