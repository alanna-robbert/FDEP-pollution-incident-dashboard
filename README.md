# FL DEP Pollution Incident Dashboard

A web dashboard for exploring pollution incidents reported to the Florida Department of Environmental Protection (FL DEP) through its Pollution Notification Program (PNP). It has a searchable, sortable data table and a map with charts showing where incidents happen and what kinds of spills are reported.

**Live app:** [alanna-robbert.github.io/FDEP-pollution-incident-dashboard](https://alanna-robbert.github.io/FDEP-pollution-incident-dashboard/)

## Features

- **Data table** (`html/index.html`): every incident record, with a search across all fields, a filter on each column, column sorting and 50 records per page.
- **Map & charts** (`html/map.html`):
  - a map of Florida with clustered incident markers and a popup for each incident
  - a bar chart of incidents by county
  - a breakdown of spill types (sewer, petroleum/fuel, wastewater, chemical and others)
  - a search box and a county filter that update the map and charts together
- **Summary statistics**: total incidents, counties affected, facilities and reporters.
- **CSV upload**: replace the dataset with a newer export straight from the page.

## Project structure

```text
FDEP-pollution-incident-dashboard
├── css
│   └── index.css              shared styles for both pages
├── html
│   ├── index.html             data table page
│   └── map.html               map & charts page
├── php
│   ├── api.php                returns every incident as JSON
│   ├── upload.php             replaces the table with an uploaded CSV
│   ├── import.php             replaces the table with the published Google Sheet CSV
│   ├── db_config.example.php  template for database credentials
│   └── db_config.php          real credentials (not committed)
├── .htaccess                  blocks browser access to db_config.php
├── .gitignore
├── LICENSE
└── README.md
```

## How it works

```text
FL DEP PNP reports ──► Google Sheet (published as CSV)
                              │
                   import.php │   upload.php ◄── CSV uploaded from the page
                              ▼        │
                        MySQL table ◄──┘
                       (fdepPollution)
                              │
                         api.php (JSON)
                              │
                   ┌──────────┴──────────┐
                   ▼                     ▼
              index.html             map.html
             (data table)        (map + charts)
```

## How the project came together

The incident data comes from FL DEP's public [Pollution Notification Program reports](https://prodenv.dep.state.fl.us/DepPNP/reports/viewIncidentDetails?page=1). Those reports are hard to explore in bulk, so the records were gathered into a Google Sheet that's published as a CSV.

The first version of the dashboard was a single HTML page that read that CSV directly in the browser. As the dataset grew, it moved to a small backend:

1. **Database.** The records were loaded into a MySQL table (`fdepPollution`). `import.php` pulls the published Google Sheet and refreshes the table.
2. **API.** `api.php` serves the table as JSON, so both pages load from one source instead of each parsing a large CSV.
3. **Map & charts.** A second page was added with [Leaflet](https://leafletjs.com/) and marker clustering for the map, and [Chart.js](https://www.chartjs.org/) for the county and spill-type charts. Spill types are sorted into categories by matching keywords in each incident's name and report.
4. **Uploads.** `upload.php` lets a user replace the data with a newer CSV export without editing the database by hand. It checks that the required columns are there first.
5. **Organization.** The code was moved from the server into this repository and split into `html/`, `css/` and `php/` folders. Database credentials live in one config file that stays off GitHub and can't be opened from a browser.

## Built with

- HTML, CSS and vanilla JavaScript
- [Leaflet](https://leafletjs.com/) and [Leaflet.markercluster](https://github.com/Leaflet/Leaflet.markercluster), with map tiles from [OpenStreetMap](https://www.openstreetmap.org/)
- [Chart.js](https://www.chartjs.org/)
- PHP (mysqli) and MySQL

## Running it yourself

1. You need a web server with PHP 7.4+ (with mysqli) and a MySQL database containing an `fdepPollution` table: an auto-increment `id` column plus one column for each CSV header.
2. Copy `php/db_config.example.php` to `php/db_config.php` and fill in your database credentials.
3. Keep the `css/`, `html/` and `php/` folders side by side on the server. The pages load `../css/index.css` and call `../php/*.php`.
4. Visit `html/index.html` through the web server. Opening it as a local file won't load the data.

For local testing, run `php -S localhost:8000` from the project folder and open <http://localhost:8000/html/index.html>.

## Data source

Florida Department of Environmental Protection, [Pollution Notification Program](https://prodenv.dep.state.fl.us/DepPNP/reports/viewIncidentDetails?page=1).

## License

See [LICENSE](LICENSE).

© 2026 Alanna Fitzgerald Robbert
