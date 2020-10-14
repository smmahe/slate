---
title: SCML ModelServer API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python


search: true

code_clipboard: true
---

# Root

## SCMLModelServer

Welcome to the SCMLModelServer API. 

You can use our API to access the endpoints, which serves the predictions of our ML models for the specific projects.

You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This example API documentation page was created with [Slate](https://github.com/slatedocs/slate). Feel free to edit it and use it as a base for your own API's documentation.

### Actions
The query string parameters helps in identifying whether a user with a valid permission can access the resource. The general structure template of a v2 url is shown with an example.

`actions?op=VALID_OP&org=None&pid=VALID_PID&expid=VALID_EXPID`


<aside class="notice">
You must replace <code>VALID_PID</code> with actual project id and <code>VALID_EXPID </code> with actual experiment id 
and replace <code>VALID_OP</code> with operation.

</aside>

### Query String Parameters
Parameter | RefersTo | Type | Description
--------- | ------- | ----------- | ----------
op | operation | String | Operation that is to be performed by scmlmodelserver.
org | Organization | String | This is an `optional` parameter. Default value is None. Eg: ```org=None```
pid | project id | String | The project id for which the operation is to be applied
expid | experiment id | String | Needs to be a valid experiment id


### Inference 
### Native Exact Query

 Native Exact Query endpoint returns the predictions that match the exact location and time, for the time those that are available in the predictions. The data pertaining to the query is returned almost instantanesouly.


`operation=scmlmodelserver.native`



```python

modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.native&pid=VALID_PID&expid=VALID_EXPID'
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


#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.native&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`
> Request Body 

```json
{
	"t":"2020-09-30 00:00:00",
	"groupcol":"ABC"
}
```

#### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
t | yyyy-mm-dd HH:MM:SS | String | The datetime parameter in the specified format yyyy-mm-dd HH:MM:SS to get predictions for the specific instant in time.
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 


This API is currently used to serve predictions made by algorithms like forecasting and regression.

### Range Query

 Range Query endpoint enables users to query predictions for a location within the time-range for which the predictions were made.
 

`operation=scmlmodelserver.range`

```python

modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.range&org=None&pid=VALID_PID&expid=VALID_EXPID'
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
291 | 2020-10-12 00:01:00 | 1.380413 | 0.014606  | 1.351783 | 1.409042
291 | 2020-10-12 00:02:00 | 1.378264 | 0.014029  | 1.350765 | 1.405762



#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.range&org=None&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`
> Request Body 

```json
{
    "start":"2020-10-12 00:00:00",
    "end":"2020-10-12 00:02:00",
    "groupcol":"291"
}
```

#### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
start | yyyy-mm-dd HH:MM:SS | String | start-time in specified format
end | yyyy-mm-dd HH:MM:SS | String | end-time in specified format
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 

This API is currently used to serve predictions made by algorithms like forecasting and regression.

### Anomaly

API to query anomaly labelled grid data points. In this setting we define a near-exact match. The near exact match rounds down the value to the near existing value in the grid and returns the data point with anomaly score and class.

`operation=scmlmodelserver.anomalyNative`

```python

modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.anomalyNative&org=None&pid=VALID_PID&expid=VALID_EXPID'
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

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.anomalyNative&org=None&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`
> Request Body 

```json
{
    "dim1":0.5,
    "dim2":0.9,
    "groupcol":"ABC"
}
```

#### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
dim1 | amm | float | The amm value to be queried
dim2 | voc | float | The voc value to be queried
groupcol | | String | A valid value of the groupcol. Typically a location for which the predictions need to be fetched. 

This API is currently used to serve predictions made by anoamly detection algorithms.

### Standard SQL
  operation=`scmlmodelserver.sql`

```python
modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.sql&org=None&pid=VALID_PID&expid=VALID_EXPID'
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

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.sql&org=None&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`

> Request Body 

```json
{
    "q":"select * from $df limit 10
}
```

#### Body Parameters
Parameter | Format | Type | Description
--------- | ------- | ----------- | ----------
q | SQL Statement | String | A valid SQL statement


### ActiveLoad
  operation=`scmlmodelserver.activeLoad`

```python
modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.activeLoad&org=None&pid=VALID_PID&expid=VALID_EXPID'
response = requests.post(modelserver_url)
print(response)
```
This endpoint deletes past last run of an experiment if it is present in scmlmodelserver. It loads the latest run predicitions for serving. This endpoint is called by a trigger which automatically updates latest run of an experiment for serving.

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.activeLoad&org=None&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`

The response contains the same output as Plain SQL response.

### Delete 
  operation=`scmlmodelserver.delete`
  
```python
modelserver_url = 'http://{BASE_URL}/v2/actions?op=scmlmodelserver.delete&org=None&pid=VALID_PID&expid=VALID_EXPID'
response = requests.post(modelserver_url)
print(response)
```
An endpoint to delete the current run object from memory and any associated files of the current run from scmlmodelserver.

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlmodelserver.sql&org=None&pid=VALID_PID&expid=VALID_EXPID`

`Content-type:application/json`


> Response

```json
{
    "func_status": "success",
    "status_code": 200
}
```
### Architecture 
ModelServer System Architecture [link](https://github.com/smmahe/test/blob/master/Model_Server_System.pdf).

## SCMLExperiments 
### Experiment

When a new algorithm needs to be added to the zoo so that it is part of SCML Algorithms, a new experiment object is created with config.
config is a nested object, which contains parameters specific for the algorithm in `algorithm` config.

`query` config encapsulates information needed to query data from cloud datastore.

`prediction` contains information about the validity of predictions typically a week, unit of prediction whether it is hourly, minute.

`state` refers to the state of the experiment. `NORMAL` is the desired state.
Any other state requires either retraining or changes so the following run can complete and update state accordingly.

### Run 
Run is the execution of an experiment. Generates the predictions to be served and updates the experiment object to always refer to the current valid run. When a run exists successfully it updates experiment to be in `NORMAL` state.


### Get Experiments For a Project

Get all experiments for a project. The caller specifies the project id and is able to fetch all the experiments for the project.

```python

api = 'http://{BASE_URL}/v2/actions?op=scmlexperiments.listExperiments&org=None&pid=VALID_PID'
response = requests.post(api)
print(response)

```

#### HTTP Request

`GET http://{BASE_URL}/v2/actions?op=scmlexperiments.listExperiments&org=None&pid=VALID_PID`

#### Response

A jsonarray of experiment objects


### Get Experiment By Id For a Project

Get a specific experiment for a project by experiment id. 

```python

api = 'http://{BASE_URL}/v2/actions?op=scmlexperiments.getExperiment&org=None&pid=VALID_PID'
response = requests.post(api,json=json_data)
print(response)

```

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlexperiments.getExperiment&org=None&pid=VALID_PID`

> Request Body

A json containing the experiment id

```json
{
  "expid": "valid_expid"
}
```

#### Response

A json response containing the experiment object 

### Get Any Run For an Experiment of a Project

Get a specific valid run object by specifying the run number for an experiment of a project.

```python

api = 'http://{BASE_URL}/v2/actions?op=scmlexperiments.getRunForExperiment&org=None&pid=VALID_PID'
response = requests.post(api,json=json_data)
print(response)

```

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlexperiments.getRunForExperiment&org=None&pid=VALID_PID`

> Request Body

A json containing the expid value and a run number

```json
{
  "expid" : "valid_expid",
  "run"   : "10"
}
```
#### Response

A json response containing the run object

### Get Latest Run For an Experiment of a Project


Returns the valid latest run object of an experiment. 

```python

api = 'http://{BASE_URL}/v2/actions?op=scmlexperiments.getLatestRun&org=None&pid=VALID_PID'
response = requests.post(api)
print(response)
```

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlexperiments.getLatestRun&org=None&pid=VALID_PID`

> Request Body

A json containing the expid value 

```json
{
  "expid" : "valid_expid" 
}
```
#### Response

A json response containing the latest run object for that experiment


### Get Last N Runs For an Experiment of a Project


Returns the valid latest run object of an experiment. 

```python

api = 'http://{BASE_URL}/v2/actions?op=scmlexperiments.getLastNRuns&org=None&pid=VALID_PID'
response = requests.post(api,json=json_data)
print(response)
```

#### HTTP Request

`POST http://{BASE_URL}/v2/actions?op=scmlexperiments.getLastNRuns&org=None&pid=VALID_PID`

> Request Body

A json containing the expid value 

```json
{
  "expid" : "valid_expid",
  "N"     : "N"
}
```
#### Response

A json response containing the last n run objects for an experiment. At most can be able to query last 10 runs.


