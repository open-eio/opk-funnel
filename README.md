# opk-funnel 

A simple CLI for combining sensor values into one packet.

Options:

- --filename (or -f) : specify the named pipe filename
- --syntax (or -s): specify the syntax for combining packets in the output:  {JSON, CSV} (CSV is the default)

usage: 

> funnel [-h] [-f FUNNELFILE] [-s SYNTAX] [-v]


## Background

This 'funnel' CLI acts as an inter-process server, using 'named pipes' to collect JSON packets from multiple pull commands and collate them into a single JSON packet. 

Because opening a named pipe for writing will 'block' the process until a read from that named pipe is performed, we need to explicitly tell the shell to run the command in the background with a '&' at the end of the line.

Then, when the named pipe is read, the process 'unblocks'.

## Example

In the following example, three JSON packets (provided as example files in the repo) are processed by the 'funnel' cli, writing them one by one into a named pipe named 'funnelfile' (which is the default -- one can change the name of this named pipe by using the option '-f' parameter)

The files being used are:

*packet1.json*:  {"hello":3,"temp":2}

*packet2.json*:  {"soup":6, "bubbles":2}

*packet3.json*:  {"cond":2, "up":2}

Below, we 'cat' the example files and pipe them into the 'pipe' CLI; but this could just as well have been any other 'stdout' process.

> cat packet1.json | ./funnel &

> cat packet2.json | ./funnel &

> cat packet3.json | ./funnel &

Note that 'funnelfile' is a 'named pipe' that we've created for our process.  In order to avoid excessive reads/writes to the system, it may be optimal to keep this funnel file across processes. 

Now, let's gather everything we've written to the named pipe, and put it into one combined JSON packet:

> ./funnel > combined_packet.json

When we examine the contents of this packet, we see:

*combined_packet.json*: {{"hello":3,"temp":2},{"cond":2, "up":2},{"soup":6, "bubbles":2}}
