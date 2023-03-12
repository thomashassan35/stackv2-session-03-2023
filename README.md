# stackv2-thing-in training session-03-2023
Thing'in the future training session for stackv2 

This training material will go through the basics of using Thing'in portal and Thing'in REST API
using a premade Postman collection and/or programmatic interfaces. 
It consists of 2 parts:
- Introduction (~2.5 hours) : basic usage of the portal and the API, along with data and query examples for simple use cases
- Hands-on use case (~2-3 hours) where attendants will construct a graph together using the knowledge from part 1. This part also covers security aspects and a few more advanced functionalities of the API.


# Part 1: Introduction 

This tutorial / hands-on session covers a few basics required to use Thing'In the future APIs, as well as some more advanced examples.
For all elements of this tutorial, and for your future experience with Thing'In the future, you can refer to the documentation/Wiki : https://wiki.thinginthefuture.com/. 

## Agenda and outline of this document
1. Presentation of the Thing in platform (talk, ~30 min)
2. Reminder / introduction to Thing'in data exchange formats and query language
3. Importing the Postman collection
4. Creation of a dataset using Building Topology Ontology (BOT) (~1h)
    * Presentation of building injector (IFC + topology)
    * Creation of the dataset
    * Injection of the dataset on the Thing in platform
5. Query on the dataset (~1h)
    * Presentation of the wizard 
    * Query the dataset, with wizard and with swagger or postman
    * Select queries
    * Update queries
    * Aggregation queries
    * Geographic queries
    * Use Blobs (binary large objects)
    
    
## Thing'in data formats and query language

### Summary of data management in Thing'in (Avatars/Digital Twin, relationships, data model)
Here are the main things to remember when manipulating `Digital Twins` in Thing'in:
- The term `Digital` Twin is interchangeable with the term `Avatar`
- A `Digital Twin` in Thing'in refers to a node in a global graph
- A `Digital Twin` has a global,shareable identifier, named `iri`, e.g. `http://www.stackv2.com/training-test/green-user-1`
- A `Digital Twin` has an internal identifier, named `uuid`, e.g. `63727326-ac66-4101-a79f-5c4c93a5da65`
- Both `iri` and `uuid` can be used to refer to the `Digital Twin`, (for example to query and retrieve it).
- The `uuid` is generated from the `iri` by Thing'in server. It is consistent in time even when you delete and recreated a `Digital Twin`. 
- Before creating `Digital Twins`, users should create a `Domain`
- A `Digital Twin` belongs to one and only one `Domain`.
- The `Domain` also has an `iri`
- All `ìris` of `avatars`/`Digital Twins` in a domain start with the `Domain` `ìri` (think about it as the domain iri being a prefix).
- All `Digital Twin` should have one or more `classes` (they can be multi-typed)
  - In RDF, this class is defined using the rdf:type relationship
  - In JSON, the `_classes` field of the `Digital Twin` serves as a reference to hold all classes from ontologies
  - Alternatively, the `_labels` field of the twin can be used to reference a user-defined class (a.k.a label), cf. data model information below this paragraph

Here are the main thingins to remember when manipulating relationships:
- Relationships between `Digital Twins` are first class citizen, and can be created, modified, deleted in a similar way
- Relationships are also called `object property`, when they are based on an ontology
- Relationships also have a '_class_'. This class is either the object property used in RDF formats, or the `label` field when using JSON 

Finally, here are the main things to consider in terms of data model:
- Ontologies can be used to model your data. This has many benefits:
  - Using already available data models / standard models
  - Sharing your data model / constructing your data model with partners in a coherent way
  - Adding rules to your model and check that your data complies with those rules
- Alternatively, user-defined classes/properties/relationships can be used. There are advantages to this approach:
  - Fast way to make a simple model
  - 

