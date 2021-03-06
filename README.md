App: TA-browscap_express
Author: Robert Labrie, The Network Inc

This technology add-on provides a dynamic lookup to add fields to user 
agent (browser) data. It is a re-write of the TA-browscap add-on by
David Shpritz. The data is provcided by the Broser Capabilities Project. The parser is a re-write of the parser in the pybrowscap library. This add-on is faster than other browscap based projects, because it maintains a cache of previously matched user agent strings. This significantly improves the time for subsequent lookups, without sacrificing accuracy.

http://browscap.org/
http://pypi.python.org/pypi/pybrowscap/

All fields in the browscap file are included, plus one additional field:

ua_fromcache=True if this record was read from cache, false if from the browscap file

# Installation #
To install:  
1.  Untar the TA-browscap.tar.gz file in your $SPLUNK_HOME/etc/apps
   directory.  
2.  Change to the $SPLUNK_HOME/etc/apps/TA-browscap/bin directory  
3.  Download the browscap.csv file from the project:  
   wget -O browscap.csv http://browscap.org/stream?q=BrowsCapCSV  
4.  Restart Splunk.

The optional configuration file, browscap_lookup.ini, allows changing the default location of the browscap_lite.csv (cache) file.

# Usage #
To use:  
The lookup expects a field named "http_user_agent". In the search bar,
you can run something like:
index=iis | eval http_user_agent=urldecode(cs_User_Agent) | lookup browscap_lookup_express http_user_agent

The UserAgent string *must* be urldecoded. 

This should produce the additional fields.

***THE FIRST FEW SEARCHES WILL BE VERY SLOW***

TA-browscap_express builds a cache of user agents which exist in your data. The first time a string is encountered, the entire browscap file must be searched. Subsequent searches will be faster. Be patient while the cache is built. I suggest using a search limiter like "| head 10", then increasing that number. In a few minutes, your cache will be populated and the searches will be very fast.

Not all browsers are cached. Generic and default browsers are deliberately not cached. To search for browsers which are always looking up in the browscap file try adding "| search ua_fromcache=false". You can either exlcude these UA strings at the start, or better, file a bug with the browscap project on github to get the UA string added to the library.

# Cache file #
The cache file, browscap_lite.csv, is checked first, speeding up subsequent searches. The location of the file is as follows:
1.  The script directory. This is the default location, unless:
2.  SPLUNK_HOME/var/run/splunk if the SPLUNK_HOME environment variable is defined, this location will be used, unless:
3.  It's overridden in browscap_lite.ini

# Blacklist file #
The optional file, blacklist.txt, contains a list of UA strings, one per line, which should not be checked. This is good for managing custom or blatantly forged UA strings that you don't want to waste time going to the main browscap file for, since they'll always return default/generic.

<pre>
CHANGE LOG  
20130607 v1: 	Initial
20140820 v2:	Better
20150407   :	**delete your cache** new fields for browscap v6001
		For the windows crowd, added extractUAStrings.ps1
</pre>