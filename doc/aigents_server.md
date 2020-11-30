# Aigents Server requirements configuration, operation and API use

The following describes configuration and operation of Aigents server as a console application or a server deamon (so called “Aigents Farm”) with no user interface.

##Aigents Requirements

Aigents Server can be installed on any Java-friendly operating environment fulfilling the following requirements.

1. Minimum 4G RAM size, the more - the better.
1. Minumum 32G HDD/SSD size.
1. Java 1.7 or higher installed (JDK from Oracle, not OpenJDK from IBM, because the latter is known to have bugs).
1. Ability to open ports for HTTP/HTTPS and TCP/IP (telnet) connections.
1. Optionally - ability to access email servers for incoming and outgoing email.

##Aigents Basic configuration

The following describes very basic steps of Aigents server setup.

1. Download Aigents.jar file to application folder such as /home/aigents/ (or any other accessible folder on Linux or Windows system).
1. Download dependency *.jar files from http://aigents.com/download/latest/ folder to application folder (such as /home/aigents/). Dependency files can be also downloaded from appropriate sources for Java 1.7:
	1. mail.jar - Oracle Java Mail - from http://www.oracle.com/technetwork/java/javamail/index.html
	1. servlet.jar - Oracle Java Servlet - from http://www.java2s.com/Code/Jar/j/Downloadjavaxservletjar.htm
	1. javax.json-1.0.2.jar - JSON API for Java - from http://www.java2s.com/Code/Jar/j/Downloadjavaxjson102jar.htm
1. Disable access to application folder to web users so its content could not be opened by web server
1. Configure port intended to host Aigents TSP/IP console (for example - 1123) or HTTP Web API (for example 1180) so these ports ar made available for access from the internet (or intranet if you are going to access Aigents locally)
1. Start Aigents server in application folder: 
	```
	java -cp Aigents.jar:* -Xms2048m -Xmx3072m net.webstructor.agent.Farm
	```
	_Note: To avoid known issue with Java 8, you need to add the option to Java command line: -Dsun.zip.disableMemoryMapping=true_
1. Setup administrative account (need to fill properties email, name, surname, secret question, secret answer) - from Aigents command line interface:
```
I:Started TCP at 1123.
I:Started HTTP at 1180.
my email admin@admin.org, name admin, surname admin.
Login:email admin@admin.org, name admin, surname admin.
I:What your secret question, secret answer?
my secret question password, secret answer 12345.
I:What your password?
my password 12345.
...
I:Ok. Hello Admin Admin!
My Aigents 1.2.0 Copyright © 2017 Anton Kolonin, Aigents Group.
...
```

Optionally, for telnet terminal access, re-configure TCP/IP port, filling tcp port property (default is port 1123) - from Aigents command line interface:
...
your tcp port 1123.
Dec:Dec:[aigents [tcp port 1123]]. I:Ok.
what your tcp port?
Int:[aigents tcp port]?
I:My tcp port 1123.
...

