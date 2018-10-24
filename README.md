# cbioportal-valididation-demo
Example commands and portal info for demo purposes

## Pull docker image
Checkout the docker image which contains the validation and import code.
```
docker pull thehyve/cbioportal:v1.16.0_backport4787-4917
```

## Validate study
Perform validation of cBioPortal formatted data.
```
# Set local files and folders
study_dir=/Users/sander/Desktop/demo/cbioportal-valididation-demo/acc_tcga_pan_can_atlas_2018/
report_dir=/Users/sander/Desktop/demo/reports
portalinfo_dir=/Users/sander/Desktop/demo/cbioportal-valididation-demo/portalinfo_dir

docker run -it --rm --net cbio-net \
    -v $study_dir:/study:ro \
    -v $report_dir:/outdir \
    -v $portalinfo_dir:/portalinfo:ro \
    thehyve/cbioportal:v1.16.0_backport4787-4917 \
    validateData.py -p /portalinfo -s /study --html=/outdir/report.html
```

## Import study
This requires `portal.properties` to be configured with database connection details.
```
# Set local files and folders
study_dir=/Users/sander/Desktop/demo/cbioportal-valididation-demo/acc_tcga_pan_can_atlas_2018
report_dir=/Users/sander/Desktop/demo/reports
portalinfo_dir=/Users/sander/Desktop/demo/cbioportal-valididation-demo/portalinfo_dir
portal_properties=/Users/sander/Desktop/demo/portal.properties

docker run -it --rm --net cbio-net \
    -v $portal_properties:/cbioportal/portal.properties:ro \
    -v $study_dir:/study:ro \
    -v $report_dir:/outdir \
    -v $portalinfo_dir:/portalinfo:ro \
    thehyve/cbioportal:v1.16.0_backport4787-4917 \
    metaImport.py -p /portalinfo -s /study --html=/outdir/report.html -o

docker restart cbioportal-container
```

## Create new portal info directory
This is required when genes, gene tables or cancer types have changed in the database, or if the portalInfo directory does not exist yet.
```
# Set local files and folders
portalinfo_dir=/Users/sander/Desktop/demo/cbioportal-valididation-demo/portalinfo_dir

docker run --rm --net cbio-net \
    -v $portal_properties:/cbioportal/portal.properties:ro \
    -v $portalinfo_dir:/portalinfo \
    -w /cbioportal/core/src/main/scripts \
    thehyve/cbioportal:v1.16.0_backport4787-4917 \
    ./dumpPortalInfo.pl /portalinfo
```
