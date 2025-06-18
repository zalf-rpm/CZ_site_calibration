# CZ_site_calibration

This repository contains data and scripts to calibrate crop parameters for the MONICA model against experimental data from the Czech Republic.

## Repository structure and content

Important files are marked in __bold__.

<ins> data folder </ins>
* cz folder: holds gridded data for the CZ Republic. For the calibration, only files referring to "soil" are relevant (as we use SoilGrids as soil data). All other files will only come in once you run spatial simulations for the entire country.
* cz_latlon: is a supporting file to translate coordinates into grid cells for accessing gridded data.
* __Fertilisation_min__: defines all fertilization strategies with a Date as (dd.mm.yyy), Amount_kg_ha (nitrogen applied), and fertilizer Material.
* __Meta.csv__: defines each unique experiment with a Name, Weather (referring to the respective __weatherXX.csv__), Soil (referring to the respective soil parameters in the __Soil.csv__), Year (=year of harvest), Sowing (date as dd.mm.yyy), Harvest (date as dd.mm.yyy), Fertilisation_min (referring to the respective fertilization strategy in the __Fertlisation_min.csv__), Crop, and Lat, Long, Elevation of the experimental site.
* monica-parameters folder: holds all cultivar and species parameters. Throughout the calibration process, the underlying values will be changed.
* __Soil.csv__: lists all soil parameters at the experimental sites.
* __weatherXX.csv__: one weather file for each experimental site.

<ins> __calibratethese_ww_pheno.csv__ </ins>: is a table containing all parameters for calibration related to crop phenology. It lists the parameter name plus the lower and upper boundaries within which the respective value should lay.

<ins> __calibratethese_ww_bio.csv__ </ins>: is a table containing all parameters for calibration related to crop biomass and yield production. It lists the parameter name plus the lower and upper boundaries within which the respective value should lay.

<ins> __crop_sim_site_MAP_WW.csv__ </ins>: defines which experiments to consider for the calibration.

<ins> crop.json </ins>: lists the path to species and cultivar parameters for each crop, and generic sowing and harvest working steps where the dates are then replaced with actual sowing and harvest dates from the __Meta.csv__.

<ins> Measurements.csv </ins>: lists all measured outcomes for each experiment. These values form the basis for the spotpy algorithm to compare simulations against.

<ins> Monica_io3.py </ins> and <ins> monica_run_lib.py </ins>: scripts to make MONICA run

<ins> run_a_few_monicas.cmd </ins>: starts 5 MONICA models at a time to parallelize the calibration process. Make sure you define the correct paths.

<ins> run_consumer.py </ins> and <ins> run_producer.py </ins>: only needed to run the spatial simulations for CZ, once the calibration is finished (both scripts still need updates).

<ins> __sampler_MONICA.py__ </ins>: is the equivalent to the consumer, but for spotpy. Here you have to define which experiments to consider (__crop_sim_site_MAP_WW.csv__), which parameters to calibrate (__calibratethese_ww_xxx.csv__), the Measurements.csv, and the number of repetitions.

<ins> __spotpy_setup_MONICA.py__ </ins>: is the equivalent to a producer. First, the [spotpy algorith](https://spotpy.readthedocs.io/en/latest/) (randomly) selects one value within the value range for each of the parameters (as defined in the __calibratethese_ww_xxx.csv__). It then runs a MONICA simulation for each of the selected experiments (as defined in the __crop_sim_site_MAP_WW.csv__) based on the experimental setup from the __Meta.csv__ and the selected parameter value (-s, in case multiple parameters are calibrated simultaneously). It then compares the simulated outcomes to the measured ones from the Measurements.csv and calculates RMSE as error measure between simulations and measurements. Spotpy repeats this process ( 1) selecting parameter values, 2) simulating experiments, 3) comparing simulation vs. measurement) until RMSE becomes sufficiently small and finally outputs the parameter values that correspond to the smallest RMSE. Those are the calibrated parameters that can either be used for continuing the calibration process or eventually, when all parameters are calibrated, to run spatial simulations for the CZ republic.

Make sure that you
  * choose the right directory to weather files
  * set the outcomes of interest to TRUE and the ones that should be disregarded to FALSE, and
  * comment out all calibration parameters that are not used for the respective calibration step. This needs to match the respective __calibratethese_ww_xxx.csv__.

<ins> sim.json </ins>: indicates characteristics of the weather files and under calibration events defines which simulation outcomes should be extracted for the comparison with measurements.

<ins>site.json </ins>: lists paths to soil parameters.

