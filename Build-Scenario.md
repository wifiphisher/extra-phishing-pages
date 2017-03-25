# Creating a custom phishing scenario
-----------------------------------

For specific target-oriented attacks, custom scenarios may be necessary.

### Creating a phishing scenario is easy and consists of two steps:

1) Create the config.ini
  A config.ini file lies in template's root directory and its contents can be divided into two sections:

  i) info: This section defines the scenario's characteristics.
  - Name (mandatory): The name of the phishing scenario
  - Description (mandatory): A quick description (<50 words) of the scenario
  - PayloadPath (optional): If the phishing scenario pushes malwares to victims, users can insert the absolute path of  the malicious executable here

ii) context: This section is optional and holds user-defined variables that may be later injected to the template.

#### Example
-------

Here`s an example of a config.ini file:
```
 # This is a comment
 [info]
 Name: ISP warning page
 Description: A warning page from victim`s ISP asking for DSL credentials
 [context]
 victim_name: John Phisher
 victim_ISP: Interwebz
```

2) Create the template files
  A template contains the static parts of the desired HTML output and may consist of several static HTML files, images, CSS or Javascript files. Dynamic languages (e.g. PHP) are not supported.

### Placeholders
------------

The HTML files may also contain some special syntax (think placeholders) describing how dynamic content will be inserted. The dynamic contect may originate from two sources:

  i) Beacon frames. Beacon frames contain all the information about the target network and can be used for information  gathering. The main process gathers all the interesting information and passes them to the chosen template on the   runtime.

At the time of writing, the main process passes the following data:

  ```target_ap_essid``` : The ESSID of the target Access Point<br/>
  ```target_ap_bssid``` : The BSSID (MAC) address of the target Access Point<br/>
  ```target_ap_channel``` : The channel of the target Access Point<br/>
  ```target_ap_vendor``` : The vendor's name of the target Access Point<br/>
  ```target_ap_logo_path``` : The relative path of the target Access Point vendor's logo in the filesystem<br/>
  ```APs``` : A list containing AP dictionaries of the Access Points captured during the<br/>
      ```AP``` : A dictionary holding the following information regarding an Access Point:<br/>
          ```channel``` : The channel of the Access Point<br/>
          ```essid```  :The ESSID of the Access Point<br/>
          ```bssid```  :The BSSID (MAC) address of the Access Point<br/>
          ```vendor```  :The vendor's name of the Access Point<br/>

  Note that the above values may be 'None' accordingly. For example, all the target_* values will be None if there user did not target an Access Point (by using --essid option). The 'target_ap_logo_path' will be None if the logo of the specific vendor does not exist in the repository.

  ii) The config.ini file (described above). All the variables defined in the "Context" section may be used from within the template files.

  In case of naming conflicts, the variables from the configuration file will override those coming from the beacon frames.

### Logging credentials
-------------------

In order for wifiphisher to know which credentials to log, the values of the 'name' HTML attributes need to be prefixed with the 'wfphshr' string. During POST requests, wifiphisher will log all variables that are prefixed with this string.

#### Example
-------

<b>Here's a snippet from a template (index.html):</b>

```
<p> Dear {{ victim_name }}, This is a message from {{ ISP }}.</p>
<p>A problem was detected regarding your {{ target_ap_vendor }} router. </p>
<p> Please write your credentials to re-connect over PPPOE/PPPOA.</p>
<input type="text" name="wphshr-username"></input>
<input type="text" name="wphshr-password"></input>
```

In this example, <b>'victim_name'</b> and <b>'ISP'</b> variables come from config.ini, while <b>'target_ap_vendor'</b> variable is from the beacon frames. Both "wphshr-username" and "wphshr-password" will be logged.
