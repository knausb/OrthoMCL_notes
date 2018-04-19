# OrthoMCL_notes

Notes on how to use [OrthoMCL](http://orthomcl.org/orthomcl/).


## Installation

We'll need the following.

* [NCBI BlastP](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download)
* perl, test for presence with    
    $ perl --version
* MySQL, test for presence as follows.    
    $ mysql --version
Use apt-get install if not present.    
* [mcl](https://www.micans.org/mcl/index.html), test for presence as follows.
    $ mcl --version
Use apt-get install if not present.
* OrthoMCL, download tarball from the site.


## MySQL notes

Read the [fun manual](https://dev.mysql.com/doc/refman/5.7/en/)!

Start and stop the server.

    $ sudo service mysql status
    $ sudo service mysql start
    $ sudo service mysql stop

Common commands.

     mysql --user=root --password=the_mysql_root_password
     SHOW DATABASES;
     CREATE DATABASE orthomcl;
     SHOW TABLES FROM music;
     USE music;
     SHOW TABLES;
     EXIT;
     QUIT;


## Configuration

Add to your [bash PATH](https://unix.stackexchange.com/q/26047).

Create `my_orthomcl_dir`.    
Copy `orthomclSoftware/doc/Main/OrthoMCLEngine/orthomcl.config.template` to `my_orthomcl_dir/orthomcl.config`.    
Edit this file as directed in the `UserGuide.txt`.    




