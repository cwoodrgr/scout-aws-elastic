# Scout AWS Elasticsearch Driver 

This package is a wrapper around [babenkoivan/scout-elasticsearch-driver](https://packagist.org/packages/babenkoivan/scout-elasticsearch-driver), adding AWS support.

## Requirements
The package has been tested with the following configuration:

* PHP version &gt;= 7.2.13
* Laravel Framework version &gt;= 5.7
* Elasticsearch version &gt;= 6

## Upgrading
Version 1.2.4 requires the `scout_elastic_aws` config file to be re-published or add `aws_enabled` key.

```php
'aws_enabled' => env('SCOUT_AWS_ELASTIC_ENABLED', true)
```

## Installation
Use composer to install package
```sh
composer require ruger/scout-aws-elastic
```

## Configuration
To configure the package you need to publish the following settings:
```sh
php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"
php artisan vendor:publish --provider="ScoutElastic\ScoutElasticServiceProvider"
php artisan vendor:publish --provider="Ruger\ScoutAwsElastic\ScoutAwsElasticServiceProvider"
```
After publishing the configuration files, you can configure the connection to your Elasticsearch cluster with the following `.env` variables (Replace values with your own values):
```ini
SCOUT_DRIVER=elastic

SCOUT_ELASTIC_HOST=localhost
SCOUT_ELASTIC_SCHEME=https
SCOUT_ELASTIC_PORT=443
SCOUT_ELASTIC_USER=
SCOUT_ELASTIC_PASS=
SCOUT_AWS_ELASTIC_ENABLED=true
SCOUT_AWS_ELASTIC_REGION=us-east-2
SCOUT_AWS_ELASTIC_ACCESS_KEY=<your_aws_access_id>
SCOUT_AWS_ELASTIC_ACCESS_SECRET=<your_secret_aws_key>
```
## Usage
Because this package is a wrapper for babenkoivan/scout-elasticsearch-driver, all usage documentation can be found [here](https://github.com/babenkoivan/scout-elasticsearch-driver/blob/master/README.md) 

## RuleBuilder
The RuleBuilder class can be extended to provide dynamic bool query array generation.

This can be used in conjunction with the `rule()` function provided by the ScoutElastic package.

### Available Methods:
* `public function build(array $rules)` - Creates the rule array based on the associative array passed.
* `protected function must(string $field, $value, array $attributes)` - Creates a must match query clause.
* `protected funciton mustNot(string $field, $value, array $attributes)` - Creates a must_not match query clause.
* `protected function should(string $field, $value, array $attributes)` - Creates a should match query clause.
* `protected function filter(string $field, $value, array $attributes)` - Creates a filter term query clause.
* `protected funciton range(string $field, array $values, string type)` - Creates a range query clause.

### Example
#### IndexConfigurator:
```php
<?php

namespace App;

use ScoutElastic\Migratable;
use ScoutElastic\IndexConfigurator;

class MyModelIndexConfigurator extends IndexConfigurator
{
    use Migratable;
    
    protected $settings = [];
}
```

#### Model:
```php
<?php

namespace App;

use ScoutElastic\Searchable;
use Illuminate\Database\Eloquent\Model;

class MyModel extends Model
{
    use Searchable;
    
    protected $fillable = [
       'searchField',
       'filterableField'
    ];
    
    protected $indexConfigurator = MyModelIndexConfigurator::class;
}
```

#### Rule:
```php
<?php

namespace App;

class MyRule extends Ruger\ScoutAwsElastic\Builders\RuleBuilder
{
    public function query(string $query): void
    {
        $this->must('query', $query, ['fuzziness' => 'AUTO', 'prefix_length' => 2]);
    }
    
    public function someFilter(string $filter): void
    {
        $this->filter('someFilter', $filter);
    }
}
```

#### Controller:
```php
<?php

namespace App\Http\Controllers;

use App\MyModel;
use Facades\App\MyRule;
use Illuminate\Http\Request;

class MyController extends Controler
{
    public function search(Request $request)
    {
        $validated = $request->validate([
            'query'      => 'required|string',
            'someFilter' => 'nullable|boolean',
        ]);
        
        // Search with a custom filter
        return MyModel::search('')->rule(function() use ($validated) {
            MyRule::build($validated);
        })->get();
    }
}
```
