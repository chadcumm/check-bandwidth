Many servers and devices today have multiple connection as standard, which (and especially) through bonding means that just monitoring a single port is not always enough.

Originally based on the check\_traffic and check\_snmp\_cisco\_ifstatus, the originally check\_bandwidth plugin was a re-write, using the Net::SNMP library, taking the best bits of both to create something I needed, as neither of the packages supported the correct feature set I required.

Today, check-bandwidth is again a complete re-write of the original program, but this time I've re-written it to support extra features and the ability to monitor multiple interfaces across multiple devices, even if they're all using different connection protocols, communities and access privileges!