# Build
docker build -t overv/openstreetmap-tile-server:local .

# Import
time docker run \
    -v /mnt/shared/osm/poland-20230408.osm.pbf:/data/region.osm.pbf \
    -v /mnt/data/osm-overv/database:/data/database \
    -e THREADS=16 \
    -e "OSM2PGSQL_EXTRA_ARGS=-C 8192" \
    -e "FLAT_NODES=enabled" \
    --shm-size=8g \
    overv/openstreetmap-tile-server:local \
    import

# Run
docker run \
    -p 8080:80 \
    -v /mnt/data/osm-overv/database:/data/database \
    -v /mnt/data/osm-overv/tiles:/data/tiles \
    -v ./scripts:/data/scripts \
    -e THREADS=16 \
    -e "OSM2PGSQL_EXTRA_ARGS=-C 8192" \
    --shm-size=8g \
    overv/openstreetmap-tile-server:local \
    run

# Render Poland
# 55.18244670430346, 13.716520656765233
# 48.919399063956604, 24.505093946865774 
time /data/scripts/render_list_geo.pl -n 80 -l 100 -z 0 -Z 15 -x 13 -X 24 -y 48 -Y 56

