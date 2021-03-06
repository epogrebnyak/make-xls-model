## Introduction

Excel spreadsheets often [get messy](problem.md). Some help comes form writing aside formulas for the cells 
and separating control parameters from data. 

```make-xls-model``` can populate Excel cells with formulas based on list of equations and can help managing data and control parameters. It generates a stand-alone Excel spreadsheet with no dependencies or VBA code, just new clean formulas in it.

## Simple illustration

GDP forecast value is a multiple of previous year nominal GDP value, deflator (Ip) and real growth rate (Iq): 

```
GDP = GDP[t-1] *Iq * Ip
```

Imagine we want to do this calculation Excel sheet below. In ```C1``` we want a formula ```=B1*C2*C3```.  ```make-xls-model``` can generate this formula based on equation in cell ```A4``` and place it in ```C1```.

|   | A   | B     | C     |
|---|-----|-------|-------|
| 1 | GDP | 23500 |       |
| 2 | Iq  |       | 1,05  |
| 3 | Ip  |       | 1,03  |
| 4 | GDP = GDP[t-1]\*Iq\*Ip  |       |  |

Result:

|   | A   | B     | C     |
|---|-----|-------|-------|
| 1 | GDP | 23500 | 25415 |
| 2 | Iq  |       | 1,05  |
| 3 | Ip  |       | 1,03  |

C1 wil contain ```=B1*C2*C3```

##Interface

```model.py``` is command-line interface to the package:

```
Usage:   
    model.py <xlfile> 
    model.py <xlfile> [--from-dataset | -D] [--slim | -s]
    model.py <xlfile> (--update | -U) [--sheet=<name>]
```

```model.py <xlfile>``` reads inputs from ```data```, ```controls```, ```equations``` and ```names``` 
sheets of ```<xlfile>``` and writes resulting spreadsheet to ```model``` sheet in ```<xlfile>```. 
It overwrites ```model``` sheet in ```<xlfile>``` without warning.

   
|   Option  | Description      |
|-----|------|
|```--from-dataset``` or ``-D`` |  derive 'data' and 'controls' sheets content from 'dataset' sheet |
|```--slim``` or ```-s```   |  produce no extra formatting on 'model' sheet (labels and years only) |
| ```--update``` or ```-U``` |  update Excel formulas on 'model' sheet or other sheet specified in ```[--sheet=<name>]``` |


##Excel file

- The program requires inputs from ```equations```,  ```data```, ```controls``` sheets 
- With ```-D``` key the program will attempt to extract data and controls from ```dataset``` sheet   
- ```names``` sheet with variable names is optional
- Spreadsheet model will be placed to ```model``` sheet


## Examples 

There are several Excel files provided in [examples](examples) folder, invoked by [examples.bat](examples/examples.bat).

## Assumptions and limitations

- uses ```xlwings``` and runs on Windows only, no linux
- annual labels only (continious integers)
- data organised by row only
- one model sheet in file
- a variable appears only once on model sheet

##One-line descriptions

Core functionality (engine):  
Autogenerate formulas in Excel cells based on variable names and list of eqation

Final use (application):
Make clean Excel spreadsheet model with formulas based on historic data, equations and control parameters

##Other repos

Parts of the code may be found in my other repos
- <https://github.com/epogrebnyak/eqcell>
- <https://github.com/epogrebnyak/roll-forward> (private)

## Requirements

The script is executed in [Anaconda](https://store.continuum.io/cshop/anaconda/) environment, we use Python 3.5. Everything runs only on Windows. 

Formal [requirements.txt](requirements.txt) is to follow.

## Possible problems and workarounds

###PyCharm
Using PyCharm one may encounter this error when running `mxm.py`

```
    ...
    from . import _xlwindows as xlplatform
    ...
    import win32api
    ImportError: DLL load failed: ....
```

To cope with it one should add paths like these to the PATH environmental variable

```
    c:\Users\user\Miniconda2\envs\xls3
    c:\Users\user\Miniconda2\envs\xls3\DLLs
    c:\Users\user\Miniconda2\envs\xls3\Scripts
```

and run PyCharm from command line after the right Anaconda environment is activeted. Like this

```
    activate xls3
    "c:\Program Files (x86)\JetBrains\PyCharm 5.0.4\bin\pycharm.exe"
```

The script does not intend to:
- do any forecast calculations outside Excel
- resolve/optimise formulas, including circular references
- spread Excel model to many sheets.
