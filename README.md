# Saga-gis toolchain for burned area mapping
![header](https://user-images.githubusercontent.com/111765142/191145104-af857c75-9913-4db8-84eb-9211f6371eb5.png =x240)
Burned area (Kras, Slovenia, Italy)

[some link](https://docs.google.com/document/d/1GPtPFcteq4Acpxi92wx8Xh0RLyAXM6RzKxIIifj8RAo/edit?usp=sharing)

## Imagery:

Input imagery are cloud free scenes before and after event. Level 1 or Level 2 data can be used.  
<br/><sub/>Some links:
-[EarthExplorer_NASA](https://earthexplorer.usgs.gov/)
-[Copernicus_ESA](https://scihub.copernicus.eu/dhus/#/home)
-[WorldView](https://worldview.earthdata.nasa.gov/?v=-41.77842088367239,17.969859441159233,26.980443247397467,53.613930015916836&l=Coastlines_15m,VIIRS_SNPP_CorrectedReflectance_BandsM11-I2-I1&lg=false&t=2019-02-12-T00%3A00%3A00Z)

## Importing toolchain into SAGA GIS
Toolchain in .xml format can be downloaded [here] and then droped into SAGA GIS for GUI:

<img src="https://user-images.githubusercontent.com/111765142/190589287-b2615f72-79f4-4a8e-9c53-48b5f5cac0b0.png" width="500">

### Importing imagery into SAGA GIS:

- Geoprocessing-->File -->Satellite Imagery -->Import .... scene
<sub/><br/>Tool for Landsat(no calibration required), but if Radiometric calibration(radiance) is applied to level 1 data(L1) then 
<br/>Output Data Type should be "floating point numbers")

<img src="https://user-images.githubusercontent.com/111765142/188313379-32e0162b-dac1-4631-84e4-92fabbbfe35b.png" width="400" >

<sub/>Preprocessing is not required, there is an option where Landsat L1 products can be processed to 
<br/>Top of atmosphere Reflectance with options to apply different DOS(Dark Object Substraction) 
<br>methods to get parallel product to surface reflactance    
<sub/>- Geoprocessing-->File -->Imagery --> Landsat -->Top of Atmosphere Reflectance

## Toolchain ### inputs outputs
### Inputs
<sub/><br/>1 pre fire bands
<br/>2 post fire bands
<br/>QA_PIXEL optional input for Landsat imagery

<br/>3 output file name preappendix and postappendix
<br/>4 Thresholds(=th) for dNBR vectorization

<img src="https://user-images.githubusercontent.com/111765142/191151606-de44389a-774b-4b41-af65-bd9ccea70ae8.png" height="240">


```
https://capsule-render.vercel.app/api?
```

<p align='center'> Decorate GitHub Profile or any Repo like me! </p>


sdfghjk**fgef**
| Date | Who | What |
| - | - | - |
| 2022-06-01 | I did it | 1. Change handling of url pattern to add numbers <br> 2. Added script to GitHub.|
