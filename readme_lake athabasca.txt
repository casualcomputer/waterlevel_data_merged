The data come from the Dahiti database. We are doing a time series forecasting for Lake Athabasca. Below are some codes for data manipulation.

/*Data manipulation codes*/ 

/*Sentinel A data*/
PROC OPTIONS OPTION = VALIDVARNAME;
RUN;

OPTIONS VALIDVARNAME = 'ANY';

data SENTINEL_A_V0_ATHABASCA(drop='Lake water level (m)'N measure_year 'Date (YYYYMMDD)'N);
format Dates YYMMDD10.;
	set WORK.SENTINELA_WATERLEVEL_V0;
	Dates = 'Date (YYYYMMDD)'N;
 	YY = year('Date (YYYYMMDD)'N);
	MM = month('Date (YYYYMMDD)'N);
	DD = day('Date (YYYYMMDD)'N);
	Water_level = 'Lake water level (m)'N;
	Satelite = 'SENTINEL_A';
	where Lake_name='Athabasca';
run;

/*Sentinel B data*/
PROC OPTIONS OPTION = VALIDVARNAME;
RUN;

OPTIONS VALIDVARNAME = 'ANY';

data SENTINEL_B_V0_ATHABASCA(drop='Lake water level (m)'N measure_year 'Date (YYYYMMDD)'N);
format Dates YYMMDD10.;
	set WORK.SENTINEL_3B_WATER_LEVEL_VERSION0;
	Dates = 'Date (YYYYMMDD)'N;
 	YY = year('Date (YYYYMMDD)'N);
	MM = month('Date (YYYYMMDD)'N);
	DD = day('Date (YYYYMMDD)'N);
	Water_level = 'Lake water level (m)'N;
	Satelite = 'SENTINEL_B';
	where Lake_name='Athabasca';
run;

/*Union Sentinel A data with Sentinel B data: lake_athabasca_sentinelAB_merged_full_data.csv*/
DATA  SENTINEL_AB_MERGED_V0_ATHABASCA;
SET SENTINEL_A_V0_ATHABASCA SENTINEL_B_V0_ATHABASCA;
RUN;

/*take the median of the waterlevel for each combo of date, and satelite, relative_orbit: Sentinel_A_B_merged_lake athabasca.csv*/
proc summary data=SENTINEL_AB_MERGED_V0_ATHABASCA median NWAY;
  var Water_level;
  class Dates Satelite Relaive_orbit;
  output out=medians (drop=_:) median=median_waterlevel;
run;
  

