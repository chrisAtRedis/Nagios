When checking out our Nagios plugin, I noticed that although cluster functionality is incoporated in this plugin, other 
functionality such as shard stats seem to be missing.
In the true FOSS spirit of standing on the shoulder of giants, I decided to reuse an existing Nagios plugin. Here
are the configuration steps in order to include shard stats in your Nagios dashboard. The following steps are
somewhat Ubuntu-specific (also applying to Debian); your mileage can vary and it's always a good idea to check 
distribution-specific documentation beforehand:

1. Install Nagios 3 the usual way on a server ideally _separate_ from any cluster node (packages nagios3 and 
nagios-plugins),
2. On the cluster nodes to be monitored, install the package nagios-nrpe-server. This will take care of basic agent
functionality on the nodes to be monitored. This will also look after basic server monitoring capabilities such as resource
usage and other server vital signs.
3. Copy the localhost_nagios2.cfg in the /etc/nagios3/conf.d directory to redis_node1.cfg (or a similar name) in the same 
directory.
4. Leaving the generic_host template intact ("use generic-service" statement), replace localhost with the 
address of the FQDN or IP address of the cluster node in the relevant stanzas of this file. 
5. Add a further stanza (easiest done by copying an existing stanza) for this cluster node and use check_redis_stats 
as the relevant check_command entry. 
6. In your commands.cfg file (in /etc/nagios3) add another command stanza with the command line: 
```/usr/local/bin/check_redis_stats.pl -P <port #> -H <target node address>
This will take care of invoking a Perl script which does the actual checking on the target node.
7. Clone the following Github repo into a directory:
```https://github.com/harisekhon/nagios-plugins
In this directory either do a full build of all the plugins or simply the perl-libs (make build or make perl-libs)
8. Copy the support libraries into a path where Perl will be able to find them (hint: 
