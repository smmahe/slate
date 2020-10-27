---
title: ML ModelServer API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python


search: true

code_clipboard: true

---

# Home

This document describes various operations available across SmartClean Matrix platform.

The code examples can be viewed in the dark area to the right, and the programming language of the examples can be switched with the tabs in the top right.

The current base URL is

<aside class="notice">
<code>https://console.smartclean.io/api</code>
</aside>

Each microservice hosts its endpoints in a proper versioned manner allowing backward compatibility. Breaking version upgrades, if any, will be communicated appropriately.

Please contact SmartClean for obtaining valid credentials required to invoke the endpoints.

## Actions
The general schema of invoking a microservice endpoint is:

<code>`https://<base URL>/<service>/<version>/actions?op=<operation>&org=<orgId>&pid=<projectId>&expid=<experimentId>`</code>


<aside class="notice">
You must replace <code>service</code> with the microservice identifier, <code>orgId</code> with your organisation identifier, <code>projectId</code> with valid project identifier, <code>operation</code> with operation and <code>version</code> with the version of API being used.
</aside>

## Query String Parameters
Parameter | RefersTo | Type | Description
--------- | ------- | ----------- | ----------
op | operation | String | Operation that is to be performed by the microservice.
org | Organization | String | This identifies the organization context of the caller.
pid | project id | String | The project id for which the operation is to be applied.

# ML ModelServer

Welcome to the ML ModelServer API documentation.

The API can be used to access the endpoints pertaining to SmartClean's ML offerings that serve predictions of various ML models for enabled projects and organisations.

<aside class="notice">
The Latest available version is <code>version v2</code>.
</aside>

## Native Exact Query

Native Exact Query endpoint returns the predictions that match the exact location and time, for the time those that are available in the predictions. The data pertaining to the query is returned almost instantanesouly.


`operation=scmlmodelserver.native`



```python

modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.native&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url,
json = {
        "t":"2020-09-30 00:00:00",
        "groupcol":"ABC"
      })     
print(response)

```

insid | datecol | y | yhat | yhat_lower | yhat_upper
------|----|---------------------|---|-----------|---------
ABC|2020-09-30 00:00:00|0.0|0|0|3


### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.native&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`
> Request Body 

```json
{
	"t":"2020-09-30 00:00:00",
	"groupcol":"ABC"
}
```

### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
t | yyyy-mm-dd HH:MM:SS | String | The datetime parameter in the specified format yyyy-mm-dd HH:MM:SS to get predictions for the specific instant in time.
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 


This API is currently used to serve predictions made by algorithms like forecasting and regression.

## Range Query

 Range Query endpoint enables users to query predictions for a location within the time-range for which the predictions were made.
 

`operation=scmlmodelserver.range`

```python

modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.range&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url,
json = {
        "start":"2020-10-12 00:00:00",
        "end":"2020-10-12 00:02:00",
        "groupcol":"291"
      })  
print(response)
```

devid | datecol | predicted | predicted_std | predicted_lower | predicted_upper
------|----------------| ----------| --------------| ---------------- |----------------
291 | 2020-10-12 00:00:00 | 1.381434 | 0.0157629 | 1.350538 | 1.412329
292 | 2020-10-12 00:01:00 | 1.380413 | 0.014606  | 1.351783 | 1.409042
293 | 2020-10-12 00:02:00 | 1.378264 | 0.014029  | 1.350765 | 1.405762



### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.range&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`
> Request Body 

```json
{
    "start":"2020-10-12 00:00:00",
    "end":"2020-10-12 00:02:00",
    "groupcol":"291"
}
```

### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
start | yyyy-mm-dd HH:MM:SS | String | start-time in specified format
end | yyyy-mm-dd HH:MM:SS | String | end-time in specified format
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 

This API is currently used to serve predictions made by algorithms like forecasting and regression.

## Anomaly

