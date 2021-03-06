# Sahale [![Build Status](https://travis-ci.org/etsy/Sahale.svg)](https://travis-ci.org/etsy/Sahale)

A tool to record and visualize metrics captured from Cascading (Scalding) workflows at runtime.

Designed to target the pain points of analysts and end users of Cascading, Sahale provides insight into a workflow's runtime resource usage and makes job debugging and locating relevant Hadoop logs easy. The tool reveals optimization opportunities by exposing inefficient MapReduce jobs in a larger workflow, and enables users to track the execution history of their workflows.

Sahale has been verified to work with several Cascading DSLs, but the example projects and several features are tailored to Twitter's [Scalding DSL](https://github.com/twitter/scalding), which is the DSL we use at Etsy. Pull requests for better support for other flavors of Cascading are welcome!

## Installation

There are two components to Sahale, a `flowtracker` JAR that contains the code that tracks running jobs and a Node.JS dashboard for visualizing the tracked jobs.

### Flowtracker

The `flowtracker` JAR is published to Maven Central for easy inclusion in your projects:

```xml
<dependency>
  <groupId>com.etsy.sahale</groupId>
  <artifactId>flowtracker_2.10</artifactId>
  <version>1.0.1</version>
</dependency>
```

#### Configuration

You will need add some configuration for the flow tracker.  Create the file `src/main/resources/flow-tracker.properties` in your project with the following contents:

```
host=http://host.where.nodejs.server.is.running
port=5735
```

The `host` setting will be the hostname of the server where you install the dashboard (see below)

You can also add the setting `sahale.flow.selected.configs` in this file.  This is a comma-delimited list of Hadoop configuration properties that will be tracked along with the other job information.

If this configuration step is not performed job tracking will fail.

### Dashboard

#### Prerequistes

1. A MySQL instance that Sahale can use.

2. A server with Node.JS and `npm` installed.

#### Installation

1. Set up the MySQL database.  Follow the instructions in [`create_db_tables.sql`](https://github.com/etsy/Sahale/blob/master/src/main/sql/create_db_tables.sql) to do this.

2. Clone the Sahale repo onto the server where you would like to run the dashboard.

3. Modify [`db-config.json`](https://github.com/etsy/Sahale/blob/master/db-config.json) to point to your database.

4. Run `npm install` from the root directory of the Sahale repo checkout.

Now you can run `node app` to start the Sahale dashboard.  It will be running on port 5735, so go to `<hostname>:5735` in your browser to see the dashboard.

## Upgrading

Sahale server (NodeJS app) and FlowTracker (Scala client jar) must always maintain parity between clients and server versions. An upgrade deployment must coordinate the distribution of the new client jar and restart of the updated server. In rare cases (tagged in the Git repo) Sahale will make breaking changes that will require addition steps. The two notable cases are listed below:

### Upgrade to 1.0.0

There were no data model or other incompatibility changes, but the `flowtracker` JAR is now being published to Maven Central.  It is no longer required to manually build this JAR; see the above instructions for its use and configuration.

### Upgrade from 0.7 to 0.8 

The data model has changed. Please recreate your backing MySQL tables using the script in `src/main/sql` before using the new client jar or restarting the NodeJS app. The old tables and data will remain, the new tables will be suffixed with `_new` unless you opt to alias them. No additional change is required.
 
### Upgrade from 0.5 to 0.6 

Mark incompatible changes between older and newer versions of Scala/Scalding. If your org still uses older versions of Scala/Scalding, please see [this](https://github.com/etsy/Sahale/commit/238794f33ba17326a156c396f3dc1dede2b0c743) commit. All other changes and feature upgrades in the 0.6 line will work as expected with this commit reverted and your own choice of versions applied to the `pom.xml`.

## The Name

Sahale was handmade at [Etsy.com](http://www.etsy.com) and is named for [Sahale Mountain](http://en.wikipedia.org/wiki/Sahale_Mountain), which is a wonderful vantage point from which to view the Cascades

