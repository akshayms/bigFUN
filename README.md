# AsterixDB BigFUN Client

[__BigFUN__](http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=7363793 "BigFUN") is a micro-benchmark to compare Big Data management systems w.r.t their features and functionality.
Here you can find the client to run BigFUN against [AsterixDB](https://asterixdb.incubator.apache.org) for both read-only and data modification (update) workloads. You can generate the data for running BigFUN using [__SocialGen__](https://github.com/pouriapirz/socialGen "SocialGen"). 
The DDL to prepare an AsterixDB instance for running BigFUN can be found in the ['ddl'](https://github.com/pouriapirz/bigFUN/blob/master/files/ddl/ddl.aql) directory under the 'files' directory.
The following table lists the BigFUN operations. The operation-id is used in the client configuration files to create BigFUN workloads. 
The AQL templates for the queries can be found in the ['queries'](https://github.com/pouriapirz/bigFUN/tree/master/files/queries) directory under the 'files' directory.

  Operation-id | Description |
  --- | --- |
  *101* | Unique record retrieval (PK lookup) |
  *102* | Record id range scan (PK scan) |
  *103* | Temporal range scan (Non-unique attribute scan) |
  *104* | Existential quantification |
  *105* | Universal quantification |
  *106* | Global aggregation |
  *107* | Grouping & aggregation |
  *108* | Top-K |
  *109* | Spatial selection |
  *1010* | Text containment search |
  *1011* | Text similarity search |
  *1012* | Select equi-join |
  *2012* | Select indexed equi-join |
  *1013* | Select left-outer equi-join |
  *2013* | Select indexed left-outer equi-join |
  *1014* | Select join with grouping & aggregation |
  *2014* | Select indexed join with grouping & aggregation |
  *1015* | Select join with Top-K |
  *2015* | Select indexed join with Top-K |
  *1016* | Spatial join |
  *insert* | (Batch) record addition |
  *delete* | (Batch) record removal |

## Prerequisites
* A suitable *nix environment (Linux, OSX)
* JDK 1.8+
* Maven 3.1.1 or greater

## Steps
1. Check out the BigFUN project in a directory via git. Assuming that the path to the directory is $HOME/bigFUN (or any other directory based on your choice), we will refer to this directory as **BIGFUN_HOME** in the rest of this document.
2. Set BIGFUN_HOME as an environment variable on the machine you are running BigFUN from (replace the path with the directory you checked out the project into in the previous step):

  ```
  > export BIGFUN_HOME=$HOME/bigFUN
  ```
3. Go to BIGFUN_HOME and build the project's artifacts by executing the following commands:

  ```
  > cd $BIGFUN_HOME
  > mvn clean package 
  ```
Upon a successful build, a new directory named 'target' will be created under BIGFUN_HOME that contains the jar file for BigFUN with its dependencies.
4. The main configuration file for BigFUN should be created as a json file with the name 'bigfun-conf.json' under the '$BIGFUN_HOME/conf' directory. The configuration file contains the desired settings from different parameters that BigFUN needs for a successful run. There are template configuration files already available under the [conf](https://github.com/pouriapirz/bigFUN/tree/master/conf) directory for both read-only and data modification tests. You can start creating your own configuration file by modifying a template and renaming it as 'bigfun-conf.json'.
5. The query generator for read-only tests needs two other configuration files under the '$BIGFUN_HOME/files' directory:

  * _query-params.txt_: This file contains the settings for various filtering predicates in different versions of the read-only queries so that the expected selectivity of each predicate can be controlled. The values should be set according to the scale of the test data, generated by [SocialGen](https://github.com/pouriapirz/socialGen "SocialGen"). Each line in 'query-params.txt' should have the following format (a '#' character at the beginning of a line marks it as a comment line):
  
    ```
    <query id>,<version id>,<parameter-1>,<parameter-2>,...<parameter-k>
    ```
  The number of parameters for a query depends on the number of filtering predicates in the query and there is 1-1 matching between them i.e. the i-th parameter corresponds to the i-th filtering predicate in the query. For a filter on a numerical attribute, the parameter normally shows the length of that filter for a specific query version (for example in q102, this value shows the primary key scan length). For temporal attributes, this value shows the length of the time interval (in ms) for the corresponding filtering predicate. For more details on filters and query versions refer to the BigFUN [paper](http://ieeexplore.ieee.org/xpl/articleDetails.jsp?arnumber=7363793 "BigFUN").
  * _workload.txt_: This file definies a specific read-only workload which is a sequence of queries that will be run, in order, by the BigFUN client in each iteration of a read-only test. Each line of the file should have the following format (a '#' character at the beginning of a line marks it as a comment line):
    
    ```
    <query id>,<version id>
    ```
6. Once you modified and saved all the required configuration files, you can run the BigFUN benchmark by invoking the 'run-bigfun.sh' script under the '$BIGFUN_HOME/scripts' directory:

  ```
  > $BIGFUN_HOME/scripts/run-bigfun.sh
  ```
As client runs, it shows messages (for tracing its progress) on the screen and once it finishes successfully, the summary report on the test's statistics can be found in file path which is set as 'stats_file' in 'bigfun-conf.json' (if 'stats_file' is not set by the user, the client writes it into its default location under the '$BIGFUN_HOME/files/output' directory).