API to query anomaly labelled grid data points. In this setting we define a near-exact match. The near exact match rounds down the value to the near existing value in the grid and returns the data point with anomaly score and class.

`operation=scmlmodelserver.anomalyNative`

```python

modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.anomalyNative&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url,
json = {
        "dim1":0.5,
        "dim2":0.9,
        "groupcol":"ABC"
})  
print(response)
```

,|grid_amm|grid_voc|anomaly_score|anomaly_class|groupcol
-----|--------|---------|-------------|--------------|---------------
212|0.555555 | 1.111111 | 0.683830 | -1 | ABC

`Anomaly Class = -1` 

### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.anomalyNative&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`
> Request Body 

```json
{
    "dim1":0.5,
    "dim2":0.9,
    "groupcol":"ABC"
}
```

### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
dim1 | amm | float | The amm value to be queried
dim2 | voc | float | The voc value to be queried
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 

This API is currently used to serve predictions made by anoamly detection algorithms.

## Standard SQL
  operation=`scmlmodelserver.sql`

```python
modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.sql&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url,
json = {
	      "q":"select * from $df limit 10"
      })  
print(response)
```

A generic SQL query endpoint. Any sql query can be passed and executed by the server to get the results. This endpoint is available 
for all the algorithms. Use the literal `$df` as part of the query which refers to the table name internally. 

,| insid | devid | Unnamed: 0 | usage
--|----|-------|------------|--------
0 | ABC | 423 | 0 | 228.28888
1 | DEF | 245 | 1 | 44.377777
2 | TGH | 865 | 2 | 107.24444
3 | PQR | 197 | 3 | 147.29523

### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.sql&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`

> Request Body 

```json
{
    "q":"select * from $df limit 10
}
```

### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
q | SQL Statement | String | A valid SQL statement


## ActiveLoad
  operation=`scmlmodelserver.activeLoad`

```python
modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.activeLoad&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url)
print(response)
```
This endpoint deletes past last run of an experiment if it is present in scmlmodelserver. It loads the latest run predicitions for serving. This endpoint is called by a trigger which automatically updates latest run of an experiment for serving.

### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.activeLoad&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`

The response contains the same output as Plain SQL response.

## Delete 
  operation=`scmlmodelserver.delete`
  
```python
modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.delete&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url)
print(response)
```
An endpoint to delete the current run object from memory and any associated files of the current run from scmlmodelserver.

### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.sql&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`


> Response

```json
{
    "func_status": "success",
    "status_code": 200
}
```

## NamedQuery
  operation=`scmlmodelserver.executeNamedQuery`

```python
modelserver_url = 'https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.executeNamedQuery&org=<orgId>&pid=<projectId>&expid=<experimentId>'
response = requests.post(modelserver_url)
print(response)
```
This endpoint creates a sql query with the values provided, executes it and returns the result to the user. Predefined queries can
be added as a template and referred by a unique ID provided at the time of creation. The endpoint offers an interface to get metrics just by providing values to specified columns.

### HTTP Request

`POST https://<baseURL>/scmlmodelserver/<version>/actions?op=scmlmodelserver.executeNamedQuery&org=<orgId>&pid=<projectId>&expid=<experimentId>`

`Content-type:application/json`

The response contains the same output as Plain SQL response.

### Body Parameters

Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
id | id_for_sql_template | String | ''' sql template '''
qcontext | query-context | String | Global context
qtype | mlsql | String | ML query type
params | list of key-value pairs | A generic key-val structure, with specific parameter in `key` and specific value in `value`



```json
  { 
    "id":"id_given_for_the_query_template",
    "qcontext":"scglobal",
    "qtype":"mlsql",
    "params":
      [
            {
              "key": "datecol",
              "value":"datecolval"
            },

            {
              "key": "date_e_val",
              "value":"valid_date"
            },
            {
              "key": "groupcol",
          "value":"valid_groupcol" 
            },
            {
              "key": "groupcol_val",
              "value":"valid_groupcol_val"
            },
            {
              "key":"col",
              "value":"predicted_column_name"
            }
      ]
  }
  ```

