## Generating Maptiles

To generate maptiles, execute `./generate_maptiles.sh [FRACTION] [QUERYFILE]` where `[FRACTION]` is some decimal between 0 and 1, and [QUERYFILE] is either `../queries/join_geo_sampling.sql` or `../queries/concise_join_geo_sampling.sql`. As currently written, the script will then generate maptiles based on a random sample of `[FRACTION]` of FCC June 2019 data.

### Data Pipeline Outline
`generate_maptiles.sh` generates maptiles as follows: 

1. Join FCC data with geographical data (by block) via BigQuery tables
2. Export data from BigQuery table to CSV in a temp GCS bucket
3. Convert CSV to GeoJSON using ogr2ogr on local machine
4. Convert GeoJSON to .pbf maptiles using tippecanoe on local machine
5. Upload maptiles to public GCS bucket

Step 3 happens via a call to `csv_to_geojson.sh`. The SQL queries used for step 1
purposely name the geography field as WKT, because ogr2ogr looks for a CSV field
named WKT to treat as the geometry attribute in the resulting GeoJSON. See the
[queries README](../queries/README.md) for more information about the queries
for step 1.

The final public GCS bucket in step 5 has not yet been determined. The bucket being
used currently is Google internal and hence private.