### Summary of data exchange formats
Thing'in provides a wide variety of data exchange formats/syntaxes, both in input to Thing'in (i.e. data creation), and output (i.e. querying/data retrieval).
However we can divide these formats in two categories : the [JSON-based syntaxes](https://wiki.thinginthefuture.com/en/public/Manipulate_Avatars), and the [semantic web syntaxes](https://wiki.thinginthefuture.com/en/public/Avatar_tutorial) based on RDF.


### Query languages

Thing'in provides 1 main query language, [Thing'in QL](https://wiki.thinginthefuture.com/en/public/Avatars_search), which is a proprietary, document-oriented query language akin to MongoDB's query language.
Thing'in provides 1 alternative query language, [Cypher](https://wiki.thinginthefuture.com/en/public/CypherForThinginQuery), which is a subset of the [Neo4J Cypher](https://fr.wikipedia.org/wiki/Cypher_(langage)) query language
Internally, both queries are translated to the database native query language (ArangoDB).
Thing'in provides an additional time-specific query language, [TCypher](https://wiki.thinginthefuture.com/en/public/Historization), when domains are historized (this will not be covered in this training material).

## Postman

To go through this hands-on session you can either use a preconfigured set of requests given in a Postman collection, or the swagger interface / thingin portal. 

### Importing the postman collection 

If you choose to use the Postman collection, the import procedure is simple:
After cloning / downloading this repository, locate the postman collection file (.json) on your system (The collection is located on the github repository under /postman). 

Then, in postman, go to 
> File -> import

And choose the preconfigured collection.

### Settings up requests in the following sections of the tutorial

In the examples below, when using the swagger/portal you have to replace the variable <yourname> with a string with no spaces or special characters, e.g. "thassan". You have to do this wherever "<yourname>" appears (in the query payloads). 
	
When using Postman, you only have to set an environment variable in your Postman Environment **once**:
Go to
> Environments
	
and add a row : under **VARIABLE**, write **yourname**, and under **CURRENT VALUE**, set it with a string of your choice, the string must have no spaces or special characters, e.g. "thassan".


### Setting up connection to ThingIn  
    
To setup connection with Thing'in, first use the credentials provided at the beginning of the session. 

For Swagger/portal :
After logging in through the portal, you have to use a TOKEN to use the APIs. This is a simple two-step process
	- click on Develop (on top) -> Get My Thingin Token -> Copy to clipboard for swagger Usage 
	- In the swagger interface,  before sending queries, do this step once : click on authorize ->  paster your token under " bearerAuth  (http, Bearer) Thing'in token (without Bearer keyword)"
	
	
In Postman, go to: 
> get developper TOKEN. 

You have to fill the Authorization with basic authentication using the credentials, then send the request
    
To ensure that the following Postman requests are well configured, go to:
>batchPostavatars TEST TOKEN

You have to fill in the payload (Body), then send the request. The response code should be a 201. If you have a bad character error, you might not have configured the <yourname> variable. 
    
**You don't have todo any additional steps for the other queries to use your credentials, it is filled automatically**
    


## Creation of the dataset

### Presentation of building injectors

There exist two types of injector to upload ifc files:
* Ifc file to Dogont ontology:
    * This injector will only inject the topology of the building using both DogOnt and Bot ontologies
* Ifc file to Ifc ontology:
    * This injector will inject the ifc according to the ifc ontology

Both injectors can be found under "Provide" > "specific injector".

An example can be found under "Explore" > "Explore Thing'in database".
Select the request "AC20 - Topological injection".
The original ifc file can be find in:
> dataset/AC20-FZK-Haus.ifc

### Create dataset

#### Bot Ontology

https://w3c-lbd-cg.github.io/bot/

![Schema of BOT ontology](https://w3c-lbd-cg.github.io/bot/assets/zones.png)


#### Mandatory properties

Each avatar have two mandatory properties:
* **__iri_**: the identifier ot the avatar (Inernational Ressource Identifier)
> ex: "__iri_": "http://stackv2.com/training/simpleBuilding/Room_A_1"

* **__classes_**: list of ontology classes. The avatar is an instance of each of these classes. An avatar have at least one class
> ex: "__classes": ["https://w3id.org/bot#Space"]

When avatars are created the are stored in a domain. Domains can be seen as container.
They are created automatically based on the **__iri_** of an avatar.
For the avatar above the following domain will be created:
> ex: "__domain":"http://stackv2.com/training/simpleBuilding/"

If an new avatar's iri follow the same prefix ("http://stackv2.com/training/simpleBuilding/"), then the avatar is store in the same domain.
> ex: "__iri_": "http://stackv2.com/training/simpleBuilding/Room_A_2", is in the same domain than: "__iri_": "http://stackv2.com/training/simpleBuilding/Room_A_1"

Using the Bot ontology, create the following:
- 1 bot:Building
- 2 bot:Storey
- 4 bot:Space (2 space per Storey)
- Link bot:Storey - hasSpace -> bot:Space
- Linkt bot:Building - hasStorey -> bot:Building

There is two possibles format RDF or JSON.
See examples bellow.

#### RDF
Input: 

Complete the query body by replacing (not necessary in Postman if done previously) :
```
<yourname>
``` 


```rdf
@prefix ex: <http://stackv2.com/training/simpleBuilding/<yourname>/> .
@prefix bot: <https://w3id.org/bot#> .
@prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

ex:Storey_1 rdf:type bot:Storey .

ex:Small_Building rdf:type bot:Building ;
	bot:hasStorey ex:Storey_1 .

```

<details>
  <summary>Answer:</summary>
  
  ```rdf
    @prefix ex: <http://stackv2.com/training/simpleBuilding/> .
    @prefix bot: <https://w3id.org/bot#> .
    @prefix rdf:   <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

    ex:Room_A_1 rdf:type bot:Space .

    ex:Room_A_2 rdf:type bot:Space .

    ex:Room_B_1 rdf:type bot:Space .

    ex:Room_B_2 rdf:type bot:Space .

    ex:Storey_1 rdf:type bot:Storey ;
	    bot:hasSpace ex:Room_A_1 ;
	    bot:hasSpace ex:Room_B_1 .

    ex:Storey_2 rdf:type bot:Storey ;
	    bot:hasSpace ex:Room_A_2 ;
	    bot:hasSpace ex:Room_B_2 .
	
    ex:Small_Building rdf:type bot:Building ;
	    bot:hasStorey ex:Storey_1 ;
	    bot:hasStorey ex:Storey_2 .
```
</details>




#### JSON

Input:

Complete the query body by replacing (not necessary in Postman if done previously) :
```
<yourname>
``` 


```json
[
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/<yourname>/Room_A_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/<yourname>/Small_Building",
	"_classes": ["https://w3id.org/bot#Building"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/<yourname>/Storey_1"
	  }
	]
  }
]
```


<details>
  <summary>Answer:</summary>
  
```json
[
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Room_A_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Room_A_2",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Room_B_1",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Room_B_2",
	"_classes": ["https://w3id.org/bot#Space"]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Storey_1",
	"_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Room_A_1"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Room_B_1"
	  }
	]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Storey_2",
	"_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Room_A_2"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasSpace",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Room_B_2"
	  },
	  {
		"_label": "http://orange-labs.fr/fog/ont/building.owl#isAbove",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_1",
		"bim2twin:dismantlingTime": 90
	  }
	]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Storey_3",
    "_classes": ["https://w3id.org/bot#Storey"],
	"_outE": [
	  {
		"_label": "http://orange-labs.fr/fog/ont/building.owl#isAbove",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_2",
		"bim2twin:dismantlingTime": 102
	  }
	]
  },
  {
    "_iri": "http://stackv2.com/training/simpleBuilding/Small_Building",
	"_classes": ["https://w3id.org/bot#Building"],
	"_outE": [
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_1"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_2"
	  },
	  {
	    "_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_3"
	  }
	]
  }
]
```
</details>

### Inject your data

#### Swagger
    
The swagger can be find in "Develop" > "Thing in API reference"

Avatar can be injected one by one:
> /avatars

Inject several avatars at once:
> /batch/avatars/

## Queries

### Use the wizard

Under "Explore" > "Explore Thing in database" > "Use wizard"

Queries can be build according to three possibles options:
* Domain: in which domain are the avatars
* Classes: avatars of the given class(es) to look for
* Geolocation: define a polygon or a radius given a point to look for avatars (works only for avatars with geo coordinates)

### Use the swagger or postman collection

#### Swagger
    
The swagger can be find in "Develop" > "Thing in API reference"

To find avatars:
> /avatars/find/

#### Postman collection

### Query your own dataset

Using the swagger or postman collection.

```json
{
  "query": {
    "$domain": "http://stackv2.com/training/ac20/"
  },
  "view": {}
}
```

### Query - Find the rooms of a given store:

Example to find storeys of a given building, where "http://stackv2.com/training/ac20/ifc-2hQBAVPOr5VxhS3Jl0O47h" is the identifier of the building node/avatar/Digital Twin:

```json
{
  "query": [
    {
      "$domain": "http://stackv2.com/training/ac20/",
      "$iri": "http://stackv2.com/training/ac20/ifc-2hQBAVPOr5VxhS3Jl0O47h",
      "->http://elite.polito.it/ontologies/dogont.owl#contains": "storey"
    },
    {
      "$alias": "storey",
      "$classes": "https://w3id.org/bot#Storey"
    }
  ],
  "view": {}
}
```

<details>
  <summary>Answer:</summary>
  
  ```json
{
  "query": [
    {
      "$domain": "http://stackv2.com/training/ac20/",
      "$iri": "http://stackv2.com/training/ac20/ifc-2eyxpyOx95m90jmsXLOuR0",
      "->http://elite.polito.it/ontologies/dogont.owl#contains": "room"
    },
    {
      "$alias": "room",
      "$classes": "https://w3id.org/bot#Space"
    }
  ],
  "view": {}
}
```
</details>



### Add a storey to the building

First create the storey:

```json
{
    "_iri": "http://stackv2.com/training/simpleBuilding/Storey_3",
    "_classes": ["https://w3id.org/bot#Storey"]
}
 ```

Swagger:
> /avatars/

In Postman :
> singlePostAvatar JSON
    
Add the storey to the building :

First find the "__uuid_" of the building. It's a property of the building's avatar.
> ex: 0aba42b6-0f4e-55e3-908b-5b1eec1d3f4a

To update the data use, the swagger:
> /avatars/update/set/{uuid}

or in Postman:
> update SET


```json
{
	"_iri": "http://stackv2.com/training/simpleBuilding/Small_Building",
	"_outE": [{
		"_label": "https://w3id.org/bot#hasStorey",
		"_targetIRI": "http://stackv2.com/training/simpleBuilding/Storey_3"
	}
    ]
}
```
    

### Aggregation query

Example of a query to dismantle a building, where relation of type "http://orange-labs.fr/fog/ont/building.owl#isAbove", have a property of time express in minutes. The query make the sum of the value of the property of type "bim2twin:dismantlingTime".
    
XXX
```json
{
  "query": [
  {
    "$alias": "a",
    "$domain": "http://stackv2.com/training/simpleBuilding/",
    "->http://orange-labs.fr/fog/ont/building.owl#isAbove": "b"
  },
  {
    "$alias": "b_e",
    "$sum": {
      "$property": "bim2twin:dismantlingTime",
      "$alias": "totalTime"
    }
  }
  ],
  "view": {}
}
```
With swagger use:
> /avatars/findAggregate/

With postman use:
>  find AGGREGATE
    
### Geographic queries

`Digital Twins` may have geographic coordinates attached to them, as a property. 
Standard such as GeoJSON are used to encode geographic information in Thing'in. 

The following queries require avatars to have geographic coordinates.
    
The query example below reads: "_given the Avatar `Arbre` (which has a geographic coordinate attached to it), 
finds avatars in the same `domain` with an attached geographic area/polygon that contains `Arbre`_":

```json
{
  "query": [
    {
      "$alias":"Arbre",
      "$iri":"http://stackv2.com/training/rennes/polygon/tree_17683"
    },
    {
      "$domain": "http://stackv2.com/training/rennes/polygon/",
      "http://www.opengis.net/gml/pos/polygon": {
        "$geoContains": {
          "$alias":"Arbre"
        }
      }
    }

  ],
  "view": {}
}
```
    
Given the geographic position of an avatar, you can also find avatars nearby in a radius of 100 meters as follow:
```json
{
  "query": [
    {
      "$alias":"Arbre",
      "$iri":"http://stackv2.com/training/rennes/polygon/tree_17683"
    },
    {
      "$domain": "http://stackv2.com/training/rennes/polygon/",
      "http://www.opengis.net/gml/pos": {
        "$distance": {
          "$alias":"Arbre",
          "$lte":100
        }
      }
    }

  ],
  "view": {}
}
```

Other geographic functionalities are described [here](https://wiki.thinginthefuture.com/en/public/Avatars_search) -> Geospatial operators
    
### Thing'in advanced functionalities
    
Thing'In the future offers an array of more advanced features for avatars such as ACLs and Security Groups, graph traversal and paths computation, 
geometry or unit conversion, avatar clusters, etc. In the next part, we will cover a few of these advanced functionalities. 
    
## End of part 1 
That's it for this first part, feel free to ask any question :)


# Part 2 

The objective of this part is to practice with what you've learned in the first part, and also introduce a few more advanced
parts of the API through a use case. 

Unlike the first part where you created your own isolated dataset, in this part all attendants will construct a common graph. 
This graph will consist of the people in this room, along with other information. 

Instructions should now be given orally to constitute different teams. 


### Dataset creation

Dataset base (already done)

The teams that you have constituted are already present in a partially made graph. 
This graph is in the domain `http://www.stackv2.com/training/`.
Visualize the graph through the portal by making a query on this domain.

This graph makes use of the organization ontology to model the teams you've constituted (green,blue,yellow,red).
Each team corresponds to a node in the graph with a few properties (name of the team, metadata e.g. iri and uuid).
All teams are attached to a common node that represents our whole "Organization" for this session. 

### Assignment 1
Your first objective will be to complete this graph with a node representing yourself. 
To do that, you will use two well-known ontologies :
- the organization ontology (http://www.w3.org/ns/org#). See https://www.w3.org/TR/vocab-org/ 
- the Friend of a friend (Foaf) ontology (http://xmlns.com/foaf/0.1/). See http://xmlns.com/foaf/0.1/ 

Create the node representing yourself. This node should use:
- The foaf:Person class
- The foaf:family_name property
- The foaf:firstName property
- The foaf:name property that should contain your whole name
- The foaf:age property (http://xmlns.com/foaf/0.1/age). If you don't agree to share it, use a number of your choice


Usually, we don't put personal data in Thing'in. That is a good moment to talk about two advanced functionality: data expiration, and security features.
Security features will be discussed in assignment 3. Concerning data expiration, you may read the following bits of information (optional).

### Optional reading : data expiration
You may have seen that each node and relationship in Thing'in are marked with the metadata `last_updated`, and `creationDate`, as well as `expireDate` (see https://wiki.thinginthefuture.com/en/public/avatarSpecialFields) .
To ensure that your personal doesn't remain, the domain where you are creating data at this moment is marked with a fixed deletion date. 
Any data will in it will be deleted after that date (tomorrow). 
This behaviour can be set when created your domain (in the POST domain operation), or for any avatar that you create
by setting the `expire_date` header to the appropriate value. We will not demonstrate the usage further in this session.


### Assignment 2
Each person in your team should then be linked to the team node, by using the `http://www.w3.org/ns/org#memberOf` 
relationship (a.k.a object property). To do that, you can either update the foaf:Person node using the avatar/update SET instruction,
of you can add a relationship using the POST relationship (see the SWAGGER and/or ask for help for detailed instructions).

### Assignment 3 
The leader of your team that you should have elected by now should be linked to the team node. 
Since he's the leader, we will use the org:headOf relationship. Collaborate with your team to create this link as it only 
needs to be added once. 

### Assignment 4
Now that teams have updated their respective graph, we can make a few queries to the whole graph (you may try to compute a subset of these queries or work with your team to do all of them)

- Search for the head (team leader) of each team and return both the team node and the head node (use the "return" field in the JSON query)
- Search for the head of teams that have 4 or more members. You may look at the cardinality object property query pattern here https://wiki.thinginthefuture.com/en/public/Avatars_search 
- Search for a particular member of your team using his name 
- Search for all team members above the age of 30

XXX
- Shown orally : computing the average age of a team (aggregation query)

### Assignment 5 : securing or data from other teams
Since we have some sensible data to protect from other teams and from the world, we will try to put in place some 
security rules. 

We will create an Access Control List, and a Security Group for each team. 
The security group has already been created, but we will need to add all users to each SG. 


### Assignment 6 : going further... 










### Blobs (Binary large objects)

`Digital Twins` can be attached to one or more [Blobs](https://wiki.thinginthefuture.com/en/public/Manipulate_blobs) (i.e. files).
Thing'in provides cloud-based Blob storage (backed by AWS S3).  This can be useful in several use cases.
For example, a sensor produces data that cannot be encoded through the graph or should be kept in an external system. 
We show below a basic example of how to create a blob and attach it to an avatar, then retrieve or delete it.
    
**Create a blob.**
With swagger, use 
>/blobs/

With postman, use 
> post BLOB

When using the swagger, you can simply use the UI to select a file.
For the other parameters : 
- description is indicative, optional
- tags are indicative, optional
- visibility can be set to 0 (public) or 255 (private, default)
    
As for avatars, the response includes the `$uuid` which we can use to retrieve it, delete it, or link the blob to an avatar.
    

**Link to an avatar**
We will not cover how to create an avatar again, refer to the previous section of the tutorial or use an existing avatar you have created, you will need either its IRI or its UUID.

With swagger, use 
>/blobs/link/

With postman, use 
> link BLOB to avatar
    
In Postman, you don't need to copy and paste the `$uuid` of the blob from the blob creation response, a post-request script handles that for you and fills the following Delete/Get requests. 
    
**Retrive a blob**
With swagger, use 
>GET /blobs/{uuid}

With postman, use 
> get BLOB
    
**Delete a blob**
    
With swagger, use
> DEL /blobs/{uuid}
    

With postman, use 
> delete BLOB