### Response
,|insid | minimum | q1 | median | q3 |maximum
-|-----|----------|---|--------|-----|-------
0 | ABC | 0 | 0.0 | 0.0 | 1.0 | 2
1 | DEF | 0 | 1.0 | 3.0 | 8.0 | 14


# ML Experiments

Welcome to the ML Experiments API documentation.

The API can be used to access the endpoints pertaining to SmartClean's ML offerings that serve predictions of various ML models for enabled projects and organisations.

<aside class="notice">
The Latest available version is <code>version v1</code>.
</aside>

## Experiment

When a new algorithm needs to be added to the zoo so that it is part of SCML Algorithms, a new experiment object is created with config.
config is a nested object, which contains parameters specific for the algorithm in `algorithm` config.

`query` config encapsulates information needed to query data from cloud datastore.

`prediction` contains information about the validity of predictions typically a week, unit of prediction whether it is hourly, minute.

`state` refers to the state of the experiment. `NORMAL` is the desired state.
Any other state requires either retraining or changes so the following run can complete and update state accordingly.

## Run 
Run is the execution of an experiment. Generates the predictions to be served and updates the experiment object to always refer to the current valid run. When a run exists successfully it updates experiment to be in `NORMAL` state.


## Get Experiments For a Project

Get all experiments for a project. The caller specifies the project id and is able to fetch all the experiments for the project.

`operation=scmlexperiments.listExperiments`

```python

api = 'https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.listExperiments&org=<orgId>&pid=<projectId>'
response = requests.post(api)
print(response)

```

### HTTP Request

`POST https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.listExperiments&org=<orgId>&pid=<projectId>`

### Response

An array of experiment objects


## Get Experiment By Id For a Project

Get a specific experiment for a project by experiment id. 

`operation=scmlexperiments.getExperiment`

```python

api = 'https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getExperiment&org=<orgId>&pid=<projectId>'
response = requests.post(api,json=json_data)
print(response)

```

### HTTP Request

`POST https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getExperiment&org=<orgId>&pid=<projectId>`

> Request Body

A json containing the experiment id

```json
{
  "expid": "valid_expid"
}
```

### Response

A json response containing the experiment object 

## Get Any Run For an Experiment of a Project

Get a specific valid run object by specifying the run number for an experiment of a project.

`operation=scmlexperiments.getRunForExperiment`

```python

api = 'https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getRunForExperiment&org=<orgId>&pid=<projectId>'
response = requests.post(api,json=json_data)
print(response)

```

### HTTP Request

`POST https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getRunForExperiment&org=<orgId>&pid=<projectId>`

> Request Body

A json containing the expid value and a run number

```json
{
  "expid" : "valid_expid",
  "run"   : "10"
}
```
### Response

A json response containing the run object

### Get Latest Run For an Experiment of a Project


Returns the valid latest run object of an experiment. 

`operation=scmlexperiments.getLatestRun`

```python

api = 'https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getLatestRun&org=<orgId>&pid=<projectId>'
response = requests.post(api)
print(response)
```

### HTTP Request

`POST https://<baseURL>/<version>/actions?op=scmlexperiments.getLatestRun&org=<orgId>&pid=<projectId>`

> Request Body

A json containing the expid value 

```json
{
  "expid" : "valid_expid" 
}
```
### Response

A json response containing the latest run object for that experiment


## Get Last N Runs For an Experiment of a Project


Returns the valid latest run object of an experiment. 

`operation=scmlexperiments.getLastNRuns`

```python

api = 'https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getLastNRuns&org=<orgId>&pid=<projectId>'
response = requests.post(api,json=json_data)
print(response)
```

### HTTP Request

`POST https://<baseURL>/scmlexperiments/<version>/actions?op=scmlexperiments.getLastNRuns&org=<orgId>&pid=<projectId>`

