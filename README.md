# Django Search Autocomplete

A flexible and easy-to-use Django package for adding search autocomplete functionality to your Django applications.

## Author

**Islam Elmasry**  
GitHub: [emji555](https://github.com/emji555)  
Email: emji555@gmail.com

## Features

- Generic search functionality that works with any Django model
- Configurable search fields and display fields
- Support for foreign key relationships
- Support for image and file fields
- Customizable number of results
- Bootstrap-styled UI out of the box
- Easy integration with existing Django projects

## Installation

1. Install the package using pip:
```bash
pip install django-search-autocomplete
```

2. Add 'search_autocomplete' to your INSTALLED_APPS in settings.py:
```python
INSTALLED_APPS = [
    ...
    'search_autocomplete',
]
```

3. Include the URLs in your project's urls.py:
```python
from django.urls import path, include

urlpatterns = [
    ...
    path('search-config/', include('search_autocomplete.urls')),
]
```

## Usage

### Method 1: Using the Configuration Interface

1. Visit `/search-config/config/` in your browser to access the configuration interface.
2. Select the model you want to search
3. Choose the fields to search in
4. Select the fields to display in results
5. Set the maximum number of results
6. Copy the generated integration code

### Method 2: Direct Implementation

1. Add the required CSS and JavaScript to your template:
```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
<script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
```

2. Add the search input to your template:
```html
<div class="search-autocomplete">
    <input type="text" id="search-input" class="form-control" placeholder="Type to search...">
    <div id="search-results" class="list-group mt-2"></div>
</div>
```

3. Initialize the search functionality:
```javascript
const searchConfig = {
    model: 'app_name.model_name',
    searchFields: ['field1', 'field2'],
    displayFields: ['field1', 'field2'],
    maxResults: 5
};

let timeout = null;
$('#search-input').on('input', function() {
    clearTimeout(timeout);
    const query = $(this).val().trim();
    
    if (query.length < 2) {
        $('#search-results').empty().hide();
        return;
    }

    timeout = setTimeout(() => {
        $.get('/search-config/search/', {
            query: query,
            model: searchConfig.model,
            search_fields: searchConfig.searchFields,
            display_fields: searchConfig.displayFields,
            max_results: searchConfig.maxResults
        })
        .done(function(data) {
            const $results = $('#search-results').empty();
            
            data.results.forEach(function(result) {
                const $item = $('<div class="list-group-item">');
                searchConfig.displayFields.forEach(function(field) {
                    const value = result[field];
                    if (field.endsWith('__str')) {
                        $item.append($('<div>').text(value));
                    } else if (value && value.startsWith('/media/')) {
                        $item.append($('<img>').attr({
                            src: value,
                            alt: field,
                            style: 'max-width: 50px; max-height: 50px;'
                        }));
                    } else {
                        $item.append($('<div>').text(`${field}: ${value}`));
                    }
                });
                $results.append($item);
            });
            
            $results.show();
        });
    }, 300);
});
```

### Configuration Options

- `model`: The Django model in format 'app_name.model_name'
- `searchFields`: List of fields to search in
- `displayFields`: List of fields to display in results
- `maxResults`: Maximum number of results to return (default: 5)

### Special Field Types

- Foreign Key Fields: Use `field_name__str` to display the string representation
- Image/File Fields: Will automatically be displayed as images if the URL starts with '/media/'

## Example

Here's a complete example using a Product model:

```python
# models.py
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField()
    price = models.DecimalField(max_digits=10, decimal_places=2)
    image = models.ImageField(upload_to='products/')

    def __str__(self):
        return self.name
```

```javascript
// template.html
const searchConfig = {
    model: 'myapp.Product',
    searchFields: ['name', 'description'],
    displayFields: ['name', 'price', 'image'],
    maxResults: 5
};
```

## License

MIT License

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. 