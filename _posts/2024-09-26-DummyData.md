---
layout: post 
title: "Building tools to generate dummy data"
author: JoeL
categories: [Blog]
tags: [RAP, Data Engineering, Capability, Data Visualisation, Automation]
image: assets/images/post-imgs/2024-09-26-DummyData/dd-cover.jpg
description: "Generating dummy data to support platform testing"
hidden: False
---

*Image Source: Image by <a href="https://unsplash.com/@goeran?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Goren Eidens</a> on <a href="https://unsplash.com/photos/white-lotus-flower-on-body-of-water-6T7kfc3VitU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>*

### Building tools to generate Dummy Data

In this blog we will present generalised code we have written for generating dummy data. We will also provide a worked example using the code to generate some dummy data. We have used dummy data in the Welsh Government for testing new platforms and data processes. The ability of dummy data to replicate the rules, structure and format of a data specification or real dataset makes it perfect for testing systems migration in place of using real data in insecure environments. Some examples of our use of dummy data include testing the Homes for Ukraine Data Platform and our internal Human Resources systems.

Before talking more about our work, we think it is important to define what we mean by dummy data. We define dummy data as data that mimics a real dataset or data specification without any statistical relationships or patterns being preserved. This is an important distinction from synthetic data. Synthetic data is generated with the goal of preserving to some extent the statistical relationships and patterns from a real dataset. 

### Tools available for generating dummy data

