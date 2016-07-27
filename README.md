##**Canmatrix** is a python package to read and write several CAN (Controller Area Network) database formats.##

###About

*Canmatrix* implements a "Python Can Matrix Object" which describes the can-communication and the needed objects (Boardunits, Frames, Signals, Values, ...)
*Canmatrix* also includes two **Tools** (canconvert and cancompare) for converting and comparing **CAN** databases.

There are several importers (read) and exporters (write) for this object.

supported file formats for import:
* .dbc candb / [Vector](http://vector.com)
* .dbf [Busmaster](https://rbei-etas.github.io/busmaster/) (open source!)
* .kcd [kayak](http://kayak.2codeornot2code.org/) (open source!)
* .arxml [autosar](http://autosar.org) system description
* .yaml dump of the python object
* .xls(x) excel xls-import, works with .xls-file generated by this lib
* .sym [peak](http://www.peak-system.com) pcan can description
 
supported file formats for export:
 * .dbc 
 * .dbf
 * .kcd
 * .xls(x)
 * .json [Canard](https://github.com/ericevenchick/CANard) (open source!)
 * .arxml (very basic implementation)
 * yaml (dump of the python object)
 * sym

***

###Install instructions
Install *canmatrix* with either "pip install canmatrix" or  "python setup.py install" and "pip install -r requirements.txt"

This installs the *canmatrix* package into your python installation.

In addition to the *canmatrix* package there are 2 scripts installed with this package:

1. **canconvert**: 

	converts CAN database formats between all supported formats. 
	
	Example: "*canconvert* someArSystemdescription.arxml file.dbc"
	
2. **cancompare**: 

	compares 2 CAN databases (of different or same format). 
	
	Example: "*cancompare* file1.dbc file2.dbc"
	
If you are using a *NIX-System, these scripts should be callable from command line

If you are using a Windows system, these scripts are usually installed at the location of your python installation. For example 'C:\python3.4\Scripts' or 'C:\python2.7\Scripts'

***

###Dependencies


*Canmatrix* depends on:
 * xlrd ([home](http://www.python-excel.org/), [PyPI](https://pypi.python.org/pypi/xlrd))
 * xlwt-future ([PyPI](https://pypi.python.org/pypi/xlwt-future))
 * XlsxWriter ([GitHub](https://github.com/jmcnamara/XlsxWriter), [PyPI](https://pypi.python.org/pypi/XlsxWriter)
 * PyYAML ([PyPI](https://pypi.python.org/pypi/pyaml))
 * lxml ([PyPI](https://pypi.python.org/pypi/lxml))

***
###Examples

There are some example python scripts in the example-folder


***
###Usage

####General

```canconvert.py -h```
```canconvert.py --help```
			show help message/usage and exits

```canconvert.py -v```
  			Output verbosity
  			
```canconvert.py -s```
  			don't print status messages to stdout. (only errors)
  			
```canconvert.py -f FORCE_OUTPUT```
    			enforce output format, ignoring output file extension (e.g., -f csv)

				
####Conversion of file formats

**convert dbc file to xlsx:**

```canconvert.py source.dbc target.xlsx```

**convert dbc file to dbf:**

```canconvert.py source.dbc target.dbf```

**convert arxml file to dbc:**

```canconvert.py source.arxml target.dbc```

Note: in case of .arxml there can be multiple can databases in. 
Thus the target ```target.dbc``` may in this case be called ```BUS-NAME-IN-ARXML_target.dbc```. 
There will be one target .dbc for each database in .arxml.

You can even convert to the same format:

**convert dbc file to dbc:**

```canconvert.py source.dbc target.dbc```

Multiple charset support:

**convert dbc file to dbc with different charset:**

```canconvert.py --dbcImportEncoding=iso-8859-1 --dbcImportCommentEncoding=cp-1252 --dbcExportEncoding=utf-8 --dbcExportCommentEncoding=utf-8 source.dbc target.dbc```

This converts ```source.dbc``` where units are coded in ```iso-8859-1``` and comments are coded in ```cp-1252``` in a ```target.dbc``` where everything is coded in ```utf-8```.
Similar charset conversions are possible or even mandatory for following formats: dbc, dbf and sym.


####possible Modifications:

**delete zero sized signals:**

```canconvert.py --deleteZeroSignals source.dbc target.dbc```

will delete signals with 0 bit length from matrix

**delete unwanted attributes from signals:**

```convert.py --deleteSignalAttributes GenMsgCycleTime,MyAttrib source.dbc target.dbc```

will delete the attributes ```GenMsgCycleTime``` and ```MyAttrib``` from all signals in ```source.dbc``` and save the result in ```target.dbc```

**delete unwanted attributes from frames:**

```convert.py --deleteFrameAttributes GenMsgCycleTime,MyAttrib source.dbc target.dbc```

will delete the attributes ```GenMsgCycleTime``` and ```MyAttrib``` from all frames in ```source.dbc``` and save the result in ```target.dbc```

**recalculate DLC:**

```canconvert.py --recalcDLC=max source.dbc target.dbc```

this will recalculate DLC for each frame in ```source.dbc```. 
In ```target.dlc```  the same DLC like in ```source.dbc``` will be stored, except the calculated DLC is bigger.
Than the calculated DLC will be stored.

**recalculate DLC:**

```canconvert.py --recalcDLC=force source.dbc target.dbc```

this will recalculate DLC for each frame in ```source.dbc```. 
In ```target.dlc``` the calculated DLC will be stored independently from ```source.dbc```.


####Extract and Merge:

**extract one ecu out of matrix**

```canconvert.py --ecus=REAR_ECU source.dbc target.dbc```

This generates a ```target.dbc``` with all Informations out of ```source.dbc``` which are needed for ```REAR_ECU```.
All frames which are received or sent by ```REAR_ECU``` are extracted. Also all attributes of the frames and the ECU.
This is some *lite* ECU-Extract.

**extract multiple ecus out of matrix:**

```canconvert.py --ecus=FRONT_ECU,REAR_ECU source.dbc target.dbc```

**extract frame[s] out of matrix:**

```canconvert.py --frames=REAR_FRAME,FRONT_FRAME source.dbc target.dbc```

Extracts the frames ```REAR_FRAME``` and ```FRONT_FRAME``` with the needed ECUs and attributes.

**merge multiple databases:**

```canconvert.py --merge=second.dbc source.dbc target.dbc```

Merges ```source.dbc``` and ```second.dbc``` in ```target.dbc```.

**merge ECU from other  database:**

```canconvert.py --merge=second.dbc:ecu=REAR_ECU source.dbc target.dbc```
 
Merges REAR_ECU out of ```second.dbc``` with ```source.dbc``` and store result in ```target.dbc```.

**merge FRAME from other database:**

```canconvert.py --merge=second.dbc:frame=REAR_FRAME source.dbc target.dbc```
 
Merges REAR_FRAME out of ```second.dbc``` with ```source.dbc``` and store result in ```target.dbc```.

**combinations and multiple extraction possible:**

```canconvert.py second.dbc:ecu=REAR_ECU:ecu=FRONT_ECU:frame=FRAME1:FRAME=FRAME2 source.dbc target.dbc```

Merges REAR_ECU and FRONT_ECU and FRAME1 and FRAME2 out of ```second.dbc``` with ```source.dbc``` and store result in ```target.dbc```.

***

###Commandline 
* all formats support im-/export of signals and frames

  -h, --help            
			
			show help message and exit

  -v                    
  
  			Output verbosity
  			
  -s                    
  
  			don't print status messages to stdout. (only errors)
  			
  -f FORCE_OUTPUT       
  
  			enforce output format, ignoring output file extension (e.g., -f csv)
                        
  --deleteZeroSignals  
  
  			delete zero length signals (signals with 0 bit length) from matrix default False
                        
  --recalcDLC=RECALCDLC
  
                        recalculate dlc; max: use maximum of stored and calculated dlc; force: force new calculated dlc

  --jsonExportCanard    
  			
  			Export Canard compatible json format
  			
  --ecus=ECUS           
  
  			Copy only given ECUs (comma separated list) to target matrix
  			
  --frames=FRAMES
  			
  			Copy only given Framess (comma separated list) to target matrix
  			
  --merge=MERGE         
  
  			merge additional can databases. Syntax: --merge filename[:ecu=SOMEECU][:frame=FRAME1][:frame=FRAME2],filename2

* dbc: 
  
  --dbcImportEncoding

                        Import charset of dbc (relevant for units), maybe utf-8 default iso-8859-1

  --dbcImportCommentEncoding

                        Import charset of Comments in dbc default iso-8859-1

  --dbcExportEncoding

                        Export charset of dbc (relevant for units), maybe utf-8 default iso-8859-1

  --dbcExportCommentEncoding

                        Export charset of comments in dbc default iso-8859-1
 

* dbf:

  --dbfImportEncoding

                        Import charset of dbf, maybe utf-8 default iso-8859-1

  --dbfExportEncoding

                        Export charset of dbf, maybe utf-8 default iso-8859-1
* sym:

  --symImportEncoding

                        Import charset of sym format, maybe utf-8 default iso-8859-1

  --symExportEncoding

                        Export charset of sym format, maybe utf-8 default iso-8859-1

* xls(x):

  --xlsMotorolaBitFormat
  
                        Excel format for startbit of motorola coded signals. Valid values: msb, lsb, msbreverse default msbreverse. [more about starbits...](https://github.com/ebroecker/canmatrix/wiki/signal-Byteorder)


 
* arxml:

  --arxmlIgnoreClusterInfo

                        Ignore any can cluster info from arxml; Import all frames in one matrix default 0


* yaml
* json:

 --jsonExportCanard  
 
                        Export Canard compatible json format
	
* kdc
* csv

## Export

|Format|ECUs|Mux  |S/R of Signal|Cycletype|Cycletime|Baudrate |Extended|Byteorder|scaling|min/max|attributes|value tables|signal groups| 
|---|----|----|----------|---------|---------|---------|--------|---------|-------|---|---|---|---|
|arxml |    |+    |		 |         |         |+        |+       |+        |       |       |          |            |             |
|dbc   |+   |+	   |+    	 |+        |+        |         |+       |+        |+      |+      |+         |+           |+            |
|dbf   |+   |+    |+		 |+        |+        |         |+       |+        |+      |+      |+         |+           |            |
|json  |    |	   |		 |         |         |         |        |         |+      |       |          |            |             |
|kcd   |+   |+    |		 |+        |+        |+        |        |         |       |       |          |+           |             |
|sym   |    |+	   |		 |         |         |         |+       |+        |+      |+      |          |+           |             |
|xls(x)|+   |+	   |+		 |+        |+        |         |        |+        |+      |+      |          |+           |             |
|csv |+   |+	   |+		 |+        |+        |         |        |+        |+      |+      |*          |+           |             |



## Import
|Format|ECUs|Mux  |S/R of Signal|Cycletype|Cycletime|Baudrate |Extended|Byteorder|scaling|min/max|attributes|value tables|signal groups| 
|---|----|----|----------|---------|---------|---------|--------|---------|-------|---|---|---|---|
|arxml |+   |+     |+	         |+        |+        |+        |+       |+        |+      |+      |          |+           |+            |
|dbc   |+   |+	   |+    	 |+        |+        |         |+       |+        |+      |+      |+         |+           |+            |
|dbf   |+   |+     |+		 |+        |+        |         |+       |+        |+      |+      |+         |+           |             |
|kcd   |+   |+     |		 |+        |+        |         |        |         |       |       |          |+           |             |
|sym   |    |+	   |		 |         |         |         |+       |+        |+      |+      |          |+           |             |
|xls(x)|+   |+	   |+		 |+        |+        |         |        |+        |p      |p      |          |p           |             |



***


Have Fun,

feel free to contact me for any suggestions

Eduard

