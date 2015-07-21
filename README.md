#Service Area Toolset
##Pre Processing
Pre processing tools to get data set up for service area generation.
###1. Tie Resolver

Takes, as input, a csv of recipient ZCTAs containing ties for provider ZCTAs. Provider ZCTAs are chosen in the following manner:

1. If any of the potential provider ZCTAs match the recpient ZCTA then the matching set is chosen.

2. If any of the candicate providers are adjacent to the recipient ZCTA, the provider sharing the longest common boundary with the recpient ZCTA is chosen.

3. In the event criteria 1 is not met, the closest provider ZCTA is identified by calculating the distance between the recipient and provider centroid.

Ouput is written to a CSV in a user specified location in the same format as the input CSV.

###2. Zip to ZCTA Crosswalk
Retrieves the most recent national Zip to ZCTA crosswalk from UDS (located at http://udsmapper.org/zcta-crosswalk.cfm) and saves it in a user specified folder. The crosswalk is migrated to a temporary table in a user specified geodatabase/workspace where all the of Iowa Zip codes are found and written to a new table in the same format.

###3. Inital Dyad Table Creator
Creates a Dyad table from the initial visit datasset

The national table is deleted after processing is complete

### 4. Dyad Table ZCTA Checker
Checks that all recpient and provider ZCTAS found in the Dyad Table are also in the ZCTA shapefile that will be used for generating service areas

A warning is generated if ZCTAs in the dyad table are not found in the shapefile/feature class, as well as the number of visits/data that will not be accounted for.

###5. Dyad Table ZCTA Reconciler
Uses the crosswalk table generated from Zip to ZCTA crosswalk script to update the dyad table with the correct ZCTA assignments.

## Service Area Generation
After using the Pre Processing tools, this toolset can be used to create service areas, re create dyad tables, and aggregate service areas based on user defined criteria

### 1. Serivice Area Generator
ZCTAs and a dyad table of recipient and provider ZCTAs are required to generate base DSAs. Service areas are generated in the following method

  1. Seeds are found by identifying instances where the recipient and provider ZCTA are the same, and the dyad max = 1 (the maximum number of visits occured in between this dyad)

  2. All neighbors to the seeds are found that share the same provider ZCTA as the neighboring seed. Neighbors are assigned accordingly

  3. The remaining unassigned ZCTAs are assigned to a service area in the following manner

    1. If the dyad max occured between an unassigned ZCTA and a neighbor, or the ZCTA the neighbor is assigned to - it is assigned accordingly

    2. Otherwise, the unassigned ZCTA is assigned to the neighbor where the most visits occured

### 2. Visit Aggregator and Localization of Care Calculator
Takes ZCTAs, corresponding Service Areas, and a Dyad table to aggregate visits and calculate Localiztion of Care (LOC) for each service area

### 3. Dyad Table Creator
Uses newly created service areas to generate a new dyad table of care occuring between service areas


## Service Area Analysis
Tool for analyzing service areas created using the Service Area Generation toolset. The service areas are evaluated and those below a user specified threshold are assigned to an adjacent service area by evaluating care occuring between adjacent service areas (with assignment occuring where the sum of the care sought and recieved between an adjacent service areas is greatest).