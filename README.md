# nginx_notes

Nginx is one of the most widely used web servers available today, in part
because of its capabilities as a load balancer and reverse proxy server
for HTTP and other network protocols.

## mac OS specific

Docroot is: /usr/local/var/www

The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080

nginx will load all files in /usr/local/etc/nginx/servers/

To have launch start nginx now and restart at login: brew services start nginx

Or, if you don't need a background service you can just run: nginx

## Basics

### key files, directories, and commands

#### `/etc/nginx`
This directory is the default configuration root for the NGINX server.
Within this directory, you'll find config files that instruct NGINX on
how to behave

#### `/etc/nginx/nginx.conf`
This is the default config entry point used by the NGINX service. This
config file sets up global settings for things like worker process, tuning,
logging, loading dynamic modules, and references to other NGINX config files.
In a default config, the `/etc/nginx/nginx.conf` file includes top-level
http block, or context, which includes all config files in `/etc/nginx/conf.d/`

#### `/etc/nginx/conf.d/`
This directory contains the default HTTP server configuration file. Files
in this directory ending in `.conf` are included in the top-level http
block from within the `/etc/nginx/nginx.conf` file. It's best practice
to utilize `include` statements and organize your configuration in this
way to keep your configuration files concise. In some package repos, this
folder is named `sites-enabled`, and configuration files are linked from
a folder named `sites-available`; this convention is depracated.

#### `/var/log/nginx`
This directory is the default log location for NGINX. Within this directory
you'll find an `access.log` file and an `error.log` file. The access log
contains an entry for each request NGINX serves. The error logfile contains
error events and debug info if the `debug` module is enabled.

### NGINX commands `nginx`

* `-h` - shows the help menu.
* `-v` - shows the NGINX version
* `-V` - shows the NGINX version, build info, and config arguments, which
  shows the modules built into the NGINX binary.
* `-t` - tests the NGINX config
* `-T` - tests the NGINX config and prints the vaidated info to the screen.
  This command is useful when seeking support.
* `-s signal` - the `-s` flags sends a signal to the NGINX master process.
  You can send signals such as `stop`, `quit`, `reload`, and `reopen`.
  * `stop` - discontinues the NGINX process immediately.
  * `quit` - stops the NGINX process after it finishes processing inflight
    requests.
  * `reload` - reloads the config
  * `reopen` - instructs NGINX to reopen logfiles.
  
### Basic functionality

#### Master and worker processes
NGINX has 1 master process and 1 or more worker processes. If caching is
enabled, the cache loader and cache manager processes also run at startup.
The main purpose of the master process is to read and evaluate config files,
as well as maintain the worker processes. 

The worker processes do the actual processing of requests. NGINX relies on 
OS-dependent mechanisms to efficiently distribute requests among worker processes. 
The number of worker processes is defined by the `worker_processes` directive
in the **nginx.conf** config file and can either be set to a fixed number
or configured to adjust automatically to the number of available CPU cores.

The process ID of the master process is written, by default, to the **nginx.pid**
file, which is located in the **/usr/local/nginx/logs** or **/var/run**
directory.

#### Creating NGINX config files
By default the file is named **nginx.conf** and for NGINX plus is placed
in the **/etc/nginx** directory. For NGINC open source, the location depends
on the package system used to install NGINX and the OS. It is typically one
of **/usr/local/nginx/conf**, **/etc/nginx**, or **/usr/local/etc/nginx**.

##### Directives
The configuration file consists of *directivess* and their parameters.
Simple (single-line) directives each end with a semicolon. Other directives
act as "containers" that group together related directives, enclosing them
in curly braces `{}`; these are often referred to as *blocks*.

##### Feature-specific config files
To make the configuration easier to maintain, it's recommended to split it
into a set of feature-specific files stored in **/etc/nginx/conf.d** directory
and use the `include` directive in the main **nginx.conf** file to reference
the contents of the feature-specific files.

#### Contexts
A few top-level directives, referred to as *contexts*, group together the
directives that apply to different traffic types:
* `events` - general connection processing
* `http` - HTTP traffic
* `mail` - Mail traffic
* `stream` - TCP and UDP traffic

Directives placed outside of these contexts are said to be in the main
context.

##### Virtual servers
In each of the traffic-handling contexts, you include one or more `server`
blocks to define *virtual servers* that control the processing of requests.
The directives you can include within a `server` context vary depending
on the traffic type.

For HTTP traffic (the `http` context), each `server` directive controls
the processing of requests for resources at particular domains or IP addresses.
One or more `location` contexts in a `server` context define how to process
specific sets of URIs.

For mail and TCP/UDP traffic the `server` directives each control the processing
of traffic arriving at a particular TCP port or UNIX socket.

##### Inheritance
In general, a *child* context--one contained within another context--inherits
the settings of directives included at the parent level. Some directives
can appear in multiple contexts, in which case you can override the setting
inherited from the parent by including the directives in the child context.

#### Reloading config
With NGINX plus, you can dynamically reconfigure load balancing across the
servers in an upstream group without reloading the configuration. You can
also use the NGINX plus API and key-value store to dynamically control
access, for ex, base on client IP address.

## Web server
At a high level, configuring NGINX as a web server is a matter of defining
which urls it handles and how it processes http requests for resources at
those URIs. At a lower level, the config defines a set of *virtual servers*
that control the processing of requests for particular domains or IP addresses.

Each virtual server for HTTP traffic defines special config instances called
*locations* that control processing of specific sets of URIs. Each location
defines its own scenario of what happens to requests that are mapped to this
location. NGINX plus provides full control of these processes. Each location
can proxy the request or return a file. In addition, a URI can be modified,
so that the request is redirected to another location or virtual server. Also,
a specific error code can be returned and you can configure a specific page
to correspond to each error code.





























