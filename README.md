# whatportis
فرمان برای یافتن نام و شماره پورت. اغلب اتفاق می افتد که ما باید پورت پیش فرض یک سرویس خاص را بدانیم، یا برعکس - معمولاً چه سرویس هایی در یک پورت خاص گوش می دهند. برای این وظایف است که ابزار Whatportis طراحی شده است.

<p dir="auto">It's a common task to search the default port number of a service. Some ports are available in the <code>/etc/services</code> file, but the list is not complete and this solution is not portable.</p>

<p dir="auto">Whatportis is a simple tool that downloads the <a href="http://www.iana.org/assignments/port-numbers" rel="nofollow">Iana.org</a> database and uses it to explore the official list of ports.</p>

<h2>Usage</h2>

Whatportis allows you to find what port is associated with a service:

<pre>$ whatportis redis
+-------+------+----------+---------------------------------------+
<span class="pl-k">|</span> Name  <span class="pl-k">|</span> Port <span class="pl-k">|</span> Protocol <span class="pl-k">|</span> Description                           <span class="pl-k">|</span>
+-------+------+----------+---------------------------------------+
<span class="pl-k">|</span> redis <span class="pl-k">|</span> 6379 <span class="pl-k">|</span>   tcp    <span class="pl-k">|</span> An advanced key-value cache and store <span class="pl-k">|</span>
+-------+------+----------+---------------------------------------+</pre>

Or, conversely, what service is associated with a port number:

<pre>$ whatportis 5432
+------------+------+----------+---------------------+
<span class="pl-k">|</span> Name       <span class="pl-k">|</span> Port <span class="pl-k">|</span> Protocol <span class="pl-k">|</span> Description         <span class="pl-k">|</span>
+------------+------+----------+---------------------+
<span class="pl-k">|</span> postgresql <span class="pl-k">|</span> 5432 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> PostgreSQL Database <span class="pl-k">|</span>
+------------+------+----------+---------------------+</pre>

You can also search a pattern without knowing the exact name by adding the --like option:

<pre>$ whatportis mysql --like
+----------------+-------+----------+-----------------------------------+
<span class="pl-k">|</span> Name           <span class="pl-k">|</span>  Port <span class="pl-k">|</span> Protocol <span class="pl-k">|</span> Description                       <span class="pl-k">|</span>
+----------------+-------+----------+-----------------------------------+
<span class="pl-k">|</span> mysql-cluster  <span class="pl-k">|</span>  1186 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> MySQL Cluster Manager             <span class="pl-k">|</span>
<span class="pl-k">|</span> mysql-cm-agent <span class="pl-k">|</span>  1862 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> MySQL Cluster Manager Agent       <span class="pl-k">|</span>
<span class="pl-k">|</span> mysql-im       <span class="pl-k">|</span>  2273 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> MySQL Instance Manager            <span class="pl-k">|</span>
<span class="pl-k">|</span> mysql          <span class="pl-k">|</span>  3306 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> MySQL                             <span class="pl-k">|</span>
<span class="pl-k">|</span> mysql-proxy    <span class="pl-k">|</span>  6446 <span class="pl-k">|</span> tcp, udp <span class="pl-k">|</span> MySQL Proxy                       <span class="pl-k">|</span>
<span class="pl-k">|</span> mysqlx         <span class="pl-k">|</span> 33060 <span class="pl-k">|</span>   tcp    <span class="pl-k">|</span> MySQL Database Extended Interface <span class="pl-k">|</span>
+----------------+-------+----------+-----------------------------------+</pre>

<h2>Installation</h2>

<pre>$ pip install whatportis</pre>

<h2>Database Sync</h2>

Whatportis uses a local JSON file (~/.whatportis_db.json) to explore the list of ports. The first usage will create it for you, then you can use the --update option to synchronize it :

<pre>$ whatportis --update
Previous database will be updated, <span class="pl-k">do</span> you want to continue<span class="pl-k">?</span> [y/N]: y
Downloading https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.csv...
Populating database...
14145 ports imported.</pre>

<h2>JSON output</h2>

You can display the results as JSON, using the --json option :

<pre>$ whatportis 5432 --json
[
    {
        <span class="pl-s"><span class="pl-pds">"</span>name<span class="pl-pds">"</span></span>: <span class="pl-s"><span class="pl-pds">"</span>postgresql<span class="pl-pds">"</span></span>,
        <span class="pl-s"><span class="pl-pds">"</span>port<span class="pl-pds">"</span></span>: <span class="pl-s"><span class="pl-pds">"</span>5432<span class="pl-pds">"</span></span>,
        <span class="pl-s"><span class="pl-pds">"</span>protocol<span class="pl-pds">"</span></span>: <span class="pl-s"><span class="pl-pds">"</span>tcp, udp<span class="pl-pds">"</span></span>,
        <span class="pl-s"><span class="pl-pds">"</span>description<span class="pl-pds">"</span></span>: <span class="pl-s"><span class="pl-pds">"</span>PostgreSQL Database<span class="pl-pds">"</span></span>
    }
]</pre>

<h2>REST API</h2>

Whatportis can also be started as a RESTful API server. This feature is not enabled by default, you must install an extra package :

<pre>$ pip install whatportis[server]
$ whatportis --server localhost 8080
 <span class="pl-k">*</span> Serving Flask app <span class="pl-s"><span class="pl-pds">"</span>whatportis.server<span class="pl-pds">"</span></span> (lazy loading)
 <span class="pl-k">*</span> Environment: prod
 <span class="pl-k">*</span> Debug mode: off
 <span class="pl-k">*</span> Running on http://127.0.0.1:8080/ (Press CTRL+C to quit)</pre>
 
 The endpoints are /ports for the whole list (can be long) and /ports/<search> to search a specific port :

<pre>$ curl http://127.0.0.1:8080/ports/3306
{<span class="pl-s"><span class="pl-pds">"</span>ports<span class="pl-pds">"</span></span>:[{<span class="pl-s"><span class="pl-pds">"</span>description<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>MySQL<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>name<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>mysql<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>port<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>3306<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>protocol<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>tcp, udp<span class="pl-pds">"</span></span>}]}

$ curl http://localhost:8080/ports/redis
{<span class="pl-s"><span class="pl-pds">"</span>ports<span class="pl-pds">"</span></span>:[{<span class="pl-s"><span class="pl-pds">"</span>description<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>An advanced key-value cache and store<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>name<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>redis<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>port<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>6379<span class="pl-pds">"</span></span>,<span class="pl-s"><span class="pl-pds">"</span>protocol<span class="pl-pds">"</span></span>:<span class="pl-s"><span class="pl-pds">"</span>tcp<span class="pl-pds">"</span></span>}]}</pre>
