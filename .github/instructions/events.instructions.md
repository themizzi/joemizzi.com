---
applyTo: events/**/*.md
---

Please include a flyer for the event. If not found on any provided link, please try searching for it online. If none is found, then do not put a placeholder.

Please include the event location and create a new entry in `locations` if one does not already exist.

Please include the event date in the `doors` date field.

Please put the current date in the `date` field in the format `YYYY-MM-DDTHH:MM:SS-05:00` with a time of `00:00:00`.

Please include a list of artists performing at the event in the `artists` field. If an artist does not already exist in the `artists` folder, please create a new entry for them.

## Super Events and Child Events

Represent a multi-day festival with one parent event page and child event pages for each day or separately listed performance.

On the parent festival page:

* Set `is_super_event = true` and give it a unique `event_id`.
* Use `event_start` and `event_end` for the festival date range (format: `YYYY-MM-DDTHH:MM:SS±HH:MM`).
* Use `event_city` when the festival city is known but no specific venue is assigned.
* Add the festival-level location, flyer, and website/ticket link.

On each child event page:

* Set `super_event` to the parent page's `event_id`.
* Add that child's `artists`, venue, flyer, and performance-specific link.
* Set `doors` to the child's performance date/time when known; omit it if the date/time is TBA.

Child pages are linked automatically from the parent page and excluded from the homepage and full events listing. Only parent super events and standalone events appear in those listings. Use Schema.org `subEvent` / `superEvent` relationships to represent the hierarchy.

## Location Fields

When creating a new location entry in `content/locations/`:

* Required: `address`, `city`, `state`, `title`
* Optional: `country` (only include for non-USA venues, e.g., "Canada")
