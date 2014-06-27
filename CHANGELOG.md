# Changelog

## 1.0

* Django 1.7 compatibility.
* API
  * If the `contains` parameter is an invalid latitude and longitude pair, return the invalid pair in the error message.

## 0.2

* Python 3 compatibility. [#14](https://github.com/opennorth/represent-boundaries/pull/14)
* Fix various small bugs and encoding issues and add help text.
* API
  * Add CORS support.
  * New API throttle.
  * Enable filtering boundaries by `external_id`.
  * If a request is made with invalid filters, return a 400 error instead of a 500 error.
  * JSON
    * Add `extent` and `last_updated` to the detail of boundary sets.
    * Add `external_id` to the list of boundaries.
    * Add `extent`, `centroid` and `extra` to the detail of boundaries.
* Loading shapefiles
  * Calculate the geographic extent of boundary sets and boundaries.
  * Re-load a boundary set if the `last_updated` field in its definition is more recent than in the database, without having to set the `--reload` switch.
  * If two boundaries have the same slug, and the `--merge` option is set to `union` or `combine`, union their geometries or combine their geometries into a MultiPolygon.
  * Follow symbolic links when traversing the shapefiles directory tree.
  * If `DEBUG = True`, prompt the user about the risk of high memory consumption. [#15](https://github.com/opennorth/represent-boundaries/pull/15)
  * Log an error if a shapefile contains no layers.
  * Add an example definition file.
  * Definition files
    * New `name` field so that a boundary set's slug and name can differ.
    * New `is_valid_func` field so that features can be excluded when loading a shapefile.
    * New `extra` field to add free-form metadata.
  * ZIP files
    * If the `--clean` switch is set, convert 3D features to 2D when loading shapefiles from ZIP files.
    * Clean up temporary files created by uncompressing ZIP files.
    * Support ZIP files containing directories.
* Management commands
  * Add a `compute_intersections` management command to output information about overlapping boundaries from a pair of boundary sets.
  * Remove the `startshapedefinitions` management command.

## 0.1

This first release is a [significant refactoring](https://github.com/opennorth/represent-boundaries/commit/db2cdaa381ecde423dd68962d79811925092d4da) of [django-boundaryservice](https://github.com/newsapps/django-boundaryservice) from [this commit](https://github.com/newsapps/django-boundaryservice/commit/67e79d47d49eab444681309328dbe6554b953d69). Minor changes may not be logged.

* Don't `SELECT` geometries when retrieving boundary sets from the database.
* Fix various small bugs and encoding issues and improve error messages.
* API
  * Use plural endpoints `boundary-sets` and `boundaries` instead of `boundary-set` and `boundary`.
  * Move boundary detail endpoint from `boundaries/<boundary-slug>/` to `boundaries/<boundary-set-slug>/<boundary-slug>/`.
  * Remove some fields from list endpoints, remove geospatial fields from detail endpoints, and add geospatial endpoints.
  * Add a `touches` boundary filter.
  * Change the semantics of the `intersects` boundary filter from "intersects" to "covers or overlaps".
  * If the parameter `format=apibrowser` is present, display a HTML version of the JSON response.
  * Support `format=kml` and `format=wkt`.
  * JSON
    * Rename `name` to `name_plural`, `singular` to `name_singular`, and `boundaries` to `boundaries_url` on boundary sets.
    * Move `boundaries_url` under `related` on boundary sets.
    * Change `boundaries_url` from a list of boundary detail URLs to a boundary list URL.
    * Add `licence_url` to the detail of boundary sets.
    * Remove `slug`, `resource_uri`, `count` and `metadata_fields` from the detail of boundary sets.
    * Rename `kind` to `boundary_set_name` and `set` to `boundary_set_url` on boundaries.
    * Move `boundary_set_url` under `related` on boundaries.
    * Add `shape_url`, `simple_shape_url`, `centroid_url` and `boundaries_url` under `related` to the detail of boundaries.
    * Remove `slug`, `resource_uri` and `centroid` from the detail of boundaries.
* Loading shapefiles
  * Allow multiple `definition.py` files anywhere in the shapefiles directory tree, instead of a single `definitions.py` file.
  * Use EPSG:4326 (WGS 84, Google Maps) instead of EPSG:4269 (NAD 83, US Census) by default.
  * Add a `--reload` switch to re-load shapefiles that have already been loaded.
  * Remove the `--clear` switch.
  * Make the simplification tolerance configurable.
  * Definition files
    * Rename `ider` to `id_func`, `namer` to `name_func`, and `href` to `source_url`.
    * New `slug_func` to set a custom slug.
    * New `licence_url` field to link to a data license.
    * If `singular`, `id_func` or `slug_func` are not set, use sensible defaults.