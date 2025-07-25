# CMMC Level 1 with OSCAL

## Obtain an appropriate CMMC Level 1 catalog

https://github.com/FATHOM5CORP/oscal/tree/main/content/SP800-171/oscal-content
is OSCAL 1.0.x -- not easy to automatically upgrade to 1.1.x

### Download a 800-171 OSCAL v1.1.x catalog 

From: https://cyberesi-cg.com/oscal-cprt-catalog-project/
Download: OSCAL_Cat_sp_800_171_2_0_0_230915132251.xml

```
wget https://cyberesi-cg.com/oscal_catalogs_1c/OSCAL_Cat_sp_800_171_2_0_0_230915132251.xml

oscal-cli validate OSCAL_Cat_sp_800_171_2_0_0_230915132251.xml
```

### Optional: convert to JSON
```
oscal-cli convert --to=JSON \
    OSCAL_Cat_sp_800_171_2_0_0_230915132251.xml \
	OSCAL_Cat_sp_800_171_2_0_0_230915132251.json

oscal-cli validate OSCAL_Cat_sp_800_171_2_0_0_230915132251.json
```

### Create cmmc-level-1-catalog.json
Use the local `cmmc-level-1-profile.json` that references the downloaded XML file:
```
oscal-cli validate cmmc-level-1-profile.json

oscal-cli resolve-profile --to=JSON cmmc-level-1-profile.json \
    cmmc-level-1-catalog.json
	
oscal-cli validate cmmc-level-1-catalog.json
```

## Discovery: use `trestle author` to allow SSP updates in markdown

### Note: Trestle may not be appropriate for 800-171
From https://github.com/usnistgov/OSCAL/issues/1002

The SP 800-171r2 is not a catalog of controls but rather a collection of requirements mapped to security controls in a many-to-many pattern. We planned for OSCAL 1.1.0 the development of an OSCAL model that would support a mapping between standards and/or requirements. **Catalog model is mot considered by us suitable for the representation of 800-171r2, NIST's CSF, or other similar guidelines** because it will not serve well the security automation, as it will be difficult if not impossible to properly, automatically track and adjudicate the control implementation's and assessment's findings onto the 800-171r2 requirements. The many-to-many relation and support for traceability and aggregation of findings are not modeled in the OSCAL Catalog model. **We acknowledge the community's need of such model and scheduled its development as part of OSCAL 1.1.0.**

### Set up trestle locally
```
mkdir local-trestle
cd local-trestle

uv venv
source .venv/bin/activate
uv pip install compliance-trestle
trestle version
trestle init
```

### Import and split Level 1 catalog

```
trestle import --file ../cmmc-level-1-catalog.json --output cmmc-level-1

cd catalogs/cmmc-level-1
trestle split -f ./catalog.json -e 'catalog.metadata,catalog.groups,catalog.back-matter'

cd catalog
trestle split -f ./groups.json -e 'groups.*.controls.*'
```
