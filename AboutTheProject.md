# Introduction #

`check-bandwidth` is a plug-in for Nagios which uses the `Net::SNMP` interface in Perl to connect to one or more SNMP agents running on network devices to check the bandwidth usage of those ports (aggregated if there multiple ports are being checked), along with connection status.

# Previous Versions #

Originally created out of the `check_traffic` and `check_snmp_cisco_ifstatus` plug-ins, I re-wrote and combined them both to create the feature set I needed, as neither were complete for my needs.

Using `Net::SNMP`, I created a program that simply checked the the requested port. If it was the version run, that data would be cached to a file and then when re-run, it would check for that file and compare the differences in the tx/rx values against the time between checks to work out the bandwidth usage.

It also supported running on-the-fly, where no cache was used and the program simple paused for a set number of seconds before re-running the checks and comparing the two data values that way (if no cache file exists, this was also the fall-back option).

By `1.1.0`, the program was updated to support Windows (as well as Linux and OS X) along with PnP compatible output data and use of different protocols for the SNMP agent (although it didn't support `v3` at the time).

Also, due to the way `Net::SNMP` worked (and my understanding of it), the `1.x.x` series never fully supported the ability to use 64-bit counters nor to check a ports connectivity.

The second series (`2.x.x`) was originally designed to be a rewrite, through `Net::SNMP::Interfaces` to help overcome these problems. Although it did initially work, it struggled with a massive increase in program execution time (mainly as the program tended to fetch all the information it may need, rather than just what it did). This series never made it out of the testing phase.

# Current Version #

Today, the current version (`3.x.x`) is again a complete re-write, but this time from a new perspective. First of all, it reverts back to using just `Net::SNMP`, but does support 64-bit counters (on `v2c` or `v3` connections) and port status checking.

More importantly, it was re-written from the group up to support checking of multiple ports on multiple interfaces/devices, each with their own connection details (such as version, community and username/password, if required).

With many servers, devices, etc. sporting more than one port, allowing features, through binding, such as load-balancing, fall-over, etc. monitoring overall usage on just one port is no longer the only option - `check_bandwidth` was designed to work in these environments.

This is even useful with multiple switches - connecting a server to two separate switches (if one should fail, you can fall over to the other) - `check_bandwidth` is designed to work here as well.

In fact, is doesn't matter how many network ports you want to check, across any number of devices you have available - just add `--interface` commands to monitor them all!