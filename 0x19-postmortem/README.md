Postmortem

Upon the release of my AirBnB clone project, approximately 21:17 Central Africa Time (CAT), an outage occurred on an isolated Ubuntu 20.04 container running an Apache web server. GET requests on the server led to 500 Internal Server Error's, when the expected response was an HTML file defining a simple Hbnb site.

Debugging Process:

Hisham Balboul encountered the issue upon opening the website from an external browser, roughly 21:26 CAT. He promptly proceeded to undergo solving the problem.

Checked running processes using ps aux. Two apache2 processes - root and www-data - were properly running.

Looked in the sites-available folder of the /etc/apache2/ directory. Determined that the web server was serving content located in /var/www/html/.

In one terminal, ran strace on the PID of the root Apache process. In another, curled the server. Expected great things... only to be disappointed. strace gave no useful information.

Repeated step 3, except on the PID of the www-data process. Kept expectations lower this time... but was rewarded! strace revelead an -1 ENOENT (No such file or directory) error occurring upon an attempt to access the file /var/www/html/hbnb/8-index.htmll.

Looked through files in the /var/www/html/ directory one-by-one, using Vim pattern matching to try and locate the erroneous .htmll file extension. Located it in the hbnb directory.

Removed the trailing l from the file name.

Tested another curl on the server. 200 A-ok!

Summation:

In short, a typo. In full, the apache app was encountering a critical error when tyring to load the file class-wp-locale.phpp8-index.htmll. The correct file name, located in the hbnb directory of the application folder, was 8-index.html.

Patch involved a simple fix on the typo, removing the trailing l.

Prevention:

This outage was not a web server error, but an application error. To prevent such outages moving forward, please keep the following in mind.

Test! Test the application before deploying. This error would have arisen and could have been addressed earlier had the app been tested.

Status monitoring. Enable some uptime-monitoring service such as UptimeRobot to alert instantly upon outage of the website.
