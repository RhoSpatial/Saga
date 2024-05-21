# Saga-GIS toolchain for burned area mapping
![header](https://user-images.githubusercontent.com/111765142/191145104-af857c75-9913-4db8-84eb-9211f6371eb5.png)
*Shape of burned area in July 2022 on Kras (Slovenia, Italy) on pre-fire natural RGB*
### Imagery:

Input imagery are cloud free scenes before and after event. Level 2 data(surface reflectance) is default input. Level 1 can also be used;
<br/>the values of L1 are greather: cca:  L2sr= 0.8 x L1 and L1= 1.25 x L2sr; so classified grids from L1 data are not in range, but the dynamics are the same and sometimes L2sr are not available in short time after post event so we can use L1 data(also L1...RT); adjusting dNBR threshold for vectorization(ex.:L1 dNBR_th2= 0.137). Using L1 data will not give accurate classes of dNBR; running tool with overridding exports with changed value of dNBR_th2 is recomended to retrive burned area shape.   
<br/><sub/>Some links:

- [EarthExplorer_NASA](https://earthexplorer.usgs.gov/)
- [Copernicus_ESA](https://scihub.copernicus.eu/dhus/#/home)
- [WorldView_NASA](https://worldview.earthdata.nasa.gov/?v=-41.77842088367239,17.969859441159233,26.980443247397467,53.613930015916836&l=Coastlines_15m,VIIRS_SNPP_CorrectedReflectance_BandsM11-I2-I1&lg=false&t=2019-02-12-T00%3A00%3A00Z)
- [Running tool video on YouTube](https://www.youtube.com/watch?v=pFY6vt35QRs)
- [SAGA GIS on SourceForge](https://sourceforge.net/projects/saga-gis/)

## Importing toolchain into SAGA GIS
Toolchain SAGA-GIS-toolchain_Burned_area.xml can be downloaded from [SourceForge](https://sourceforge.net/projects/saga-gis-rhotoolchains/files/) or copied at the end of this readme and then droped into SAGA GIS GUI for GUI:

<img src="https://user-images.githubusercontent.com/111765142/190589287-b2615f72-79f4-4a8e-9c53-48b5f5cac0b0.png" width="420">

### Importing imagery into SAGA GIS:

- Geoprocessing-->File -->Satellite Imagery -->Import  ...  scene
<sub/><br/>Tool for Importing Landsat scene(no calibration required), but if Radiometric calibration(radiance) is applied to level 1 data(L1) then Output Data Type should be "floating point numbers")
<br/>For Sentinel-2 scenes opening individual bands manualy is advised

<img src="https://user-images.githubusercontent.com/111765142/188313379-32e0162b-dac1-4631-84e4-92fabbbfe35b.png" width="380" >

<sub/>Preprocessing is not required, there is an option where Landsat L1 products can be processed to 
<br/>Top of atmosphere Reflectance with options to apply different DOS(Dark Object Substraction) 
<br>methods to get parallel product to surface reflactance    
<sub/>- Geoprocessing-->File -->Imagery --> Landsat -->Top of Atmosphere Reflectance

## Toolchain for Burned area mapping
### Inputs
All bands must be in same grid system; ex.: Downscaling SWIR_2 from 20m to 10m resolution
- Geoprocessing-->Grid -->Grid System -->Resampling   :
 <br>*Downscaling = Nearest Neighbour; User defined Cell Size = 10m; default(SWIR_2) coordinates; Fit = nodes*
</br>
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

##### `Scatterplots of clipped indices`

<br/>dNBR L2 and dNBR L1 are highly correlated
<br/>dNDVI L2 and dNBR L2 are less correlated (NDVI is calculated from bands nearer to VIS spectrum)
<br/>
<br/>The main purpose is assesment of severity and to get trustful shapefile of burned area that can be used for future monitoring(GEE). 
<br/>
<br/>

_Feel free :_


```
 mih.zemva@gmail.com
```

#### `Burned area toolchain xml file`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<toolchain saga-version="8.1.1">
  <group>EO</group>
  <identifier>Rho_001</identifier>
  <name>Burned area mapping</name>
  <author>M. Žemva (c) 2022</author>
  <description>Calculating indices for mapping and analysing burned area
  ..........................{red..NIR.. SWIR_2.}
                    L8/L8 OLI..bands {.b4.. .b5.. .b7..}  ....._30m
                    ASTER......bands {.B2..   B3..   B6..}   ..._15m   ...._SWIR_2 ..30m             
                    S2/S2 MSI bands {.B4..   B8..   B12.}   .._10m   ...._SWIR_2 ..20m
                    Input bands must be on same Grid System(Resample/Nearest neighb./user def. Cellsize/fit nodes)

                        Normalized Burn Ratio:
                           ...NBR = (NIR - SWIR_2) / (NIR + SWIR_2 )

                           ...NDVI = (NIR - Red) / (NIR + Red) 

                  Estimating severity:

                    ...deltaINDEX = INDEX_pre-fire - INDEX_post-fire
                    Relativized Burned Ratio:     
                    ...RBR = dNBR / (NBRpre + 1.001)                       
                    
dNBR is classified by thresholds(=th) proposed by USGS and user

Delineating burned area:  ..Deleting polygons on vectorized dNBR_th2

Links :       https://www.researchgate.net/publication/228638145_Fire_intensity_fire_severity_and_burn_severity_A_brief_review_and_suggested_usage
             https://un-spider.org/advisory-support/recommended-practices/recommended-practice-burn-severity/in-detail/normalized-burn-ratio
             https://www.indexdatabase.de/db/i-single.php?id=53               
  </description>
 	<reference>
		<authors>Miha Žemva</authors>
		<year>2022</year>
		<title>Saga-gis toolchain for burned area mapping</title>
		<where>Bled, Slovenia</where>
		<link>https://github.com/RhoSpatial/Saga</link>
		<link_text>online</link_text>
	</reference> 
  <menu absolute="true">Imagery|EO</menu>
  <parameters>
    <option varname="GRID_SYSTEM" type="grid_system">
      <name>Grid System</name>
    </option>
    <input varname="REDpre" type="grid" parent="GRID_SYSTEM">
      <name>RED band  pre fire</name>
    </input>
    <input varname="NIRpre" type="grid" parent="GRID_SYSTEM">
      <name>NIR pre fire</name>
    </input> 
    <input varname="SWIR IIpre" type="grid" parent="GRID_SYSTEM">
      <name>SWIR_2 pre fire</name>
    </input>  
    <input varname="QA_PIXELpre" type="grid" parent="GRID_SYSTEM" optional="true">
      <name>QA_PIXEL pre</name><description>Only for Landsat imagery</description>
    </input>        
    <input varname="REDpost" type="grid" parent="GRID_SYSTEM">
      <name>RED band post fire</name>
    </input>
    <input varname="NIRpost" type="grid" parent="GRID_SYSTEM">
      <name>NIR  post fire</name>
    </input> 
    <input varname="SWIR IIpost" type="grid" parent="GRID_SYSTEM">
      <name>SWIR_2  post fire</name>
    </input>
    <input varname="QA_PIXELpost" type="grid" parent="GRID_SYSTEM" optional="true">
      <name>QA_PIXEL post</name><description>Only for Landsat imagery</description>
    </input>

    <output varname="Indices_coll" type="grids" parent="GRID_SYSTEM">
      <name>Indices(pre;post) collection</name>
    </output>
    <output varname="dNBR" type="grid" parent ="GRID_SYSTEM">
      <name>dNBR</name>
      <colours>1</colours>
    </output>
    <output varname="RECLASSnbr" type="grid" parent="GRID_SYSTEM">
      <name>dNBR USGS_classes </name>
    </output>
    <output varname="second" type="grid" parent="GRID_SYSTEM">
      <name>dNBR_classes_user_def dNBR_thx</name>
      <colours>12</colours>
    </output>       
    <output varname="RBR" type="grid" parent ="GRID_SYSTEM" optional="true">
      <name>RBR</name>
      <description>select //create//  for RBR(=RdNBR) calculation</description></output>
    <output varname="dNDVI" type="grid" parent="GRID_SYSTEM">
      <name>dNDVI</name>
      <colours>7</colours>
    </output>
	  <output varname="dNBR_vector_(class_dNBR_th2)" type="shapes">
			<name>Vectorized dNBR_th2</name><output_name>dNBR_class_gt_dNBR_th2</output_name>
      <description>Vectorize dNBR class greather than dNBR_th2 threshold value</description>
    </output>

    <option varname="preapp" type="text">
			   <name>name preappendix</name><value>Kras</value>
    </option>
    <option varname="postappsat" type="text">
			   <name>Sensor_Satellite input product</name><value>L8</value>
    </option> 
    <option varname="postapppreD" type="text">
			   <name>pre fire date</name><value>4_July_22</value>
    </option> 
    <option varname="postapppostD" type="text">
			   <name>post fire date</name><value>5_Avg_22</value>
    </option> 
    <option varname="dNBR_th1" type="double"><name>dNBR_th1</name>
                  <value min="-0.20000">0</value>
    </option>
    <option varname="dNBR_th2" type="double"><name>dNBR_th2 /also th for vectorization</name>
                  <value min="0.010000">0.11</value>
    </option>  
    <option varname="dNBR_th3" type="double"><name>dNBR_th3</name>
                  <value min="0.120000">0.3</value>
    </option>           
  </parameters>
  <tools>   
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">NBRpre</output>
      <option id="FORMULA">(g1 - g2) / (g1 + g2)</option>
      <option id="NAME">$(preapp)_NBRpre_$(postappsat)_$(postapppreD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NIRpre</input>
      <input id="GRIDS">SWIR IIpre</input>
    </tool> 
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">NBRpost</output>
      <option id="FORMULA">(g1 - g2) / (g1 + g2)</option>
      <option id="NAME">$(preapp)_NBRpost_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NIRpost</input>
      <input id="GRIDS">SWIR IIpost</input>
    </tool>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">NDVIpre</output>
      <option id="FORMULA">(g1 - g2) / (g1 + g2)</option>
      <option id="NAME">$(preapp)_NDVIpre_$(postappsat)_$(postapppreD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NIRpre</input>
      <input id="GRIDS">REDpre</input>
    </tool> 
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">NDVIpost</output>
      <option id="FORMULA">(g1 - g2) / (g1 + g2)</option>
      <option id="NAME">$(preapp)_NDVIpost_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NIRpost</input>
      <input id="GRIDS">REDpost</input>
    </tool>   
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">dNDVI</output>
      <option id="FORMULA">g1 - g2</option>
      <option id="NAME">$(preapp)_dNDVI_$(postappsat)__$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NDVIpre</input>
      <input id="GRIDS">NDVIpost</input>
    </tool>
  <condition type="exists" variable="QA_PIXELpre">
  <condition type="exists" variable="QA_PIXELpost">
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">qa_pre</output>
      <option id="FORMULA">ifelse(eq(g1,21824),0,1)</option>
      <option id="NAME">$(preapp)_QA_px_$(postappsat)_$(postapppreD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">QA_PIXELpre</input>
    </tool>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">qa_post</output>
      <option id="FORMULA">ifelse(eq(g1,21824),0,1)</option>
      <option id="NAME">$(preapp)_QA_px_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">QA_PIXELpost</input>
    </tool>
      <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">qa_united</output>
      <option id="FORMULA">g1+g2</option>
      <option id="NAME">$(preapp)_QA_px_$(postappsat)_United</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">qa_post</input>
      <input id="GRIDS">qa_pre</input>  
    </tool>
    <tool library="statistics_grid" tool="13" name="Save Grid Statistics to Table">
      <output id="STATS">tool__STATS</output>
      <option id="DATA_CELLS">false</option>
      <option id="NODATA_CELLS">false</option>
      <option id="CELLSIZE">false</option>
      <option id="MEAN">false</option>
      <option id="MIN">false</option>
      <option id="MAX">false</option>
      <option id="RANGE">false</option>
      <option id="SUM">false</option>
      <option id="SUM2">false</option>
      <option id="VAR">false</option>
      <option id="STDDEV">false</option>
      <option id="STDDEVLO">false</option>
      <option id="STDDEVHI">false</option>
      <option id="PCTL_VAL">5; 25; 50; 75; 95</option>
      <option id="PCTL_HST">false</option>
      <option id="SAMPLES">0</option>
      <input id="GRIDS">NBRpre</input>
      <input id="GRIDS">NBRpost</input>
      <input id="GRIDS">NDVIpre</input>
      <input id="GRIDS">NDVIpost</input>
      <input id="GRIDS">qa_pre</input>
      <input id="GRIDS">qa_post</input>
      <input id="GRIDS">qa_united</input>
    </tool>
    <tool library="grids_tools" tool="0" name="Create a Grid Collection">
      <output id="GRIDS">Indices_coll</output>
      <option id="NAME">$(preapp)_Indices_$(postappsat)_$(postapppostD)</option>
      <option id="DELETE">false</option>
      <option id="ATTRIBUTES">2</option>
      <option id="TABLE_Z">NAME</option>
      <option parms="FIELDS" id="NAME0">ID</option>
      <option parms="FIELDS" id="TYPE0">8</option>
      <option parms="FIELDS" id="NAME1">Value</option>
      <option parms="FIELDS" id="TYPE1">11</option>
      <input id="LIST">NBRpre</input>
      <input id="LIST">NBRpost</input>
      <input id="LIST">NDVIpre</input>
      <input id="LIST">NDVIpost</input>
      <input id="LIST">qa_pre</input>
      <input id="LIST">qa_post</input>
      <input id="LIST">qa_united</input>  
      <input id="TABLE">tool__STATS</input>
    </tool>  
  </condition>
  </condition>  
  <condition type="not_exists" variable="QA_PIXELpre">
  <condition type="not_exists" variable="QA_PIXELpost">
    <tool library="statistics_grid" tool="13" name="Save Grid Statistics to Table">
      <output id="STATS">tool__STATS</output>
      <option id="DATA_CELLS">false</option>
      <option id="NODATA_CELLS">false</option>
      <option id="CELLSIZE">false</option>
      <option id="MEAN">false</option>
      <option id="MIN">false</option>
      <option id="MAX">false</option>
      <option id="RANGE">false</option>
      <option id="SUM">false</option>
      <option id="SUM2">false</option>
      <option id="VAR">false</option>
      <option id="STDDEV">false</option>
      <option id="STDDEVLO">false</option>
      <option id="STDDEVHI">false</option>
      <option id="PCTL_VAL">5; 25; 50; 75; 95</option>
      <option id="PCTL_HST">false</option>
      <option id="SAMPLES">0</option>
      <input id="GRIDS">NBRpre</input>
      <input id="GRIDS">NBRpost</input>
      <input id="GRIDS">NDVIpre</input>
      <input id="GRIDS">NDVIpost</input>
    </tool>
    <tool library="grids_tools" tool="0" name="Create a Grid Collection">
      <output id="GRIDS">Indices_coll</output>
      <option id="NAME">$(preapp)_Indices_$(postappsat)_$(postapppostD)</option>
      <option id="DELETE">false</option>
      <option id="ATTRIBUTES">2</option>
      <option id="TABLE_Z">NAME</option>
      <option parms="FIELDS" id="NAME0">ID</option>
      <option parms="FIELDS" id="TYPE0">8</option>
      <option parms="FIELDS" id="NAME1">Value</option>
      <option parms="FIELDS" id="TYPE1">11</option>
      <input id="LIST">NBRpre</input>
      <input id="LIST">NBRpost</input>
      <input id="LIST">NDVIpre</input>
      <input id="LIST">NDVIpost</input>
      <input id="TABLE">tool__STATS</input>
    </tool>
  </condition>
  </condition>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">dNBR</output>
      <option id="FORMULA">g1 - g2</option>
      <option id="NAME">$(preapp)_dNBR_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">NBRpre</input>
      <input id="GRIDS">NBRpost</input>
    </tool>
    <tool library="grid_tools" tool="15" name="Reclassify Grid Values">
			<input  id="INPUT">dNBR</input>
			<output id="RESULT">RECLASSnbrP</output>
			<option id="METHOD">2</option>
			<option id="SOPERATOR">0</option>
			<option id="RETAB">
				<OPTION type="static_table" id="RETAB" name="Lookup Table">
					<FIELDS>
						<FIELD type="DOUBLE">min</FIELD>
						<FIELD type="DOUBLE">max</FIELD>
						<FIELD type="DOUBLE">new</FIELD>
					</FIELDS>
					<RECORDS>
						<RECORD><FIELD>-0.5</FIELD><FIELD>-0.25</FIELD><FIELD>1.0</FIELD></RECORD>
						<RECORD><FIELD>-0.25</FIELD><FIELD>-0.1</FIELD><FIELD>2.0</FIELD></RECORD>
						<RECORD><FIELD>-0.1</FIELD><FIELD>0.1</FIELD><FIELD>3.0</FIELD></RECORD>
            <RECORD><FIELD>0.1</FIELD><FIELD>0.27</FIELD><FIELD>4.0</FIELD></RECORD>
						<RECORD><FIELD>0.27</FIELD><FIELD>0.44</FIELD><FIELD>5.0</FIELD></RECORD>
						<RECORD><FIELD>0.44</FIELD><FIELD>0.66</FIELD><FIELD>6.0</FIELD></RECORD>
            <RECORD><FIELD>0.66</FIELD><FIELD>1.3</FIELD><FIELD>7.0</FIELD></RECORD>
					</RECORDS>
        </OPTION>
      </option>
    </tool>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
        <output id="RESULT">RECLASSnbr</output>
        <option id="FORMULA">g1</option>
        <option id="NAME">$(preapp)_dNBRclassesUSGS_$(postappsat)_$(postapppostD)</option>
        <option id="FNAME">false</option>
        <option id="USE_NODATA">false</option>
        <option id="TYPE">7</option>
        <input id="GRIDS">RECLASSnbrP</input>
    </tool>
    <tool library="grid_visualisation" tool="11" name="Create a Table from Look-up Table">
			<output id="TABLE">CLASS_DEF</output>
			<option id="LUT">
        <OPTION type="static_table" id="LUT" name="Lookup Table">
					<FIELDS>
						<FIELD type="COLOR" >Color</FIELD>
						<FIELD type="STRING">Name</FIELD>
						<FIELD type="STRING">Description</FIELD>
						<FIELD type="DOUBLE">Minimum</FIELD>
						<FIELD type="DOUBLE">Maximum</FIELD>
					</FIELDS>
          <RECORDS>
          	<RECORD><FIELD> -3000</FIELD><FIELD >Enhanced Regrowth, High</FIELD><FIELD>Enhanced Regrowth, High</FIELD><FIELD>  1</FIELD><FIELD>  1</FIELD></RECORD>
						<RECORD><FIELD> -2400</FIELD><FIELD >Enhanced Regrowth, Low</FIELD><FIELD>Enhanced Regrowth, Low</FIELD><FIELD>2</FIELD><FIELD>2</FIELD></RECORD>
						<RECORD><FIELD> -1400</FIELD><FIELD >Unburned</FIELD><FIELD>Unburned</FIELD><FIELD>  3</FIELD><FIELD>  3</FIELD></RECORD>
						<RECORD><FIELD>   200</FIELD><FIELD >Low Severity</FIELD><FIELD>Low Severity</FIELD><FIELD>  4</FIELD><FIELD>  4</FIELD></RECORD>
						<RECORD><FIELD>  2400</FIELD><FIELD >Moderate-low Severity</FIELD><FIELD>Moderate-low Severity</FIELD><FIELD>  5</FIELD><FIELD>  5</FIELD></RECORD>
						<RECORD><FIELD>  4000</FIELD><FIELD >Moderate-high Severity</FIELD><FIELD>Moderate-high Severity</FIELD><FIELD>  6</FIELD><FIELD>  6</FIELD></RECORD>
						<RECORD><FIELD> 10000</FIELD><FIELD >High Severity</FIELD><FIELD>High Severity</FIELD><FIELD>  7</FIELD><FIELD>7</FIELD></RECORD>
					</RECORDS>
        </OPTION>
      </option>
		</tool>
    <tool library="grid_visualisation" tool="10" name="Select Look-up Table for Grid Visualization">
				<input id="GRID">RECLASSnbr</input>
				<input id="LUT">CLASS_DEF</input>
		</tool>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">first</output>
      <option id="FORMULA">ifelse(lt(g1,$(dNBR_th1)),1,2)</option>
      <option id="NAME">first</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">dNBR</input>
    </tool> 
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">second</output>
      <option id="FORMULA">ifelse(gt(g1,$(dNBR_th2)),3,g2)</option>
      <option id="NAME">$(preapp)_dNBR_gt$(dNBR_th2)_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">dNBR</input>
      <input id="GRIDS">first</input>
    </tool>
    <tool library="shapes_grid" tool="6" name="Vectorising Grid Classes">
      <output id="POLYGONS">dNBR_vector_(class_dNBR_th2)</output>
      <option id="CLASS_ALL">0</option>
      <option id="CLASS_ID">3</option>
      <option id="SPLIT">1</option>
      <option id="ALLVERTICES">false</option>
      <input id="GRID">second</input>
    </tool>
    <tool library="grid_calculus" tool="1" name="Grid Calculator">
      <output id="RESULT">RBR</output>
      <option id="FORMULA">g1 / (g2 + 1.001)</option>
      <option id="NAME">$(preapp)_RBR_$(postappsat)_$(postapppostD)</option>
      <option id="FNAME">false</option>
      <option id="USE_NODATA">false</option>
      <option id="TYPE">7</option>
      <input id="GRIDS">dNBR</input>
      <input id="GRIDS">NBRpre</input>  
    </tool>
  </tools>
</toolchain> 
```