> Request Body

A json containing the expid value 

```json
{
  "expid" : "valid_expid",
  "N"     : "N"
}
```
### Response

A json response containing the last n run objects for an experiment. At most can be able to query last 10 runs.


# Third Party Integrations

Welcome to third party integrations API documentation.

This service allows listeners to receive events from SmartClean system such as: data, workorders and aggregations.

Workorders are sent to the endpoint per such event while data and aggregations are sent in batches.

Current supported integration types include custom HTTP, Azure EventHub and AWS IoT endpoints utilizing basic auth, access key, Azure shared access key or AWS HMAC signing.

<aside class="notice">
The Latest available version is <code>version v1</code>.
</aside>

## General configuration of an integration endpoint.

A typical configuration requires at least the following parameters:

Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
Type | Selection from system defined types | String | A valid type supported by the API version
Principal | Selection from system defined event sources | String | A valid event source from Matrix
Entity | Selection from system defined entity types for the principal | String | A valid entity type for the principal
Authorization | Auth type selected from system defined dictionary | String | A valid auth mechanism
METHOD | Method to use for sending requests | String | Any of POST, PUT or GET
PayloadLocation | The location of payload to be sent | String | Any of body or querystring
KeyMap | A jsonpath transformation of keys to be extracted from source and placed in new outgoing document | Object | Valid jsonpath key-value pairs corresponding to source data items
ValueMap | A lookup operation on values of a key to transform | SCValueMap | Lookups post keymap operation allows custom id mappings to be performed for outgoing data items

## Supported protocols

HTTPS

HTTP based endpoints can be used to serve the purpose of receivers that can then be further utilized to convert to appropriate protocols by clients.

Future support will include websocket, HTTP2.0 and MQTT listeners.

## Supported authorization mechanisms

1. Basic authentication
2. AWS HMAC signing with access key and secret key pair
3. Azure shared access key name and value pair

## List all third party integrations for a project

An endpoint to query all the available third party integrations for a project.

`operation=scthirdpartyintegrations.listIntegrations`

```python

api = 'https://<baseURL>/sctpi/v1/actions?op=scthirdpartyintegrations.listIntegrations&pid=<projectId>&org=<orgId>'
response = requests.post(api,json=json_data)
print(response)
```

### HTTP Request
`POST https://<baseURL>/sctpi/v1/actions?op=scthirdpartyintegrations.listIntegrations&pid=<projectId>&org=<orgId>`

### Response

A json response containing the third party integrations for specified project.

> Response 

```json

{
  "Details": {
    "Authorization": {
      "Details": {
           // Details for auth type selected.
      },
      "Key": "AuthKey",
      "Type": "AuthType",
      "Value": "If AuthType is Basic, value contains the credentials"
    },
    "Headers": [
    ],
    "KeyMap": {
    },
    "METHOD": "POST",
    "PayloadKey": "$default",
    "PayloadLocation": "body",
    "ProceedKeyMapError": false,
    "ProceedValueMapError": true,
    "URI": "https://<endpoint>",
    "ValueMap": [
    ]
  },
  "NS": "THIRD_PARTY_INTEGRATIONS",
  "PID": "projectId",
  "Principal": "scproductivity",
  "SCEntity": {
    "Actions": [
      "data.aggregations.raw"
    ],
    "Entity": "Data"
  },
  "Type": "HTTP",
  "UID": "sysdefault"
}

```

## Example JSON batch data payloads sent to an HTTP listener
```json
{
	"dataObj": [
		{
			"hour": <Hour of day of project timezone>,
			"dom": <Day of month of project timezone>,
			"dow": <Day of week of project timezone>,
			"month": <Month of year of project timezone>,
			"unixT": <Unix timestamp with millisecond precision>,
			"Region": <Timezone of the project>,
			"DevType": <Type of event source>,
			"v": <Data value item of event source>,
			"stats": <Any ML tags appended to the data item>
		}
	]
}
```
