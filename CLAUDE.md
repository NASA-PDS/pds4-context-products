# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository manages the **latest versions** of all PDS4 Context Products across NASA and IPDA archives. It serves as the primary conduit for submitting updates to existing context objects, proposing new context objects, and filing issues on existing context objects.

**Important**: This repository contains only the latest version of each context product. The full official archive with all versions is maintained by the PDS Engineering Node at https://pds.nasa.gov/data/pds4/context-pds4/.

## Repository Structure

```
data/pds4/context-pds4/
├── agency/          - Space agency context products (ESA, JAXA, KARI, ISRO, NASA)
├── facility/        - Laboratory and observatory facilities
├── instrument/      - Scientific instruments (832+ products)
├── instrument_host/ - Spacecraft and platforms
├── investigation/   - Missions and investigations
├── miscellaneous/   - Other context types
├── node/           - PDS node information
├── target/         - Celestial bodies and phenomena (1542+ products)
├── telescope/      - Telescopes
└── bundle_context.xml

data/pds4/superseded/
└── context/        - Deprecated context products
```

Each context type folder contains:
- Individual XML files (e.g., `instrument.msl.apxs_1.1.xml`)
- Collection inventory files (CSV/TAB format)
- Collection labels (XML)

## Context Product Naming and Versioning

### File Naming Convention
Context product files follow this pattern:
- `{type}.{specific_identifier}_v{major}.{minor}.xml`
- Example: `instrument.msl.apxs_1.1.xml`

### Version Numbering
- New products start at `v1.0`
- Minor changes increment minor version: `v1.0` → `v1.1`
- Major changes increment major version: `v1.0` → `v2.0`
- Version in filename must match `<version_id>` in XML

### Logical Identifiers (LIDs)
LIDs are permanent and structured as:
- Format: `urn:nasa:pds:context:{type}:{identifier}`
- Example: `urn:nasa:pds:context:instrument:msl.apxs`
- **Never change the LID when updating a product**

## Working with Context Products

### Creating a New Context Product

1. Determine the correct context type (instrument, target, investigation, etc.)
2. Consult the [Guide to PDS4 Context Products](https://pds.nasa.gov/datastandards/documents/context/PDS4_Context_Products_Guide.v3.pdf) for LID formation rules
3. Create the XML file in the appropriate `data/pds4/context-pds4/{type}/` folder
4. File must end with `_v1.0.xml`
5. Include required elements:
   - `<logical_identifier>` - Unique LID (consult with @rchenatjpl if uncertain)
   - `<version_id>` - Must match filename version
   - `<Modification_History>` - Document creation
   - `<Reference_List>` - Internal references to related context products
   - Type-specific content (e.g., `<Instrument>`, `<Investigation>`, `<Target>`)

**Important**: You only create the context product XML itself. EN will handle collection inventory and label updates.

### Updating an Existing Context Product

1. Locate the current version in `data/pds4/context-pds4/{type}/`
2. Rename the file to increment the version number:
   - Minor change: `_v1.0.xml` → `_v1.1.xml`
   - Major change: `_v1.0.xml` → `_v2.0.xml`
3. Edit the renamed file:
   - Update `<version_id>` to match new filename version
   - Add new `<Modification_Detail>` to `<Modification_History>` documenting changes
   - Make additional required changes
4. **Never change the `<logical_identifier>`**

**Important**: You only update the context product XML itself. EN will handle collection inventory and label updates.

## XML Structure Reference

### Common Elements
```xml
<Product_Context>
  <Identification_Area>
    <logical_identifier>urn:nasa:pds:context:{type}:{id}</logical_identifier>
    <version_id>1.0</version_id>
    <title>Product Title</title>
    <information_model_version>1.22.0.0</information_model_version>
    <product_class>Product_Context</product_class>
    <Modification_History>
      <Modification_Detail>
        <modification_date>YYYY-MM-DD</modification_date>
        <version_id>1.0</version_id>
        <description>Initial creation</description>
      </Modification_Detail>
    </Modification_History>
  </Identification_Area>

  <Reference_List>
    <Internal_Reference>
      <lid_reference>urn:nasa:pds:context:...</lid_reference>
      <reference_type>instrument_to_telescope</reference_type>
    </Internal_Reference>
  </Reference_List>

  <!-- Type-specific section: <Instrument>, <Investigation>, <Target>, etc. -->
</Product_Context>
```

### Schema References
Instruments use the CTLI (Context Type List - Instrument) namespace for type information:
```xml
<?xml-model href="https://pds.nasa.gov/pds4/ctli/v2/PDS4_CTLI_1M00_2100.sch" schematypens="http://purl.oclc.org/dsdl/schematron"?>
<Product_Context ... xmlns:ctli="http://pds.nasa.gov/pds4/ctli/v2">
  <Instrument>
    <Type_List_Area>
      <ctli:Type_List>
        <ctli:type>Imager</ctli:type>
      </ctli:Type_List>
    </Type_List_Area>
  </Instrument>
</Product_Context>
```

## Development Workflow

This repository follows a fork-and-pull-request workflow since only EN has write access to the official NASA-PDS repository:

1. **Raise an Issue**: Create issue in NASA-PDS/pds4-context-products for new products or updates
2. **Fork the Repository**: Create working copy in your GitHub account
3. **Sync Fork**: Ensure your fork is up-to-date with NASA-PDS/main
4. **Create Branch**: Name should include issue number (e.g., `issue_42`, `update_target_cg_issue67`)
5. **Make Changes**: Create or update context products in your branch
6. **File Pull Request**: Submit PR from your branch to NASA-PDS/main
7. **EN Review**: EN will review, provide feedback, and merge when approved

## Validation

Context products should be validated using the PDS Validate Tool before submission. The repository includes automated validation via GitHub Actions:

### Duplicate LID Check
The workflow `check-duplicate-lids.yml` runs on every push to verify no duplicate `<logical_identifier>` values exist. This uses the `NASA-PDS/operations` repository's `check_duplicate_identifiers.py` script.

## Governance

Context products are governed by the lead agency of their parent investigation. For context products without a clear parent investigation (e.g., targets), the agency that creates the product gains ownership.

- **Investigation LID Prefix**: Determined by lead agency or via discussion
- **Instrument Host LID Prefix**: Determined by lead agency or via discussion
- **Instrument LID Prefix**: Determined by lead agency or via discussion

For questions about governance or LID assignment, contact Richard Chen (@rchenatjpl).

## Code Owners

All changes are reviewed by @nasa-pds/PDS4-IM-Team as defined in `.github/CODEOWNERS`.

## Key References

- [Guide to PDS4 Context Products](https://pds.nasa.gov/datastandards/documents/context/PDS4_Context_Products_Guide.v3.pdf) - Essential for LID formation and content requirements
- Official PDS4 Context Archive: https://pds.nasa.gov/data/pds4/context-pds4/
- Issue Templates: Located in `.github/ISSUE_TEMPLATE/` (create.yml, update.yml)
