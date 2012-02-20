# logstash-shipper
Sometimes the JVM is too heavy for edge collection.

Python is the least common denominator on systems. Currently I don't even HAVE a system ruby installed on my production systems. The only ruby we have is provided by omnibus for Chef.

This is a work-in-progress of a fairly lightweight shipper for logstash.

I started writing my own. Since my python-fu is 2 years old and weak, I went searching for a refresher on handling file rotation. I ended up finding a nice LogWatch example that handled it already.

I took it and added the first steps to make it a logstash-shipper.

## How it works
I still need to throw in the option parsing but current it reads a directory for files ended in .log. As the files are written, it converts the log line to a subset of logstash's `json_event` format, and ships it over zeromq.

This means it only works with logstash HEAD right now.

# Help output
```
usage: logstash-shipper.py [-h] -a ADDRESS [-m {bind,connect}] -p PATH

Logstash logfile shipper

optional arguments:
  -h, --help            show this help message and exit
  -a ADDRESS, --address ADDRESS
                        0mq address
  -m {bind,connect}, --mode {bind,connect}
                        bind or connect mode
  -p PATH, --path PATH  path to log files

    Logstash shipper provides an lightweight method for shipping local
    log files to Logstash.
    It does this using zeromq as the transport. This means you'll need
    a zeromq input somewhere down the road to get the events.

    Events are sent in logstash's json_event format.

    Examples 1: Listening on port 5556 (all interfaces)
        cli: logstash-shipper -a tcp://*:5556 -m bind -p /var/log/
        logstash config:
            input { zeromq {
                type => 'shipper-input'
                mode => 'client'
                topology => 'pushpull'
                address => 'tcp://shipperhost:5556'
              } }
            output { stdout { debug => true } }

    Example 2: Connecting to remote port 5556 on indexer
        cli: logstash-shipper -a tcp://indexer:5556 -m connect -p /var/log/
        logstash config:
            input { zeromq {
                type => 'shipper-input'
                mode => 'server'
                topology => 'pushpull'
                address => 'tcp://*:5556'
              }}
            output { stdout { debug => true } }
```

# TODO
Things needing done:

- convert to supporting single file OR directory of files
- some basic tests
- Testing on something OTHER than python 2.6/ubuntu 10.04 LTS

# pyzmq
If for some reason pyzmq isn't building nicely for you, try `easy_install pyzmq-static`. It compiles its own version of zmq 2.1 for you (and libuuid as well).

# License
The original script that did the heavy lifting was MIT so this one is as well.
