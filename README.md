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
