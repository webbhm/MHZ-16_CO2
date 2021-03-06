# MH-Z16_CO2
Python code for the [Sandbox Electronics CO2 sensor](http://sandboxelectronics.com/?product=mh-z16-ndir-co2-sensor-with-i2cuart-5v3-3v-interface-for-arduinoraspeberry-pi)
This code is for integrating the MH-Z16 sensor and I2C interface board (IC SC16I5705) with the OpenAg MVP.  The original code came from [here](https://github.com/SandboxElectronics/NDIR/tree/master/NDIR_RasPi_Python)

##Instructions:
### Sensor Installation

The CO2 sensor, with the interface card, works off of the I2C bus (same as the temp/humidity sensor).  To use it, simply wire it in with the other I2C sensors, matching the power, gnd, SCA and SCL wires.
The default address is 0x4D, though it can be changed through some soldering.  The code defaults to this value, but it can be changed by supplying an address parameter to the constructor:
```
sensor=Sensor(0x4d)
```
### Software Installation
This code requires time and smbus, which should already be in the python library.
Copy the following python files to your python directory.
  - NDIR.py
  - getCO2Chart.py
    
Two example files are included in the repository.  These are the 'base' MVP code plus the code changes for this sensor.  If this is the only addes sensor, you can rename these files and replace the existing MVP files with them.  The example files are:  

  - logSensors_example.py
  - index-example.html
  - render-example.sh
  
For ver 1.0 the python directory is `/home/pi/Documents/OpenAg-MVP/python`
For later versions, the directory is `/home/pi/MVP/python`

Open logSensors.py in a text editor and make the following changes:

At the top of the file add:
```
import NDIR
```
At the bottom of the file add:
```
try:
    sensor=NDIR.Sensor(0x4D)
    sensor.begin()
    logData("CO2 Concentration", "Success", "co2", str(sensor.getCO2()), '')
 exception (IOError):
    logData("CO2 Concentration", "Failure", "co2", str(IOError))
```
To get the data into the UI charts requires modifying the render.sh script, and the index.html.
In version 1.0 the script will be in /home/pi/MVP_UI/scripts/
For later versions it will be in /home/pi/MVP/scripts

index.html, for version 1.0 is in /home/pi/Documents/OpenAg-MVP/web
for later versions, it is in /home/pi/MVP/web
python "
The render script collects the data and runs the code to build the charts.  In this case it will be the co2_chart.svg.
If not already at the top of the script file, add (for ver 1.0):

> python_dir="/home/pi/Directory/OpenAg-MVP/python/

Then at the bottom of the file add:

>python "$python_dir"getCO2Chart.py

To the index.html, make the following modification:
Under the <div class="tab">, add a line like the others, but with the CO2 information.  NOTE: the sequence of these lines determines the order of the tabs.  This line creates a button that invokes the java script to display the the appropriate html.
> <button class="tablinks" onclick="openTab(event, 'CO2')">CO2</button>
The following is the html that will be displayed, copy it anywhere with the other tab division code:
```
<div id="CO2" class="tabcontent">
    <h3>CO2 Concentration</h3>
    <p>Reading of the CO2 sensor</p>
      <object type="image/svg-xml" data="co2_chart.svg"/>
        "Your browser does not support SVG
      </object>
</div>
```

### Testing
To test the sensor and code, double click on the NDIR.py file to open in in the python IDE.  Then run it by clicking on "Run"/"Run Module".  You will not see any return information, but just a prompt.  At the prompt, type in the following:
```
s=Sensor()
s.test()
```
You shuold see several lines of CO2 readings.
Work your way up the stack by then running from the command line:
For ver 1.0
```
python /home/pi/Documents/OpenAg-MVP/python/getCO2Chart.py
python /home/pi/Documents/OpenAg-MVP/python/logSensors.py
bash /home/pi/MVP_UI/scripts/render.sh
```
For later versions:
```
python /home/pi/MVP/python/getCO2Chart.py
python /home/pi/MVP/python/logSensors.py
bash /home/pi/MVP/scripts/render.sh
```  
  - double click on index.html to display it in a browser view the chart
