# OCPP 2.0.1: Device model initialization and inserting of config values

If there is no custom database used for the device model, and 'initialize_device_model' is set to true in the 
constructor of ChargePoint, the device model will be created or updated when ChargePoint is created. This document will
give more information about the files you need and what will be updated when the 'initialize_device_model' is set
to true.


## Database, component schemas and config file paths

Along with the 'initialize_device_model' flag, a few paths must be given to the constructor:
- The path of the device model migration files (normally `resources/v201/device_model_migration_files`).
- The path of the device model database.
- The path of the directory with the device model schemas. There should be two directories in it: 'standardized' and 
  'custom', both containing device model schemas.
- The path of the config file.


## Component schemas and config file

When the database is created for the first time, it will insert all components, variables, characteristics and 
attributes from the component schemas. 

It will then set all the values read from the config file. 

The config and the component schemas depend on each other. If the definition in the component schema does not match the
definition in the config, no values will be set in the device model database. So if for example there is a variable set 
in the config that is not in the component schema, the config values will not be set. 


## Update config values

Each time the ChargePoint class is instantiated, the config file is read and the values will be set to the database 
accordingly. Only the initial values will be set to the values in the config file. So if for example the CSMS changed a 
value, it will not be updated to the value from the config file.


## Update component config

To update a component, just place the correct json schema in the `component_config/custom` folder. When restarting the 
software, it will:
- Check if there are Components in the database that are not in the component config's. Those will be removed.
- Check if there are Components in the component config's that are not in the database. Those will be added.
- Check if anything has changed inside the Component (`Variable`, `Characteristics` or `Attributes`). 
  Those will be removed, changed or added to the database as well. 
  
Note: When the `evse_id` or `connector_id` of a component is changed, this is seen as the removal of a Component and 
addition of a new one. 

Note: OCPP requires EVSE and Connector numbering starting from 1 counting upwards.
