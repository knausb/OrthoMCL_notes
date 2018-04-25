# OrthoMCL_notes

Notes on how to use [OrthoMCL](http://orthomcl.org/orthomcl/).
These notes were generated while following the [UserGuide](http://orthomcl.org/common/downloads/software/v2.0/UserGuide.txt).

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

OrthoMCL uses a database named `orthomcl` which makes it a good example for the `CREATE` command.

Determine MySQL [port number](https://stackoverflow.com/a/18353323).

    SHOW VARIABLES WHERE Variable_name = 'port';

The default is 3306 and is known to most of the internet, which is a security issue.
[This can be changed](https://www.quora.com/How-do-I-configure-MySQL-to-listen-on-a-port-other-than-port-3306) by editing `my.cnf` (for me it was `/etc/mysql/my.cnf`) to change the `port` parameter.
Then stop and sart your mysql server.

## Configuration

Add to your [bash PATH](https://unix.stackexchange.com/a/26059).

    export PATH=~/bin/orthomclSoftware-v2.0.9/bin:$PATH


Create `my_orthomcl_dir`.    
Copy `orthomclSoftware/doc/Main/OrthoMCLEngine/orthomcl.config.template` to `my_orthomcl_dir/orthomcl.config`.    
Edit this file as directed in the `UserGuide.txt`.    



Install [perl's DBI](https://superuser.com/a/68434) module.

    sudo apt-get install libdbi-perl
    sudo apt-get install libdbd-mysql-perl


Run the `orthmclInstallSchema` program to install the schema. (Run the program with no arguments to get help.  This is true of all following orthomcl programs.)


## FASTA sanitization

We'll need to make our FASTA files OrthoMCL compliant.

    mkdir compliantFasta/
    cd compliantFasta/
    orthomclAdjustFasta pinf ../pinf_omcl.fasta 2
    orthomclAdjustFasta pram ../pram_omcl.fasta 1
    orthomclAdjustFasta psoj ../psoj_omcl.fasta 1
    cd ..

We then filter the genes based on length and percent stop codons.

    orthomclFilterFasta compliantFasta/ 10 20
    
## blastp

First we need to make our blast database.

    ~/bin/ncbi-blast-2.7.1+/bin/makeblastdb -in goodProteins.fasta -dbtype prot

Then perform the search.
Note that the UserGuide states we need to use `-m 8`.
This option does not exist in the current version of blastP 2.7.1+.
Instead, we've used `-outfmt 6`.
This should be a tab delimited file containing the following columns (m 8):
  query_name, hitname, pcid, len, mismatches, ngaps, start('query'), 
  end('query'), start('hit'), end('hit'), evalue, bits

    ~/bin/ncbi-blast-2.7.1+/bin/blastp -query goodProteins.fasta -db goodProteins.fasta -outfmt 6 -evalue 1e-5 -out myBlastP.out

## orthomclBlastParser


    mkdir my_orthomcl_dir
    orthomclBlastParser myBlastP.out compliantFasta/ >> my_orthomcl_dir/similarSequences.txt


## orthomclLoadBlast


   orthomclLoadBlast config_file my_orthomcl_dir/similarSequences.txt


Where `config_file` is as follows 

```
dbVendor=mysql
dbConnectString=dbi:mysql:orthomcl
dbLogin=root
dbPassword=myPassword
similarSequencesTable=SimilarSequences

```

## orthomclPairs

    orthomclPairs config_file2 pairslog cleanup=no

Where config_file2 appears below.

```
dbVendor=mysql
dbConnectString=dbi:mysql:orthomcl
dbLogin=root
dbPassword=my_db_password
similarSequencesTable=SimilarSequences
orthologTable=Ortholog
inParalogTable=InParalog
coOrthologTable=CoOrtholog
interTaxonMatchView=InterTaxonMatch
percentMatchCutoff=50
evalueExponentCutoff=-5
```

## orthomclDumpPairsFiles

Here we can reuse the config file from orthomclPairs.

    orthomclDumpPairsFiles config_file2

This should create a directory called `pairs/`.


## mcl


    mcl mclInput --abc -I 1.5 -o my_orthomcl_dir/mclOutput

## orthomclMclToGroups


    orthomclMclToGroups my_prefix 1000 < mclOutput > groups.txt


You're finished!
