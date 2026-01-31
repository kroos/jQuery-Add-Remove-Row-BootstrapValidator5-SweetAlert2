# jQuery addRemRow Plugin

A powerful, feature-rich jQuery plugin for dynamically adding and removing form rows with support for validation, SweetAlert2 confirmations, nested fields, and comprehensive reindexing.

## Features

- ✅ Dynamic row addition/removal with configurable limits
- ✅ BootstrapValidator integration
- ✅ SweetAlert2 confirmation with AJAX delete support
- ✅ Nested field support
- ✅ Automatic reindexing of names, IDs, and data attributes
- ✅ Comprehensive public API for programmatic control
- ✅ Event callbacks for customization
- ✅ Row templating
- ✅ Data management methods
- ✅ Filtering and sorting capabilities

## Installation

### Dependencies

Required:
- **jQuery 3.6+**

Optional:
- **Bootstrap 4/5** (for styling, optional for functionality)
- **BootstrapValidator** (for validation, optional)
- **SweetAlert2** (for delete confirmations)
- **Font Awesome** (for icons)

### Basic Setup

```html
<!-- Required CSS (Bootstrap) -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Required JS -->
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>

<!-- Optional: SweetAlert2 -->
<script src="https://cdn.jsdelivr.net/npm/sweetalert2@11"></script>

<!-- Plugin -->
<script src="addRemRowValidator5Swal2Ajax.js"></script>
```

### NPM Installation
- ensure jQuery is loaded.

```npm

npm install addremrow-validator5-swal2-ajax
npm install bootstrapValidator5
npm install sweetalert2

```

then in app.js
a) webpack / laravel-mix
```
require('addremrow-validator5-swal2-ajax');

require('bootstrapValidator5');

window.swal = require ('sweetalert2');
````

b) Vite
```
import  'addremrow-validator5-swal2-ajax';

import Swal from 'sweetalert2';
window.Swal = Swal;

import  'bootstrapValidator5';
```


## Configuration Options

### Basic Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `addBtn` | string | `''` | jQuery selector for the "Add Row" button |
| `maxRows` | number | `5` | Maximum number of rows allowed |
| `startRow` | number | `0` | Starting index for rows |
| `fieldName` | string | `'data'` | Base field name for form inputs |
| `rowSelector` | string | `'rowserial'` | CSS class for row container |
| `removeClass` | string | `'serial_remove'` | CSS class for remove buttons |
| `nestedwrapper` | string | `null` | Selector for nested wrapper elements |
| `rowTemplate` | function | `null` | Function that returns HTML for new rows |
| `onAdd` | function | `null` | Callback triggered when adding a row |
| `onRemove` | function | `null` | Callback triggered before removing a row |

### Reindexing Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `reindexRowName` | array | `['name', 'data-bv-field', 'data-bv-for']` | Attributes containing field names to reindex |
| `reindexRowID` | array | `['id', 'for', 'aria-describedby']` | Attributes containing IDs to reindex |
| `reindexRowIndex` | array | `['data-index', 'data-id']` | Attributes containing index references |

### Validation Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `validator.form` | string | - | jQuery selector for the form element |
| `validator.fields` | object | - | BootstrapValidator field configurations |

### SweetAlert2 Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `swal.options` | object | See code | SweetAlert2 configuration options |
| `swal.ajax.url` | string | `null` | AJAX endpoint for delete operations |
| `swal.ajax.method` | string | `'DELETE'` | HTTP method for delete |
| `swal.ajax.dbPrimaryKeyId` | string | `'id'` | Database primary key field name |

## Usage Examples

### Basic Implementation

```javascript
$('#rowWrapper').addRemRow({
  addBtn: '#addRowBtn',
  maxRows: 10,
  fieldName: 'skills',
  rowSelector: 'skill-row',
  removeClass: 'btn-remove'
});
```

### With Custom Template

```javascript
$('#rowWrapper').addRemRow({
  addBtn: '#addRowBtn',
  maxRows: 5,
  fieldName: 'employees',
  rowTemplate: function(index, fieldName) {
    return `
      <div id="employee_${index}" class="employee-row">
        <input type="hidden" name="${fieldName}[${index}][id]">

        <div class="row">
          <div class="col-md-4">
            <input type="text"
                   name="${fieldName}[${index}][name]"
                   class="form-control"
                   placeholder="Name">
          </div>
          <div class="col-md-4">
            <input type="email"
                   name="${fieldName}[${index}][email]"
                   class="form-control"
                   placeholder="Email">
          </div>
          <div class="col-md-4">
            <button type="button"
                    class="btn btn-danger btn-remove"
                    data-index="${index}">Remove</button>
          </div>
        </div>
      </div>
    `;
  }
});
```

### With BootstrapValidator

```javascript
$('#rowWrapper').addRemRow({
  addBtn: '#addRowBtn',
  maxRows: 3,
  fieldName: 'contacts',
  validator: {
    form: '#myForm',
    fields: {
      '[name]': {
        validators: {
          notEmpty: { message: 'Name is required' }
        }
      },
      '[email]': {
        validators: {
          notEmpty: { message: 'Email is required' },
          emailAddress: { message: 'Invalid email format' }
        }
      }
    }
  }
});
```

### With SweetAlert2 Delete Confirmation

```javascript
$('#rowWrapper').addRemRow({
  addBtn: '#addRowBtn',
  fieldName: 'products',
  swal: {
    options: {
      title: 'Delete Product',
      text: 'This will permanently delete the product!',
      icon: 'warning',
      confirmButtonText: 'Delete',
      cancelButtonText: 'Cancel'
    },
    ajax: {
      url: '/api/products',
      method: 'DELETE',
      dbPrimaryKeyId: 'product_id'
    }
  }
});
```

### With Nested Wrappers

```javascript
$('#rowWrapper').addRemRow({
  addBtn: '#addRowBtn',
  fieldName: 'categories',
  nestedwrapper: '.nested-fields',
  rowTemplate: function(index, fieldName) {
    return `
      <div id="category_${index}" class="category-row">
        <input type="text"
               name="${fieldName}[${index}][name]"
               placeholder="Category Name">

        <div class="nested-fields" id="nested_${index}">
          <!-- Nested fields with their own reindexing -->
          <input type="text"
                 name="${fieldName}[${index}][sub][0][name]"
                 data-index="${index}_0">
          <input type="text"
                 name="${fieldName}[${index}][sub][1][name]"
                 data-index="${index}_1">
        </div>

        <button class="btn-remove" data-index="${index}">Remove</button>
      </div>
    `;
  }
});
```

## Public API Methods

### Basic Operations

```javascript
// Initialize plugin
const plugin = $('#wrapper').addRemRow(options);

