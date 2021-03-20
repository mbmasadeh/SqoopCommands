## How to ELT a structured data from PostgreSQL to HDFS
<h4>Prerequisites, Hardware requirements</h4>
<ul>
  <li>Create a three V-Machines over Windows Hyper-V</li>
  <p>Note: Must set the every machine RAM to "Fixed" in the memory Tab</p>
  <li>The installed OS is CentOS based 7.5 for all machines</li>
  <ll>The first machine is holding Namenode (At least 12 GB of RAM and 40 HDD/SSD).</li>
  <li>The second one is hodling the Datanode1 (Is Slave1), (At least 10 GB of RAM and 40 HDD/SSD).2</li>  
  <li>Both of the first machine and the second machine will run these ecosystems (HDFS, YARN, Mapreduce, Hive, HBase and Sqoop).</li>
  <li>The third server is running a database engine, in our example for today it's gonna be PostgreSql 10 (At least 6 GB of RAM and 20 HDD/SSD)</li>
</ul>

<h4>To install Hadoop, follow this link</h4>
<a href="">https://mbmasadeh.github.io/HDPMultiNodeHadoopCluster</a> 
<p>Now by installing hadoop, both of server one and server two are ready with all components including Sqoop (which is our core subject in this project).</p>

<p>Now lets jump to server 3 (Database server)</p>
<p>The database is PostgreSQL 10 (you can choose any ver you want, and so you can any other DB type such as mysql and sql server</p>

<h4>Installing PostgreSQL DB engine</h4>
<p> Warning : you have to ensure that your server in free from any pre-installation PostgreSQL, if you have a prevoius installation,
the old version must un-install (completely) from the system. </p>

## Step 1: Add PostgreSQL 10 repository  
<pre><code>
# yum install https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm -y
</code></pre>
<p>Note: if you have any other repository, you can use it instead </p>
 
## Step 2: Install PostgreSQL 10
<pre><code>
# yum install postgresql10 postgresql10-server postgresql10-contrib postgresql10-libs -y
</code></pre>
 
## Step 3: Initialize PostgreSQL
<pre><code>
# /usr/pgsql-10/bin/postgresql-10-setup initdb
</code></pre>
 
## Step 4: Enable and Start PostgreSQL 10
<pre><code>
# systemctl enable postgresql-10.service
# systemctl start postgresql-10.service
</code></pre>
 
Note: Later you can use this command to Stop and restart PostgreSQL 10 services:
<pre><code>
# systemctl stop postgresql-10.service
</code></pre>
<p>then</p>
<pre><code>
# systemctl start postgresql-10.service
</code></pre>
 
<p>Now PostgreSQL 10 is installed successfully</p>
<p>As our example is to ELT data from PostgreSQL to HDFS, we need to download a sample database with tables and relation to PostgreSQL
The data sample must be build in the system before getting extract.</p>
 
## To access PostgreSQl and allow our user "root" access, follow the following:
<pre><code>
$ sudo -u postgres psql -U postgres (master user)
# CREATE ROLE root LOGIN;//the name of the ROLE is root without password
# CREATE DATABASE dvdrental;// create a database in the same name of the database sample
# GRANT ALL PRIVILEGES ON DATABASE dvdrental TO root; //Permission access for root to dvdrental
</code></pre>

<p>Lets access the new database with the role root</p>
<pre><code>
# psql "database" "rolename"
# psql dvdrental root
</code></pre>

<p>Cool, you are in
to exit press \q</p>

<h4>Download a database sample</h4>
<p>Now lets download and extract the database sample to PostgreSQL 10
We will download the desired file into a pre-created directory using wget command
lets first create the directory:</p>

<pre><code>
$ mkdir /etc/downloads 
</code></pre>
<p>Navigate to this new directory</p>

<pre><code>
$ cd /etc/downloads
</code></pre>

<p>Get the database sample to this directory
Navigate to this site to download the sample</p>
<pre><code>
$ wget -c https://sp.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip 
</code></pre>

<p>If the command is not found, then install wget packege</p>
<pre><code>
$ yum -y install wget
</code></pre>

<p>Greate !!, the downloaded file extention is .zip, we need to extract it.
Lets download zipping tools</p>
<pre><code>
$ sudo yum install unzip
</code></pre>
<p>While you are in the target dir, just write this command with the file name</p>
<pre><code>
$ unzip dvdrental.zip
</code></pre>

<p>Good, now the new unzipping file is dvdrental.tar, the database is ready to host the sample</p>
<pre><code>
$ pg_restore -U root -d dvdrental /etc/downloads/dvdrental.tar
</code></pre>
<p>Thats it for downloading the sample, to check the sample</p>
<pre><code>
$ psql dvdrental root
 \dt //List all tables.
</code></pre>

<p>Now the database work is done, but still we need to make the postgresql server discovorable from the server thats hosting Sqoop service, in our
example its Slave1 (the second server)</p>

<h4>Setup the DB configuration to accept remote connection</h4>

<p>In the database server we need to open it for all connections (its not recomended for the production manner)</p> 
<p>In the database server, paste this command to open pg_hba.conf file in order to allow some access permissions</p>
<pre><code>
vim /var/lib/pgsql/10/data/pg_hba.conf
</code></pre>

<p>Navigate to the end of the file to allow server "Slave1" to access this server.</p>
<p>You have the option to allow all servers to access this server, or allow only the servers you want</p>
<p>In the "IPv6 local connections" add this line</p>
<p> host  all   all   <slave1 ip address>/<port>  Trust</p>
<p>close the editor by press Esc then :wq then enter<p/>
<p>One more file to edit it before run the sqoop command is "postresql.conf</p>
<p>Paste this command</p>
  <pre><code>
  vim /var/lib/pgsql/10/data/postgresql.conf
  </code></pre>
  <p>Find this section "Listen_addresses" and make it equal '*'</p>
  <p>close the file and restart PostgreSQL 10</p>
  
<h4>Slave1 Sqoop</h4>
  <p>You must ensure about the server whom running Sqoop ecosystem, in this example its Slave1</p>
  <p>
    Now its the time for sqoop, run this command to get the table name "actor" to HDFS
  </p>
   <pre><code>
   sqoop import --connect jdbc:postgresql://"Database server IP"/dvdrental --username root --password "the password" --table actor -m 1
    </code></pre>
<p>Now all the structured data must be moved to HDFS in as one sheet due to "-m1" referes to a one MapReduce job only</p>
 
 Done...!!
