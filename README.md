# Welcome to ESPBA!
#### ESPBA === `E`xtremely `S`imple `P`HP `B`ackend for `A`ngular.

*A few hundred lines port into your legacy data.*

 ESPBA is meant for You who is stucked with a PHP and MySQL / MariaDB backend, and not is able to (or want to) use frameworks like [slim](https://www.slimframework.com/).  With ESPBA you can start coding your clientside Angular  app without worrying about the serverside.  The story behind ESPBA was the need of a new fancy frontend against an old pile of legacy data,  hosted on a server where I was not able to use nodejs, Sequelize, mongodb etc. I believe this is a very common issue. 

Install ESPBA, update the settings and you have an out-of-the-box CRUD port to your legacy data. You can create, read, update and delete your PHP served MySQL-records as object literals (JSON) directly in your Angular app. As for now, ESPBA supports 

* Multi param SELECT, UPDATE and DELETE
* LIMIT
* ORDER BY, including RAND()
* LIKE statements

You *cannot*

* SELECT from multiple tables (combining)
* use JOIN, GROUP BY or UNION

Supporting complex queries is not the aim for ESPBA. If that is an issue I recommend you are using [views](https://dev.mysql.com/doc/refman/5.7/en/views.html), *or* use nested `get(table, { params }).then` calls to build up the data structure.

----
     

### Requirements

* PHP >=5.1 (or 5.0 with PECL PDO installed). ESPBA is using [PDO](http://php.net/manual/en/book.pdo.php) behind the hood (but you can easily create your own "driver" through the abstract DbProvider class). This means any PHP featured server from 2005 and forth should be adequate.
* MySQL /  MariaDB in any version, it should work on Oracle, FireBase and MSSQL as well, any database that honours the [SQL-92](http:///en.wikipedia.org/wiki/SQL-92) standard.
* Angular 1.x 
---
### Installation

`bower install ESPBA` or download this repository.

---
### The important files 
```
root
└───server
│   │  pw.php
│   │  Db.php
│   │  espba.php
│   
└───client
    │  ESPBA.service.js
```
Copy the `/server` files to your preferred destination. I like to place them in an `app/api` directory, since this is all about Single Page Apps with no server-software anyway.  Update credentials in the `pw.php` file.  The reason for separating credentials is the ability to separate sensitive info from a git repository, such as github, by adding an `app/api/pw.php` to the `.gitignore` instruction. 

### Angular ESPBA service



#### `.setHost(host)`
Sets the API host. ESPBA exposes `Access-Control-Allow-Origin	: *`, so if needed you can have your data hosted on a remote server, different from the `http` or `https` server hosting your website..

#### `.setApiPath(apiPath)`
The relative path to the ESPBA PHP scripts from `host`.

#### `.setReturnMode(mode)`
Defines the overall return mode. Acceptable values is `'response'` and `'json'`. if `returnMode` is set to` 'response'`, which is the default, you receive the entire http response from ESPBA. Records (or error messages) can be retrieved through `response.data`.  If `returnMode` is set to` 'json'`, you receive the `data` object only, that can be used right away as in ` $scope.animals = data`.

The example snippets assume you have table named animals

Field  | Type | Sample
------ | -------  | --------
id  | int | 27
type  | varchar | mammals
name | varchar | Tiger

#### `.get(table, data, selectParams)`
Retrieves records from a table.  SELECT criterias  is defined by `data`, additional params defined by `selectParams`. 

    //get all animals        
    ESPBA.get('animals').then(function(data) {
      $scope.animals = data
    })

    //get mammals only
    ESPBA.get('animals', { type: 'mammals'} ).then(function(data) {
      $scope.mammals = data
    })

    //get mammals with name starting with t, like tiger
    ESPBA.get('animals', { type: 'mammals', name: 't*'} }.then(function(data) {
      $scope.mammals = data
    })
 
    //get mammals limited to 10 records. ordered by name
    ESPBA.get('animals', { type: 'mammals'}, { limit: 10, orderBy 'name'  } }.then(function(data) {
      $scope.mammals = data
    })
    
     //get mammals limited to 10 records. ordered by name descending
    ESPBA.get('animals', { type: 'mammals'}, { limit: 10, orderBy: { field: 'name', order: 'desc'  } }.then(function(data) {
      $scope.mammals = data
    })
 
    //get 10 random mammals  
    ESPBA.get('animals', { type: 'mammals'}, { limit: 10, orderBy: 'rand()' } }.then(function(data) {
      $scope.mammals = data
    })
  
  
