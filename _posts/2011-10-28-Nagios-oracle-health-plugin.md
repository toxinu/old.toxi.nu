--- 
layout: post
title: Nagios Oracle health plugin
---

This is more a memo than an article. A quick and dirty [check_oracle_health][1] plugin installation, with prerequires.

Developped by [Gerhard Lausser][2], an active [Shinken][3] developper with [Jean Gabès][4].

Like most of Nagios plugins, it's in Perl language and it's use DBD::Oracle module, and this module need an Oracle client.

## Oracle Instant Client

You have to create a fucking Oracle account... Yes I know you think : 

_"What's this great article ?! Proprietary software are just awesome !"_.

Go to [Oracle Instant Client Downloads Page][5]. Get the __Instant Client Package - Basic__ and __sdk__.  
Or use my personnal [links][6] above.

For the story, i have fucked my brain during three hours cause I have download the __Instant Client Package - Basic Lite__ and try to work with a Western American charset database... And of course, Basic Lite does not support it.

Derp.

{% highlight bash %}
# x86
wget http://dl.socketubs.net/nagios/oracle/instantclient-basic-linux-11.2.0.3.0.zip
wget http://dl.socketubs.net/nagios/oracle/instantclient-sdk-linux-11.2.0.3.0.zip
# X86_64
wget http://dl.socketubs.net/nagios/oracle/instantclient-basic-linux.x64-11.2.0.3.0.zip
wget http://dl.socketubs.net/nagios/oracle/instantclient-sdk-linux.x64-11.2.0.3.0.zip

unzip instantclient-basiclite-linux*.zip
unzip instantclient-sdk-linux*.zip
mv instantclient_11_2 /opt
{% endhighlight %}

## DBD::Oracle

Install pre-requires :

{% highlight bash %}
apt-get install libaio-dev
cpan DBI
{% endhighlight %}

Get module sources.

{% highlight bash %}
wget http://search.cpan.org/CPAN/authors/id/P/PY/PYTHIAN/DBD-Oracle-1.56.tar.gz
tar xvf DBD-Oracle-1.56.tar.gz
cd !$
{% endhighlight %}

DBD::Oracle module need path to Oracle Instant Client.

{% highlight bash %}
export ORACLE_HOME=/opt/instantclient_11_2
export LD_LIBRARY_PATH=$ORACLE_HOME
echo 'ORACLE_HOME="/opt/instantclient_11_2"' >> /etc/environment
echo 'LD_LIBRARY_PATH=$ORACLE_HOME' >> /etc/environment
{% endhighlight %}

And compile it.

{% highlight bash %}
perl Makefile.PL -V 11.2
make
make install
{% endhighlight %}

## Tnsnames.ora

This file contain informations about your different databases.

{% highlight bash %}
mkdir -p /opt/instantclient_11_2/network/admin
vim !$/tnsnames.ora
{% endhighlight %}

This is and example of _tnsnames.ora_ file :

{% highlight bash %}
<addressname> =
 (DESCRIPTION =
   (ADDRESS_LIST =
     (ADDRESS = (PROTOCOL = TCP)(Host = <hostname>)(Port = <port>))
   )
 (CONNECT_DATA =
   (SERVICE_NAME = <sid>)
 )
)
{% endhighlight %}

## check_oracle_health

To finish, we have to install the Nagios plugin.

{% highlight bash %}
wget http://labs.consol.de/wp-content/uploads/2011/09/check_oracle_health-1.7.6.1.tar.gz
tar xvf check_oracle_health-1.7.6.1.tar.gz
cd !$
./configure
make
make install
{% endhighlight %}

That's all !

[1]: http://labs.consol.de/nagios/check_oracle_health
[2]: https://github.com/lausser
[3]: http://www.shinken-monitoring.org/
[4]: https://github.com/naparuba
[5]: http://www.oracle.com/technetwork/database/features/instant-client/index-097480.html
[6]: http://dl.socketubs.net/nagios/oracle/
