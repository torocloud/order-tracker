# Order Tracker API
A simple order tracker api

## Prerequisites

	- Apache Maven 3+
	- [Martini Desktop](https://www.torocloud.com/martini/download)

## Building the Martini Package

```bash
$ mvn clean package
```

This will create a ZIP file named  `order-tracker-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

## Usage

This package exposes operations for a simple order tracker REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `services/api/OrderTracker` under the `code` folder after importing the package to your Martini Desktop application.

## Setup

This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database.

The package uses HSQL as the default datastore but you can change this to MySQL, Postgres, MSSQL, or Oracle by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like for HSQL and MySQL

```
# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/<database-to-use>.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>
mysql.username=root
mysql.password=
```

* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.

## Operations

The base url is `<host>/api/order-tracker` where `host` is the location where Martini is deployed. By default, it's `localhost:8080`.

### Tracker

`GET /tracker/{trackerId}`

Returns an order tracker that matches the given `trackerId`

**Sample Request**

**curl**

```bash
curl -X GET \
	http://localhost:8080/api/order-tracker/tracker/8eed8d34-06ee-4043-bbe0-089354ddec7c \
	-H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "tracker_id": "8eed8d34-06ee-4043-bbe0-089354ddec7c",
    "order": "Test Order",
    "ship_to": "Test Location",
    "order_date": "2020-03-26T10:11:10+0800"
}
```

`POST /tracker`

Creates a new order tracker

**Request Body**


* `order` - Name of the order
* `ship_to` - Where the order will ship to

**Sample Request**

**curl**

```bash
curl -X POST \
	http://localhost:8080/api/order-tracker/tracker \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-d '{
	    "order": "Test Order",
	    "ship_to": "Test Location"
	}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "tracker_id": "8eed8d34-06ee-4043-bbe0-089354ddec7c",
    "order": "Test Order",
    "ship_to": "Test Location",
    "order_date": "2020-03-26T10:11:10+0800"
}
```

`PUT /tracker`

Updates the order tracker that matches the `trackerId`

**Request Body**

* `tracker_id` - The id of the tracker to update
* `order` - Name of the order
* `ship_to` - Where the order will ship to

**Sample Request**

**curl**

```bash
curl -X PUT \
	http://localhost:8080/api/order-tracker/tracker \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-d '{
	    "tracker_id": "8eed8d34-06ee-4043-bbe0-089354ddec7c",
	    "order": "Test Order (Updated)"
	}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status": "Successfully updated order tracker"
}
```

`DELETE /tracker/{trackerId}`

Deletes a order tracker that matches the `trackerId`

**Sample Request**

**curl**

```bash
curl -X DELETE \
	http://localhost:8080/api/order-tracker/tracker/8eed8d34-06ee-4043-bbe0-089354ddec7c \
	-H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status": "Successfully deleted order tracker"
}
```

### Tracker Status

`GET /tracker/{trackerId}/status`

Returns a list of tracker statuses by the given `trackerId`

**Sample Request**

**curl**

```bash
curl -X GET \
	http://localhost:8080/api/order-tracker/tracker/3076b85e-8189-4c70-81d5-3421711efefc/status \
	-H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
[
    {
        "status_id": "6357000c-d855-43b8-b312-dd1493b408bc",
        "tracker_id": "3076b85e-8189-4c70-81d5-3421711efefc",
        "status": "ORDER CONFIRMED",
        "location": "Test Location",
        "date": "2020-03-26T15:21:59+0800"
    },
    {
        "status_id": "f9f00335-2984-4871-a3b3-46159e69c904",
        "tracker_id": "3076b85e-8189-4c70-81d5-3421711efefc",
        "status": "SHIPPING",
        "location": "Test Location",
        "date": "2020-03-26T15:22:29+0800"
    },
    {
        "status_id": "606ca535-baeb-44a9-8c38-0df1efe65cf4",
        "tracker_id": "3076b85e-8189-4c70-81d5-3421711efefc",
        "status": "DELIVERED",
        "location": "Your Location",
        "date": "2020-03-26T15:23:15+0800"
    },
    ...
]
```

`GET /tracker/{trackerId}/status/{statusId}`

Returns a tracker status that matches the given `statusId` and `trackerId`

**Sample Request**

**curl**

```bash
curl -X GET \
	http://localhost:8080/api/order-tracker/tracker/3076b85e-8189-4c70-81d5-3421711efefc/status/6357000c-d855-43b8-b312-dd1493b408bc \
	-H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status_id": "6357000c-d855-43b8-b312-dd1493b408bc",
    "tracker_id": "3076b85e-8189-4c70-81d5-3421711efefc",
    "status": "ORDER CONFIRMED",
    "location": "Test Location",
    "date": "2020-03-26T15:21:59+0800"
}
```

`POST /tracker/{trackerId}/status`

Creates a new tracker status for the given `trackerId`

**Request Body**

* `status` - The status of the order
* `location` - Location of the status or order

**Sample Request**

**curl**

```bash
curl -X POST \
	http://localhost:8080/api/order-tracker/tracker/3076b85e-8189-4c70-81d5-3421711efefc/status \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-d '{
	    "status": "ORDER CONFIRMED",
	    "location": "Test Location"
	}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status_id": "606ca535-baeb-44a9-8c38-0df1efe65cf4",
    "tracker_id": "3076b85e-8189-4c70-81d5-3421711efefc",
    "status": "DELIVERED",
    "location": "Your Location",
    "date": "2020-03-26T15:23:15+0800"
}
```

`PUT /tracker/{trackerId}/status`

Updates a tracker status for the given `trackerId` that matches the `statusId`

**Request Body**

* `status_id` - The id of the status to update
* `status` - The status of the order
* `location` - Location of the status or order

**Sample Request**

**curl**

```bash
curl -X PUT \
	http://localhost:8080/api/order-tracker/tracker/3076b85e-8189-4c70-81d5-3421711efefc/status \
	-H 'Accept: application/json' \
	-H 'Content-Type: application/json' \
	-d '{
		"status_id": "606ca535-baeb-44a9-8c38-0df1efe65cf4",
	    "status": "ORDER CONFIRMED (Updated)"
	}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status": "Successfully updated order status"
}
```

`DELETE /tracker/{trackerId}/status/{statusId}`

Deletes a tracker status for the given `trackerId` that matches the `statusId`

**Sample Request**

**curl**

```bash
curl -X DELETE \
	http://localhost:8080/api/order-tracker/tracker/3076b85e-8189-4c70-81d5-3421711efefc/status/606ca535-baeb-44a9-8c38-0df1efe65cf4 \
	-H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.

```json
{
    "status": "Successfully deleted order status"
}
```