---
layout: page
title: "Import Formats"
category: doc
date: 2015-06-18 19:52:21
order: 260
---

* [Order - Standard CSV](#order_standard_csv)
* [Order - Standard JSON](#order_standard_json)
* [Product - Standard CSV](#product_standard_csv)
* [Product - Standard JSON](#product_standard_json)
* [Delivery - Standard CSV](#delivery_standard_csv)
* [Delivery - Standard JSON](#delivery_standard_json)
* [Bill of Materials - Standard CSV](#bom_standard_csv)
* [Bill of Materials - Standard JSON](#bom_standard_json)

---

ShipStream supports importing <a href="/ref/order.html#ordercreate">orders</a>,
<a href="/ref/product.html#product_create">products</a>, <a href="/ref/delivery.html#delivery_create">deliveries</a>
and <a href="/ref/bom.html">BOMs</a>
either via the Merchant Panel or the <a href="/ref/import.html">Import</a> API.

The file formats supported are:

- CSV (delimiters are auto-detected)
- Excel (table format the same as Standard CSV)
- JSON Lines (single-line or multi-line JSON objects delimited by newlines)

The file data may be compressed with GZip or BZip2 compression.

The import formats all conform to the respective API '*.create' methods so please see the documentation
for those methods for details about supported field names and values.

---
<h2 id="order_standard_csv">
Order - Standard CSV
</h2>

The header row defines which columns are used and each following line should consist of the
order data, SKU and quantity for one order item. If the order contains multiple items they
can be specified using additional rows and the order data can be either repeated (it will
be ignored) or omitted as long as the 'order_ref' column is not omitted since it is used
to group the order items together.

<strong>See [Order Properties](/ref/order.html#order_properties) for the supported field names and values.</strong>

<strong>Note:</strong>  
To group multi-product orders together for CSV imports either the `unique_id` or the `order_ref` must be supplied unless there is only one order per file.  
When the `unique_id` is supplied with no `order_ref` then the `unique_id` will be used to group rows.  
When the `order_ref` is supplied with no `unique_id` then the `order_ref` will be used to group rows.  
If both `unique_id` and `order_ref` are supplied then the `unique_id` will be used to group rows.

<strong>Note:</strong>  
Order Custom Fields can be used in Order Imports. To add an Order Custom Field, add the field's code to the header with a prefix symbol.  

There are two types of prefixes: 
- Prefix with `#` when the values are identified by "id". See <a href="https://help.shipstream.io/article/0hv4sjkn3d-custom-fields-for-orders#enumerated_order_custom_field_options" title="Enumerated Order Custom Field Options">Enumerated Order Custom Field Options</a> article section for how to find the "id".
- Prefix with `@` when the values uploaded are numeric values or text.

Here's an example of how to use these prefixes: 
```
"@claim_reasons","@cost_of_goods","@allow_mailer","#claim_reason"
"Damaged in shipping|Did not fit",41.32,true,13
```

<strong>Note:</strong>  
For fields that allow multiple values, separate each value with the pipe character `|`.

#### Example Input File [\[Download Sample\]](/samples/order_import_sample.csv)

```
order_ref,shipping_method,firstname,lastname,company,street1,city,region,postcode,country,telephone,sku,qty,@claim_reasons,@cost_of_goods,@allow_mailer,#claim_reason
123456,ups_01,Bill,Gates,Microsoft,11 Times Square,New York,NY,10036,US,212.245.2100,product1,5,Damaged in shipping|Did not fit,41.32,true,13
123456,,,,,,,,,,,product2,1,,,,
123456,,,,,,,,,,,product3,2,,,,
```

<h2 id="order_standard_json">
Order - Standard JSON
</h2>

Importing orders in JSON format should follow the '<a href="/ref/order.html#ordercreate">order.create</a>' inputs exactly.

#### Example Input File [\[Download Sample\]](/samples/order_import_sample.json){:download="order_import_sample.json"}

```json
{ 
  "order_ref" : "123456", 
  "shipping_method" : "ups_01", 
  "firstname" : "Bill",
  "lastname" : "Gates",
  "company" : "Microsoft", 
  "street1" : "11 Times Square", 
  "city" : "New York", 
  "region" : "NY", 
  "postcode" : "10036", 
  "country" : "US", 
  "telephone" : "212.245.2100", 
  "items" : { 
    "product1" : 2, 
    "product2" : 3, 
    "product3" : 1
  },
  "custom_fields" : {
    "claim_reasons" : ["Damaged in shipping", "Did not fit"],
    "cost_of_goods" : "41.32",
    "allow_mailer" : "true",
    "claim_reason" : {"id" : 13}
  }
}
```

<h2 id="product_standard_csv">
Product - Standard CSV
</h2>

The header row should contain all field names and each following row contains product data. Each row must specify a SKU at minimum.

<strong>See [Product Properties](/ref/product.html#product_properties) for the supported field names and values.</strong>

If a field supports multiple values such as 'hts_country_code' or 'special_other' then multiple values can be assigned by specifying values separated by the 'pipe' character: |

#### Example Input File [\[Download Sample\]](/samples/product_import_sample.csv)

```
sku,name,barcode,goods_type,weight,weight_unit,length,width,height,dimension_unit,country_of_manufacture,hts_base_code,hts_country_code,requires_packaging,can_contain_other_items,allowed_container_styles,valid_containers,disallowed_containers,special_supplies,special_other,unit_qty,backorders,dynamic_allocation,virtual_inventory
"productsku","Product Name","productbarcode","NORMAL","1.75","lb","123","100","28","in","DK","1234.56","BH:1000|ZW:1001",1,0,"rigid_box|pallet","containerssku1|containerssku2","containerssku3|containerssku4","suppliessku1|suppliessku2","othersku1|othersku2",5,1,2,"optimistic"
```

<h2 id="product_standard_json">
Product - Standard JSON
</h2>

Importing products in JSON format should follow the '<a href="/ref/product.html#product_create">product.create</a>' inputs exactly.

#### Example Input File [\[Download Sample\]](/samples/product_import_sample.json){:download="product_import_sample.json"}

```json
{
  "name" : "Product 3",
  "sku" : "product-3",
  "barcode" : "product3",
  "goods_type" : "NORMAL",
  "weight" : 1.75,
  "weight_unit" : "lb",
  "length" : 123,
  "width" : 100,
  "height" : 28,
  "dimension_unit" : "in",
  "country_of_manufacture" : "DK",
  "hts_base_code" : 1234.56,
  "hts_country_code" : "BH:1000|ZW:1001",
  "requires_packaging" : 1,
  "can_contain_other_items" : 0,
  "allowed_container_styles" : [ "rigid_box", "bubble_mailer" ],
  "valid_containers" : [ "containersku1", "containersku2" ],
  "disallowed_containers" : [ "containersku3", "containersku4" ],
  "special_supplies" : [ "supplysku1", "supplysku2" ],
  "special_other" : [ "othersku1", "othersku2" ],
  "unit_qty" : 5,
  "additional_regulatory_info" : "EX1995120111C",
  "meets_hazmat_specs" : 1,
  "backorders" : 1,
  "dynamic_allocation" : 2,
  "virtual_inventory" : "optimistic"
}  
```

<h2 id="delivery_standard_csv">
Delivery - Standard CSV
</h2>

The "id" field is only used to group multiple lines into a single delivery. If importing
a single delivery it can be blank, but if importing multiple deliveries it should be unique
for each separate delivery in the CSV file.  

When using Delivery Imports the `merchant_ref` values must be unique to the `delivery_type` specified.  i.e.: Merchant Ref supplied to three new ASNs should not match former ASNs already in the system, nor the ASNs within the upload file.

Since an import can create multiple items at once the System uses the Merchant Ref to check for duplicates.  
 Example: Upload a file with 10 different ASNs.  After importing, the System states that of the ten, five had errors and five successfully imported.  Correct those five, whether that is in the file or by adding a SKU to the System, etc.  With the corrections made, import the same file but have the System handle duplicates by Dropping them.  This way the same file can be reused without accidentally entering a duplicate ASN.  Allowing the focus to be on fixing the ASNs that failed instead of needing to also make a new file to import the ASNs.

<strong>See [Delivery Properties](/ref/delivery.html#delivery_properties) for the supported field names and values.</strong>

#### Example Input File [\[Download Sample\]](/samples/delivery_import_sample.csv)

```
id,delivery_type,sender_name,carrier_name,expected_delivery,merchant_ref,sender_ref,sku,qty
1,asn,Bill Gates,FedEx,"2014-07-31",12345,333,product1,50
1,,,,,,,product2,100
2,asn,Bill Gates,FedEx,"2014-08-12",12346,339,product3,40
2,,,,,,,product4,200
```

<h2 id="delivery_standard_json">
Delivery - Standard JSON
</h2>

Importing deliveries in JSON format should follow the '<a href="/ref/delivery.html#delivery_create">delivery.create</a>' inputs exactly.

#### Example Input File [\[Download Sample\]](/samples/delivery_import_sample.json){:download="delivery_import_sample.json"}

```json
{ 
  "delivery_type" : "asn", 
  "sender_name" : "Bill Gates", 
  "carrier_name" : "FedEx", 
  "expected_delivery" : "2014-07-31", 
  "merchant_ref" : "12345", 
  "sender_ref" : "333", 
  "items" : [ 
    { 
      "sku" : "product1", 
      "qty" : 5
    }, 
    { 
      "sku" : "product2", 
      "qty" : 1 
    } 
  ] 
}
```

<h2 id="bom_standard_csv">
Bill of Materials - Standard CSV
</h2>

The "id" field is only used to group multiple lines into a single bill of materials. If importing a single bill of materials it can be blank, but if importing multiple bills of materials it should be unique for each separate bill of materials in the CSV file.

The header row should contain all field names and each following row contains bill of materials data. Each row must specify a SKU at minimum.

<strong>See [BOM Properties](/ref/bom.html#bom_properties) for the supported field names and values.</strong>

#### Example Input File [\[Download Sample\]](/samples/bom_import_sample.csv)

```
id,name,sku,yield_qty,is_kit_on_demand,is_kit_to_stock,assembly_instructions,priority,component_sku,component_qty
1,BOM 1,bom1,1,Yes,No,,0,component1,1
1,BOM 1,bom1,1,Yes,No,,0,component2,1
```

<h2 id="bom_standard_json">
Bill of Materials - Standard JSON
</h2>

Importing bill of materials in JSON format should follow the '<a href="/ref/bom.html#bom_create">bom.create</a>' inputs exactly.

#### Example Input File [\[Download Sample\]](/samples/bom_import_sample.json){:download="bom_import_sample.json"}

```json
{
  "id": "1",
  "sku": "bom1",
  "name": "BOM 1",
  "yield_qty": "1",
  "is_kit_on_demand": true,
  "is_kit_to_stock": false,
  "priority": "0",
  "components": [
    {
      "sku": "component1",
      "qty": "1"
    },
    {
      "sku": "component2",
      "qty": "1"
    }
  ]
}  
```
