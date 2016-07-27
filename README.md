# cpswt-db 

Covers the design of the database and interface format
between all of the subsystems of CPSWT

In this repository is the design of the database entries for the
federates, their versions, projects, organizations, users, builds,
experiments, interactions, parameters, coas, executions and docker
images. These database entries provide the storage and query formats
used by the modeling subsystem (WebGME), build subsystem, experiemnt
configuration and management subsystem, execution subsystem, and
vulcan project management and collaboration subsystem.

The database will be surrounded by a proxy which restricts access to
the repository and proivdes convenience methods for creating and
sharing data within and between projects. The interface methods for
this proxy are also defined in this repository.

## Database Design

The design of the database can be found in
[example](./src/example.js), which has been copied below for
ease of reading / reference.

The main entities that exist in the database are:
  * Projects
  * Users
  * Organizations
  * Federates
  * Interactions
  * Parameters
  * COAs
  * Builds
  * Experiments
  * Executions
  * Docker Images
  
Every object in the database is referenced by a unique ID (GUID). This
GUID is the primary method by which an object is indexed for look up
and storage. No subsystem but the databased and its proxy are allowed
to create GUIDs.

For the relevant entities, the versions of the entity are tracked and
stored as separate objects.  Other objects which are compatible with
only specific versions of those entities can specify their references
using version matching of the form `^2.X.X`, to specify a range within
the major version `2`, or can reference a specific version. Note that
not all entities within the database require versioning or multiple
versions. The versions of an object are all stored under the same
GUID, to maintain consistency; the versions differ only in their
actual data and the verion numbers.

