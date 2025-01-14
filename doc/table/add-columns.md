# Add Columns

Before adding columns to your table, you must make [Datasource](https://livewire-powergrid.docsforge.com/main/datasource/) fields available as columns.

To make a new column, use the `addColumns()` method. This method is inside your PowerGrid file (e.g. `DishTable.php`).

## Usage

The `addColumns()` method requires the datasource `$field` name as the first parameter.

Optionally, you can pass a `closure` as  a second parameter to process the data coming from your field.

The example above creates 4 columns:

- *id*:  based on the `id` field.
- *name*: based on the `name` field.
- *name_uppercase*: returns the `name` field value transformed to UPPER CASE.
- *price_after_taxes*: returns the `price` value with taxes. This example uses a fictitious tax calculator class.

```php
//..
public function addColumns(): ?PowerGridEloquent
{
  return PowerGrid::eloquent()
    ->addColumn('id')
    ->addColumn('name')
    ->addColumn('name_uppercase', function (Dish $model) {
      return strtoupper($model->name);
    })
    ->addColumn('price_after_taxes', function (Dish $model) {
      return taxCalculator::vat($model->price, 'PT');
    });
}
```

> **❗ Important:** After creating a column, you must include it in your Table using the [Column::add()](https://livewire-powergrid.docsforge.com/main/include-columns/) method.

## Closure Examples

Sometimes, you need to display data in a human-friendly way.

This is often the case with date, currency and boolean values.

Let's check some examples using `closures` to format data!


### Link in cell

You can use `closures` to render `HTML` inside table cells.

The example below creates a new column called `location_link` containing a link formed by the `lat_long` field and the `location_name` field.

```php
//..
public function addColumns(): ?PowerGridEloquent
{
  return PowerGrid::eloquent()
    ->addColumn('location_link', function (Dish $model) {
      return '<a href="https://www.google.com/maps/search/' . $model->lat_long . '">'. $model->location_name .'</a>'; 
    });
}
```

The example above produces the HTML `<a href="https://www.google.com/maps/search/-22.973587702676607,-43.18527287193542">Copacabana</a>` which would look like: [Copacabana](https://www.google.com/maps/search/-22.973587702676607,-43.18527287193542).

<br/>

### Date

The database field `created_at` has date stored as `yyyy-mm-dd H:i:s` (2021-01-20 10:05:44).

The following code demonstrates a new custom column `created_at_formatted`.

In this column, date is parsed and displayed as `d/m/Y H:i` (20/01/2021 10:05).

```php
//..
public function addColumns(): ?PowerGridEloquent
{

  return PowerGrid::eloquent()
    ->addColumn('created_at_formatted', function (Dish $model) {
      return Carbon::parse($model->created_at)->format('d/m/Y H:i');
    });
}
```

### Currency

The next example creates a new custom column called `price_in_eur`.

This custom column displays the `price` amount (`170.90`) formatted as Portuguese Euro (`170,90 €`):

```php
//..
public function addColumns(): ?PowerGridEloquent
{
  $fmt = new NumberFormatter('pt_PT', NumberFormatter::CURRENCY);

  return PowerGrid::eloquent()
    ->addColumn('price_in_eur', function (Dish $model) {
      return $fmt->formatCurrency($model->price, "EUR");
    });
}
```

### Boolean

True/false is not friendly for the end user. Displaying "Yes/No" is a better alternative.

In this example, we have a new custom column `available` which displays "yes"/"no" based on the database field `in_stock` (true/false).

```php
//..
public function addColumns(): ?PowerGridEloquent
{

  return PowerGrid::eloquent()
    ->addColumn('available', function (Dish $model) {
      return ($model->in_stock ? 'yes' : 'no');
    });
}
```

---
