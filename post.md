Introduction to ElasticSearch:

Elastic is a search server based on [Apache Lucene](https://lucene.apache.org/core/), and provides a distributable full text search engine that’s accessible through a restful interface. 

ElasticSearch is schema-less, and uses JSON instead of XML. It is open-source and built in Java, which means you can run ElasticSearch on any platform, as Java is platform independent. 

ElasticSearch is a document-based store. It is an alternative to traditional document stores, so it can be used to replace other document stores like MongoDB or RavenDB.

## Fast and Scalable: 

ElasticSearch is incredibly fast when it comes to searching, so if your current document search is not giving you read performance or is not scaling as well as you would want it to, ElasticSearch is highly scalable.

## Terminology:

For all those coming from traditional MySQL databases, here is a table comparing ElasticSearch terminology with traditional relational database terminology:

A table comparing terminologies.

| MySQL (RDBMS)  ||   ||    Elastic Search | 
|--- | --- | ---|--- | ---|
| Database |   ||   | Index | 
| Table| ||  |Type| 
| Row|    ||  |Document| 

## How to Setup ElasticSearch: 

To get started, you need to download elasticsearch from [this link](https://www.elastic.co/products/elasticsearch), and unzip the zipped file in a folder where you want to place elasticsearch. 

To run this, open a command window, go to your bin folder, and type **elasticsearch** to run it. Make sure you have the `JAVA_HOME` environment variable defined. 

### Interacting with ElasticSearch:

To check whether ElasticSearch has been correctly installed and started locally, use the following URL in your browser :

```bash
http://localhost:9200/
```
It should show you an output like:

```bash
{
  "name" : "Domo",
  "cluster_name" : "elasticsearch_root",
  "version" : {
    "number" : "2.2.0",
    "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
    "build_timestamp" : "2016-01-27T13:32:39Z",
    "build_snapshot" : false,
    "lucene_version" : "5.4.1"
  },
  "tagline" : "You Know, for Search"
}

```

Once ElasticSearch has started, you can use any REST API client such as [postman](https://www.getpostman.com/) or [fiddler](http://www.telerik.com/fiddler). 

Restful APIs are used to interact with ElasticSearch. The generic pattern used to make a RESTful call is as shown below: 

```bash
REST API Format : http://host:port/[index]/[type]/[_action/id]
```

**HTTP Methods used: GET, POST, PUT, DELETE**

- To get a list of all available indices in your ElasticSearch, use the following URL :
```bash
http://localhost:9200/_cat/indices
```

- To get the status of an index (say, a company), use the following URL:
```bash
http://localhost:9200/company?pretty
``` 

The first part (_localhost_) denotes the **host** (server) where your ElasticSearch is hosted, and the default **port** is 9200.  
  
```bash
http://localhost:9200/company/employee/_search
``` 
The second part (_company_) is **index** , followed by the (_employee_) **type** name, followed by (__search_) **action**.
 
ElasticSearch lets you use HTTP methods such as GETs, POSTs, DELETEs, and PUTs along with a payload that would be in a JSON structure.

In this tutorial, I assume you are using the REST API client  [postman](https://www.getpostman.com/) or [fiddler](http://www.telerik.com/fiddler) to run the below mentioned RESTful calls.

Let's take a look at how to create an index, insert data into it and then retrieve data from ElasticSearch.

**Creating an Index:**

```bash
http://localhost:9200/company

PUT
{
"settings": {
   "index": {
     	"number_of_shards": 1,
     	"number_of_replicas": 1
   },
   "analysis": {
     "analyzer": {
       "analyzer-name": {
         	"type": "custom",
         	"tokenizer": "keyword",
         	"filter": "lowercase"
       }
     }
   },
   "mappings": {
     "employee": {
       "properties": {
         "age": {
           	"type": "long"
         },
         "experience": {
           	"type": "long"		
         },
         "name": {
           	"type": "string",
        	   "analyzer": "analyzer-name"
         }
       }
     }
   }
 }  
}
```
Once you run the above command , this is the Response received:

```bash
{
  "acknowledged": true
}
```
The above command creates an index named `Company` with a type named `employee` with the fields `age`, `experience` and `name`.

**What are Analysers:**

ElasticSearch is a text-based search engine based on apache lucene. The data to be indexed is processed according to the requirements prior to the splitting into terms. This process is called analysis, and is performed by analyzers. 

The Analysis process involves :

1. Splitting the text into tokens
1. Standardizing these tokens so they become searchable. 

Analysis is comprised of three functions:

1. Character Filtering
1. Tokenization, and
1. Token filters

Character Filtering is applied on the input text string to filter out the unwanted terms. The Tokenizers are used to split a string into a stream of tokens. The terms generated after the tokenization process are passed through a token filter, which transforms the terms as per the standard requirement of user. For example : Token filters can be used to change the tokenized terms to uppercase.

In the above-created Index, we added the following analyzer :

```bash
   "analysis": {
     "analyzer": {
       "analyzer-name": {
         	"type": "custom",
         	"tokenizer": "keyword",
         	"filter": "lowercase"
       }
     }
   }
```

Here we created a custom analyser named "analyzer-name", with the following components:

1. **"type": "custom"**
An analyzer of type `custom` allows you to combine a Tokenizer with zero or more Token Filters, and zero or more Char Filters. Since no character filter has been used in above defined index , the analyzer has been defined with type custom.

1. **"tokenizer": "keyword"**
This tokenizer emits the entire input as a single output.

1. **"filter": "lowercase"**
The lowercase filter will convert all tokens entering into it to lowercase.

**Inserting Data:**

We need to pass the document in the form of a JSON object as a data parameter when making the HTTP API call.

```bash
http://localhost:9200/company/employee/?_create
POST
{
"name": "Andrew",
"age" : 45,
"experience" : 10
}

```
Response : 
```bash
{
  "_index": "company",
  "_type": "employee",
  "_id": "AVM8D42POa82oxyTa_Pu",
  "_version": 1,
  "_shards": {
	"total": 2,
	"successful": 1,
	"failed": 0
  },
  "created": true
}

```
This way we can insert one document at a time, In order to insert multiple documents, we use the Bulk API of elasticsearch


**Retrieving Data:**

To read all records of a type within an index, use the following commands with the GET http method: 

```bash
http://localhost:9200/company/employee/_search

http://localhost:9200/vehicles/car/_search

http://localhost:9200/vehicles/bike/_search

http://localhost:9200/vehicles/truck/_search

```

The above URLs search in Index named  **vehicles**, having document types car, bike and truck. Each one of these documents will contain specific data related to car, bike and truck respectively. 

The above URLs, under our vehicles index, we have got document types such as company, car, bike and truck. Each one of these documents will contain specific data related to either car,  bike or truck. 

- One can perform lot of other operations using REST APIs on elasticsearch, such as:
- Checking the status of ElasticSearch Server,
- Performing CRUD (Create, Read, Update and Delete) and Search Operations against your indexes.
- Perform operations like paging, sorting, filtering, scripting, faceting, aggregations, etc.
	
**Retrieving Data with Conditional Search:**
- **Fetch all documents :** The above mentioned URL can be rewritten using the match_all parameter to return all documents of a **type** within an **index**.

_Most REST clients (such as postman) dont accept body with a **GET** method, so you can use **PUT** instead, I have shown the examples with a **GET** method._

```bash
http://localhost:9200/company/employee/_search
GET 
{
    "query": { 
        "match_all": {}
    }
}
```

Response:

```bash
{
"took":4,
"timed_out":false,
"_shards":{
            "total":5,
            "successful":5,
            "failed":0
            },
            "hits":{
                        "total":1,
                        "max_score":1.0,
                        "hits":[
                                    {
                                    "_index":"company",
                                    "_type":"employee",
                                    "_id":"AVM8D42POa82oxyTa_Pu",
                                    "_score":1.0,"
                                    _source":{
                                                 "name": "Andrew",
                                                "age" : 45,
                                                "experience" : 10
                                                }
                                    }
                        ]
            }
}
```

- **Fetch all employees with a particular name :**
To retrieve all employees having name as ‘Andrew’, you can use query parameter and specify the condition within it. 

```bash
http://localhost:9200/company/employee/_search
GET 
{
    "query": {
        "match": {
            "name": "Andrew"
        }
    }
}

```
**Response:**

```bash
{
"took":7,
"timed_out":false,
"_shards":{
          "total":5,
          "successful":5,
          "failed":0
          },
"hits":{
          "total":1,
          "max_score":0.30685282,
          "hits":[
                              {
                              "_index":"company",
                              "_type":"employee",
                              "_id":"AVM8D42POa82oxyTa_Pu",
                              "_score":0.30685282,"
                              _source":{
                                        "name": "Andrew",
                                        "age" : 45,
                                        "experience" : 10
                                        }
                              }
                    ]
          }
}
```

- **Fetch all employees with age greater than a number** :
- 
```bash
http://localhost:9200/company/employee/_search
GET 
{
    "query": {
	    "range": { 
      	  	"age": { "gte":  35 }
    	     }
      }
}
```

**Response:**
```bash
{
	"took": 3,
	"timed_out": false,
	"_shards": {
		"total": 1,
		"successful": 1,
		"failed": 0
		},
"hits": {
	"total": 3,
	"max_score": 1,
	"hits":[{
				"_index":"company",
				"_type":"employee",
				"_id":"AVM8D42POa82oxyTa_Pu",
				"_score":0.30685282,"
				_source":{
					"name": "Andrew",
					"age" : 45,
					"experience" : 10
				}
			}]
	}
}
```

- **Fetch data with multiple conditions:**
You can also combine multiple clauses and add them in one query parameter, such as

```bash
http://localhost:9200/company/employee/_search
GET
{
    "bool": {
        "must":     { "match": {"name": "Andrew" }},
        "should":   { "range": {"age": { "gte":  35 }}}
    }
}
```

**Response:

```bash
{
"took":31,
"timed_out":false,
"_shards":{
          "total":5,"successful":5,"failed":0},
          "hits":{"total":1,
                    "max_score":0.04500804,
                    "hits":[{
                              "_index":"company",
                              "_type":"employee",
                              "_id":"AVM8D42POa82oxyTa_Pu",
                              "_score":0.04500804,
                              "_source":{
                                        "name": "Andrew",
                                        "age" : 45,
                                        "experience" : 10
                              }
                    }]
          }
}
```

## Summary: 

ElasticSearch will help you resolve many search optimization problems in your existing applications. It is useful for giving your users a quality search experience and also let them find what they are really looking for.