```javascript
[
    "__OBJECTS__": {
	// this is where the actual objects would be stored, the
	// subsequent lists at the top level would only contain GUID
	// references.  This is the main object storage used by code
	// accessing object members.  object members which point to
	// other objects point using the GUID and VERSION, which they
	// can look up in this dictionary.  The other lists that exist
	// below are merely for querying the currently available
	// objects (by GUID) of a certain type
	"<GUID>": {
	    "VERSION": {
		"ACTUAL OBJECT HERE"
		// for clarity, the object structures are shown below
		// in their respective lists instead of here
	    },
	},
    },
    // These top level lists below show the structure, but again would
    // only contain GUID/VERSION references
    "projects": [
	// which projects are currently part of this deployment?
	// incomplete
	{
	    "name": ""
	    "GUID": "",
	    "roots": [
		// these are the root federates visible for the
		// current project, they are provided to the user for
		// selecting experiments, configuring, or navigating
		// the project.
		{
		    "GUID": "",
		    "VERSION": ""
		},
	    ],
	},
    ],
    "Users": [
	// Which users are currently part of this deployment?
	// incomplete
	{
	    "GUID": "",
	    "name": "",
	    "organizations": [
		{
		    "name": "",
		    "roles": [
		    ],
		    "privileges": [
		    ],
		    "authentication": [
		    ],
		},
	    ],
	},
    ],
    "organizations": [
	// which organizations are currently part of this deployment?
	// incomplete
	{
	    "name": "",
	    "GUID": "",
	    "projects": [
	    ],
	},
    ],
    "Federates": [
	{
	    // Federates are structured differently than in previous
	    // C2WT; There is no such thing as a *Federation* anymore,
	    // since we want to support heirarchical federations and
	    // federations as federates, we have removed federations
	    // as first class concepts and restructured federates to
	    // be a tree structure which can be arbitrarily deep
	    // (i.e. federates can be nested arbitrarily within other
	    // federates.

	    // To support this type of collection, we must still be
	    // able to quickly figure out which federates are
	    // deployable, what it means to deploy them, and how to
	    // properly separate federates

	    // To do this, we have added new attributes to federates
	    // which describe how to deploy it (and whether or not it
	    // is deployable) and we provide a list to what are the
	    // roots of various federate trees (which would have
	    // previously been called federations).  Note that there
	    // may be sub-federations which are not directly
	    // deployable, it is up to us to decide if we want this to
	    // be the case or if all sub-federations are deployable
	    // and therefore should exist in the top-level list
	    "name": "Community1DemandController",
	    "documentation": "",
	    "Docker Image": "",
	    "repository url": "http://github.com/finger563/gridlabd-federates",
	    "type": {
		// this is a classic style federate completely
		// contained in the current heirarchy
		"directly deployable",
		// This federate exists as a proxy for one or more
		// federates
		"deploy by proxy",
		// children may be deployable
		"not directly deployable"
	    },
	    "federates": [
		// reference objects to federates specifying which
		// version of which federate exists as a child to the
		// current federate.
		{
		    "GUID": "",
		    "VERSIONS": "^2.0.0"
		}
	    ],
	    "interactions": [
		// reference objects to interactions specifying which
		// version of which interaction this federate uses and
		// whether it is an input or output
		{
		    "guid": "",
		    "version": "",
		    "direction": "",
		},
	    ],
	    "parameters": [
		{
		    // giving parameters GUIDS is probably not a good
		    // idea; they might have to be entirely local
		    "guid": "",
		    "version": "",
		    "direction": "",
		},
	    ],
	    "coas": [
		// Because federates are heirarchical, all federates
		// may have coas associated with them now; but we can
		// further restrict this in the UI if we choose
		{
		    "GUID": "",
		    "VERSIONS": "^2.0.0"
		}
	    ],
	    "configuration": {
		// need type info here for the configuration data
		"step size": "",
		"files": [
		    "Community1DemandController.config"
		]
	    },
	    "last build": {
		// Reference to a build information object for this
		// federate.  Not all federates can be built.
		{
		    "GUID": "",
		}
	    },
	    "experiments": [
		// Reference to experiments to which this federate
		// belongs
		{
		    "GUID": "",
		    "VERSIONS": "^2.0.0"
		}
	    ],
	},
    ],
    "coas": [
	// Structure of a COA
	{
	    "name": "",
	    "GUID": "",
	    "objects": [
		{
		    "name": "",
		    "parameters": [
		    ],
		},
	    ],
	    "parameters": [
	    ],
	},
    ],
    "Experiments": [
	// Structure of an experiment
	{
	    "name": "TESDemo2016Exp1",
	    "GUID": "",
	    "parameters": [
	    ],
	    "coas": [
	    ],
	    "federates": [
	    ],
	    "executions": [
		// Reference to execution objects for any scheduled or
		// running instance of this experiment
	    ],
	},
    ],
    "Interactions": [
	// structure of an interaction
	{
	    "name": "",
	    "GUID": "",
	    "parameters": [
	    ],
	},
    ],
    "Parameters": [
	// structure of a parameter
	{
	    "name": "",
	    "GUID": "",
	    "type": "",
	    "editable": false,
	    "default value": "",
	},
    ],
    "builds": [
	// Build information created by the build system and checked
	// by the execution engine
	{
	    "GUID": "",
	    "time": "",
	    "user": "",
	    "project": "",
	    "configuration": {
		"federate": "",
	    },
	    "status": {
	    },
	    "results": {
	    }
	},
    ],
    "executions": [
	// What are the currently running or scheduled executions?
	{
	    "name": "",
	    "GUID": "",
	    "time": "",
	    "user": "",
	    "experiment": "",
	    "configuration": [
	    ],
	    "status": {
	    },
	    "results": [
	    ],
	},
    ],
    "Docker Images": [
	// Contains the info for the docker images needed for this
	// project.  Federates may share docker image information?
	{
	    "GUID": "",
	    "name": "",
	    "repository url": "",
	    "tag": "",
	},
    ],
]
```

## Database Proxy Interface

The proxy wraps the database for interfacing with the subsystems. No
other processes are allowed to write directly to the database, all
their information must go through the proxy for conversion and
sanitization. The proxy is responsible with generation of the
GUIDs/UUIDs used to identify objects throughout all projects. The
subsystems request creation of new objects, transfer of objects, or
retrieval of objects based on GUIDs.

## Project Management Subsystem

This subsystem is responsible for creating the CPSWT projects, users,
and organizations. Additionally, it is responsible for the sharing of
resources between these projects, users, and organizations.

## Modeling Subsystem

The modeling subsystem provides the user interface for creating
federates, interactions, coas, federations, and experiments within a
vulcan CPSWT project. The modeling subsystem interfaces with the
project management subsystem and the database proxy interface to
ensure that each of the newly created model objects gets properly
represented as a new (or new version of an) object in the
database. Additionally, the modeling subsystem must provide an
interface for selecting which shared resources should be included in
the users' project. These shared resources are provided by a call into
the project management subsystem.

## Build Subsystem

The build subsystem provides the creation, management, logging, and
interfacing for any of the build jobs currently running for the CPSWT
projects in its vulcan project. The build system monitors for selct
changes (e.g. new code committed to project code repository) in the
database (though interface of the proxy) and when these change events
happen it schedules the build jobs related to the change. While the
build is running it stores and updates the status in the database
finalizing it with the final status of the bulid and the location of
the build artifacts (if any).

## Experiment Configuration Subsystem

## Execution Subsystem
