# Watchmen of Israel Website and Store

Technical overview by Jonathan D. Fluck | October 2026

This concept overview is for developers and maintainers who need to understand how the Watchmen of Israel website is organized and how its store behaves. It covers two core concepts: the application architecture and store operation, including product administration and automatic Shabbat closure.

The website at [watchmenofisrael.org](https://watchmenofisrael.org) combines public ministry information with a custom Django store for books and an electronic Zadok calendar.

## Application architecture

The application runs on a DigitalOcean Linux droplet. Nginx receives web traffic and forwards application requests to Gunicorn, which runs Django. Django handles application logic, renders pages, and accesses stored data through its models.

| Component | Role |
| --- | --- |
| DigitalOcean droplet | Hosts the Linux environment and application services. |
| Nginx | Receives web traffic and forwards application requests. |
| Gunicorn | Runs the Django application. |
| Django and Python | Handle requests, store logic, data models, and administration. |
| HTML and JavaScript | Present public pages and product information. |

## Primary use cases

- Public visitors read the ministry’s mission, vision, beliefs, and FAQ pages.
- Visitors register for updates through the mailing-list signup form.
- Store visitors browse product descriptions when the store is open.
- Administrators create and update product records through Django admin.

## Product administration

The store uses a reusable product detail page so products share a consistent presentation. Administrators manage individual product records through Django admin. Developers change the models when a product type needs a different data structure.

Model changes and product records serve different purposes. Adding a record supplies data to an existing structure. Changing a model can require a database schema update through Django migrations.

## Automatic Shabbat closure

The store closes from local sunset on Friday until local sunset on Saturday. During Shabbat, visitors see a dedicated closure screen instead of the store. The hosting environment remains online.

The application uses libraries to calculate local sunset times. It checks whether the current time falls within the Shabbat interval and selects the corresponding screen.

| Time interval | Store state | Displayed screen |
| --- | --- | --- |
| Friday sunset until Saturday sunset | Closed | Shabbat message stating that the store is closed. |
| Outside the Shabbat interval | Open | Normal store. |

Sunset times change through the year. Calculating the boundaries allows the closure schedule to follow those changes without manually adjusting weekly opening hours.

## Database maintenance

Django migrations keep the database schema aligned with the application’s models. The migration commands have separate roles:

- `makemigrations` generates migration files from model changes.
- `migrate` applies the migration files to the database.

Deployments that change models must account for the corresponding schema changes. Product administration and other data-dependent workflows rely on the deployed code and database having compatible structures.