// Add a row
plugin.add();

// Remove a specific row
plugin.remove(2);

// Get current row count
const count = plugin.getCount();

// Reset all rows
plugin.reset();

// Destroy plugin
plugin.destroy();

// Reindex all rows
plugin.reindexAll();
```

### Data Management

```javascript
// Get specific row element
const $row = plugin.getRow(0);

// Get all rows
const $allRows = plugin.getAllRows();

// Get data from specific row
const rowData = plugin.getRowData(0);

// Get data from all rows
const allData = plugin.getAllData();

// Set data for specific row
plugin.setRowData(0, { name: 'John', email: 'john@example.com' });

// Set data for all rows
plugin.setAllData([
  { name: 'John', email: 'john@example.com' },
  { name: 'Jane', email: 'jane@example.com' }
]);
```

### State Management

```javascript
// Check if max rows reached
if (plugin.isMaxRowsReached()) {
  console.log('Cannot add more rows');
}

// Disable/enable add button
plugin.disableAdd();
plugin.enableAdd();

// Disable/enable remove buttons
plugin.disableRemove();
plugin.enableRemove();

// Update options dynamically
plugin.updateOptions({
  maxRows: 8,
  startRow: 1
});

// Get current configuration
const config = plugin.getConfig();
```

### Validation

```javascript
// Validate specific row
const isValid = plugin.validateRow(0);

// Validate all rows
const allValid = plugin.validateAll();
```

### Advanced Operations

```javascript
// Filter rows
plugin.filterRows(function(rowData, index, $row) {
  return rowData.status === 'active';
});

