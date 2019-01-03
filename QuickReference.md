
# Overview
NASA services can be queried from Python in multiple ways.
* Generic Virtual Observatory (VO) queries.
  * Call sequence is consistent, including for non-NASA resources.
* Astroquery interfaces
  * Call sequences not quite as consistent, but follow similar patterns.
  * See https://astroquery.readthedocs.io/en/latest/
* Ad hoc archive-specific interfaces

# VO Services
## Lookup Services in VO Directory (Registry)


```python
from navo_utils.registry import Registry

# Simple example:  Find Cone Search services related to SWIFT.
services=Registry.query(service_type='cone', keyword='swift')
```

### Use different arguments/values to modify the simple example
| Argument | Description | Examples |
| :-----: | :-----------: | :--------: |
| **service_type (required)** | Type of service | 'cone', 'image', 'spectra', 'table' |
| **keyword** | Results will contain the keyword in ivoid, title, or description | 'galex', 'swift' |
| **waveband** | Resulting services have data in the specified waveband(s) | 'gamma-ray', 'X-ray', 'EUV', 'UV' , 'optical' , 'infrared' , 'millimeter' , 'radio', 'UV,optical' |
| **publisher** | Name (or substring of) publishing organization | 'CDS', 'HEASARC', 'Space Telescope', 'IPAC' |

### Inspect the results.
See http://docs.astropy.org/en/stable/table/ for more on working with Astropy Tables.


```python
print(services.info)  # Show the number of rows and the column names.
print(services)  # Default display of Astropy Table.
print(services[:3]['short_name', 'reference_url'])  # Show first 3 short names and reference URLs.

# or in a Jupyter notebook
services.show_in_notebook()  # Display an interactive table in the notebook.
Registry.display_results(services)  # Format result to make long descriptions more readable.
```

## Cone search
Example:  Find a cone search service for the USNO-B catalog and search it around M51 with a .5 degree radius.  (More inspection could be done on the service list instead of blindly choosing the first service.)  The *coords* argument can be anything accepted by: https://astroquery.readthedocs.io/en/latest/api/astroquery.utils.parse_coordinates.html#astroquery.utils.parse_coordinates

More information on creating Astropy SkyCoord objects: http://docs.astropy.org/en/stable/api/astropy.coordinates.SkyCoord.html


```python
from navo_utils.cone import Cone
services=Registry.query(service_type='cone', keyword='usno-b')
results = Cone.query(service=services[0], coords='m51',radius=.5)
```

## Image search
Example:  Find an image search service for GALEX, and search it around coordinates 13:37:00.950,-29:51:55.51 (M83) with a radius of .2 degrees.  Download the first file in the results.


```python
from navo_utils.image import Image, ImageColumn
services=Registry.query(service_type='image', keyword='galex', publisher='Space Telescope')
image_results_list = Image.query(service=services[0], coords='13:37:00.950,-29:51:55.51', radius=0.2)
image_results = image_results_list[0]  # List would be longer if list of services was input.

from astropy.utils.data import download_file
local_path = download_file(image_results[0][ImageColumn.ACCESS_URL])
```

## Spectral search
Example:  Find a spectral service for x-ray data.  Query it around Delta Ori, and download the first data product.  Note that the results table can be inspected for potentially useful columns.


```python
from navo_utils.spectra import Spectra, SpectraColumn
services = Registry.query(service_type='spectr', waveband='x-ray')
spectra_results_list = Spectra.query(service=services[0], coords='delta ori', radius=0.1)
spectra_results = spectra_results_list[0]  # List would be longer if list of services was input.

from astropy.utils.data import download_file
local_path = download_file(spectra_results[0][SpectraColumn.ACCESS_URL])
```

## Table search
Example:

# Non-VO examples
## NED

## SDSS
