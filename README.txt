**USING PYTHON 3.9.6 IS RECOMMENDED**
**INSTALL JUPYTER NOTEBOOK**
**KINDLY RE-RUN THE ENTIRE NOTEBOOK IF YOU FACE DIFFICULTIES, ALL AT ONCE UNLIKE CELL BY CELL EXECUTION, BECAUSE RUNNING A CELL TWICE AT
SOME POINT OF TIME MAY CAUSE INCONSISTENCIES**
**INSTALL JUPYTER NBCONVERT APP BY EXECUTING "sudo apt install jupyter-nbconvert"**
**BEFORE EXECUTING ANY SHELL SCRIPT, FIRST MAKE IT EXECUTABLE BY COMMAND "chmod +x <file_name>.sh"**
**FOR MORE SPECIFIC INFORMATION OF A QUESTION,PLEASE REFER THE COMMENTS IN 'QUESTION.ipynb' FILE**
**THE PATTERN OF IPYNB FILES FOR EVERY QUESTION GOES LIKE 'Q1.ipynb','Q2.ipynb' ... 'Q9.ipynb'** 

REQUIREMENTS:
You are requested to install below packages before executing scripts
	1.numpy
	2.pandas
	3.json
	4.datetime
	5.math
	
ASSUMPTIONS:
	Week is from [Sunday to Saturday].{ Overlapping weeks in QUESTION 4 is an exception }
	Cases on a particular date/day come at the end of that date/day.
	Given the data is cumulative, Then, "Cases in a week = Cases in this week's Saturday - Cases in last week's Saturday".
	Wherever, division is Not Applicable, 0 is the output.

QUESTION 1:
	'neighbor-districts-script.sh' invokes question 1.
	Dependency with other questions : No 
	Required Input files: 'neighbor-districts.json','cowin_vaccine_data_districtwise.csv'
	Data Preprossing happens
	  DID NOT MERGE DELHI BECAUSE IN BOTH NEIGHBOURS DATA AND VACCINE DATA DELHI IS PRESENT AS SUBPARTS ONLY(SO NO NEED FOR MERGING)
          DELETED THE DISTRICTS THAT ARE NOT IN VACCINE DATA BUT PRESENT IN NEIGHBOUR DATA
          CHANGED  '_district' TO '' AND '_' TO ' ' IN BOTH KEYS AND VALUES OF DICTIONARY
          PREFIXED THE DISTRICTS THAT HAVE SAME NAME BUT ARE PRESENT IN DIFFERENT STATES WITH THE STATE ID(BASED ON Q CODE)
          CHANGED THE WRONGLY NOTED DISTRICTS NAMES IN NEIGHBOURS FILE(ACCORDING TO DISTRICT NAMES IN VACCINE DATA)
          PREFIXED THE DISTRICTS NAMES WITH STATE ID IN NEIGHBOURS DATA
	Output files: 'neighbor-districts-modified.json'
	
