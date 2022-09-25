# Saga-gis toolchain for burned area mapping
![header](https://user-images.githubusercontent.com/111765142/191145104-af857c75-9913-4db8-84eb-9211f6371eb5.png)
Shape of burned area in July 2022 on Kras(Slovenia, Italy)

### Imagery:

Input imagery are cloud free scenes before and after event. Level 1 or Level 2 data can be used.  
<br/><sub/>Some links:
- [EarthExplorer_NASA](https://earthexplorer.usgs.gov/)
- [Copernicus_ESA](https://scihub.copernicus.eu/dhus/#/home)
- [WorldView](https://worldview.earthdata.nasa.gov/?v=-41.77842088367239,17.969859441159233,26.980443247397467,53.613930015916836&l=Coastlines_15m,VIIRS_SNPP_CorrectedReflectance_BandsM11-I2-I1&lg=false&t=2019-02-12-T00%3A00%3A00Z)

## Importing toolchain into SAGA GIS
Toolchain in .xml format can be downloaded ..select RAW and right click..Save as [here](https://github.com/RhoSpatial/Saga-gis-toolchain-for-burned-area-mapping-/blob/main/SAGA-GIS-toolchain_Burned_area.xml) and then droped into SAGA GIS GUI for GUI:

<img src="https://user-images.githubusercontent.com/111765142/190589287-b2615f72-79f4-4a8e-9c53-48b5f5cac0b0.png" width="420">

### Importing imagery into SAGA GIS:

- Geoprocessing-->File -->Satellite Imagery -->Import  ...  scene
<sub/><br/>Tool for Importing Landsat scene(no calibration required), but if Radiometric calibration(radiance) is applied to level 1 data(L1) then 
<br/>Output Data Type should be "floating point numbers")

<img src="https://user-images.githubusercontent.com/111765142/188313379-32e0162b-dac1-4631-84e4-92fabbbfe35b.png" width="380" >

<sub/>Preprocessing is not required, there is an option where Landsat L1 products can be processed to 
<br/>Top of atmosphere Reflectance with options to apply different DOS(Dark Object Substraction) 
<br>methods to get parallel product to surface reflactance    
<sub/>- Geoprocessing-->File -->Imagery --> Landsat -->Top of Atmosphere Reflectance

## Toolchain for burned area mapping
### Inputs
All bands must be in same grid system; ex.: Downscaling SWIR_2 from 20m to 10m resolution
- Geoprocessing-->Grid -->Grid System -->Resampling   
<br>Downscaling=Nearest Neighbour; User defined Cell Size(of other bands); default coordinates; Fit nodes</br>
- clipping all bands to prebound study area 
 
<br>1 pre fire bands (red, NIR, SWIR_2)
<br/><sub>Optional(pre)  QA_PIXEL input for Landsat imagery</sub>
<br/><sub></sub>
<br/>2 post fire bands (red, NIR, SWIR_2)
<br/><sub>Optional(post)  QA_PIXEL input for Landsat imagery</sub>
<br/>
<br/>3 output file name preappendix and postappendix
<br/>
<br/>4 Thresholds(=th) for dNBR classification and vectorization

<img src="https://user-images.githubusercontent.com/111765142/191151606-de44389a-774b-4b41-af65-bd9ccea70ae8.png" width="380">

### Outputs  
- Indices(pre;post) collection...............pre post (NBR and NDVI and QA_pixel) 4 or 6 grids
<br/>- dNBR(Normalized Burn Ratio)
<br/>- dNBR USGS_classes
<br/>- dNBR_classes_user_def dNBR_thx
<br/>- RBR.................................optional
<br/>- dNDVI
<br/>- Vectorized dNBR_th2

QA_pixel export bands(3x) in Indices(pre;post) collection:(pre and post(range 0;1), united(range 0;2), value=0=good pixel

![image](https://user-images.githubusercontent.com/111765142/192162376-22db7cd7-51fd-43c5-898f-e646cb807240.png)
<sub>RGB ( NBRpost, NBRpre, NDVIpost ) (c)NASA</sub>

### Landsat 8(OLI) Level 1 vs. Level 2  
| Date | Cloud free Imagery | level |
| - | - | - |
| 2022-07-04 | LC08_L1TP_191028_20220704_20220708_02_T1 | L1 |
| 2022-07-04 | LC08_L2SP_191028_20220704_20220708_02_T1 | L2 |
| 2022-08-05 | LC08_L1TP_191028_20220805_20220818_02_T1 | L1 |
| 2022-08-05 | LC08_L2SP_191028_20220805_20220818_02_T1 | L2 |
| 2022-08-05 | LC08_L1TP_191028_20220805_20220805_02_RT |near Real Time |

<p align='center'>Fell free :</p>

```
 mih.zemva@gmail.com
```

