# WEBSRVUTL
Webservice Utilities running on IBM i for providing superfast Web Services and running Webapplications based on AJAX-Requests

## Description

The Library WEBSRVUTL with the Service Program WERSRVUTL gives RPG-Programmers a fast and easy way to provide Web Services powered by IBM i.

Rainer Ross is the developer of the hotel search engine www.myhofi.com - this application is powered by IBM i, built with HTML5, CSS3, JavaScript, Db/2 inMemory, Symmetric Multiprocessing, Watson Content Analytics and runs on the server side with pure free RPG Web Services. myhofi.com was awarded in 2015 with the IBM Innovation Award.

## ReadyToClickExamples

Providing JSON www.myhofi.com/myapp/websrv01.pgm?id=1
```
{
    "success": true,
    "errmsg": "",
    "items": [
        {
            "id": 1,
            "name": "MINERALÖL-TANKSTELLE",
            "country": "DE",
            "zip": "12559",
            "city": "BERLIN",
            "street": "GOETHESTR. 8",
            "sales": 535647.59,
            "credit": 5000.00,
            "balance": 1650.00,
            "date": "2015-02-06"
        }
    ]
}
```

Providing XML www.myhofi.com/myapp/websrv02.pgm?id=1
```
<data>
	<item>
		<id>1</id>
		<name>MINERALÖL-TANKSTELLE</name>
		<country>DE</country>
		<zip>12559</zip>
		<city>BERLIN</city>
		<street>GOETHESTR. 8</street>
		<sales>535647.59</sales>
		<credit>5000.00</credit>
		<balance>1650.00</balance>
		<date>2015-02-06</date>
	</item>
</data>
```

Webapplication with AJAX-Request to the JSON-Webservice www.myhofi.com/devhtm/websrv03.html