QUESTION 2:
	'edge-generator.sh' invokes question 2.
	Dependency with other questions : Yes (QUESTION 1's output)
	Required Input files: 'neighbor-districts-modified.json'
	Built undirected graph using edge list format
	No repetition of edges { If (a,b) present, (b,a) will not present }
        sorted output based on first field
	Output files: 'edge-graph.csv'
	
QUESTION 3:
	'case-generator.sh' invokes question 3.
	Dependency with other questions : No
	Required Input files: 'districts.csv'
	Dropped dates greater than 14 Aug 2021
	Data Preprossing happens
	Set start date as 15 Mar 2020 with zeros in dataframe
        calculated 
           NO OF NEW CASES IN THAT WEEK=NO OF CASES IN THE LAST DAY OF THE THAT WEEK - NO OF  CASES IN THE LAST DAY OF THE PREVIOUS  WEEK   
           NO OF NEW CASES IN THAT MONTH=NO OF CASES IN THE LAST  DAY OF THAT MONTH - NO OF CASES IN THE LAST DAY OF THE PREVIOUS MONTH
	Cosumes more time to complete execution

        DUE TO SOME ERROR IN DATA ,THE CUMULATIVE DATA ENTRY IS NOT FOLLOWED
        SO SOME WEEKS and MONTHS  HAVE REPORTED NEGATIVE NUMBER OF CONFIRMED CASES
        FOUND THOSE ROWS THAT HAVE NEGATIVE CONFIRMED CASES AND REPLACED THEM BY ZERO
        sorted outputs based on first field
	Output files: 'cases-week.csv','cases-month.csv','cases-overall.csv'

QUESTION 4:
	'peaks-generator.sh' invokes question 4.
	Dependency with other questions : No
	Required Input files: 'districts.csv'
	Dropped dates greater than 14 Aug 2021
	active cases = confirmed - recovered - deceased
	Set start date as 15 Mar 2020 with zeros in dataframe
	Overlapping for weeks is implemented
	Merged districts of one state into one, thus obtained the information for states
	With respect to information provided (Roughly, wave-1 was in the summer of 2020, while wave-2 was in April-May of 2021.),
	So divided total weeks and total months into two parts anf found peak 1 in first part and peak 2 in second part(by finding max in each part)
	Cosumes more time to complete execution
        sorted the outputs based on first field
	Output files: 'district-peaks.csv','state-peaks.csv','overall-peaks.csv'
	
QUESTION 5:
	'vaccinated-count-generator.sh' invokes question 5.
	Dependency with other questions : No
	Required Input files: 'cowin_vaccine_data_districtwise.csv'
	Dropped dates greater than 14 Aug 2021
	Preprossed dataframe
        THERE ARE SOME DISTRICT KEYS WHICH HAVE SAME VALUE IN VACCINE DATA SO I
        GROUPBYED THOSE DISTRCIT KEYS AND CREATE THEM AS ONLY 1 ENTRY(BY ADDING ALL COLUMN VALUES CORRESPONDING TO THAT GROUP)

        CALCULATED THE NO OF NEW CASES GENERATED IN A WEEK BY SUBTRACTING THAT WEEK END DOSE 1 FROM PREVIOUS WEEKENDS
        DOSE1 AND SIMILARLY FOR DOSE2

        CALCULATED THE NO OF NEW CASES GENERATED IN A MONTH BY SUBTRACTING THAT MONTH END DOSE 1 FROM PREVIOUS MONTHENDS
        DOSE1 AND SIMILARLY FOR DOSE2

        FOR OVERALL THE DATA IS DIRECTLY AVAILABLE FROM FINAL DATE(SINCE IT IS CUMULATIVE)

	Cosumes more time to complete execution
        SINCE THE DATA IS CUMULATIVE , IN SOME ROWS OF DATA DID NOT FOLLOW THE CUMULATIVE RULE
        SO WE GET SOME NEGATIVE VLUES FOR SOME WEEKS OR MONTHS IN SOME DISTRICTS OR STATES
        AS THE NEGATIVE VALUES ARE NOT MEANINGFUL
        I MADE  THESE NEGATIVE VALUES ZEROS IN BOTH DOSE 1 AND DOSE 2 COLUMNS
        sorted the outputs based on first field
	Output files: 'district-vaccinated-count-week.csv','district-vaccinated-count-month.csv','state-vaccinated-count-week.csv',
					'state-vaccinated-count-month.csv','district-vaccinated-count-overall.csv','state-vaccinated-count-overall.csv'
					
QUESTION 6:
	'vaccination-population-ratio-generator.sh' invokes question 6.
	Dependency with other questions : No
	Required Input files: 'cowin_vaccine_data_districtwise.csv','DDW_PCA0000_2011_Indiastatedist.csv'				
	Renamed and Extracted only useful columns
	vaccination ratio = females vaccinated / males vaccinated
        population ratio=females population / males population
        ratio of ratios =vaccination ratio / population ratio
	Merged districts of one state into one, thus obtained the information for states
	Appended state codes to the districts in census data
	Changes are made in census data distrct names to match with the vaccination data district names
	AS THE CENSUS DATA IS OLD,CHANGED TELANGANA DISTRICTS INTO ANDHRA PRADESH DISTRICTS AND LADAKH DISTRICTS INTO
        JAMMU AND KASHMIR DISTRICTS AND ALSO SOME CHANGES TO OTHER DISTRICTIDS
         
        AS THE CENSUS DATA IS OLD , 'DAMAN & DIU' AND 'DADRA & NAGAR HAVELI' HAVE COMBINED AND FORMED A SINGLE STATE NOW
        SO FROM CENSUS DATA COMBINED THE STATES DATA THAT HAVE STATE ID 'DN' AND MAKING THEM AS A SINGLE STATE

        THERE ARE SOME DISTRICT KEYS WHICH HAVE SAME VALUE IN VACCINE DATA SO I
        GROUPBYED THOSE DISTRCIT KEYS AND CREATE THEM AS ONLY 1 ENTRY(BY ADDING ALL COLUMN VALUES CORRESPONDING TO THAT GROUP)

	Merged dataframes consisting vaccination ratio and population ratio to find ratioofratios
	Sorted dataframes based on ratioofratios in all output files
	Output files: district-vaccination-population-ratio.csv','state-vaccination-population-ratio.csv','overall-vaccination-population-ratio.csv'
	
QUESTION 7:
	'vaccine-type-ratio-generator.sh' invokes question 7.
	Dependency with other questions : No
	Required Input files: 'cowin_vaccine_data_districtwise.csv'
	Renamed and Extracted only useful columns
	vaccine ratio = covishield / covaxin
        IF COVAXIN VALUE IS ZERO  I MADE  THE VACCINE RATIO 0 

        THERE ARE SOME DISTRICT KEYS WHICH HAVE SAME VALUE IN VACCINE DATA SO I
        GROUPBYED THOSE DISTRCIT KEYS AND CREATE THEM AS ONLY 1 ENTRY(BY ADDING ALL COLUMN VALUES CORRESPONDING TO THAT GROUP)

	Merged districts of one state into one, thus obtained the information for states
	Sorted dataframes based on vaccineratio in all output files
	Output files: vaccine-district-ratio.csv','vaccine-state-ratio.csv','vaccine-overall-ratio.csv'
	
QUESTION 8:
	'vaccinated-ratio-generator.sh' invokes question 8.
	Dependency with other questions : No
	Required Input files: 'cowin_vaccine_data_districtwise.csv','DDW_PCA0000_2011_Indiastatedist.csv'
	Renamed and Extracted only useful columns
	
	Merged districts of one state into one, thus obtained the information for states
	Appended state codes to the districts in census data
	Changes are made in census data district names to match with the vaccination data district names
	AS THE CENSUS DATA IS OLD,CHANGED TELANGANA DISTRICTS INTO ANDHRA PRADESH DISTRICTS AND LADAKH DISTRICTS INTO
        JAMMU AND KASHMIR DISTRICTS AND ALSO SOME CHANGES TO OTHER DISTRICTIDS
         
	AS THE CENSUS DATA IS OLD , 'DAMAN & DIU' AND 'DADRA & NAGAR HAVELI' HAVE COMBINED AND FORMED A SINGLE STATE NOW
        SO FROM CENSUS DATA COMBINED THE STATES DATA THAT HAVE STATE ID 'DN' AND MAKING THEM AS A SINGLE STATE
     
        THERE ARE SOME DISTRICT KEYS WHICH HAVE SAME VALUE IN VACCINE DATA SO I
        GROUPBYED THOSE DISTRCIT KEYS AND CREATE THEM AS ONLY 1 ENTRY(BY ADDING ALL COLUMN VALUES CORRESPONDING TO THAT GROUP)

	vaccinated dose 1 ratio = First Dose Administered / Total Population
	vaccinated dose 2 ratio = Second Dose Administered / Total Population
	Sorted dataframes based on vaccinateddose1ratio in all output files
	Output files: 'district-vaccinated-dose-ratio.csv','state-vaccinated-dose-ratio.csv','overall-vaccinated-dose-ratio.csv'

QUESTION 9:
	'complete-vaccination-generator.sh' invokes question 9.
	Dependency with other questions : No
	Required Input files: 'cowin_vaccine_data_districtwise.csv','DDW_PCA0000_2011_Indiastatedist.csv'
	Renamed and Extracted only useful columns
	Merged districts of one state into one, thus obtained the information for states
	rate of vaccination per day = no of cases in last week / 7 { Here,  referred  last week is ['2021-08-08' to '2021-08-14']}
        population left=total population(FROM CENSUS)-dose1 value in the end date(14 AUG 21)
   
	Appended state codes to the districts in census data
	Changes are made in census data to match with the vaccination data
        AS THE CENSUS DATA IS OLD,CHANGED TELANGANA DISTRICTS INTO ANDHRA PRADESH DISTRICTS AND LADAKH DISTRICTS INTO
        JAMMU AND KASHMIR DISTRICTS AND ALSO SOME CHANGES TO OTHER DISTRICTIDS
         
	AS THE CENSUS DATA IS OLD , 'DAMAN & DIU' AND 'DADRA & NAGAR HAVELI' HAVE COMBINED AND FORMED A SINGLE STATE NOW
        SO FROM CENSUS DATA COMBINED THE STATES DATA THAT HAVE STATE ID 'DN' AND MAKING THEM AS A SINGLE STATE
     
	Merged both vaccination and population dataframes
	No of days required to vaccinate population left = ceil of ( population left / rate of vaccination per day )
	
	Now, the date on which vaccination will be completed = last date { '2021-08-14' } + No of days required to vaccinate population left
	Output files: 'complete-vaccination.csv'
	
	
*** THE END ****