# Introduction #

`check_bandwidth`, although generally a simple program, can get complex quickly with the range of arguments available, which can change input and output settings in a wide variety of ways.

# Command-Line Arguments #

This document will cover what each argument means, and how you can use it.

## --interface (description) ##

This is the most important argument on the command-line, and the one without which you cannot run the program. `--interface` allows you to add a device (through an SNMP agent) to be checked, with each device supporting a range of ports. It allows the following syntax:

  * `[version://][username[:password]@]hostname[:port]/[community:]interface[,interface]`

where:

  * `version` can be either `v1`, `v2` (the default if not specified), or `v3` to select the level of the protocol you want to use. If you choose `v3`, by default the `MD5` authentication type is used - you can set this explicitly, or change it to `SHA`, with the `v3md5` and `v3sha` options respectively.
  * If you are connecting with `v3` (including `v3md5` and `v3sha`), you are required to enter a username and password after the `://` in the following format: `v3://username:password@hostname` (although the password is optional: `v3://username@hostname` will work, but send an empty password to the SNMP agent. If you connect using `v1` or `v2`, you cannot use a username (and password), and the program will reject the syntax.
  * The `hostname` is the name of the device (or IP address) you want to connect to - this address will be used as part of the cache file system to store the data. Should it change, the cache files will no longer be referenced.
  * If your SNMP agent is running on a different port to the standard (`161`) you can add `:port_number` after the hostname statement.
  * For `v1` and `v2` connections, the agent will require a community to connect to in order to read the data - the community only needs to be read-only (no write access is used) and if none is given, `public` will be used by default instead.
  * Finally, after the community, you can list the ports each interface number you would like to check (separated by a comma), e.g. `1,4,5` for three ports - the first, fourth and fifth listed.

The following are examples on the syntax:

  * `check_bandwidth3 --interface v1://switch.example.com/read.only.access:11` will connect to the device `switch.example.com` (over the standard port), using the `read.only.access` community and get the data for interface `11` using  the `snmpv1` protocol.
  * `check_bandwidth3 --interface v2://switch.example.com/public:11,12` changes so that we're not connecting over `snmpv2c` using the `public` community (which will allow us to check for 64-bit counters, if available, while also checking two interfaces: `11` and `12` at the same time - the traffic from both of them will be aggregated, so if they both report `1.75Mbps`, `check_bandwidth` will report back `3.5Mbps`. This uses many of the defaults, and can also be called as just `--interface switch.example.com/11,12`.
  * `check_bandwidth3 --interface v2://sw2.example.com/switch.ro:3 --interface v3://user:pa55@sw1.example.com/5,6` begins to show the power of the program. This command is for a device with three network ports - with one connected to `sw2` (this is the backup switch) and two connected to `sw1` (the master switch). However, the real power is that they both connected differently (one supports `snmpv3` with the other can only allow `snmpv2c`). The will all get checked and if ports `5` and `6` on `sw1.example.com` report `2Mpbs` each, while port `3` on `sw2.example.com` reports just `0.25Mbps` the output by `check_bandwidth` will how `4.25Mbps`.

## --timeout (seconds) ##

Controls how long (in seconds) that the program should wait before declaring an interface as non-responsive and failing. The returning of time-out errors are controlled based on the number of interfaces passed, and options such as `--check-up` and `--down-warning`.

## --pause (seconds) ##

The `--pause` option sets how long the program should wait before re-testing an interface we it either cannot find a cache file containing historical data (or that historical data has timed out), or if `--on-the-fly` has been passed, in which case the program will not attempt to retrieved cached data and simple re-run the tests after `--pause` seconds (the default is `10`).

Note that the time-out of historical data from the cache files is controlled by the speed of the port and nothing else - this is done to prevent the risk of a port performing a complete cycle between program runs and therefore reporting an inaccurate bandwidth usage.