![capture20170813131922025](https://user-images.githubusercontent.com/10383523/29249116-26fb77dc-802a-11e7-8545-9011d20df3f0.png)

* The AJAX-Request is powered by the JavaScript UI-Library www.webix.com
```
webix.ajax().post("/myapp/websrv01.pgm", {id:0},
    function(text, data) {
    }
);
```

## Super simple to use

* Read the HTTP Environment Variables `getenv()`
* Get Input from HTTP-Server `getInput()`
* Get KeyValue from Input-Data `getKeyVal()`
* Create HTTP-Header `getHeader()`
* Read Data from the HTTP-Server `readStdin()`
* Write Data to the HTTP-Server `wrtStdout()`

## How to use in your RPG-Program

```
//------------------------------------------------------------------//
// Main                                                             //
//------------------------------------------------------------------//
  dcl-proc main;                                                  
                                                                
  dcl-s   LocHeader   like(GblHeader);         // HTTP-Header     
  dcl-s   LocId       like(Id);                // Id              
                                                                
    LocHeader = getHeader(JSON);               // Get HTTP-Header 
                                                                
    getInput();                                // Get Input       
  
    monitor;                                                  
     LocId = %dec(getKeyValue('Id'):10:0);     // Get Id      
     on-error;                                                
     LocId = *zero;                                          
    endmon;
  
    LocLen = crtjson(LocJson_p:LocId);	       // Create JSON-Data
  
    wrtStdout(%addr(LocHeader:*data):%len(LocHeader):DsApierr);    
    wrtStdout(LocJson_p:LocLen:DsApierr);      // Send HTTP-Data 
    
  end-proc;  
//------------------------------------------------------------------//
```

### Procedure `getenv()` read the HTTP Environment Variables - [Useful Link](http://www.easy400.net/cgidev2o/exhibit6.htm)
```
LocMethod  = %str(getenv('REQUEST_METHOD':DsApierr)); // Result GET or POST
```

### Procedure `getInput()` reads the input from GET and POST Requests and parse the Input Data in Keys and Values

* The maxlength of a key is 128 bytes. The key can be upper case, lower case and mixed case
* The maxlength of a value is 1,000,000 bytes
* The number of variables is 100, when you need more variables then change https://github.com/RainerRoss/WEBSRVUTL/blob/master/QCPYSRC/WEBSRVUTL.RPGLE in line 59 to your own number of needed variables like this
```
 dcl-ds DsKeyVal qualified dim(500) inz; 
```
#### When the Environment Variable is not delivered then put the command in a Monitor Statement like this
```
Monitor;
 LocAuth  = %str(getenv('AUTH_TYPE':DsApierr)); // Authentification Type
 on-error;
End-Mon;
```

### Procedure `getKeyVal()` get KeyValue from Input-Data 

Example GET-Request `http://www.mycompany/com/myapp/request.rpg?id=5&name=Ross&city=Munich`
* `?` -> Starts the Query_String -> the Input Parameters
* `&` -> Separator Key=Value&Key=Value

```
Key 	Value
--------------
id	5
name	Ross
city	Munich
```
In your RPG-Program
```
Dcl-S  Id	int(10);  
Dcl-S  Name	varchar(30);
Dcl-S  City	varchar(30);

Monitor;
 Id = %dec(getKeyVal('id'):10:0); // Authentification Type
 on-error;
End-Mon;
Name = getKeyVal('name');
City = getKeyVal('city'); 
```

### Procedure `getHeader()` generates the HTTP-Header

* Generates a Text-HTTP Header `Header = getHeader(TEXT)`
* Generates a JSON-HTTP Header `Header = getHeader()` or `Header = getHeader(JSON)`
* Generates a XML-HTTP Header `Header = getHeader(XML)`


### Procedure `wrtStdout()` writes Data to the HTTP-Server

#### The Procedure has three Parameters
```
* Data	      Pointer	
* Data-Length int(10)
* Error       Array
```
#### Example Char(256)
```
Dcl-S  MyData  char(256) ccsid(*UTF8);

MyData = 'HelloWorld';
wrtStdout(%addr(MyData):%len(%trimr(MyData)):DsApierr); 
```
#### Example VarChar(256)
```
Dcl-S  MyData  varchar(256) ccsid(*UTF8);

MyData = 'HelloWorld';
wrtStdout(%addr(MyData:*data):%len(MyData):DsApierr); 
```

## Software Prerequisites

License Programs

* 5770SS1 Option 3 – Extended Base Directory Support
* 5770SS1 Option 12 – Host Servers
* 5770SS1 Option 30 – Qshell
* 5770SS1 Option 33 – PASE
* 5770SS1 Option 34 – Digital Certificate Manager
* 5770SS1 Option 39 – Components for Unicode
* 5770TC1 - TCP/IP	
* 5770JV1 - Java
* 5770DG1 – HTTP-Server: Apache 2.4.12

Non-License Software (open source)

* YAJL from Scott Klement (create and parse JSON) - Download [here](http://www.scottklement.com/yajl/)

## How to install

* Create a library  `CRTLIB LIB(WEBSRVUTL) TEXT('Webservice Utilities')`
* Create a source physical file `CRTSRCPF FILE(WEBSRVUTL/QCLPSRC)`
* Create a source physical file `CRTSRCPF FILE(WEBSRVUTL/QCPYSRC)`
* Create a source physical file `CRTSRCPF FILE(WEBSRVUTL/QMODSRC)`
* Copy the files from `QCLPSRC, QCPYSRC, QMODSRC` to your SRCPF's
* Compile the CL-Program `CRTBNDCL PGM(WEBSRVUTL/WEBSRVUTLC) SRCFILE(WEBSRVUTL/QCLPSRC)`
* Create the Binding Directory and the Service Program `CALL PGM(WEBSRVUTL/WEBSRVUTLC)` 

## Start and stop the HTTP-Server ADMIN Instance

* Start HTTP-Admin  `STRTCPSVR SERVER(*HTTP) HTTPSVR(*ADMIN)`
* Stop HTTP-Admin `ENDTCPSVR SERVER(*HTTP) HTTPSVR(*ADMIN)`

## Create a new HTTP-Server Instance `MYSERVER`

* Open your browser and start the IBM i HTTP-Admin: http://yourIP:2001/HTTPAdmin
* Create the new HTTP-Server Instance
```
Server name:        MYSERVER
Server description: My new Webserver
Server root:        /www/myserver
Document root:      /www/myserver/htdocs
IP address:         All IP addresses
Port:               8010
Log directory:      /www/myserver/logs
Access log file:    access_log
Error log file:     error_log
Log maintenance     7 days
```
* Start HTTP-Server Instance MYSERVER `STRTCPSVR SERVER(*HTTP) HTTPSVR(MYSERVER)`

* Verify that MYSERVER is running `WRKACTJOB SBS(QHTTPSVR)`
![capture20170813140950764](https://user-images.githubusercontent.com/10383523/29249537-6410cea4-8031-11e7-8c9f-0edefbefac4a.png)

* Call the example Homepage from your browser `http://yourIP:8010/index.html`

## Create your first website

* Open your favorite editor create a new file named `MyFirstWebsite.html` in the `/www/myserver/htdocs` folder and copy https://github.com/RainerRoss/WEBSRVUTL/blob/master/HTML/MyFirstWebsite.html into the `MyFirstWebsite.html` file
#### Make sure that `MyFirstWebsite.html` has the CCSID 1208 (UTF-8)
* Show the files in the folder htdocs `wrklnk '/www/myserver/htdocs/*'`
* Select 8 on `MyFirstWebsite.html` and check the CCSID
* Change the CCSID `CHGATR OBJ('/www/myserver/htdocs/MyFirstWebsite.html') ATR(*CCSID) VALUE(1208)`
* Call `MyFirstWebsite.html` from your browser `http://yourIP:8010/MyFirstWebsite.html`

## Create your first app

* Open your favorite editor create a new file named `MyFirstApp.html` in the `/www/myserver/htdocs` folder and copy https://github.com/RainerRoss/WEBSRVUTL/blob/master/HTML/MyFirstApp.html into the `MyFirstMyFirstApp.html` file
#### Make sure that `MyFirstApp.html` has the CCSID 1208 (UTF-8)
* Show the files in the folder htdocs `wrklnk '/www/myserver/htdocs/*'`
* Select 8 on `MyFirstApp.html` and check the CCSID
* Change the CCSID `CHGATR OBJ('/www/myserver/htdocs/MyFirstApp.html') ATR(*CCSID) VALUE(1208)`
* Call `MyFirstApp.html` from your browser `http://yourIP:8010/MyFirstApp.html`

## Create your first HelloWorld Web Service
* Create a library  `CRTLIB LIB(MYAPP) TEXT('My Web Applications')`
* Create a source physical file `CRTSRCPF FILE(MYAPP/QRPGSRC)`
* Create a source physical file `CRTSRCPF FILE(MYAPP/QSQLSRC)`
* Copy the file https://github.com/RainerRoss/WEBSRVUTL/blob/master/Examples/HelloWorld.RPGLE to your SRCPF
* Add MYAPP and WEBSRVUTL to your Library List `ADDLIBLE LIB(MYAPP) POSITION(*LAST) ADDLIBLE LIB(WEBSRVUTL) POSITION(*LAST)`
* Compile the program `CRTBNDRPG PGM(MYAPP/HELLOWORLD) SRCFILE(MYAPP/QRPGSRC)`

### Some modifications on the HTTP-Server Instance `MYSERVER` to run Web Services
* Open HTTP-Admin from your Browser `http://yourIP:2001/HTTPAdmin -> all Servers -> MYSERVER -> Tools -> Edit configuration`
* Insert 
```
DefaultNetCCSID 1208
```
* Check the IBM i CCSID `DSPSYSVAL QCCSID` when the CCSID is 65535 then insert the following line depending on your CCSID e.g. US = 37, DE = 1141
```
DefaultFsCCSID 37
```
* Insert these lines for the ability to run Web Services from Library `MYAPP`
```
ScriptAliasMatch /myapp/(.*)  /qsys.lib/myapp.lib/$1

<Directory /qsys.lib/myapp.lib>
  SetEnv QIBM_CGI_LIBRARY_LIST "MYAPP;WEBSRVUTL;YAJL"
  Require all granted
</Directory>
```
* Insert these lines for the ability to run Web Services from Library `MYAPP` and Basic Authentication against the IBM i User Profiles
```
<Directory /qsys.lib/myapp.lib>
  SetEnv QIBM_CGI_LIBRARY_LIST "MYAPP;WEBSRVUTL;YAJL"
  AuthType Basic
  AuthName "My Applications"
  PasswdFile %%SYSTEM%%
  UserID %%CLIENT%%
  Require valid-user
</Directory>  
```

* When you want GZIP the data from server to browser then insert the following lines
```
#=========================================================================
# GZIP Options
#=========================================================================
# Deflate Module
LoadModule deflate_module /QSYS.LIB/QHTTPSVR.LIB/QZSRCORE.SRVPGM
# Insert Filter for Content Types except Images
AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json application/xml
#
SetEnvIf User-Agent ^Mozilla/4 gzip-only-text/html
SetEnvIf User-Agent ^Mozilla/4\.0[678] no-gzip
SetEnvIf User-Agent \bMSIE !no-gzip
SetEnvIf User-Agent \bMSIE !gzip-only-text/html
SetEnvIf User-Agent \bMSI[E] !no-gzip
SetEnvIf User-Agent \bMSI[E] !gzip-only-text/html
#
# Compression Level Highest 9 - Lowest 1
DeflateCompressionLevel 3
#
#=========================================================================
# E-Tags
#=========================================================================

Header unset ETag
FileETag None

#=========================================================================
```
* Stop HTTP-Server Instance MYSERVER `ENDTCPSVR SERVER(*HTTP) HTTPSVR(MYSERVER)`
* Start HTTP-Server Instance MYSERVER `STRTCPSVR SERVER(*HTTP) HTTPSVR(MYSERVER)`
* Call `HelloWorld` from your browser `http://yourIP:8010/myapp/HelloWorld.pgm`

## Create a Web Service to provide JSON-Data
* Copy the file https://github.com/RainerRoss/WEBSRVUTL/blob/master/Examples/Customer.sql to your SRCPF in `MYAPP/QSQLSRC`
* Create the Physical File Customer `RUNSQLSTM SRCFILE(MYAPP/QSQLSRC) SRCMBR(CUSTOMER)` and fill this File with data or copy your own Customer Physical File to the Library `MYAPP`
* Add MYAPP, WEBSRVUTL and YAJL to your Library List `ADDLIBLE LIB(MYAPP) POSITION(*LAST) ADDLIBLE LIB(WEBSRVUTL) POSITION(*LAST) ADDLIBLE LIB(YAJL) POSITION(*LAST)`
* Copy the file https://github.com/RainerRoss/WEBSRVUTL/blob/master/Examples/WEBSRV01.RPGLE to your SRCPF in `MYAPP/QRPGSRC`
* Compile the program `CRTBNDRPG PGM(MYAPP/WEBSRV01) SRCFILE(WEBSRV01/QRPGSRC)`
* Call `WEBSRV01` from your browser `http://yourIP:8010/myapp/Websrv01.pgm?id=1`

## Create a Web Service to provide XML-Data
* Copy the file https://github.com/RainerRoss/WEBSRVUTL/blob/master/Examples/Customer.sql to your SRCPF in `MYAPP/QSQLSRC`
* Create the Physical File Customer `RUNSQLSTM SRCFILE(MYAPP/QSQLSRC) SRCMBR(CUSTOMER)` and fill this File with data or copy your own Customer Physical File to the Library `MYAPP`
* Add MYAPP, WEBSRVUTL and YAJL to your Library List `ADDLIBLE LIB(MYAPP) POSITION(*LAST) ADDLIBLE LIB(WEBSRVUTL) POSITION(*LAST) ADDLIBLE LIB(YAJL) POSITION(*LAST)`
* Copy the file https://github.com/RainerRoss/WEBSRVUTL/blob/master/Examples/WEBSRV02.RPGLE to your SRCPF in `MYAPP/QRPGSRC`
* Compile the program `CRTBNDRPG PGM(MYAPP/WEBSRV02) SRCFILE(WEBSRV02/QRPGSRC)`
* Call `WEBSRV02` from your browser `http://yourIP:8010/myapp/Websrv02.pgm?id=1`
