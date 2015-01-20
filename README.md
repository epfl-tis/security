## Overview

This application encrypts, decrypts, and imports Python variables (often passwords) into the global namespace. It gives you a simple (but secure) way to store private information alongside your Python code, and then use that information within a production environment. The code is pure Python, has no third-party dependencies, and requires almost no recoding of your program. Unencrypted secrets never go over the wire or touch a production hard drive.

Implementation is as simple as:

    from security import *

## Description

#### Development  

In a development environment, importing this module will automatically encrypt all of the raw files listed in RAW_FILES, and save an encrypted version of each file to disk. I suggest you add _ before each raw file name (indicating insecure information), and then add _* to your .gitignore file. Doing so will make GIT automatically exclude those secret files (files starting with _) from your repository. For example, security.py is configured by default to process the raw file _passwords.py (this file IS included in this repo for demonstration purposes, but should otherwise be excluded from your own repo).
  
#### Production  
  
In a production environment, importing this module will automatically decrypt and then import all python code (usually constants) from the original raw .py file (for example, from _passwords.py). The module will import all references into the global namespace, so you may then reference secured variables as you normally would in Python. The module assumes that it is in a production environment unless it is running on a machine listed in DEV_MACHINES. 

## Step-by-Step Instructions

    1. Make a private key, such as:  
    
          kksdhfs984y5hbswfd8WEZJD8asdhasi!JHADHjasbd78asjdai  
          
       Save your private sequence of characters into a file in the root directory:  
          
          /key.rc4  
          
       Save your private sequence of characters into the Google metadata server (optional):  
       
          http://metadata.google.internal/computeMetadata/v1/project/attributes/rc4  
    
    2. Copy this file (security.py) from GitHub into your project directory.  

    3. Add the following line of code to your program (it will auto-run the module):  

          from security import *   

    4. Trick developer GUI programs (such as PyCharm) into interpreting your raw  
       file on development machines, and thus provide code completion, by adding the  
       following lines of code to your main program. This code will have no effect  
       in production environments as the _passwords.py file will not exist in   
       production environments (you should change _passwords below to reflect  
       the actual modules you are encrypting and have listed in RAW_FILES):  

          try: from _passwords import *  
          except: pass  

    5. Store your passwords or other private info as regular python variable statements  
       in your _passwords.py file (and/or other files listed in RAW_FILES). For example:  

          MYSQL_PASSWORD = MyExample!password4   
          SENDGRID_PWD = THisIS_my44sendgridpwd   
          LOGGLY_URL = 'http://logs-01.loggly.com/inputs/00-00-00-00-00/'   
        
    6. Everything will update each time you execute:   from security import *  
       That happens automatically when you run your program, so be sure to run your  
        program at least one time on your development machine.  
          
    7. Use GIT to 'push-to-deploy' to your production environments. You're done!  
       
## Technical notes 
 
#### Important!
  
    A private key file (security.rc4) and raw password file (_passwords.py) are included 
    in this repository for demonstration purposes only. Do NOT include those files in
    your own repo. Install security.rc4 in your root directory and exclude _passwords.py
    from the repo in your .gitignore file.  
  

#### Encryption
  
Encryption is implemented with the standard RC4 algorithm. Generate a long, random sequence of ASCII characters and save it into a local file (as defined by the constant KEY_FILE; see the file security.rc4 as an example). You may want to store this file in the root directory, as doing so requires root access; but you may store it in any location you set within KEY_FILE. 

#### Google Compute Engine  
  
This application was initially developed for Google Compute Engine. Store your private key in the Google metadata server (with the private URL defined in METADATA_KEY). This application will automatically (and securely) request your private key from the metadata server. Google automatically enforces access rights and permissions for the metadata server. The advantage of this approach is that you can maintain your private key in just one location (to be used by many GCE instances), and then revoke or change the key with minimal disruption. 
  
 
