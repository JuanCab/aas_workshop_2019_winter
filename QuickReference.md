
# 1. Overview
NASA services can be queried from Python in multiple ways.
* Generic Virtual Observatory (VO) queries.
  * Call sequence is consistent, including for non-NASA resources.
  * Some reference notebooks available at https://heasarc.gsfc.nasa.gov/vo/summary/python.html
* Astroquery interfaces
  * Call sequences not quite as consistent, but follow similar patterns.
  * See https://astroquery.readthedocs.io/en/latest/
* Ad hoc archive-specific interfaces

# 2. VO Services
## 2.1 Lookup Services in VO Directory (Registry)


```python
# Surpress known innocuous warnings
import warnings
warnings.filterwarnings("ignore", module="astropy.io.votable.*")

from navo_utils.registry import Registry

# Simple example:  Find Cone Search services related to SWIFT.
services=Registry.query(service_type='cone', keyword='swift')
```

### 2.1.1 Use different arguments/values to modify the simple example
| Argument | Description | Examples |
| :-----: | :-----------: | :--------: |
| **service_type (required)** | Type of service | 'cone', 'image', 'spectra', 'table' |
| **keyword** | Results will contain the keyword in ivoid, title, or description | 'galex', 'swift' |
| **waveband** | Resulting services have data in the specified waveband(s) | 'gamma-ray', 'X-ray', 'EUV', 'UV' , 'optical' , 'infrared' , 'millimeter' , 'radio', 'UV,optical' |
| **publisher** | Name (or substring of) publishing organization | 'CDS', 'HEASARC', 'Space Telescope', 'IPAC' |

### 2.1.2 Inspect the results.
See http://docs.astropy.org/en/stable/table/ for more on working with Astropy Tables.


```python
print(services.info)  # Show the number of rows and the column names.
print(services)  # Default display of Astropy Table.
print(services[:3]['short_name', 'reference_url'])  # Show first 3 short names and reference URLs.

# or in a Jupyter notebook
services.show_in_notebook()  # Display an interactive table in the notebook.
Registry.display_results(services)  # Format result to make long descriptions more readable.
```

## 2.2 Cone search
Example:  Find a cone search service for the USNO-B catalog and search it around M51 with a .5 degree radius.  (More inspection could be done on the service list instead of blindly choosing the first service.)  The *coords* argument can be anything accepted by: https://astroquery.readthedocs.io/en/latest/api/astroquery.utils.parse_coordinates.html#astroquery.utils.parse_coordinates

More information on creating Astropy SkyCoord objects: http://docs.astropy.org/en/stable/api/astropy.coordinates.SkyCoord.html


```python
from navo_utils.cone import Cone
services=Registry.query(service_type='cone', keyword='usno-b')
results = Cone.query(service=services[0], coords='m51',radius=.5)
```

## 2.3 Image search
Example:  Find an image search service for GALEX, and search it around coordinates 13:37:00.950,-29:51:55.51 (M83) with a radius of .2 degrees.  Download the first file in the results.


```python
from navo_utils.image import Image, ImageColumn
services=Registry.query(service_type='image', keyword='galex', publisher='Space Telescope')
image_results_list = Image.query(service=services[0], coords='13:37:00.950,-29:51:55.51', radius=0.2)
image_results = image_results_list[0]  # List would be longer if list of services was input.

from astropy.utils.data import download_file
local_path = download_file(image_results[0][ImageColumn.ACCESS_URL])
```

## 2.4 Spectral search
Example:  Find a spectral service for x-ray data.  Query it around Delta Ori, and download the first data product.  Note that the results table can be inspected for potentially useful columns.


```python
from navo_utils.spectra import Spectra, SpectraColumn
from astropy.coordinates import SkyCoord

delori = SkyCoord.from_name("delta ori")

services = Registry.query(service_type='spectr', waveband='x-ray')
spectra_results_list = Spectra.query(service=services[0], coords=delori, radius=0.1)
spectra_results = spectra_results_list[0]  # List would be longer if list of services was input.

from astropy.utils.data import download_file
local_path = download_file(spectra_results[0][SpectraColumn.ACCESS_URL])
```

## 2.5 Table search
Example:  Find the HEASARC Table Access Protocol (TAP) service, get some information about the available tables.


```python
from navo_utils.tap import Tap
tap_services = Registry.query(service_type='table',source='heasarc')
tap_url = str(tap_services[0]['access_url'])

Tap.list_tables(tap_url)  # list table names and descriptions
Tap.list_tables(tap_url, contains='chan')  # list table names and descriptions where name contains 'chan'
table_list = Tap.get_tables(tap_url)  # Same as list_tables, but returns list of table objects
Tap.list_columns(tap_url,tablename="public.zcat")  # list columns for table 'public.zcat'
```

Example:  Perform a cone search on the ZCAT catalog.


```python
query = """
select name, ra, dec, bmag, redshift from public.zcat as cat 
where contains(point('ICRS',cat.ra,cat.dec),circle('ICRS',202.5,47.0,1.0))=1 
and cat.bmag < 14
"""
results=Tap.query(tap_url,query)
```

# 3. Astroquery 
Many archives have Astroquery modules for data access, including:

* [HEASARC Queries (astroquery.heasarc)](https://astroquery.readthedocs.io/en/latest/heasarc/heasarc.html)
* [HITRAN Queries (astroquery.hitran)](https://astroquery.readthedocs.io/en/latest/hitran/hitran.html)
* [IRSA Image Server program interface (IBE) Queries (astroquery.ibe)](https://astroquery.readthedocs.io/en/latest/ibe/ibe.html)
* [IRSA Queries (astroquery.irsa)](https://astroquery.readthedocs.io/en/latest/irsa/irsa.html)
* [IRSA Dust Extinction Service Queries (astroquery.irsa_dust)](https://astroquery.readthedocs.io/en/latest/irsa/irsa_dust.html)
* [JPL Spectroscopy Queries (astroquery.jplspec)](https://astroquery.readthedocs.io/en/latest/jplspec/jplspec.html)
* [MAST Queries (astroquery.mast)](https://astroquery.readthedocs.io/en/latest/mast/mast.html)
* [NASA ADS Queries (astroquery.nasa_ads)](https://astroquery.readthedocs.io/en/latest/nasa_ads/nasa_ads.html)
* [NED Queries (astroquery.ned)](https://astroquery.readthedocs.io/en/latest/ned/ned.html)

For more, see https://astroquery.readthedocs.io/en/latest/

## 3.1 NED
Example:  Get an Astropy Table containing the objects from paper 2018ApJ...858...62K.  For more on the API, see https://astroquery.readthedocs.io/en/latest/ned/ned.html 


```python
from astroquery.ned import Ned
objects_in_paper = Ned.query_refcode('2018ApJ...858...62K')
```
