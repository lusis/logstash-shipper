# logstash-shipper
Sometimes the JVM is too heavy for edge collection.

Python is the least common denominator on systems. Currently I don't even HAVE a system ruby installed on my production systems. The only ruby we have is provided by omnibus for Chef.

This is a work-in-progress of a fairly lightweight shipper for logstash.

I started writing my own. Since my python-fu is 2 years old and weak, I went searching for a refresher on handling file rotation. I ended up finding a nice LogWatch example that handled it already.

I took it and added the first steps to make it a logstash-shipper.

## How it works
I still need to throw in the option parsing but current it reads a directory for files ended in .log. As the files are written, it converts the log line to a subset of logstash's `json_event` format, and ships it over zeromq.

This means it only works with logstash HEAD right now.

# TODO
Things needing done:

- option parsing
- convert to supporting single file OR directory of files
- some basic tests
- Testing on something OTHER than python 2.6/ubuntu 10.04 LTS

# pyzmq
If, for some reason pyzmq isn't building nicely for you, try `easy_install pyzmq-static`. It compiles its own version of zmq 2.1 for you (and libuuid as well).

# License
The original script that did the heavy lifting was MIT so this one is as well.
