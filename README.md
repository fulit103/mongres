# Mongres ETL for Node.js
### Synchronize a PostgreSQL database with MongoDB.


Mongres is configured using plain text files located in a defined syncs folder (`./syncs` by default).
The file should contain a JSON object (or an array of objects) like this:

      {
        "source"  : {
          "type"    : "postgresql",
          "name"    : "inventory",
          "user"    : "foo",
          "pass"    : "f00b4r",
          "host"    : "example.com",
          "port"    : 5432
        },
        "target" : {
          "type"    : "mongodb",
          "name"    : "inventory",
          "user"    : "",
          "pass"    : "",
          "host"    : "localhost",
          "port"    : 27017
        },
        "operations": [
          {
            "source"      : "t_category",
            "target"      : "category",
            "query"       : {"_id": "category_id"},
            "update"      : {
              "$set"      : {
                "name"    : "category_name",
                "desc"    : "description"
              }
            },
            "references"  : ["product.cat"]
          },
          {
            "source"      : "t_inventory join t_product using(product_id)",
            "target"      : "inventory",
            "cursor"      : true,
            "limit"       : 1000,
            "query"       : {"_id": "inventory_id", "pro_id": "product_id"},
            "update"      : {
              "$set"      : {
                "sku"     : "product_sku",
                "avail"   : "date_available",
                "qty"     : "quantity",
                "price"   : {
                  "list"  : "price_list",
                  "sale"  : "price_sale"
                }
              }
            }
          }
        ]
      }
      
Multiple sync objects (the container of `source`, `target`, and `operations`) in the same file will be executed sequentially as defined.  Other files in the syncs folder may be ran concurrently.

### TODO:
* Add postgres target and mongo source options.
* Add configurable filters to limit results of source queries.
* Automate the creation of database triggers and delta tables.
* Add process to monitor the databases in real-time (using pg_notify, etc).
