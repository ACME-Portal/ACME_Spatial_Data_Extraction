# ACME Spatial Data Extraction

Written by Wylie Fuller, Macgregor Aubertin-Young, and Laura Eliuk 

This code is written in python, can be run in the Python window in ArcGIS.
Two step process: first, extract buffers from camera point data; second, extract spatial data from each buffer size
The second step of the process includes loops for both feature type and buffer size; list all feature types of interest in the "feature = " line of code

NOTE: To extract data from multi-part features (ex. grizmap), do each feature type individually and not within the loop; this is because "class_fields" must be specified for multi-part features
