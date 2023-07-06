# soliddriver-checks

```soliddriver-checks is``` a tool for ```KMP(Kernel Module Package)``` and ```installed/running kernel module``` checking, with this tool, users can have an report of their KMP(s) and KM status (the report can be formatted in ```html```, ```excel``` and ```json```), the tool can be run as command line tool or service:

##### Note: This tool only check the KMP and KM are built meet SUSE’s requirement, don’t look into the code perspective. So any bugs or security related issues inside the kernel module can not be found.

- KM: kernel module
- KMP: kernel module package

## Installation:
- pypi: ```pip install soliddriver-checks```
- zypper: the rpms can be found on [build.opensuse.org](https://build.opensuse.org/package/show/home:huizhizhao:soliddriver-checks/soliddriver-checks)

## How should you address the issues?
You should contact your IHVs to rebuild the KMP for you to meet the requirement of SUSE, and ask them to to follow the [Kernel Module Packages Manual](https://drivers.suse.com/doc/kmpm/) or contact SUSE directly if they don’t know how to do it.
- Highlight in yellow: Warning, this is not necessary but have potential issue, like no signature, not GPL compatible license and etc.
- Highlight in red: Error, could cause issue during the installation, after kernel upgrade or during the kernel module is running and etc.

### KMP Report:
- “Summary of the result from vendor perspective”: Summarize the number of check failure. 
- “Check result in details”: Show the detail of every KMP been checked by this tool.

### KM Report:
- Show the detail of every kernel module is running or founded under ```“/lib/modules”```.

```
Usage: soliddriver-checks [OPTIONS] [CHECK_TARGET]

  Run checks against CHECK_TARGET.

  CHECK_TARGET can be:
    KMP file
    directory containing KMP files
    "system" to check locally installed kernel modules

Options:
  -f, --format [html|xlsx|json]  Specify output format
  -i, --filter TEXT              Filter kernel module to report (Beta)
  -o, --output TEXT              Output destination. Target can be filename or
                                 point existing directory If directory, files
                                 will be placed in the directory using a
                                 autmatically generated filename. If target is
                                 not an existing directory, file name is
                                 assumed and output files will use the path
                                 and file name specified. In either case, the
                                 file extension will be automatically appended
                                 matching on the output format
  --version
  --help                         Show this message and exit.
```

filter:
“key” [ = | != | match | no ] “value”</br>
and | or</br>
( )</br>
EBNF grammer can be found in filter.py
- All the ```“key”``` can be found in the json file generated by this tool, they are:</br>
  - For Kernel Module:
    ```[ modulename | filename | license | signature | supported | running | kmp ]```

    - modulename -> Name of the kernel module
    - filename   -> File path
    - license.   -> License
    - signature  -> Signature
    - supported  -> Value of “supported” flag
    - running    -> Running status (True | False)
    - kmp        -> Installed by which KMP
  - For Kernel Module Package:
  ```[ name | path | vendor | signature | license | wm2_invoked | supported_flag | km_signatures | km_licenses | symbols | modalias]```
    - name           -> Name
    - path           -> Path
    - vendor         -> Vendor
    - signature      -> Signature
    - license        -> License
    - wm2_invoked    -> Weak Module Invoked
    - supported_flag -> Supported Flag (Kernel Module)
    - km_signatures  -> Signatures (Kernel Module)
    - km_licenses    -> Licenses (Kernel Module)
    - symbols        -> Symbols (Kernel Module)
    - modalias       -> Modalias (Kernel Module)

- match: match only take the python regex pattern.

  - Search by evaluation of ```soliddirver-checks```:</br>
 ```“key.level” [ = | != ] “PASS” | “WARNING” | “ERROR”```
  - If you want to search by row evaluation, the ```“key”``` should be ```“level.level”```:</br>
 ```“level.level” [ = | != ] “PASS” | “WARNING” | “ERROR”```

#### Examples:
- Check KMPs under a directory, and generated a HTML report:</br>
    ```soliddriver-checks /path/to/kmps -f html -o [report-name].html```
- Check current system’s KM, and generated a excel report:</br>
    ```soliddriver-checks -f xlsx -o [report-name].xlsx```
- Run soliddirver-checks as service:</br>
    ```export REFRESH_INTERVAL=12 # data will be refreshed every 12 hours.```
    ```soliddriver-checks-service```
- Generate report from remote (make sure you have the server running remotely), and generated a HTML report:</br>
    ```soliddriver-checks http://remote-ip:8080/kms_info -f html -o [report-name].html```
- Generate a kernel module report only contain which are not meet SUSE requirement:</br>
    ```soliddriver-checks -f html -o [report-name].html -i ‘“level.level” != “PASS”’```





