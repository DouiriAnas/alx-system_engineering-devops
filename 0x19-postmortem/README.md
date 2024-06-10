
# Postmortem Report

## Incident report for 504 error / Site Outage

### Summary

On June 10th, 2024 at midnight, the server access went down, resulting in a 504 error for anyone trying to access the website. The server is based on a LAMP stack.

### Timeline

- **00:00 PST** - 500 error for anyone trying to access the website.
- **00:05 PST** - Ensuring Apache and MySQL are up and running.
- **00:10 PST** - The website was not loading properly. Background checks revealed the server and database were working correctly.
- **00:12 PST** - After a quick restart, the Apache server returned a status of 200 and OK while trying to curl the website.
- **00:18 PST** - Reviewing error logs to identify the source of the error.
- **00:25 PST** - Checked `/var/log` and found that the Apache server was being prematurely shut down. PHP error logs were missing.
- **00:30 PST** - Checking `php.ini` settings revealed all error logging had been turned off. Turned the error logging on.
- **00:32 PST** - Restarted Apache server and checked the error logs for PHP errors.
- **00:36 PST** - Reviewing PHP error logs revealed a mistyped file name causing incorrect loading and premature closing of Apache.
- **00:38 PST** - Fixed the file name and restarted the Apache server.
- **00:40 PST** - Server is now running normally and the website is loading properly.

### Root Cause and Resolution

The issue was due to a wrong file name referenced in the `wp-settings.php` file. The server responded with a 500 error when trying to curl it. Error logs revealed no logs were being created for PHP errors, and Apache logs provided little information. After turning on error logging in `php.ini`, restarting Apache revealed a misspelled file extension (.phpp). Fixing the file name and redeploying via Puppet corrected the issue across all servers.

### Corrective and Preventive Measures

- Ensure all servers and sites have error logging turned on to easily identify errors.
- Test all servers and sites locally before deploying on a multi-server setup to catch errors early and reduce fixing time if the site goes down.


