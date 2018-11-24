# Roundcube Calendar plugin with CalDAV support

Using Kolab-plugins from https://git.kolab.org/ (based on v3.2.9)    
Using the CalDAV driver from https://gitlab.awesome-it.de/kolab/roundcube-plugins/tree/feature_caldav (based on v3.2.8)

Hosted at https://git.faster-it.com/roundcube_calendar    
Mirrored at https://github.com/fasterit/roundcube_calendar    


## About

Roundcube is an awesome web mailer and a good platform for calendaring as the work from
Kolab Systems AG shows. Their Roundcube plugins (primarily developed to support the
Kolab Groupware server) have been extended by "Awesome IT" (a GbR out of Karlsruhe, Germany)
to support CalDAV calendars.

Getting this all to work is a bit of a hassle so we - Faster IT GmbH - decided to
combine the various parts into a distribution when we were asked to implement this for a nonprofit company.
For the public benefit we re-publish the work under the original AGPLv3+ license.

This version is compatible with the [rcmcarddav plugin](https://github.com/blind-coder/rcmcarddav)
from [Benjamin Schieder](http://www.benjamin-schieder.de/carddav.html).    
We hacked this by moving the ancient SabreDAV version the WebDAV driver uses into its own
`OldSabre` namespace so it does not collide with rcmcarddav's version.     
We updated it to 1.8.12 which is the last version of the 1.x series (SabreDAV is at 3.1 now).

That way you can have both CalDAV and CardDAV supported simultaneously in Roundcube by Free Software.

There is work underway at "Awesome IT" to update SabreDAV to a recent version
(see their [gitlab](https://gitlab.awesome-it.de/kolab/roundcube-plugins/commit/5a0825b89a0b0183bf8469e66b667e294309b609))
but at the time of writing this (2016-03-07) it has not yet been completed.


## Installation

Install the latest 1.1 branch version of Roundcube.
This plugin is not compatible with 1.2 and later at this time.
See the bottom of this README for where to find some patches in case you want to port this plugin to newer Roundcube releases.

Clone the repository and copy `calendar` and `libcalendaring` to the `plugins` directory
of your roundcube installation:

    $ git clone git://github.com/fasterit/roundcube_calendar.git
    $ cd roundcube_calendar
    $ cp -r calendar/ libcalendaring/ /var/www/htdocs/roundcube/plugins/ # adjust as needed

Copy and edit the supplied config.inc.php.dist:

    $ cd /var/www/htdocs/roundcube/plugins/
    $ cp config.inc.php.dist config.inc.php
    $ $EDITOR config.inc.php

Change the driver to include caldav (this is why you use this version of the calendar
plugins, right?);

    // backend type (database, kolab, caldav, ical)
    $config['calendar_driver'] = array('database', 'caldav');
    $config['calendar_driver_default'] = 'caldav';

Provide a unique crypt_key. This is used to scramble the passwords for CalDAV calendars
before they are stored into the database:

    // Crypt key to encrypt passwords for added iCAL/CalDAV calendars
    $config['calendar_crypt_key'] = "<run pwgen -sy 32 to get some nice random data for here>";

Now we need to import the database tables for the database driver (these tables are also required
for CalDAV!) and the caldav driver. Your database name (<db-name>) will usually be roundcubemail or roundcube.
Check with `mysql -e 'show databases;'`.

    $ mysql <db-name> < /var/www/htdocs/roundcube/plugins/calendar/drivers/database/SQL/mysql.initial.sql
    $ mysql <db-name> < /var/www/htdocs/roundcube/plugins/calendar/drivers/caldav/SQL/mysql.initial.sql

Finally enable the calendar plugin in your **global** roundcube `config.inc.php`:

    $ $EDITOR /var/www/htdocs/roundcube/config/config.inc.php

Add 'calendar' to the list of active plugins:

    $config['plugins'] = array(
        (...)
        'calendar',
    );


## Usage

After installation, add a CalDAV Calendar by going to the Calendar view and clicking the [+] in the
lower right corner. Select CalDAV Calendar and provide a name, URL and username / password for your calendar.

In case you want to add calendars that are served with self-signed SSL certificates, be sure to set

    // Set to false to allow CURL to connect with SSL hosts that it can't verify the certificates from
    // e.g. for self-signed certificates.
    // technical note: This sets CURLOPT_SSL_VERIFYPEER _and_ CURLOPT_SSL_VERIFYHOST.
    $config['calendar_curl_secure_ssl'] = false;

in `calendar/config.inc.php`. And know what you are doing.


## Debugging

See `/var/www/htdocs/roundcube/logs/` for errors.

## Support

There is none. Really. This code is not for the faint of heart.    
Patches welcome and some for Roundcube 1.2 are available in github issues and pull requests at
https://github.com/fasterit/roundcube_calendar .