// Sort rows
plugin.sortRows(function(a, b) {
  return a.name.localeCompare(b.name);
});
```

## Event Callbacks

### onAdd Callback

```javascript
$('#wrapper').addRemRow({
  // ... other options ...
  onAdd: function(index, event, $row, fieldName) {
    console.log('Row added:', index);
    console.log('Field name prefix:', fieldName);

    // Access the row's input
    const $input = $row.find('input[name$="[name]"]');

    // Set focus
    $input.focus();

    // Return false to prevent row addition
    // return false;
  }
});
```

### onRemove Callback

```javascript
$('#wrapper').addRemRow({
  // ... other options ...
  onRemove: function(index, event, $row, fieldName) {
    console.log('Removing row:', index);

    // Custom confirmation
    if (!confirm('Are you sure?')) {
      return false; // Prevent removal
    }

    // Async operation example
    return new Promise((resolve) => {
      setTimeout(() => {
        console.log('Async cleanup complete');
        resolve();
      }, 1000);
    });
  }
});
```

## Advanced Examples

### Dynamic Row Template with Select2

```javascript
$('#wrapper').addRemRow({
  addBtn: '#addRowBtn',
  fieldName: 'products',
  rowTemplate: function(index, fieldName) {
    return `
      <div id="product_${index}" class="row product-row">
        <div class="col-md-5">
          <select name="${fieldName}[${index}][category]"
                  class="form-control select2"
                  data-index="${index}">
            <option value="">Select Category</option>
            <option value="1">Electronics</option>
            <option value="2">Clothing</option>
          </select>
        </div>
        <div class="col-md-5">
          <input type="text"
                 name="${fieldName}[${index}][name]"
                 class="form-control"
                 placeholder="Product Name">
        </div>
        <div class="col-md-2">
          <button class="btn btn-danger btn-remove"
                  data-index="${index}">×</button>
        </div>
      </div>
    `;
  },
  onAdd: function(index, event, $row) {
    // Initialize Select2 for the new row
    $row.find('.select2').select2();
  }
});
```

### Integration with External APIs

```javascript
$('#wrapper').addRemRow({
  addBtn: '#addRowBtn',
  fieldName: 'invoices',
  swal: {
    ajax: {
      url: '/api/invoices',
      method: 'DELETE',
      dataType: 'json',
      data: function(dbId) {
        return {
          invoice_id: dbId,
          _token: $('meta[name="csrf-token"]').attr('content')
        };
      }
    }
  },
  onRemove: function(index, event, $row, fieldName) {
    // Custom pre-delete logic
    const invoiceNumber = $row.find('[name$="[invoice_number]"]').val();

    if (invoiceNumber.startsWith('INV-')) {
      // Perform additional cleanup
      return cleanupInvoice(invoiceNumber);
    }
  }
});

async function cleanupInvoice(invoiceNumber) {
  // Custom async cleanup
  await fetch(`/api/invoices/${invoiceNumber}/cleanup`, {
    method: 'POST'
  });
}
```

## Best Practices

### 1. **Template Management**
- Keep templates separate using template literals or external templates
- Ensure all inputs have proper `name` attributes for serialization

### 2. **Validation**
- Always initialize BootstrapValidator before addRemRow
- Use consistent field naming patterns
- Consider server-side validation as primary

### 3. **Performance**
- Limit `maxRows` to reasonable numbers
- Use efficient selectors in templates
- Debounce rapid add/remove operations if needed

### 4. **Accessibility**
- Include proper labels for all inputs
- Maintain ARIA attributes during reindexing
- Ensure keyboard navigation works

### 5. **Error Handling**
- Handle AJAX errors gracefully
- Provide user feedback for failed operations
- Validate data before submission

## Troubleshooting

### Common Issues

1. **Rows not adding:**
   - Check if `maxRows` limit is reached
   - Verify `addBtn` selector is correct
   - Check browser console for errors

2. **Reindexing not working:**
   - Ensure `rowSelector` class is on row containers
   - Verify attribute names in reindex arrays
   - Check for JavaScript errors

3. **Validation not triggering:**
   - Confirm BootstrapValidator is loaded
   - Check field name patterns match validator config
   - Verify form selector is correct

4. **SweetAlert2 not showing:**
   - Ensure SweetAlert2 is loaded
   - Check `swal.ajax.url` is set for database deletions
   - Verify no conflicting SweetAlert2 initializations

### Debugging Tips

```javascript
// Enable debug logging
$('#wrapper').addRemRow({
  // ... options ...
  onAdd: function(index, event, $row) {
    console.log('Add event:', { index, row: $row });
  },
  onRemove: function(index, event, $row) {
    console.log('Remove event:', { index, row: $row });
  }
});

// Check plugin state
console.log('Row count:', plugin.getCount());
console.log('Config:', plugin.getConfig());
```

## Browser Support

- Chrome 50+
- Firefox 45+
- Safari 10+
- Edge 15+
- IE 11 (with polyfills)

## License

MIT License - Feel free to use in commercial and personal projects.

## Contributing

1. Fork the repository
2. Create a feature branch
3. Commit changes
4. Push to the branch
5. Create a Pull Request

## Support

For issues and feature requests, please use the GitHub issue tracker.
```

## Additional Notes

The extended plugin now includes comprehensive methods for:

1. **Data Management** - Get/set row data, retrieve all data
2. **State Control** - Enable/disable buttons, check limits
3. **Dynamic Configuration** - Update options on the fly
4. **Validation Integration** - Trigger validation programmatically
5. **Advanced Operations** - Filter and sort rows
6. **Utility Methods** - Get configuration, access rows
