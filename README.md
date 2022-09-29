# Saga-gis toolchain for burned area mapping
![header](https://user-images.githubusercontent.com/111765142/191145104-af857c75-9913-4db8-84eb-9211f6371eb5.png)
*Shape of burned area in July 2022 on Kras (Slovenia, Italy) on pre-fire natural RGB*
### Imagery:

Input imagery are cloud free scenes before and after event. Level 2 data(surface reflectance) is default input. Level 1 can also be used;
<br/>the values of L1 are greather: cca:  L2sr= 0.8 x L1 and L1= 1.25 x L2sr; so classified grids from L1 data are not in range, but the dynamics are the same and sometimes L2sr are not available in short time after post event so we can use L1 data(also L1...RT); adjusting dNBR threshold for vectorization(ex.:L1 dNBR_th2= 0.137).  
<br/><sub/>Some links:
- [EarthExplorer_NASA](https://earthexplorer.usgs.gov/)
- [Copernicus_ESA](https://scihub.copernicus.eu/dhus/#/home)
- [WorldView_NASA](https://worldview.earthdata.nasa.gov/?v=-41.77842088367239,17.969859441159233,26.980443247397467,53.613930015916836&l=Coastlines_15m,VIIRS_SNPP_CorrectedReflectance_BandsM11-I2-I1&lg=false&t=2019-02-12-T00%3A00%3A00Z)

## Importing toolchain into SAGA GIS
Toolchain in .xml format can be downloaded from [SourceForge](https://sourceforge.net/projects/saga-gis-rhotoolchains/files/) or **_..selecting Raw and right click..Save as...[Here](https://github.com/RhoSpatial/Saga-gis-toolchain-for-burned-area-mapping-/blob/main/SAGA-GIS-toolchain_Burned_area.xml) _** and then droped into SAGA GIS GUI for GUI:

<img src="https://user-images.githubusercontent.com/111765142/190589287-b2615f72-79f4-4a8e-9c53-48b5f5cac0b0.png" width="420">

### Importing imagery into SAGA GIS:

- Geoprocessing-->File -->Satellite Imagery -->Import  ...  scene
<sub/><br/>Tool for Importing Landsat scene(no calibration required), but if Radiometric calibration(radiance)(not meant for this toolchain) is applied to level 1 data(L1) then 
<br/>Output Data Type should be "floating point numbers")

<img src="https://user-images.githubusercontent.com/111765142/188313379-32e0162b-dac1-4631-84e4-92fabbbfe35b.png" width="380" >

<sub/>Preprocessing is not required, there is an option where Landsat L1 products can be processed to 
<br/>Top of atmosphere Reflectance with options to apply different DOS(Dark Object Substraction) 
<br>methods to get parallel product to surface reflactance    
<sub/>- Geoprocessing-->File -->Imagery --> Landsat -->Top of Atmosphere Reflectance

## Toolchain for Burned area mapping
### Inputs
All bands must be in same grid system; ex.: Downscaling SWIR_2 from 20m to 10m resolution
- Geoprocessing-->Grid -->Grid System -->Resampling   
<br>*Downscaling = Nearest Neighbour; User defined Cell Size = 10m; default(SWIR_2) coordinates; Fit = nodes*</br>
- clipping all bands to prebound area 
 
<br>**1** - pre fire bands (red, NIR, SWIR_2)
<br/><sub>Optional(pre)  QA_PIXEL input for Landsat imagery</sub>
<br/>
<br/>**2** - post fire bands (red, NIR, SWIR_2)
<br/><sub>Optional(post)  QA_PIXEL input for Landsat imagery</sub>
<br/>
<br/>**3** - output file name preappendix and postappendix
<br/>
<br/>**4** - Thresholds(=th) for dNBR classification and vectorization

<img src="https://user-images.githubusercontent.com/111765142/191151606-de44389a-774b-4b41-af65-bd9ccea70ae8.png" width="420">

### Outputs
- Indices(pre;post) collection...............pre post (NBR and NDVI and QA_pixel(optional)) 4 or(7) grids
- dNBR(Normalized Burn Ratio)
- dNBR USGS_classes
- dNBR_classes_user_def dNBR_thx
- RBR.....................................optional
- dNDVI
- Vectorized dNBR_th2

QA_pixel export bands(3x) in **Indices(pre;post) collection**:(pre and post(range 0;1), united(range 0;2), value=0=good pixel

![image](https://user-images.githubusercontent.com/111765142/192162376-22db7cd7-51fd-43c5-898f-e646cb807240.png)
<sub>RGB ( NBRpost, NBRpre, NDVIpost ) (c)NASA</sub>

### Landsat 8(OLI) Level 1 vs. Level 2  
| Date | Cloud free Imagery | level |
| - | - | - |
| 2022-07-04 | LC08_L1TP_191028_20220704_20220708_02_T1 | L1 |
| 2022-07-04 | LC08_L2SP_191028_20220704_20220708_02_T1 | L2 |
| 2022-08-05 | LC08_L1TP_191028_20220805_20220818_02_T1 | L1 |
| 2022-08-05 | LC08_L2SP_191028_20220805_20220818_02_T1 | L2 |
| 2022-08-05 | LC08_L1TP_191028_20220805_20220805_02_RT |L1 near Real Time |

<br/>
<br/>


![IndicScatt](https://user-images.githubusercontent.com/111765142/193045250-79ffa037-7f27-4624-9ab0-ba84ed0b73ee.png)

<br/>
<br/>dNBR L1 and L2 are highly correlated; while dNBR and dNDVI less. NDVI uses bands nearer to VIS spectrum where there is more power.
The main purpose is assesment of severity and to get trustful shapefile of burned area that can be used for future monitoring(GEE). 

_Feel free :_


```
 mih.zemva@gmail.com
```

