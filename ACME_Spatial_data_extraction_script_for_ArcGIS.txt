## SPATIAL DATA EXTRACTION SCRIPT
## Written by Wylie Fuller, Macgregor Aubertin-Young, and Laura Eliuk 
##
## This code is written in python, can be run in the Python window in ArcGIS.
##
## Two step process: first, extract buffers from camera point data; second, extract spatial data from each buffer size
##	The second step of the process includes loops for both feature type and buffer size; list all feature types of interest in the "feature = " line of code
## 	NOTE: To extract data from multi-part features (ex. grizmap), do each feature type individually and not within the loop; this is because "class_fields" must be specified for multi-part features
##
##



## PART ONE - BUFFER EXTRACTION
## 	
## Note: this only needs to be done once to create the buffer shapefiles; for subsequent data extraction, skip to step 2 

# 1) Set your working directory to the location you want to store the output files
arcpy.env.workspace = r"H:\Wylie 2019\Rockies_Arrays"

# 2) Make a list from 250 to 5000 with gaps of 250 (250, 500, 750, etc)
buffers = range(250, 5250, 250)
for distance in buffers:  
...     input_feature = "Parks_Cameras"   				# point file name 
...     dist_name = str(distance)   					
...     output = "parks_cam_buffer" + dist_name + ".shp"   
...     distanceField = distance  
...     disolve_options = "ALL"   
...     arcpy.Buffer_analysis(input_feature, output, distanceField)  


## PART TWO - EXTRACT SPATIAL COVARIATES FROM BUFFERS
## 	

# 1) Import necessary libraries, "Re" and "Arcpy"
import re  
import arcpy  

# 2) Set the folder where you want ListFeatureClasses (below) to find the input buffer shapefiles
arcpy.env.workspace = r"F:\GIS\Willmore\buffers"

# 3) Specify feature types to be used in loop
#	input the file name for each spatial layer:

feature = ["o01_Reservoirs_HFI2010", "o02_BPSDL_HFI2010"]

# 4) Run for-loop extracting data from each feature type at each buffer scale 
for featuretype in feature:
... # Use the tabulate intersection python code in a for loop
...     ww_buffers_list = arcpy.ListFeatureClasses()  # List all the feature-classes (shapefiles) in your working directory folder 
... 
...     for ww_buffer in ww_buffers_list:
...         
...         in_zone_features = ww_buffer   # Equivalent to "i" in a for loop - the named object the list you're iterating through 
...         zone_fields = "site_name"   # Cam_name identifier for the camera buffers
...         in_class_features = str(featuretype)    # Feature you're intersecting the camera buffers with
... 
... # Extract the number from the camera buffer file name (this code removes everything that isn't a number and saves the number as a new object "numbers")
...         name = re.sub("\D", "", ww_buffer)   
... 
... # Make the output file name
...         out_table = str(featuretype) + "_" + str(name) + ".csv"
... 
... # Run the tabulate intersection tool
...         arcpy.TabulateIntersection_analysis(in_zone_features, zone_fields, in_class_features, out_table, class_fields="", sum_fields="", xy_tolerance="-1 Unknown", out_units="UNKNOWN")


# NOTE- if you have multi-part features (cutblocks with different age-classes, GRIZ landcover data) fill in class_fields = "" with the column 
# that names different features - ie. class_fields="ageclass" or class_fields="landcover_class"
# Run multipart features separately (omit lines 1-4 of the loop) so that class_fields can be specified. The for loop code does not account for this 


