JsonPath with support for Flat Table Generation
========
[![Build Status](https://travis-ci.org/Galbar/JsonPath-PHP.svg)](https://travis-ci.org/Galbar/JsonPath-PHP)
[![Coverage Status](https://coveralls.io/repos/Galbar/JsonPath-PHP/badge.svg?branch=master&service=github)](https://coveralls.io/github/Galbar/JsonPath-PHP?branch=master)
[![Latest Stable Version](https://poser.pugx.org/galbar/jsonpath/v/stable)](https://packagist.org/packages/galbar/jsonpath)
[![License](https://poser.pugx.org/galbar/jsonpath/license)](https://packagist.org/packages/galbar/jsonpath)

This is a [JSONPath](http://goessner.net/articles/JsonPath/) implementation for PHP. 

This implementation features all elements in the specification except the `()` operator (in the spcecification there is the `$..a[(@.length-1)]`, but this can be achieved with `$..a[-1]` and the latter is simpler).

On top of this it implements some extended features:
* Regex match comparisons (p.e. `$.store.book[?(@.author =~ /.*Tolkien/)]`)
* For the child operator `[]` there is no need to surround child names with quotes (p.e. `$.[store][book, bicycle]`) except if the name of the field is a non-valid javascript variable name.
* `.length` can be used to get the length of a string, get the length of an array and to check if a node has children.

Features
========
This implementation has the following features:
* Convert json tree into a flat table using the set of jsonpath stings.

JsonPath Example
================
Consider the following json:
```
{ "store": {
    "name":"My Store",
    "book": [
      { "category": "reference",
        "author": "Nigel Rees",
        "title": "Sayings of the Century",
        "price": 8.95,
        "available": true,
        "authors": ["Nigel Rees"]
      },
      { "category": "fiction",
        "author": "Evelyn Waugh",
        "title": "Sword of Honour",
        "price": 12.99,
        "available": false,
        "authors": []
      },
      { "category": "fiction",
        "author": "Herman Melville",
        "title": "Moby Dick",
        "isbn": "0-553-21311-3",
        "price": 8.99,
        "available": true,
        "authors": ["Nigel Rees"]
      },
      { "category": "fiction",
        "author": "J. R. R. Tolkien",
        "title": "The Lord of the Rings",
        "isbn": "0-395-19395-8",
        "price": 22.99,
        "available": false,
        "authors": ["Evelyn Waugh", "J. R. R. Tolkien"]
      }
    ],
    "bicycle": {
      "color": "red",
      "price": 19.95,
      "available": true,
      "model": null,
      "sku-number": "BCCLE-0001-RD"
    },
    "bicycleSet": [{
      "color": "red",
      "price": 19.95,
      "available": true,
      "model": null,
      "sku-number": "BCCLE-0001-RD"
    },{
      "color": "green",
      "price": 19.75,
      "available": false,
      "model": null,
      "sku-number": "RCADF-0002-CQ"
    }]
  },
  "authors": [
    "Nigel Rees",
    "Evelyn Waugh",
    "Herman Melville",
    "J. R. R. Tolkien"
  ],
  "Bike models": [
    1,
    2,
    3
  ]
}
```

# JsonPath => Result


`[$.store.bicycle.price]` 

| |
|---|
|19.95|

`[$.store.name,$.store.bicycle.price,$.store.bicycle.sku-number]`

| | | |
|---|---|---|
|My Store|19.95|BCCLE-0001-RD|

`[$.store.book[*].price,$.store.book[*].price,$.store.book[*].authors[*],$.store.book[*].title,$.store.name]`


| | | | | |
|---|---|---|---|---|
|8.95|8.95|Nigel Rees|Sayings of the Century|My Store|
|12.99|12.99|null|Sword of Honour|My Store|
|8.99|8.99|Nigel Rees|Moby Dick|My Store|
|22.99|22.99|Evelyn Waugh|The Lord of the Rings|My Store|
|22.99|22.99|J. R. R. Tolkien|The Lord of the Rings|My Store|


Usage
====

```php

$json = '{... your json as string ...}';
$jsonPath = '[$.your.jsonpath.1,$.your.jsonpath.2]';

$jsonObject = new JsonObject($json);
$result = $jsonObject->getTable($jsonPath);

```