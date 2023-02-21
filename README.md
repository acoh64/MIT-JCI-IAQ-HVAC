# MIT-JCI-IAQ-HVAC
This repository contains the indoor air quality (IAQ) and heating, ventilation, and air-conditioning (HVAC) time series data used in the paper "Data-driven control of airborne disease transmission and energy use in university buildings" (include citation and link)

## Description
As a part of a MIT-JCI collaboration, a large data set of IAQ-HVAC data was collected in various spaces on the MIT campus. This data was used to test models of airborne disease transmission and analyze building operating modes to balance transmission rates with energy consumption.

## Creators
- Michael Risbeck

  - https://orcid.org/0000-0003-0889-200X

  - Senior Artificial Intelligence Scientist

  - Johnson Controls

- Alexander E. Cohen

  - https://scholar.google.com/citations?user=89drxM4AAAAJ&hl=en&oi=ao

  - Graduate Student, Dunkel and Bazant Groups (Physical Applied Math)

  - Massachusetts Institute of Technology

- Jon D. Douglas

  - Director of Healthy Buildings Services and Solutions, ASHRAE Member

  - Johnson Controls

- Young M. Lee

  - Distinguished Fellow and Senior Director of Artificial Intelligence Product Development, ASHRAE Member

  - Johnson Controls

- Martin Z. Bazant

  - https://www.mit.edu/~bazant/

  - E. G. Roos (1944) Professor of Department of Chemical Engineering and Professor of Applied Mathematics

  - Massachusetts Institute of Technology

## Recommended Citation
This will be updated once the manuscript is accepted for publication.

## Dates
The data set was collected during April 2022.

## Data Overview
The full dataset consists of timeseries data for each monitored room, one common timeseries weather file, and a table of fixed parameters for the rooms.
Timeseries data files are in Parquet format, while the table of parameters is a single CSV file.

Each set of timeseries data comes in both a "raw" and "clean" version (filenames `*_raw.parquet` and `*_clean.parquet` respectively). 
The raw data contains values exactly as collected, including possible irregular sample rate and gaps when data collection was unavailable. 
The clean data is the result after interpolation and imputation to remove clear outliers and fill in missing values.

Timeseries data files for each room (filenames `<room>_*.parquet`, where `<room>` is the room name with spaces removed) contain the following columns:
- CO₂ Concentration (ppm)

- Occupancy (#)

- Supply Air Flow (cfm)

- Supply Air Temperature (°F)

- Room Relative Humidity (%)

- Room Temperature (°F)

- TVOC Concentration (ppb)

- PM10 Concentration (µg/m³)

- PM2.5 Concentration (µg/m³)

 Note that "TVOC" stands for "total volatile organic compounds", while "PM" is "particulate matter".

The weather data files (filenames `Weather_*.parquet`) contain the following
columns:
- Outdoor Air Relative Humidity (%)
- Outdoor Air Temperature (°F)

All timeseries files contain a datetime index giving local time for each row.

The room parameter file `room_parameters.csv` contains the following columns:
- Name

- Area (ft²)

- Ceiling Height (ft)

- Design Occupancy (#)

- Assumed Peak Occupancy (#)

- Mean Ventilation (ACH)

- ASHRAE Minimum Ventilation (ACH)

- Assumed Maximum Ventilation (ACH)

- In-Zone Filtration (ACH)

The "Name" column in this table correspond to the names in the timeseries data files.

### Timeseries Room Data Details
Timeseries room data was collected by a mix of in-room Kaiterra sensors, measurements taken by the BMS, and manually counted or imputed occupancy counts. 
Within each room dataset, the following columns were collected by the deployed Kaiterra sensors:
- CO₂ Concentration (ppm)

- Room Relative Humidity (%)

- Room Temperature (°F)

- TVOC Concentration (ppb)

- PM10 Concentration (µg/m³)

- PM2.5 Concentration (µg/m³)

In rooms where multiple sensors are placed, these values represent an average of the measurements.
Gaps are filled by linear interpolation, with the exception of CO₂ Concentration in which large gaps were repaired with exponential decays to avoid implying significant overnight occupancy. 
The CO₂ Concentration also undergoes a "recalibration" procedure so that minimum values are set to 400 ppm.
(Note that the sensors themselves contain similar logic, so this procedure generally does not change values significantly.
However, it was applied to remove some periods where measured values were near 300 ppm, which is unrealistically low.)
Outlier removal was not otherwise performed for these data streams.

Columns collected by the BMS are as follows:
- Supply Air Flow (cfm)

- Supply Air Temperature (°F)

Note that all monitored rooms were supplied by 100% outdoor air, so the "Supply Air Flow" column gives the provided outdoor-air ventilation.
Unfortunately, BMS data was not available for the final week of the study, so those periods have been imputed with constant values, as will be evident in the clean data files.
Outliers from the flow measurements were removed during data cleaning. 
For rooms without flow measurements, values were set to the estimated ventilation rates as described in the paper.
(This procedure was also applied for Office, as the measured flow includes only part of the total supply flow to the room.)

Finally, the "Occupancy" column in the clean data files are as calculated by the occupancy-estimation procedure described in the paper. 
We provide these values for convenience and note that alternative occupancy-estimation procedures could be applied using the other data streams.
In the raw data files for Classroom 1 and Office, there are also periods where occupancy was manually counted.
For other spaces, the raw occupancy data is completely empty.

### Timeseries Weather Data Details
To collect weather data, two QuantAQ sensors were deployed on roofs of monitored buildings.
Unfortunately, these sensors were placed in areas receiving significant direct sunlight, which led to unrealistically high temperature measurements (upwards of 90 °F many afternoons).
Thus, we have instead opted to use data collected from a nearby weather station.
Values in the raw file are the hourly values reported from the weather station, while the values in the clean file are linearly interpolated to match the sample rate of the other timeseries data.

### Room Parameter Details
During sensor installation, "Ceiling Height" for each room was measured using a laser distance meter.
For spaces with variable height, the value is a rough average.
"Design Occupancy" was estimated by counting seats in the room.
"Area" was measured from architectural drawings provided by the facilities team.
"Assumed Peak Occupancy" was taken as a fraction of "Design Occupancy" based on observed room utilization.
"In-Zone Filtration" was calculated from nameplate capacity of installed in-zone filtration devices (present only in Classroom 3).
"Mean Ventilation" is taken as the mean measured value in room with BMS flow measurements and set to the estimated value otherwise.
"ASHRAE Minimum Ventilation" was calculated from ASHRAE Standard 62.1-2019 based on space usage.
"Assumed Maximum Ventilation" was generally estimated based on expected design heat loads, with some manual adjustments to match measured data.

## Jupyter Notebook Overview
For convenience, we have provided a Jupyter notebook which shows how to visualize different variables in the data set.

## Keywords
COVID-19, airborne transmission, IAQ, HVAC, healthy buildings, energy consumption

## Acknowledgements
We thank Ye Li from MIT Libraries for helping set up this data repository.