Optionally, for HTTP/HTTPS web service access, re-configure web site parameters http origin, http port, cookie domain, cookie name (needed for Web API access only, default port is 1180):
...
your http origin http://aigents.mysite.org, http port 1180, cookie domain aigents.mysite.org, cookie name myaigent.
Dec:[aigents ([http origin http://aigents.mysite.org] [http port 1180] [cookie domain aigents.mysite.org] [cookie name myaigent])].
I:Ok.
your http origin, http port, cookie domain, cookie name?
Int:[aigents (http origin http port cookie domain cookie name)]?
I:My cookie domain aigents.mysite.org, cookie name myaigent, http origin http://aigents.mysite.org, http port 1180.
...

Optionally, for email access, setup email account (POP3 and SMTP) and for sending notifications (SMTP), filling properties email, email login, email password, mail.pop3s.host, mail.smtp.host (for example, based on mysite.org) - from Aigents command line interface:
...
your email aigent@mysite.org, email login aigent, email password qwerty, mail.pop3s.host mail.mysite.org, mail.smtp.host mysite.org.
Dec:[aigents ([email aigent@mysite.org] [email login aigent] [email password qwerty] [mail.pop3s.host mail.mysite.org] [mail.smtp.host mysite.org])].
I:Ok.
what your email, email login, email password, mail.pop3s.host, mail.smtp.host?
Int:[aigents (email email login email password mail.pop3s.host mail.smtp.host)]?
I:My email aigent@mysite.org, email login aigent, email password 'qwerty', mail.pop3s.host mail.mysite.org, mail.smtp.host mysite.org.
...

Save configuration and exit Aigents server with command-line interface:
...
you save!
I:Ok.
bye
Dec:[name bye].
...
I:Ended.

Start Aigetns over per step 8 above, but now better using it as daemon with nohup Linux command or console application with screen Linux command so that server keeps running uninterruptedly upon current session termination:
#nohup java -cp Aigents.jar:* -Xms2048m -Xmx3072m net.webstructor.agent.Farm console off &
[1] 12345
#appending output to nohup.out
...

or

#screen
#java -cp Aigents.jar:* -Xms2048m -Xmx3072m net.webstructor.agent.Farm
...
... open another Linux terminal session ...
...

If telnet terminal access is configured, ensure that you can remotely control Aigents server console, using telnet on TCP/IP port specified above (may be not secure because telnet protocol is not encrypted):
#telnet aigents.mysite.org 1123
Trying 111.22.0.33...
Connected to aigents.mysite.org.
Escape character is '^]'.
my name admin
What your password?
my password 12345
Ok. Hello Admi Admin!
My Aigents 1.2.0 Copyright © 2017 Anton Kolonin, Aigents Group.
...
bye
No thing.
Connection closed by foreign host.

If HTTP/HTTPS web service is configured, check access to Aigents Web service API HTTP/HTTPS protocol on TCP/IP port specified above to connect to Aigents server via Web brower or any HTTP client:
URL:http://aigents.mysite.org:1180/?my%20name%20admin
What your password?
URL::http://aigents.mysite.org:1180/?my%20password%2012345
Ok. Hello Admin Admin!
My Aigents 1.2.0 Copyright © 2017 Anton Kolonin, Aigents Group.
...

Check location of storage and logging files in application folder (containing Aigents.jar file):
Storage al.txt file sfould be backed up - either using either Dropbox folder to host the entire application folder or by custom backing up scripts.
Logging files named such as aigents-log-2017-12-31-log.txt should be cleaned up periodically - manually or using custom backing up scripts.

Aigents Basic Web API use
The following describes basics of Aigents Web API over HTTP/HTTPS protocol for the purpose of Web monitoring and news collection.
Consider that Aigents Web API is similar to plain HTTP/HTTPS REST service API with few differences:
Request is submitted in Aigents Language (AL)
Response is returned in either AL or JSON (in specific cases)
GET and POST methods can be used interchangeably, PUT and DELETE are not supported

Authentication is based on cookies (configured with properties cookie domain and cookie name, as described in previous section). That is, respective fields of the HTTP header should be filled. When Aigents server parses the HTTP request, it reads Cookie field of HTTP header. If filled in, it keeps it to maintain the session. If not filled in, it generates new one to create new session.
When Aigents server builds the HTTP response, it fills Set-Cookie field of HTTP header with value of existing on newly created session cookie. On the client side, the sessions and cookies may be maintained, for PHP example, with PEST wrapper of CURL functions (https://github.com/educoder/pest):
$baseURL = "http://aigents.mysite.org:1180/?";
$cookie;
function url_pest( $url ) {
	global $baseURL, $cookie;
	$pest = new Pest($baseURL);
	try {
		$url = $baseURL.urlencode($url);
		if (isset($cookie)) {
			$pest->curl_opts[CURLOPT_COOKIE] = $cookie;
		}
		$result = $pest->get($url);
		$cookie=$pest->last_headers['set-cookie'];
		unset($pest);
		return $result;
	} catch( Exception $e ) {
		unset($pest);
		throw($e);
	}
}
Note 1: While the following examples are given using AL over HTTP/HTTPS protocol, the same interactions can be performed over TCP/IP protocol using Telnet or any other client.
Note 2: When submitting AL requests over HTTP/HTTPS, space and its escaped version %20 are interchangeable, so "my%20name%20admin" is equivalent for "my name admin".

Requests and response are currently processed as HTTP GET requests, so the response is submitted to URL including domain name and optional port, followed by slash and question mark with following URI-encoded text of complete statement in Agent Language, to be parsed accordingly to language defintion (http://aigents.com/papers/2014AgentLanguageKolonin.pdf) while response is written to the stream, for example – the authentication is done as shown below:

URL:http://aigents.mysite.org:1180/?my%20name%20admin
What your password?
URL:http://aigents.mysite.org:1180/?my%20password%2012345
Ok. Hello Admin Admin!
My Aigents 1.2.0 Copyright © 2017 Anton Kolonin, Aigents Group.

Operations with sites, things and news used for web monitoring are performed accordingly to belief ontology of Aigents for Web (http://aigents.com/papers/2014AgentWatchingKolonin.pdf), as described below. All of the following operations are possible after user is authenticated (logged in) as shown above.

Listing sites is done with “my sites ...” statement, for example:
URL:http://aigents.mysite.org:1180/?what%20my%20sites?
Your sites http://money.cnn.com/data/markets/dow/, http://wired.com, http://www.nytimes.com/pages/business/, http://www.reuters.com/.

Listing things is done with “my knows ...” statement, for example:
URL:http://aigents.mysite.org:1180/?what%20my%20knows?
Your knows “goole $topic”, “dow jones $value”.

Listing news is done with “what times …, is … sources … ” statement, for example:
URL:http://aigents.mysite.org:1180/?what times yesterday is “google $topic”, text?
There text google chrome helper; text google chrome helper.
URL:http://aigents.mysite.org:1180/?what times 2014-10-18 is “google $topic”, topic?
There topic chrome helper; topic chrome helper.
URL:http://aigents.mysite.org:1180/?what times 2014-10-18 is “google $topic”, text, sources?
There sources http://www.reuters.com/, text google chrome helper; sources http://www.wired.com/ text google chrome helper.
URL:http://aigents.mysite.org:1180/?what sources http://www.reuters.com/ is “google $topic”, text, times?
There text google chrome helper, times 2014-10-18; text google glass technology, times 2017-10-19; text google now intelligence, times 2017-10-20.

Adding sites is done with “my sites … ” statement, for example:
URL:http://aigents.mysite.org:1180/?my%20sites%20http://www.reuters.com/,%20http://www.wired.com/.
Ok.

Adding things is done with “my knows … ” statement, for example:
URL:http://aigents.mysite.org:1180/?my%knows%kung-fu,%20'google $topic',%20“dow jones $value”.
Ok.

Configuring extra patterns for things is done with “... patterns …” statement, for example:
URL:http://aigents.mysite.org:1180/?kung-fu patterns bruce-lee, kung-fu, “kung fu”, shaolin.
Ok.

Configuring typed variables (of types such as word, time, number and money) for patterns is done having variables explicitly specified for a thing with “... has ...” statement, for example:
URL:http://aigents.mysite.org:1180/?dow jones $value” has value.
Ok.
URL:http://aigents.mysite.org:1180/?value is number.
Ok.
URL:http://aigents.mysite.org:1180/?Trump $action Putin” has action.
Ok.
URL:http://aigents.mysite.org:1180/?action is word.
Ok.
URL:http://aigents.mysite.org:1180/?Operation starts at $hour_x” has hour_x.
Ok.
URL:http://aigents.mysite.org:1180/?hour_x is time.
Ok.
URL:http://aigents.mysite.org:1180/?offer_price pas patterns “offer price $price” has price_y.
Ok.
URL:http://aigents.mysite.org:1180/?price_y is money.
Ok.

Aigents Extended Web API examples
The following presents various scenarios of Aigents use demonstrated with Aigents server test suite implemented in PHP.
Download English lexicon to application folder http://aigents.com/download/latest/lexicon_english.txt
Make sure Aigents server can be started on the localhost at port 1080 as described above.
Run Aigents server in application folder with no storage mode (with store path '' option):
#nohup java -cp Aigents.jar:* -Xms2048m -Xmx3072m net.webstructor.agent.Farm store path "''", cookie domain localtest.com, console off &

Create separate test folder for tests and download PHP test suote to it from http://aigents.com/download/latest/aigents_php.zip
Unzip contents of aigents_php.zip file in tests folder, with http subfolder created.
Host Web server in the http subfolder created above as root Web folder, on port 80 under domain localtset.com. For other domain, will need to create respective URL-s in PHP files. For hosting Wev server, Python SimpleHTTPServer application can be used, as in the following example:
Make sure you have Python with SimpleHTTPServer downloaded and installed.
Run Python web server as the following command:
#python -m SimpleHTTPServer 80

Run Aigents tests as the following command:
#php -f agent_test.php

Wait till tests are succeeded with success:
...
GET:Ok.
SUCCESS!

Study PHP files involved in tests for different use cases.
Feel free to changhe or customize Aigents PHP code as distributed under MIT License.

Aigents Social Network Integration with Web API
Aigents Server can be used to collect and analyse information in social networks, as it is described in articles How to get your personal analytics for Steemit social network with help of Aigents bot, Using Aigents bot service to get social network analytics for Facebook, Google+ and VKontakte, Personal social graph analysis for Steemit and Golos and Social Graph as Mirror in the Net.
The following describes how it can be configured and used to access social analytics functions of Aigents, either using existing demo Aigetns service at https://aigents.com/al?what your name (as Aigents-branded application) or proprietary servers configured as described above (under brand of your own).
There are many protocols can be used to access the Aigents Social Analytics, such as the following:
Using Aigents Chat - available for Aigents users at https://aigents.com/ only;
Using Aigents Facebook Messenger Bot - available for Aigents users only;
Using Aigents Telegram Bot - available for Aigents users only;
Using raw TCP/IP protocols via sockets, as discussed earlier - available at aigents.com:1123 and for proprietary servers both;
Using HTTP/HTTPS web servcice as discussed earlier - available at https://aigents.com/al? and for proprietary servers both - as discussed below:
To get reports on any user analytivs on public networks based on blockchain technology such as Steemit, Golos or Ethereum with demo Aigents service, use the following flow:
Logout prompt (recommended to do in the beginning of the session since long idle time to start new session unconditionally):
	https://aigents.com/al?my login

Login prompt:
https://aigents.com/al?my logout
	What your email, name, surname?

Enter login email accordingly to registration:
https://aigents.com/al?my email <your email>
	What your <secret question>?

Confirm login email accordingly to configured secret question and answer:
https://aigents.com/al?my <secret question> <secret answer>
	Ok. Hello <your name>! My Aigents 1.2.0 Copyright © 2018 Anton Kolonin, Aigents Group.

Request fresh report on at Steemit user (in HTML):
https://aigents.com/al?steemit id akolonin report fresh
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Steemit user requested earlier in HTML format:
https://aigents.com/al?steemit id akolonin report
	<HTML>

Request fresh report on at Steemit user in JSON format, for period of given number of days:
https://aigents.com/al?steemit id akolonin report fresh, format json, period 365
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Steemit user requested earlier in JSON format:
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>
	
Request fresh report on at Steemit user in HTML format, for period of given number of days, with specific tag tag (e.g. “psychology”) with specific options:
https://aigents.com/al?steemit id akolonin report fresh, format html, period 365, areas psychology 
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Steemit user requested earlier in HTML format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format html
	<HTML>

Request fresh report on at Steemit user in HTML format, for period of given number of days, with specific tag tag (e.g. “ai”) with specific options:
https://aigents.com/al?steemit id akolonin report fresh, format html, period 365, areas ai, best friends, my words by periods, my karma by periods 
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Steemit user requested earlier in HTML format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format html
	<HTML>

Request fresh report on at Steemit user in JSON format, for period of given number of days, with specific tag tag (e.g. “ai”) with specific option:
https://aigents.com/al?steemit id akolonin report fresh, format json, period 365, areas ai, my karma by periods 
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Steemit user requested earlier in JSON format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>

Request fresh report on at Golos user in JSON format, for period of given number of days, with specific options:
https://aigents.com/al?golos id akolonin report fresh, format json, period 365, my karma by periods 
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Golos user requested earlier in JSON format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>

Request fresh report on at Golos user in JSON format, for period of given number of days, with specific tag (Russian tag names are transliterated like “поиск” => “ru--poisk”) with specific options:
https://aigents.com/al?golos id akolonin report fresh, format json, areas ru--poisk, period 365, my karma by periods 
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Golos user requested earlier in JSON format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>

Request fresh report on at Golos user in JSON format, for period of given number of days, with specific option - best friends, listing only top of them above 20% threshold:
https://aigents.com/al?golos id akolonin report fresh, format json, period 365, best friends, threshold 20
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Golos user requested earlier in JSON format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>

Request fresh report on at Golos user in JSON format, for period of given number of days, with specific option - best friends, listing all of them above 0% threshold:
https://aigents.com/al?golos id akolonin report fresh, format json, period 365, best friends, threshold 0
	Your report is being prepared, please check back in few minutes… 

Get completed report on any Golos user requested earlier in JSON format (refresh if in browser):
https://aigents.com/al?steemit id akolonin report, format json
	<JSON>
	
Extended command options may be used for fine tuning contents and format of analytics:
format <file type>
<file type> := html | json
(whether to encode report in html or json)
Example: 
https://aigents.com/al?steemit id akolonin report fresh, format json

threshold <percents> 
<percents> := 0  - 100
(number of percents of importance to hide least important items)
Example:
https://aigents.com/al?steemit id akolonin report fresh, format json, threshold 50

period <days>
<days> := 0  - 1000
(number of days to include in analysis from current date)
Example: 
https://aigents.com/al?steemit id akolonin report fresh, format json, threshold 50, days 365

areas <tag> 
<tag> := <keyword>
(tag to restrict scope of analysis, only one tag may be used; multiple tags will be enabled later)
Example:
https://aigents.com/al?steemit id akolonin report fresh, format json, threshold 50, days 365, areas ai

Note: When using https://golos.io/, only English encodings of tags is supported, so if need to use tag “разум”, should use “ru--razum” instead.

Note: To change report parameters, each time need to do 2 or more calls, depending on amount of data to be included in report, like in the following.
First call - request report with parameters, for example: 
https://aigents.com/al?steemit id akolonin report fresh, format json, threshold 50, days 365, areas ai
Next calls - request report data in polling manner, each 10-30 seconds, until it returns valid json or html data, for example:
https://aigents.com/al?steemit id akolonin report
For access of personal analytics of users on Facebook, VKontakte and Google+, it is required to use proprietary application server configued with respect to policies of these social networks in respect to personal data and privacy protection, with the following options:
Application id, key and token for proprietery application obtained for respective social networks, accordingly to their API and developer policies. Obtained application keys and tokens should be configured with the following commands with command line, telnet or web chat with counfuguration saved and server restarted after changes.

Facebook:
your facebook id <facebook_id>, facebook key <facebook_key>.

Google+:
your google id <google_id>, google key <google_key>.

VKontakte (with server token required):
your vkontakte id <vkontakte_id>, vkontakte key <vkontakte_key>, vkontakte token <vkontakte_token>.

For any user of these social networks, the user should accept account binding for respective proprietary application with given social network account. The proritetary application should maintain user tokens obtained in the course of account binding and provide these tokens to the engine upon request for analytics with expra token property, as in the following examples:
facebook id 10203057937968601, token <token>, report
google id 113174676192873877221, token <token>, report, format json

The proritetary application should maintain security measures necessary to secure application keys and user tokens so they are not transmitted over public networks without encryption and are not stored in publicly available databases. It is responsibility of prorietary application to ensure protection of personal data of social network users, securing access to respective keys and tokens stored in application itself, in the Aigents server and being transmitted between them.
Aigents Web UI Customization
For custom Aigents Web User Interface (UI), download the http://aigents.com/download/latest/aigents_web.zip archive containing basic set of JavaScript files and HTML/CSS templates and feel free to override or replace them with account to Aigents branding requirement below.
Feel free to changhe or customize Aigents Web UI code as distributed under MIT License.
Aigents Branding
For any academic, non-commercial or commercial use of Aigents service or user interface, it is required to provide Aigents branding information in the form of text "Powered by Aigents" accompanied with one of the following icons, all linked to the https://aigents.com web site:
  
Aigents Contact Information
To stay in touch with Aigetns creators and maintainers for help and feedback, use the following channels:
Via email (contact at aigents dot com): 
On Facebook:
Aigents page
Artificial General Intelligence in Russia group
Social Intelligence group
Aigents chat on Messenger
On Google+
On YouTube
On Steemit
On Golos
On VKontakte
