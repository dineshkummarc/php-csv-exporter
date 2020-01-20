![Packagist Version](https://img.shields.io/packagist/v/sujan/php-csv-exporter)
![Packagist](https://img.shields.io/packagist/dt/sujan/php-csv-exporter?color=green)
![GitHub](https://img.shields.io/github/license/sujancse/php-csv-exporter?color=yellow)

## Overview
A fast and tiny PHP library to export data to CSV, Excel, etc. The library is based on a PHP generator. It used only 20MB memory to download 5 Million data, whereas without generator it will take up to 500MB of memory. Tested on Laravel 6.

### Installation
```$xslt
composer require sujan/php-csv-exporter
```

## Usage
All you have to do is to pass the **Query Builder**


`use Sujan\Exporter\Exporter;
`
```$xslt
$columns = [
    'id',
    'name',
    'email'
];
```
###### From Query Builder (RECOMMENDED)
```
$queryBuilder = User::query(); // Query builder
// $queryBuilder = User::where('email', 'example@email.com'); // Query builder

Exporter::init($queryBuilder, $columns, 'users.csv');
// Exporter::init($queryBuilder, $columns, 'users.xlsx'); // For ExcelSheet export
Exporter::export();
```

###### From Collection
```
$usersCollection = User::all(); // Collection

Exporter::init($usersCollection, $columns, 'users.csv');
Exporter::export();
```

###### From Array
```
$usersArray = User::latest()->get()->toArray(); // Array

Exporter::init($usersArray, $columns, 'users.csv');
Exporter::export();
```

Or you can pass `Collection` or `Array`. But it is **highly recommended** to pass the **`Query Builder`** as it will use generator to save memory usage.

**For eloquent relation**
```$xslt
$columns = [
    'id',
    'title',
    'user' => [ // user is a relation
        'name'
    ]
];

$queryBuilder = Post::with('user'); // Query builder

Exporter::init($queryBuilder, $columns, 'users.csv');
Exporter::export();
```

Where `user` is the relation name, which is same as is in the `$columns` variable.

## Usage with raw PHP (PDOStatement)

Usage with PDO is straight forward. You **MUST** have to add the following code
```$xslt
$stmt->setFetchMode(PDO::FETCH_ASSOC); // N.B. must be included
```

And then pass the `$stmt` to `Exporter`
```$xslt
    $servername = "localhost";
    $username = "root";
    $password = "password";
    $dbname = "laravel";

    $columns = [
        'id',
        'name',
        'email'
    ];

    try {
        $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
        $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
        $stmt = $conn->prepare("SELECT id, name, email FROM users");
        $stmt->execute();

        // set the resulting array to associative
        $stmt->setFetchMode(PDO::FETCH_ASSOC);

        Exporter::init($stmt, $userData, 'users.csv');
        Exporter::export();
    }
    catch(PDOException $e) {
        echo "Error: " . $e->getMessage();
    }
    $conn = null;
```

## XLSX export
Just use the file extension as `.xlsx`
```
Exporter::init($queryBuilder, $columns, 'users.xlsx');
```

## You are always welcome to contribute
