# Graph Temporal Data Warehouse (GTDW)

GTDW approach is an extension of classical multidimensional modelling based on the fact, measure, dimension, level and hierarchy concepts. This extension consists of the introduction of the temporal concept in aggregation relationships due to the changes in the dimensions. The graph formalisation and the graph-oriented database implementation were adopted to overcome the constraints generated by the use of an ER model. This model separates the business concepts from the descriptors (attributes) to allow the independent evolution of each entity. Facts, dimensions and hierarchy levels are represented by nodes corresponding to the basic multidimensional concepts to which other nodes representing descriptors are linked. A temporal label consisting of a valid_time (VT) and a transaction_time (TT) has been assigned to all entities to allow the identification of the different instances. Additionally, the From_Date (FT) and To_Date (TD) labels determine the lifespans of the relationships in the dimensions

According to the GTDW approach, we chose to perform the instantiation process on Neo4j using the SSB data while generating several temporal changes in the aggregation relationships of some hierarchical levels and attributes; temporal queries were then applied to this temporal DW. Note that in the initial SSB schema, there were no hierarchical levels; we generated them from the attributes, as shown in

![Cover](https://github.com/Redwass/GTDW/blob/main/figures/ssb_instance_change.jpg)

As a reminder, the Star Schema Benchmark, or SSB, was designed to evaluate the performance of database systems for star schema data warehouse queries. The SSB schema is based on the [TPC-H benchmark] (http://www.tpc.org/tpch/), but in a modified form with LINEORDER as a central fact table and dimension tables for customer, part, supplier, and date. The queries are also based on the TPC-H queries, but the number of queries is reduced to 13 and have been chosen to cover as much of the SSB as possible, so that potential users can derive a performance evaluation of the weighted subset they expect to use in practice. They are divided into 4 subgroups according to the difficulty and the number of domensions involved. The idea is that the total number of fact table rows retrieved will be determined by the selectivity (i.e., total Filter Factor FF) of restrictions on dimensions. More details in [Paper Download](https://www.cs.umb.edu/~poneil/StarSchemaB.PDF)


As the SSB data span from 1992 to 1998, the following changes in relationships were made:

(1) For the dimension Customer, one hundred randomly selected customers have changed their assignment on the hierarchical levels  C_City, C_Nation and C_Region. Thus, these clients are assigned to two different levels according to the two time intervals T1 = [FD1 = 01/01/1992, TD1 = 01/01/1994] and T1 = [FD1 = 01/01/1994, TD1 = 31/12/9999]. 

(2) For the dimension Supplier, one hundred randomly selected suppliers have changed their assignment on the hierarchical levels C_City, C_Nation and C_Region. Thus, these suppliers are assigned to two different levels according to the two time intervals T2 = [FD2 = 01/01/1992, TD2 = 01/01/1996] and T2 = [FD2 = 01/01/1996, TD2 = 31/12/9999].

(3) For the dimension Part, the Size attributes of one hundred randomly selected products were changed . Thus, these products had two different sizes; they had one size for the time interval T3 = [FD3 = 01/01/1992, TD3 = 01/01/1995] and another size for the time interval T3 = [FD3 = 01/01/1995, TD3 = 31/12/9999]

The queries will be conditioned on the FD and TD parameters to obtain a consistent result that accurately reflects the state of the data. We were able to apply the 13 proposed queries for SSB in a temporal format, and all results were verified.

## 1. Platform, data and installation. 

To install the GAMM with SSB data, use the graph database Neo4j version 4.x.x or higher. The data to be downloaded from [Download](https://github.com/Redwass/GTDW/tree/main/DATA) is in the CSV's files. The configuration and installation commands on Windows and Linux platforms are as follows:

### 1.1 Neo4j configuration

This configuration is for 16,0 Go of RAM

#### Java Heap Size: 

dbms.memory.heap.initial_size=4096m

dbms.memory.heap.max_size=4096m

dbms.memory.pagecache.size=9216m

### 1.2 Create data for database

#### For windows 
```cmd
neo4j-admin.bat import --database=GTDW --nodes=lineorder=C:\td\lineorder-header.csv,C:\td\lineorder.csv --nodes=p_brand=C:\td\p_brand-header.csv,C:\td\p_brand.csv --nodes=c_city_name=C:\td\c_city_name-header.csv,C:\td\p_brand_name.csv --nodes=p_category=C:\td\p_category-header.csv,C:\td\p_category.csv --nodes=p_category_name=C:\td\p_category_name-header.csv,C:\td\p_category_name.csv --nodes=p_mfgr=C:\td\p_mfgr-header.csv,C:\td\p_mfgr.csv --nodes=p_mfgr_name=C:\td\p_mfgr_name-header.csv,C:\td\p_mfgr_name.csv --nodes=p_type=C:\td\p_type-header.csv,C:\td\p_type.csv --nodes=p_type_name=C:\td\p_type_name-header.csv,C:\td\p_type_name.csv --nodes=part=C:\td\part-header.csv,C:\td\part.csv --nodes=part_name=C:\td\part_name-header.csv,C:\td\part_name.csv --nodes=part_size=C:\td\part_size-header.csv,C:\td\part_size.csv --nodes=c_city=C:\td\c_city-header.csv,C:\td\c_city.csv --nodes=c_city_name=C:\td\c_city_name-header.csv,C:\td\c_city_name.csv --nodes=c_mktsegment=C:\td\c_mktsegment-header.csv,C:\td\c_mktsegment.csv --nodes=mktsegment_name=C:\td\c_mktsegment_name-header.csv,C:\td\c_mktsegment_name.csv --nodes=c_nation=C:\td\c_nation-header.csv,C:\td\c_nation.csv --nodes=c_nation_name=C:\td\c_nation_name-header.csv,C:\td\c_nation_name.csv --nodes=c_region=C:\td\c_region-header.csv,C:\td\c_region.csv --nodes=c_region_name=C:\td\c_region_name-header.csv,C:\td\c_region_name.csv --nodes=customer=C:\td\customer-header.csv,C:\td\customer.csv --nodes=customer_address=C:\td\customer_address-header.csv,C:\td\customer_address.csv --nodes=customer_name=C:\td\customer_name-header.csv,C:\td\customer_name.csv --nodes=customer_phone=C:\td\customer_phone-header.csv,C:\td\customer_phone.csv --nodes=s_city=C:\td\s_city-header.csv,C:\td\s_city.csv --nodes=s_city_name=C:\td\s_city_name-header.csv,C:\td\s_city_name.csv --nodes=s_nation=C:\td\s_nation-header.csv,C:\td\s_nation.csv --nodes=s_nation_name=C:\td\s_nation_name-header.csv,C:\td\s_nation_name.csv --nodes=s_region=C:\td\s_region-header.csv,C:\td\s_region.csv --nodes=s_region_name=C:\td\s_region_name-header.csv,C:\td\s_region_name.csv --nodes=supplier=C:\td\supplier-header.csv,C:\td\supplier.csv --nodes=supplier_address=C:\td\supplier_address-header.csv,C:\td\supplier_address.csv --nodes=supplier_name=C:\td\supplier_name-header.csv,C:\td\supplier_name.csv --nodes=supplier_phone=C:\td\supplier_phone-header.csv,C:\td\supplier_phone.csv --nodes=date=C:\td\date-header.csv,C:\td\date.csv --relationships=order_part=C:\td\order_part-header.csv,C:\td\order_part.csv --relationships=order_customer=C:\td\order_customer-header.csv,C:\td\order_customer.csv --relationships=order_supplier=C:\td\order_supplier-header.csv,C:\td\order_supplier.csv --relationships=order_date=C:\td\order_date-header.csv,C:\td\order_date.csv --relationships=p_brand_name=C:\td\p_brand_to_name-header.csv,C:\td\p_brand_to_name.csv --relationships=p_category_name=C:\td\p_category_to_name-header.csv,C:\td\p_category_to_name.csv --relationships=p_mfgr_name=C:\td\p_mfgr_to_name-header.csv,C:\td\p_mfgr_to_name.csv --relationships=p_type_name=C:\td\p_type_to_name-header.csv,C:\td\p_type_to_name.csv --relationships=part_brand=C:\td\part_to_brand-header.csv,C:\td\part_to_brand.csv --relationships=part_category=C:\td\part_to_category-header.csv,C:\td\part_to_category.csv --relationships=part_name=C:\td\part_to_name-header.csv,C:\td\part_to_name.csv --relationships=part_size=C:\td\part_to_size-header.csv,C:\td\part_to_size.csv --relationships=part_type=C:\td\part_to_type-header.csv,C:\td\part_to_type.csv --relationships=c_city_name=C:\td\c_city_to_name-header.csv,C:\td\c_city_to_name.csv --relationships=c_mktsegment_name=C:\td\c_mktsegment_to_name-header.csv,C:\td\c_mktsegment_to_name.csv --relationships=c_nation_name=C:\td\c_nation_to_name-header.csv,C:\td\c_nation_to_name.csv --relationships=c_region_name=C:\td\c_region_to_name-header.csv,C:\td\c_region_to_name.csv --relationships=customer_address=C:\td\customer_to_address-header.csv,C:\td\customer_to_address.csv --relationships=customer_city=C:\td\customer_to_city-header.csv,C:\td\customer_to_city.csv --relationships=customer_mktsegment=C:\td\customer_to_mktsegment-header.csv,C:\td\customer_to_mktsegment.csv --relationships=customer_name=C:\td\customer_to_name-header.csv,C:\td\customer_to_name.csv --relationships=customer_nation=C:\td\customer_to_nation-header.csv,C:\td\customer_to_nation.csv --relationships=customer_phone=C:\td\customer_to_phone-header.csv,C:\td\customer_to_phone.csv --relationships=customer_region=C:\td\customer_to_region-header.csv,C:\td\customer_to_region.csv --relationships=s_city_name=C:\td\s_city_to_name-header.csv,C:\td\s_city_to_name.csv --relationships=s_nation_name=C:\td\s_nation_to_name-header.csv,C:\td\s_nation_to_name.csv --relationships=s_region_name=C:\td\s_region_to_name-header.csv,C:\td\s_region_to_name.csv --relationships=supplier_address=C:\td\supplier_to_address-header.csv,C:\td\supplier_to_address.csv --relationships=supplier_city=C:\td\supplier_to_city-header.csv,C:\td\supplier_to_city.csv --relationships=supplier_name=C:\td\supplier_to_name-header.csv,C:\td\supplier_to_name.csv --relationships=supplier_nation=C:\td\supplier_to_nation-header.csv,C:\td\supplier_to_nation.csv --relationships=supplier_phone=C:\td\supplier_to_phone-header.csv,C:\td\supplier_to_phone.csv --relationships=supplier_region=C:\td\supplier_to_region-header.csv,C:\td\supplier_to_region.csv
```

#### For Linux 
```cmd
neo4j-admin import --database=GTDW --nodes=lineorder=C:\td\lineorder-header.csv,C:\td\lineorder.csv --nodes=p_brand=C:\td\p_brand-header.csv,C:\td\p_brand.csv --nodes=c_city_name=C:\td\c_city_name-header.csv,C:\td\p_brand_name.csv --nodes=p_category=C:\td\p_category-header.csv,C:\td\p_category.csv --nodes=p_category_name=C:\td\p_category_name-header.csv,C:\td\p_category_name.csv --nodes=p_mfgr=C:\td\p_mfgr-header.csv,C:\td\p_mfgr.csv --nodes=p_mfgr_name=C:\td\p_mfgr_name-header.csv,C:\td\p_mfgr_name.csv --nodes=p_type=C:\td\p_type-header.csv,C:\td\p_type.csv --nodes=p_type_name=C:\td\p_type_name-header.csv,C:\td\p_type_name.csv --nodes=part=C:\td\part-header.csv,C:\td\part.csv --nodes=part_name=C:\td\part_name-header.csv,C:\td\part_name.csv --nodes=part_size=C:\td\part_size-header.csv,C:\td\part_size.csv --nodes=c_city=C:\td\c_city-header.csv,C:\td\c_city.csv --nodes=c_city_name=C:\td\c_city_name-header.csv,C:\td\c_city_name.csv --nodes=c_mktsegment=C:\td\c_mktsegment-header.csv,C:\td\c_mktsegment.csv --nodes=mktsegment_name=C:\td\c_mktsegment_name-header.csv,C:\td\c_mktsegment_name.csv --nodes=c_nation=C:\td\c_nation-header.csv,C:\td\c_nation.csv --nodes=c_nation_name=C:\td\c_nation_name-header.csv,C:\td\c_nation_name.csv --nodes=c_region=C:\td\c_region-header.csv,C:\td\c_region.csv --nodes=c_region_name=C:\td\c_region_name-header.csv,C:\td\c_region_name.csv --nodes=customer=C:\td\customer-header.csv,C:\td\customer.csv --nodes=customer_address=C:\td\customer_address-header.csv,C:\td\customer_address.csv --nodes=customer_name=C:\td\customer_name-header.csv,C:\td\customer_name.csv --nodes=customer_phone=C:\td\customer_phone-header.csv,C:\td\customer_phone.csv --nodes=s_city=C:\td\s_city-header.csv,C:\td\s_city.csv --nodes=s_city_name=C:\td\s_city_name-header.csv,C:\td\s_city_name.csv --nodes=s_nation=C:\td\s_nation-header.csv,C:\td\s_nation.csv --nodes=s_nation_name=C:\td\s_nation_name-header.csv,C:\td\s_nation_name.csv --nodes=s_region=C:\td\s_region-header.csv,C:\td\s_region.csv --nodes=s_region_name=C:\td\s_region_name-header.csv,C:\td\s_region_name.csv --nodes=supplier=C:\td\supplier-header.csv,C:\td\supplier.csv --nodes=supplier_address=C:\td\supplier_address-header.csv,C:\td\supplier_address.csv --nodes=supplier_name=C:\td\supplier_name-header.csv,C:\td\supplier_name.csv --nodes=supplier_phone=C:\td\supplier_phone-header.csv,C:\td\supplier_phone.csv --nodes=date=C:\td\date-header.csv,C:\td\date.csv --relationships=order_part=C:\td\order_part-header.csv,C:\td\order_part.csv --relationships=order_customer=C:\td\order_customer-header.csv,C:\td\order_customer.csv --relationships=order_supplier=C:\td\order_supplier-header.csv,C:\td\order_supplier.csv --relationships=order_date=C:\td\order_date-header.csv,C:\td\order_date.csv --relationships=p_brand_name=C:\td\p_brand_to_name-header.csv,C:\td\p_brand_to_name.csv --relationships=p_category_name=C:\td\p_category_to_name-header.csv,C:\td\p_category_to_name.csv --relationships=p_mfgr_name=C:\td\p_mfgr_to_name-header.csv,C:\td\p_mfgr_to_name.csv --relationships=p_type_name=C:\td\p_type_to_name-header.csv,C:\td\p_type_to_name.csv --relationships=part_brand=C:\td\part_to_brand-header.csv,C:\td\part_to_brand.csv --relationships=part_category=C:\td\part_to_category-header.csv,C:\td\part_to_category.csv --relationships=part_name=C:\td\part_to_name-header.csv,C:\td\part_to_name.csv --relationships=part_size=C:\td\part_to_size-header.csv,C:\td\part_to_size.csv --relationships=part_type=C:\td\part_to_type-header.csv,C:\td\part_to_type.csv --relationships=c_city_name=C:\td\c_city_to_name-header.csv,C:\td\c_city_to_name.csv --relationships=c_mktsegment_name=C:\td\c_mktsegment_to_name-header.csv,C:\td\c_mktsegment_to_name.csv --relationships=c_nation_name=C:\td\c_nation_to_name-header.csv,C:\td\c_nation_to_name.csv --relationships=c_region_name=C:\td\c_region_to_name-header.csv,C:\td\c_region_to_name.csv --relationships=customer_address=C:\td\customer_to_address-header.csv,C:\td\customer_to_address.csv --relationships=customer_city=C:\td\customer_to_city-header.csv,C:\td\customer_to_city.csv --relationships=customer_mktsegment=C:\td\customer_to_mktsegment-header.csv,C:\td\customer_to_mktsegment.csv --relationships=customer_name=C:\td\customer_to_name-header.csv,C:\td\customer_to_name.csv --relationships=customer_nation=C:\td\customer_to_nation-header.csv,C:\td\customer_to_nation.csv --relationships=customer_phone=C:\td\customer_to_phone-header.csv,C:\td\customer_to_phone.csv --relationships=customer_region=C:\td\customer_to_region-header.csv,C:\td\customer_to_region.csv --relationships=s_city_name=C:\td\s_city_to_name-header.csv,C:\td\s_city_to_name.csv --relationships=s_nation_name=C:\td\s_nation_to_name-header.csv,C:\td\s_nation_to_name.csv --relationships=s_region_name=C:\td\s_region_to_name-header.csv,C:\td\s_region_to_name.csv --relationships=supplier_address=C:\td\supplier_to_address-header.csv,C:\td\supplier_to_address.csv --relationships=supplier_city=C:\td\supplier_to_city-header.csv,C:\td\supplier_to_city.csv --relationships=supplier_name=C:\td\supplier_to_name-header.csv,C:\td\supplier_to_name.csv --relationships=supplier_nation=C:\td\supplier_to_nation-header.csv,C:\td\supplier_to_nation.csv --relationships=supplier_phone=C:\td\supplier_to_phone-header.csv,C:\td\supplier_to_phone.csv --relationships=supplier_region=C:\td\supplier_to_region-header.csv,C:\td\supplier_to_region.csv
```

### 1.3 Create database
After the database is set up using the neo4j-admin command, it must be created manually on :

#### Command line
CREATE DATABASE GTDW (on system database)

#### Neo4j desktop 
on the <<+Create database>> tab with the name GTDW.

### 1.4 Create indexes
This is not required, but you can create the following indexes to optimise query execution :

create index index_part_category for (p:part) on (p.P_CATEGORY);

create index index_part_brand for (p:part) on (p.P_BRAND);

create index idxex_customer_region for (c:customer) on (c.C_REGION);

create index idxex_customer_nation for (c:customer) on (c.C_NATION);

create index idxex_customer_city for (c:customer) on (c.C_CITY);

### 1.5 Generate changes in relationships
Put customer_change, supplier_change and part_change in the neo4j Import folder and then apply these commands : 
#### 1.5.1 Changes in Customer dimension
```cypher
LOAD CSV WITH HEADERS FROM 'file:///customer_change.csv' AS row
WITH row.CUSTKEY AS CUSTKEY, row.C_CITY_ID AS C_CITY_ID, row.C_NATION_ID AS C_NATION_ID, row.C_REGION_ID AS C_REGION_ID, date(row.From_Date) AS From_Date,date(row.To_Date) AS To_Date
MATCH (c:customer {CUSTKEY: CUSTKEY})
MATCH (ct:c_city {C_CITY_ID: C_CITY_ID})
MATCH (cn:c_nation {C_NATION_ID: C_NATION_ID})
MATCH (cr:c_region {C_REGION_ID: C_REGION_ID})
MATCH (c)-[rel:customer_city {STATUS :"LAST"}]->(:c_city)
MATCH (c)-[rel3:customer_nation {STATUS :"LAST"}]->(:c_nation)
MATCH (c)-[rel5:customer_region {STATUS :"LAST"}]->(:c_region)
CREATE (c)-[rel2:customer_city]->(ct)
CREATE (c)-[rel4:customer_nation]->(cn)
CREATE (c)-[rel6:customer_region]->(cr)
set rel.To_Date = From_Date,rel.STATUS="OLD"
set rel2.From_Date = From_Date,rel2.To_Date = To_Date,rel2.STATUS="LAST"
set rel3.To_Date = From_Date,rel3.STATUS="OLD"
set rel4.From_Date = From_Date,rel4.To_Date = To_Date,rel4.STATUS="LAST"
set rel5.To_Date = From_Date,rel5.STATUS="OLD"
set rel6.From_Date = From_Date,rel6.To_Date = To_Date,rel6.STATUS="LAST"
RETURN count(rel2),count(rel4),count(rel6);
```
#### 1.5.2 Changes in Supplier dimension
```cypher
LOAD CSV WITH HEADERS FROM 'file:///supplier_change.csv' AS row
WITH row.SUPPKEY AS SUPPKEY, row.S_CITY_ID AS S_CITY_ID, row.S_NATION_ID AS S_NATION_ID, row.S_REGION_ID AS S_REGION_ID, date(row.From_Date) AS From_Date,date(row.To_Date) AS To_Date
MATCH (s:supplier {SUPPKEY: SUPPKEY})
MATCH (sc:s_city {S_CITY_ID: S_CITY_ID})
MATCH (sn:s_nation {S_NATION_ID: S_NATION_ID})
MATCH (sr:s_region {S_REGION_ID: S_REGION_ID})
MATCH (s)-[rel:supplier_city {STATUS :"LAST"}]->(:s_city)
MATCH (s)-[rel3:supplier_nation {STATUS :"LAST"}]->(:s_nation)
MATCH (s)-[rel5:supplier_region {STATUS :"LAST"}]->(:s_region)
CREATE (s)-[rel2:supplier_city]->(sc)
CREATE (s)-[rel4:supplier_nation]->(sn)
CREATE (s)-[rel6:supplier_region]->(sr)
set rel.To_Date = From_Date,rel.STATUS="OLD"
set rel2.From_Date = From_Date,rel2.To_Date = To_Date,rel2.STATUS="LAST"
set rel3.To_Date = From_Date,rel3.STATUS="OLD"
set rel4.From_Date = From_Date,rel4.To_Date = To_Date,rel4.STATUS="LAST"
set rel5.To_Date = From_Date,rel5.STATUS="OLD"
set rel6.From_Date = From_Date,rel6.To_Date = To_Date,rel6.STATUS="LAST"
RETURN count(rel2),count(rel4),count(rel6);
```
#### 1.5.3 Changes in Size attribute
```cypher
LOAD CSV WITH HEADERS FROM 'file:///part_change.csv' AS row
WITH row.PARTKEY AS PARTKEY, row.P_SIZE_ID AS P_SIZE_ID, date(row.From_Date) AS From_Date,date(row.To_Date) AS To_Date
MATCH (p:part {PARTKEY: PARTKEY})
MATCH (ps:part_size {P_SIZE_ID: P_SIZE_ID})
MATCH (p)-[rel:part_size {STATUS :"LAST"}]->(:part_size)
CREATE (p)-[rel2:part_size]->(ps)
set rel.To_Date = From_Date,rel.STATUS="OLD"
set rel2.From_Date = From_Date,rel2.To_Date = To_Date,rel2.STATUS="LAST"
RETURN count(rel),count(rel2);
```

## 2. Queries

Here are the 13 SSB queries using the Cypher Language Request (CLR) in temporal format. They can be applied on Cypher-shell or in Neo4j Browser. queries has been conditioned according to the FD and TD parameters using the clause **where FD <=Valid_Time<TD. Note that in the CRL, the clause : **return 𝑣𝑎𝑙𝑢𝑒 1,.., 𝑣𝑎𝑙𝑢𝑒 N , aggregate_function(attribute)** allows for grouping aggregation by 𝑣𝑎𝑙𝑢𝑒 1... 𝑣𝑎𝑙𝑢𝑒 N. 

Example 2 :
```cypher
OPTIONAL MATCH (c:cc_city)<-[:c_c_name]-(:c_city)<-[r1:customer_city]-(:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date), (s:sc_city) <-[:s_c_name] - (:s_city) <-[r2:supplier_city] - (s:supplier) <- [:order_supplier]-(l)
WHERE r1.From_Date <= l.valid_date < r1.To_Date 
AND r2.From_Date <= l.valid_date < r2.To_Date 
AND 1992<= d.D_YEAR <=1997
AND (c.c_city = "united ki1" OR c.c_city = "united ki5")
AND (s.s_city = "united ki1" OR s.s_city = "united ki5")
RETURN c.c_city, s.s_city, d.d_year, SUM(l.lo_revenue) AS revenu
ORDER BY d.d_year ASC, revenu DESC
```

Here is a list of SSB queries using Cypher Language Request. They can be applied on Cypher-shell or in Neo4j Browser.

##### Q1.1

```cypher
optional match (d:date{D_YEAR:1993})<-[r:order_date]-(l:lineorder)
where 1<= l.LO_DISCOUNT <=3
and l.LO_QUANTITY < 25
return sum(l.LO_REVENUE);
```

##### Q1.2

```cypher
optional match (d:date{D_YEARMONTHNUM:199401})<-[r:order_date]-(l:lineorder)
where 4<= l.LO_DISCOUNT <=6
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q1.3

```cypher
optional match (d:date{D_YEAR:1994, D_WEEKNUMINYEAR:6})<-[r:order_date]-(l:lineorder)
where 5<= l.LO_DISCOUNT <=7
and 26<= l.LO_QUANTITY <= 35
return sum(l.LO_REVENUE);
```

##### Q2.1

```cypher
profile optional match (pbn:p_brand_name)<-[:p_brand_name]-(pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[r:supplier_region]->(sr:s_region)-[:s_region_name]->(srn:s_region_name),(d:date)<-[:order_date]-(l)
where r.From_Date <= l.ORDERDATE < r.To_Date  
and srn.S_REGION_NAME = "AMERICA"
return sum(l.LO_REVENUE),d.D_YEAR,pbn.P_BRAND_NAME
ORDER BY d.D_YEAR,pbn.P_BRAND_NAME;
```

##### Q2.2

```cypher
optional match (pbn:p_brand_name)<-[:p_brand_name]-(pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[r:supplier_region]->(sr:s_region)-[:s_region_name]->(srn:s_region_name),(d:date)<-[:order_date]-(l)
where r.From_Date <= l.ORDERDATE < r.To_Date
and (pbn.P_BRAND_NAME >= "MFGR#2221" and pbn.P_BRAND_NAME <= "MFGR#2228")  
and srn.S_REGION_NAME = "ASIA"
return sum(l.LO_REVENUE),d.D_YEAR,pbn.P_BRAND_NAME
ORDER BY d.D_YEAR,pbn.P_BRAND_NAME;
```

##### Q2.3

```cypher
optional match (pbn:p_brand_name)<-[:p_brand_name]-(pb:p_brand)<-[:part_brand]-(p:part)<-[:order_part]-(l:lineorder)-[:order_supplier]->(s:supplier)-[r:supplier_region]->(sr:s_region)-[:s_region_name]->(srn:s_region_name),(d:date)<-[:order_date]-(l)
where r.From_Date <= l.ORDERDATE < r.To_Date
and pbn.P_BRAND_NAME = "MFGR#2239"  
and srn.S_REGION_NAME = "EUROPE"
return sum(l.LO_REVENUE),d.D_YEAR,pbn.P_BRAND_NAME
ORDER BY d.D_YEAR,pbn.P_BRAND_NAME;
```

##### Q3.1

```cypher
optional match (crn:c_region_name)<-[:c_region_name]-(c_region)<-[r1:customer_region]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(srn:s_region_name)<-[:s_region_name]-(sr:s_region)<-[r2:supplier_region]-(s:supplier)<-[:order_supplier]-(l),(cnn:c_nation_name)<-[:c_nation_name]-(c_nation)<-[r3:customer_nation]-(c),(snn:s_nation_name)<-[:s_nation_name]-(sn:s_nation)<-[r4:supplier_nation]-(s)
where r1.From_Date <= l.ORDERDATE < r1.To_Date 
and r2.From_Date <= l.ORDERDATE < r2.To_Date
and 1992<= d.D_YEAR <=1997
and crn.C_REGION_NAME = "ASIA"
and srn.S_REGION_NAME = "ASIA" 
return cnn.C_NATION_NAME, snn.S_NATION_NAME, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```


##### Q3.2

```cypher
optional match (cnn:c_nation_name)<-[:c_nation_name]-(c_nation)<-[r1:customer_nation]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_date]->(d:date),(snn:s_nation_name)<-[:s_nation_name]-(sn:s_nation)<-[r2:supplier_nation]-(s:supplier)<-[:order_supplier]-(l),(ccn:c_city_name)<-[:c_city_name]-(c_city)<-[r3:customer_city]-(c),(scn:s_city_name)<-[:s_city_name]-(sc:s_city)<-[r4:supplier_city]-(s)
where r1.From_Date <= l.ORDERDATE < r1.To_Date 
and r2.From_Date <= l.ORDERDATE < r2.To_Date
and 1992<= d.D_YEAR <=1997
and cnn.C_NATION_NAME = "UNITED STATES"
and snn.S_NATION_NAME = "UNITED STATES" 
return ccn.C_CITY_NAME, scn.S_CITY_NAME, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q3.3

```cypher
optional match (ccn:c_city_name)<-[:c_city_name]-(c_city)<-[r1:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_supplier]->(s:supplier)-[r2:supplier_city]->(sc:s_city)-[:s_city_name]->(scn:s_city_name),(l)-[:order_date]->(d:date)
where r1.From_Date <= l.ORDERDATE < r1.To_Date 
and r2.From_Date <= l.ORDERDATE < r2.To_Date
and 1992<= d.D_YEAR <=1997
and (ccn.C_CITY_NAME = "UNITED KI1" or ccn.C_CITY_NAME = "UNITED KI5") 
and (scn.S_CITY_NAME = "UNITED KI1" or scn.S_CITY_NAME = "UNITED KI5") 
return ccn.C_CITY_NAME, scn.S_CITY_NAME, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q3.4

```cypher
optional match (ccn:c_city_name)<-[:c_city_name]-(c_city)<-[r1:customer_city]-(c:customer)<-[:order_customer]-(l:lineorder)-[:order_supplier]->(s:supplier)-[r2:supplier_city]->(sc:s_city)-[:s_city_name]->(scn:s_city_name),(l)-[:order_date]->(d:date)
where r1.From_Date <= l.ORDERDATE < r1.To_Date 
and r2.From_Date <= l.ORDERDATE < r2.To_Date
and d.D_YEARMONTH = "Dec1997"
and (ccn.C_CITY_NAME = "UNITED KI1" or ccn.C_CITY_NAME = "UNITED KI5") 
and (scn.S_CITY_NAME = "UNITED KI1" or scn.S_CITY_NAME = "UNITED KI5") 
return ccn.C_CITY_NAME, scn.S_CITY_NAME, d.D_YEAR, sum(l.LO_REVENUE) as revenu
ORDER BY d.D_YEAR ASC, revenu DESC;
```

##### Q4.1

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where c.C_REGION = "AMERICA" 
and (p.P_MFGR = "MFGR#1" or p.P_MFGR = "MFGR#2")
and s.S_REGION = "AMERICA"
return d.D_YEAR, c.C_NATION, (sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, c.C_NATION;
```

##### Q4.2

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where c.C_REGION starts with "AMERICA"
and (d.D_YEAR = 1997 or d.D_YEAR = 1998)
and (p.P_MFGR = "MFGR#1" or p.P_MFGR = "MFGR#2")
and s.S_REGION = "AMERICA"
return d.D_YEAR, s.S_NATION, p.P_CATEGORY,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as profit
ORDER BY d.D_YEAR, s.S_NATION, p.P_CATEGORY;
```

##### Q4.3

```cypher
optional match (c:customer)<-[:order_customer]-(l:lineorder)-[:order_part]->(p:part),(d:date)<-[:order_date]-(l)-[:order_supplier]->(s:supplier)
where (d.D_YEAR = 1997 or d.D_YEAR = 1998)
and c.C_REGION = "AMERICA"
and p.P_CATEGORY = "MFGR#14"
and s.S_NATION = "UNITED STATES"
return d.D_YEAR, s.S_CITY, p.P_BRAND,(sum(l.LO_REVENUE) - sum(l.LO_SUPPLYCOST)) as supplycost
ORDER BY d.D_YEAR, s.S_CITY, p.P_BRAND;
```

