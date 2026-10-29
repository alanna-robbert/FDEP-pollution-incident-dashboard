# FDEP-pollution-incident-dashboard

Dashboard for Florida DEP Pollution Notification Program incidents: a searchable data table and a map with charts.

## Project layout

```
css/index.css        shared styles
html/index.html      data table page
html/map.html        map & charts page
php/api.php          returns all incidents as JSON
php/upload.php       replaces the table with an uploaded CSV
php/import.php       replaces the table with the published Google Sheet CSV
php/db_config.php    database credentials (not committed)
.htaccess            blocks browser access to db_config.php
```

The pages load `../css/index.css` and call `../php/*.php`, so deploy the folders with this same layout.

## Setup

1. Requires a web server with PHP 7.4+ (mysqli) and a MySQL database with an `fdepPollution` table (an `id` auto-increment column plus one column per CSV header).
2. Copy `php/db_config.example.php` to `php/db_config.php` and fill in your credentials.
3. Open `html/index.html` through the web server (not as a local file).
