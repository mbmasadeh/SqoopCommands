## How to ETL* a structured data from PostgreSQL to HDFS
<h3>First of all we need to create a three machines over Windows Hyper-V (Must set the RAM to be Fixed). You can make all of them physically if you have an enough resources.</h3>

<ul>The first one is holding Namenode (At least 12 GB of RAM and 40 HDD/SSD).</ul>
<ul>The second one is hodling the Datanode1 (Is Slave1), (At least 10 GB of RAM and 40 HDD/SSD).</ul>
<ul>Both of the first server and second server will run these ecosystems (HDFS, YARN, Mapreduce, Hive, HBase and Sqoop).</ul>
<ul>The third server is running a database engine, in our example for today it's gonna be PostgreSql 10 (At least 6 GB of RAM and 20 HDD/SSD)</ul>
<ul>All the machines are running CentOS based 7.X</ul>




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
