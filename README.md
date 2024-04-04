### sst-data-analysis-ml
# Sea Surface Temperature (SST) – Data Procurement, Analysis and Machine Learning (ML) Foundations
Internship work done for the **IP1201** course of our 3rd Semester

**Reference Paper**: [Physical Knowledge-Enhanced Deep Neural Network for Sea Surface Temperature Prediction](https://ieeexplore.ieee.org/document/10068549)

### Team Members:
 - Ashwin Santhosh (CS22B1005, GitHub: [ash0545](https://www.github.com/ash0545))
 - Aswin Valsaraj (CS22B1006, GitHub: [aswinn03](https://www.github.com/aswinn03))
 - Kaustub Pavagada (CS22B1042, GitHub: [Kaustub26Pvgda](https://www.github.com/Kaustub26Pvgda))

# Overview
## Motivation
The primary motivation for this internship was to gain practical experience in Data Analysis for ML, specifically for the SST data provided by the Group for High Resolution Sea Surface Temperature (GHRSST). Data preprocessing steps are elaborated, and we navigate through these steps while providing insights into the tools and methods used to ensure data quality and usability.


## Tools / Libraries Used
 - [podaac-data-subscriber](https://pypi.org/project/podaac-data-subscriber/) : to access data from the Physical Oceanography Distributed Active Archive Center (PODAAC)
 - [Panoply](https://www.giss.nasa.gov/tools/panoply/) : to view plots and attributes of downloaded NetCDF files
 - [xarray](https://docs.xarray.dev/en/stable/index.html) : for working with labelled multi-dimensional arrays
 - [pandas](https://pandas.pydata.org/) : for data manipulation and analysis

## Data Acquisition
 - The CMC0.2deg-CMC-L4-GLOB-v2.0 dataset was downloaded retrieved through podaac-data-subscriber
> [!NOTE]
> The podaac-data-subscriber tool requires credentials from https://urs.earthdata.nasa.gov, which are to be stored in a .netrc file.
>
> The exact command used for download was : `podaac-data-downloader -c CMC0.2deg-CMC-L4-GLOB-v2.0 -d ./data --start-date 2007-05-01T00:00:00Z --end-date 2014-12-31T23:59:59Z`
 - This data contained SST records spanning the entire globe from May 2007 to December 2014
 - The data was present as netCDF files, with 2803 files having a total size of 5.56 GB
![image](https://github.com/ash0545/sst-data-analysis-ml/assets/112403369/694ed9c8-d54b-4b4e-aa76-b5ff2345effc)


## Data Preprocessing
 - The data had the following attributes:
   - analysed_sst : SST values, defined at all grid points
   - analysis_error : estimated error standard deviation of analysed SST
   - lat and lon
   - mask : sea / land / lake / ice field composite mask
   - sea_ice_fraction : sea ice area fraction
   - time : reference time of SST field
 - As no physical meaning ascribed to values over land, they were masked out using the mask attribute and xarray
![image](https://github.com/ash0545/sst-data-analysis-ml/assets/112403369/4eda8393-677a-40fe-90f8-e49986c800d0)

 - The resulting masked data was then sliced with a bounding box over India, using xarray's [.sel()](https://docs.xarray.dev/en/stable/generated/xarray.DataArray.sel.html) method
![image](https://github.com/ash0545/sst-data-analysis-ml/assets/112403369/934c28c2-26a8-45ca-8dc3-ded95b31d32f)

 - Data compression was applied using the zlib Python library
 - The final processed data was ~170 MB, down from the initial 5.56 GB, a reduction of more than 30X

## ML Foundations
 - Our reference paper consisted of three models, which were :
   - Generative Adversarial Network (GAN)
   - Auto-Encoder
   - ConvLSTM

   We decided to focus on the ConvLSTM model, as the other two were used to _enhance_ the data, and we wanted to get hands on with the training process as soon as possible
 - A previous [implementation of ConvLSTM](https://github.com/ndrplz/ConvLSTM_pytorch/tree/master) was used
 - The entire dataset was merged into a single xarray DataArray, to be fed into the model
![image](https://github.com/ash0545/sst-data-analysis-ml/assets/112403369/b940aba6-6660-4ccb-bcaa-ae0c77e39e25)

 - Our final work for this internship was learning how to reshape data to a form expected by the model (as provided in the model's documentation)

# Helpful Links
 - Data Access : https://www.ncei.noaa.gov/access/ghrsst-long-term-stewardship-and-reanalysis-facility/
 - Bounding over Certain Regions : https://medium.com/analytics-vidhya/how-to-read-and-visualize-netcdf-nc-geospatial-files-using-python-6c2ac8907c7c
 - NetCDF Compression : https://stackoverflow.com/questions/49053692/csv-to-netcdf-produces-nc-files-4x-larger-than-the-original-csv
 - ConvLSTM Implementation : https://github.com/ndrplz/ConvLSTM_pytorch
 - Channels in CNNs : https://datascience.stackexchange.com/questions/64278/what-is-a-channel-in-a-cnn
