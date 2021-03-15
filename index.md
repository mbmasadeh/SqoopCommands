## How to ETL* a structured data from PostgreSQL to HDFS
<h3>First of all we need to create a three machines over Windows Hyper-V</h3> 
(Must set the RAM to be Fixed in the memory choice). 
You can make all of them as a physical machines if you have an enough resources.
<ul>
<li>The first one is holding Namenode (At least 12 GB of RAM and 40 HDD/SSD).</li>
<li>The second one is hodling the Datanode1 (Is Slave1), (At least 10 GB of RAM and 40 HDD/SSD).</li>
<li>Both of the first server and second server will run these ecosystems (HDFS, YARN, Mapreduce, Hive, HBase and Sqoop).</li>
<li>The third server is running a database engine, in our example for today it's gonna be PostgreSql 10 (At least 6 GB of RAM and 20 HDD/SSD)</li>
<li>All the machines are running CentOS based 7.X </li>
</ul>
</br>
<h3>To install Hadoop, follow this link</h3>
( https://...) 
Now by installing hadoop, both of server one and server two are ready with all components including Sqoop (which is our core subject in this project).

Now lets jump to server 3 (Database server)
The database is PostgreSQL 10 (you can choose any ver you want, and so you can any other DB type such as mysql and sql server

To install PostgreSQL follow these commands

Warning : you have to ensure that your server in free from any pre-installation PostgreSQL, if you have a prevoius installation,
the old version must un-install (completely) from the system.

Step 1: Add PostgreSQL 10 repository  
# yum install https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm -y
Note: if you have any other repository, you can use it instead 

Step 2: Install PostgreSQL 10
# yum install postgresql10 postgresql10-server postgresql10-contrib postgresql10-libs -y

Step 3: Initialize PostgreSQL
# /usr/pgsql-10/bin/postgresql-10-setup initdb

Step 4: Enable and Start PostgreSQL 10
# systemctl enable postgresql-10.service
# systemctl start postgresql-10.service
Note: Later you can use this command to Stop and restart PostgreSQL 10 services:
# systemctl stop postgresql-10.service
then
# systemctl start postgresql-10.service

Now PostgreSQL 10 is installed successfully
As our example is to ELT data from PostgreSQL to HDFS, we need to download a sample database with tables and relation to PostgreSQL
The data sample must be build in the system before getting extract.
To access PostgreSQl and allow our user "root" access, follow the following:
$ sudo -u postgres psql -U postgres (master user)

# CREATE ROLE root LOGIN;//the name of the ROLE is root without password
# CREATE DATABASE dvdrental;// create a database in the same name of the database sample
# GRANT ALL PRIVILEGES ON DATABASE dvdrental TO root; //Permission access for root to dvdrental
\q
Lets access the new database with the role root

$ psql <database><rolename>
$ psql dvdrental root
Cool, you are in
to exit press \q

Now lets download and extract the database sample to PostgreSQL 10
We will download the desired file into a pre-created directory using wget command
lets first create the directory:

$ mkdir /etc/downloads 
Navigate to this new directory
$ cd /etc/downloads

Get the database sample to this directory
Navigate to this site to download the sample
$ wget -c https://sp.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip 
//If the command is not found, then install wget packege
$ yum -y install wget
======================================================================================
Greate !!, the downloaded file extention is .zip, we need to extract it.
Lets download zipping tools

$ sudo yum install unzip
While you are in the target dir, just write this command with the file name 
$ unzip dvdrental.zip
=======================================
Good, now the new unzipping file is dvdrental.tar, the database is ready to host the sample
$ pg_restore -U root -d dvdrental /etc/downloads/dvdrental.tar
Thats it for downloading the sample, to check the sample
$ psql dvdrental root
> \dt //List all tables.
================================================
Now the database work is done, but still we need to make the postgresql server discovorable from the server thats hosting Sqoop service, in our
example its Slave1 (the second server)

In the database server we need to open it for all connections (its not recomended for production manner) 
192.168.1.100/24


You can use the [editor on GitHub](https://github.com/mbmasadeh/SqoopCommands/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/mbmasadeh/SqoopCommands/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