The code we have written to generate dummy data uses the faker Python package. This package is capable of generating vast amounts of fake information including names, dates, addresses and job titles. We have also used faker to generate fake information that meets the format locale-specific information like national insurance numbers and postcodes. For those interested in exploring the faker package in more detail the full documentation is available [here](https://faker.readthedocs.io/en/master/#basic-usage ).

The faker package provides most of the functionality we need to generate dummy data. However, there are some pieces of information we want more control over or to generate in a slightly different way. We also need to be able to generate information that is persistent between many tables this is particularly important when we mimic relational systems like a relational database. This is why we have created generator code for dummy data. The remainder of this blog will focus on how our Dummy Data Generator can be used and provide some useful code for doing so.

### Practical example of the dummy data generator

To use the data generator, you will first need to follow the instructions listed in the README file for the repository. This includes cloning the repository and installing the necessary dependencies.  The example below begins after cloning the repository and installing dependencies.
As an example, we will generate data that matches the following relational schema:

Figure 1: Example schema

<img class="featured-image img-fluid" width="70%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-26-DummyData/sd-schema.png" alt="Schema showing two tables Employees with columns EmployeeRef, Name, PlaceofWork, Postcode, StartDate and EndDate and EmergencyContacts with columns Name, EmployeeRef and PhoneNum">

We will be generating two tables. An employee table and a contact information table. There are some constraints in the schema:
1.	Employee ID must be a primary key
2.	An employee can have multiple emergency contacts
3.	Employee ID is a foreign key in the emergency contacts table relating to the Employee ID in the Employee table

We will also add the following logical requirements:
1.	Employment start date must be after 01/01/2000
2.	Employment end date must be after employment start date
3.	Only half of all employees will have an employment end date
4.	Postcode must be in the UK format
5.	Place of work will be sampled from a fictional distribution 

We first need to set up a JSON file which dictates the structure of our data. For the example, we create the following:

Figure 2: Example JSON file
```json
{
    "tables": {
        
        "employee": {
            "EmployeeRef": {"type":"id", "prefix":"ORG", "digits": 6, "unique": true},
            "FirstName": {"type":"name", "surname":false, "forename":true},
            "PostCode": {"type":"location", "level":"postcode"},
            "StartDate": {"type":"date", "constraints": [">01-01-2000"]},
            "EndDate": {"type":"date", "constraints": [">StartDate"], "null_prop": 0.5},
            "PlaceofWork": {"type":"custom", "name":"generate_workplace"},
            "__nrows__": 100
        },
        "emergency_contacts": {
            "EmployeeRef": {"type": "foreign-key", "table":"employee", "col":"EmployeeRef", "rel_type":"one-to-many(1)"},
            "Name": {"type":"name", "surname":true, "forename":true},
            "PhoneNum": {"type":"contact", "level":"phone", "unique":false},
            "__nrows__": 150
        }
    }
}
```

Some specific items that help us meet the requirements are:

* **unique**: Ensures that EmployeeRef maintains its integrity as a primary key
* **level**: Specifies the level at which information is required for the data item, an address for example may be generated at street level or postcode level.  
* **constraints**: Fixed constraints can be given, for example, all dates should be after 01/01/2000 or relative constraints can be given, for example, all employment end dates should be after the employment start dates. 
* **null_prop**: Specifies the likelihood of a value being left null, indicates that a column may contain missing data.
* **foreign_key**: Specifies that the values should be taken from a primary key in another table.
* **rel_type**: Specifies the relationship between tables in this case one to many (1) where every employee must have one or more contact.

The JSON file is  used to define the structure of the dummy data. The next step is writing the Python code to generate it. First, we need to write a function that will generate our custom data. We actually need a generator function, generators can be used like iterables in Python. Generators yield results in a loop which is perfect for our use case of generating dummy data. 

In this case our custom data function is:
```python
import numpy as np

def generate_workplace(faker):
    workplaces = ['Cardiff', 'Powys', 'Conwy', 'Gwynedd', 'Newport', 'Swansea']
    weights = [0.2,0.3,0.1,0.2,0.1,0.1]

    workplace = np.random.choice(workplaces, 1, p=weights)

    yield workplace[0]

custom_funcs = {'generate_workplace':generate_workplace}
```

which we use to generate a workplace location based on a prescribed distribution defined by the `weights` list so that there is a 20% chance of employment being in Cardiff, 30% in Powys and so on.

Custom functions enable us to be creative or to adapt to unique requirements when generating dummy data. For example, we could use custom functions to:
•	generate custom email addresses that meet an organisation format
•	generate high-fidelity dummy data based on a more complex model or on a probability distribution
•	generate columns that are aggregations or calculations based on other columns

With the custom function defined we are able to generate dummy data. We start by initialising the generator:

```python
generator = DummyDataGenerator('config/example.json', custom_funcs=custom_funcs, seed=2187, locale='en_GB')
```

When we initialise the generator, we pass in some key information:
* A path to the JSON schema we set up earlier.
* The custom functions dictionary we defined above.
* A random seed to ensure reproducibility.
* A locale (en_GB) which specifies the locale in which to generate information for example generate UK post code formats.

Then we generate the dummy data:

```python
data = generator.build()
```
Figure 3: Examples of dummy data

<img class="featured-image img-fluid" width="80%" height="auto" src="{{ site.basurl }}/assets/images/post-imgs/2024-09-26-DummyData/sd-employee.PNG" alt="Dummy employee data">

As the examples show we have successfully generated the data to the requirements set out above. The EmployeeRef can successfully be used to link information in the specified relationship type, start and end dates meet the constraints and all information is formatted correctly. This dataset is now ready to be used to test new automated pipelines!

### Considerations for reproducibility and accessibility

When generating dummy data an added benefit is reproducibility. Note that above we used random seeds when generating dummy data. This ensures that using the same schema we can get the same dummy data in any environment. There is also an added benefit to reproducibility by using JSON schemas as we can catalogue and store the JSON file used to generate a dataset and archive for later use. Further, it is possible to provide a JSON schema and support code to an analyst to generate their own dummy data based on your specification. This enables users to create as much data as they require.

The dummy data tool presented is aimed at analysts, data scientists and statisticians with a range of coding experience. Therefore, we consider accessibility to be of high importance. We have implemented the JSON file approach to enable the tool to be used with minimal code to generate simple dummy datasets. For those with more experience in coding we present the tool as a starting point to extend across more bespoke use cases. This can be done using either the custom functions capability or just the concepts shared in the dummy data tool.

### Considerations for diversity and inclusion

The examples above make use of the locale argument in the faker package to generate data with respect to the en_GB locale. This ensures that addresses and ID information (national insurance numbers) are in the Great British format. However, when generating dummy data it is important to be mindful of diversity and inclusion especially when using locales. We point to the use of custom functions, or iterative approaches to generation to use multiple locales when generating dummy data. This ensures that downstream use cases consider diversity and inclusion in the same way that they would for real datasets.

As an example, we provide the code below that samples from a short list of Welsh names when producing a first name field:

### Future work

We aim to continue to use dummy data to support a range of migration, test or example projects across the organisation. For those interested in applying the code themselves we provide our GitHub repository here.
