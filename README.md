# Scout AWS Elasticsearch Driver 

This package is a wrapper around [babenkoivan/scout-elasticsearch-driver](https://packagist.org/packages/babenkoivan/scout-elasticsearch-driver), adding AWS support.

## Requirements
The package has been tested with the following configuration:

* PHP version &gt;= 7.2.13
* Laravel Framework version &gt;= 5.7
* Elasticsearch version &gt;= 6

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
