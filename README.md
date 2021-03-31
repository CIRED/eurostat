# Eurostat Python Package 

Tools to read data from Eurostat website.

# Features

* Read Eurostat data and metadata as list of tuples or as pandas dataframe.
* MIT license.


# Documentation


## Getting started:

Requires Python 3.6+

```bash
pip install eurostat
```


## Read the table of contents of the main database:

### As a list of tuples:

```python
eurostat.get_toc()
```

Read the table of contents and return a list of tuples. The first element of the list contains the header line. Dates are represented as strings.

Example:
```python
>>> import eurostat
>>> toc = eurostat.get_toc()
>>> toc[0]
('title', 'code', 'type', 'last update of data', 'last table structure change', 'data start', 'data end')
>>> toc[10:13]
[('Industry - quarterly data', 'ei_bsin_q_r2', 'dataset', '30.10.2019', '30.10.2019', '1980Q1', '2019Q4'),
 ('Construction - monthly data', 'ei_bsbu_m_r2', 'dataset', '30.10.2019', '30.10.2019', '1980M01', '2019M10'),
 ('Construction - quarterly data', 'ei_bsbu_q_r2', 'dataset', '30.10.2019', '30.10.2019', '1981Q1', '2019Q4')]
```

### As a pandas dataframe:

```python
eurostat.get_toc_df()
```

Read the table of contents of the main database and return a dataframe. Dates are represented as strings.

Example:
```python
>>> import eurostat
>>> toc_df = eurostat.get_toc_df()
>>> toc_df
                                                  title  ... data end
0                                    Database by themes  ...         
1                       General and regional statistics  ...         
2     European and national indicators for short-ter...  ...         
3     Business and consumer surveys (source: DG ECFIN)   ...         
4                   Consumer surveys (source: DG ECFIN)  ...         
                                                ...  ...      ...
9860  Enterprises that provided training to develop/...  ...     2018
9861  Participation in education and training - cont...  ...         
9862  Enterprises providing training by type of trai...  ...     2015
9863  Participants in CVT courses by sex and size cl...  ...     2015
9864  Main skills targeted by CVT courses by type of...  ...     2015
```

You may also want to extract the datasets that pertains a topic. In that case, you can use:

```python
eurostat.subset_toc_df(toc_df, keyword)
```

Extract from toc_df the row where 'title' contains 'keyword' (case-insensitive).

Example:
```python
>>> f = eurostat.subset_toc_df(toc_df, 'fleet')
>>> f
title, code, type, last update of data, last table structure change, data start, data end
                                               title              code       type  ... data end
5631                                   Fishing fleet        fish_fleet     folder  ...         
5632  Fishing fleet by age, length and gross tonnage    fish_fleet_alt    dataset  ...     2018
5633  Fishing fleet by type of gear and engine power     fish_fleet_gp    dataset  ...     2018
6246   Commercial aircraft fleet by type of aircraft   avia_eq_arc_typ    dataset  ...     2017
6247    Commercial aircraft fleet by age of aircraft   avia_eq_arc_age    dataset  ...     2017
7849                    Fishing fleet, total tonnage          tag00083      table  ...     2018
7850                Fishing Fleet, Number of Vessels          tag00116      table  ...     2018
```

Note that, in the above example, the first returned row represents a folder, not a dataset.


## Read a dataset from the main database:

### As a list of tuples:

```python
eurostat.get_data(code, flags=False)
```

Read a dataset from the main database (available from the [bulk download facility][bulkdown]) and returns it as a list of tuples. The first element of the list ("the first row") is the data header.
Pay attention: the data format changes if flags is True or not. Flag meanings can be found [here][abbr].

Example:
```python
>>> import eurostat
>>> data = eurostat.get_data('demo_r_d2jan')
>>> data
[('unit', 'sex', 'age', 'geo\\time', 2018, 2017, 2016, 2015, 2014, ...),
 ('NR', 'F', 'TOTAL', 'AL', 1431715.0, None, 1417141.0, 1424597.0, 1430827.0, ...),
  ...]
>>> data = eurostat.get_data('demo_r_d2jan', True)
>>> data
[('unit', 'sex', 'age', 'geo\\time', '2018_value', '2017_flag', '2017_value', '2018_flag', '2016_value', '2016_flag', ...),
 ('NR', 'F', 'TOTAL', 'AL', 1431715.0, '', 1423050.0, 'c', 1417141.0, '', 1424597.0, '', ...),
  ...]
```

### As a pandas dataframe:

```python
eurostat.get_data_df(code, flags=False)
```

Read a dataset from the main database (available from the [bulk download facility][bulkdown]) and returns it as a pandas dataframe.
Flag meanings can be found [here][abbr].

Example:
```python
>>> import eurostat
>>> df = eurostat.get_data_df('demo_r_d2jan')
>>> df
       unit sex     age geo\time  ...     1993     1992  1991  1990
0        NR   F   TOTAL       AL  ...      NaN      NaN   NaN   NaN
1        NR   F   TOTAL      AL0  ...      NaN      NaN   NaN   NaN
2        NR   F   TOTAL     AL01  ...      NaN      NaN   NaN   NaN
3        NR   F   TOTAL     AL02  ...      NaN      NaN   NaN   NaN
4        NR   F   TOTAL     AL03  ...      NaN      NaN   NaN   NaN
    ...  ..     ...      ...  ...      ...      ...   ...   ...
168607   NR   T  Y_OPEN     UKM7  ...      NaN      NaN   NaN   NaN
168608   NR   T  Y_OPEN     UKM8  ...      NaN      NaN   NaN   NaN
168609   NR   T  Y_OPEN     UKM9  ...      NaN      NaN   NaN   NaN
168610   NR   T  Y_OPEN      UKN  ...  17934.0  17566.0   NaN   NaN
168611   NR   T  Y_OPEN     UKN0  ...  17934.0  17566.0   NaN   NaN
>>> df = eurostat.get_data_df('demo_r_d2jan', True)
>>> df
       unit sex     age geo\time  ...  1992_value 1992_flag  1991_value 1991_flag  1990_value 1990_flag
0        NR   F   TOTAL       AL  ...        NaN         :         NaN         :         NaN         :
1        NR   F   TOTAL      AL0  ...        NaN         :         NaN         :         NaN         :
2        NR   F   TOTAL     AL01  ...        NaN         :         NaN         :         NaN         :
3        NR   F   TOTAL     AL02  ...        NaN         :         NaN         :         NaN         :
4        NR   F   TOTAL     AL03  ...        NaN         :         NaN         :         NaN         :
    ...  ..     ...      ...  ...         ...       ...       ...         ...       ...
168607   NR   T  Y_OPEN     UKM7  ...        NaN         :         NaN         :         NaN         :
168608   NR   T  Y_OPEN     UKM8  ...        NaN         :         NaN         :         NaN         :
168609   NR   T  Y_OPEN     UKM9  ...        NaN         :         NaN         :         NaN         :
168610   NR   T  Y_OPEN      UKN  ...    17566.0                   NaN         :         NaN         :
168611   NR   T  Y_OPEN     UKN0  ...    17566.0                   NaN         :         NaN         :
```


## Get an Eurostat dictionary:

```python
eurostat.get_dic(code)
```

Read the metadata related to a particular code. Return a list of tuples, where the first element of each tuple is the code value and the second one is its description.

Example:
```python
>>> import eurostat
>>> dic = eurostat.get_dic('sex')
>>> dic
[('T', 'Total'),
 ('M', 'Males'),
 ('F', 'Females'),
 ('DIFF', 'Absolute difference between males and females'),
 ('NAP', 'Not applicable'),
 ('NRP', 'No response'),
 ('UNK', 'Unknown')]
```


## Check what datasets are available via SDMX:

### As a list of tuples:

```python
eurostat.get_avail_sdmx()
```

Return a list of tuples. The first element of the list contains the header line.

Example:
```python
>>> avail_sdmx = eurostat.get_avail_sdmx()
>>> avail_sdmx
[('dataflow', 'name'),
 ('DS-008573', 'Sold production, exports and imports for steel by PRODCOM list (NACE Rev. 1.1) - monthly data'),
 ('DS-016890', 'EU trade since 1988 by CN8'),
 ('DS-016893', 'EU trade since 1988 by HS6')
 ...]
```

### As a pandas dataframe:

```python
eurostat.get_avail_sdmx_df()
```

Return a dataframe with one column. Dataflow (i.e. dataset) codes are in the dataframe index.

Example:
```python
>>> avail_sdmx_df = eurostat.get_avail_sdmx_df()
>>> avail_sdmx_df
                                                             name
dataflow                                                         
DS-008573       Sold production, exports and imports for steel...
DS-016890                              EU trade since 1988 by CN8
DS-016893                              EU trade since 1988 by HS6
DS-016894                          EU trade since 1988 by HS2-HS4
DS-018995                             EU trade since 1988 by SITC
                                                          ...
yth_incl_120    Young people living in households with very lo...
yth_part_010    Frequency of getting together with relatives o...
yth_part_020    Frequency of contacts with relatives or friend...
yth_part_030    Participation of young people in activities of...
yth_volunt_010  Participation of young people in informal volu...
```

You may also want to find the datasets that pertains a topic. In that case, you can use:

```python
eurostat.subset_avail_sdmx_df(avail_sdmx_df, keyword)
```

Extract the rows where 'name' contains 'keyword' (case-insensitive).

Example:
```python
>>> keyword = 'fleet'
>>> subset = eurostat.subset_avail_sdmx_df(avail_sdmx_df, keyword)
>>> subset
                                                           name
dataflow                                                       
avia_eq_arc_age    Commercial aircraft fleet by age of aircraft
avia_eq_arc_typ   Commercial aircraft fleet by type of aircraft
fish_fleet_alt   Fishing fleet by age, length and gross tonnage
fish_fleet_gp    Fishing fleet by type of gear and engine power
tag00083                           Fishing fleet, total tonnage
tag00116                       Fishing Fleet, Number of Vessels
```


## Read the Eurostat dimensions of a dataset that is available via SDMX service:

```python
eurostat.get_sdmx_dims(code)
```

Read the dimension names of a dataset that is provided via SDMX service. Require the dataset code and return a list.

Example:
```python
>>> import eurostat
>>> dims = eurostat.get_sdmx_dims('DS-066341')
>>> dims
['DECL', 'FREQ', 'INDICATORS', 'PERIOD', 'PRCCODE']
```


## Read an Eurostat dictionary for a given SDMX dimension:

```python
eurostat.get_sdmx_dic(code, dim)
```

Read the Eurostat dimension values with their meaning for a dataset provided via SDMX service. Return them as a dictionary.

Example:
```python
>>> import eurostat
>>> dic = eurostat.get_sdmx_dic('DS-066341', 'FREQ')
>>> dic
{'A': 'Annual',
 'D': 'Daily',
 'H': 'Half-year',
 'M': 'Monthly',
 'Q': 'Quarterly',
 'S': 'Semi-annual',
 'W': 'Weekly'}
>>> flags = eurostat.get_sdmx_dic('DS-066341', 'OBS_STATUS')
>>> flags
{'-': 'not applicable or real zero or zero by default',
 '0': 'less than half of the unit used',
 'na': 'not available'}
```


## Read a dataset from the SDMX service:

### As a list of tuples:

```python
eurostat.get_sdmx_data(code, StartPeriod, EndPeriod, filter_pars, flags=False, verbose=True)
```

Read a dataset from SDMX service, with or without the flags. Return a list of tuples. The first tuple (row) contains the header.  
It allows to download some datasets that are not available from the main database (e.g., Comext).  
This service is slow, so you will better select a small subset of data and set accordingly the filter parameters along the available dimensions by setting *filter_pars* (a dictionary where keys are dimensions names, values are lists).  
To see a rough progress status, set verbose = True.

Example:
```python
>>> import eurostat
>>> StartPeriod = 2007
>>> EndPeriod = 2008
>>> filter_pars = {'FREQ': ['A',], 'PRCCODE': ['08111250','08111150']}
>>> data = eurostat.get_sdmx_data('DS-066341', StartPeriod, EndPeriod, filter_pars, flags = False, verbose=True)
Progress: 0.0%
Progress:50.0%
Progress:100.0%
>>> data
[('INDICATORS', 'DECL', 'PRCCODE', 'FREQ', 2007, 2008),
 ('EXPQNT', '001', '08111250', 'A', 10219200.0, 16082600.0),
 ('EXPVAL', '001', '08111250', 'A', 1697160.0, 1875920.0),
 ...]
```

### As a pandas dataframe:

```python
eurostat.get_sdmx_data_df(code, StartPeriod, EndPeriod, filter_pars, flags=False, verbose=True)
```

Read a dataset from SDMX service, with or without the flags. Return a pandas dataframe.  
It allows to download some datasets that are not available from the main database (e.g., Comext).  
This service is slow, so you will better select a small subset of data and set accordingly the filter parameters along the available dimensions by setting *filter_pars* (a dictionary where keys are dimensions names, values are lists).  
To see a rough progress status, set verbose = True.

Example:
```python
>>> import eurostat
>>> StartPeriod = 2007
>>> EndPeriod = 2008
>>> filter_pars = {'FREQ': ['A',], 'PRCCODE': ['08111250','08111150']}
>>> df = eurostat.get_sdmx_data_df('DS-066341', StartPeriod, EndPeriod, filter_pars, flags = True, verbose=True)
Progress: 0.0%
Progress:50.0%
Progress:100.0%
>>> df
    INDICATORS DECL   PRCCODE FREQ        2007 2007_OBS_STATUS        2008 2008_OBS_STATUS
0       EXPQNT  001  08111250    A  10219200.0                  16082600.0                
1       EXPVAL  001  08111250    A   1697160.0                   1875920.0                
2       IMPQNT  001  08111250    A   7526000.0                   4272200.0                
3       IMPVAL  001  08111250    A   1802940.0                   1208030.0                
4     PQNTBASE  001  08111250    A         0.0                         0.0                
..         ...  ...       ...  ...         ...             ...         ...             ...
875    PRODQNT  600  08111150    A         0.0                         0.0                
876    PRODVAL  600  08111150    A         0.0                         0.0                
877   PVALBASE  600  08111150    A         0.0                         0.0                
878   PVALFLAG  600  08111150    A         NaN              na         NaN              na
879    QNTUNIT  600  08111150    A         NaN                         NaN                
```


## In case you need to use a proxy (new in v.0.1.4):

Before doing anything else, you must configure the proxies.

```python
eurostat.setproxy(proxyinfo)
```

It requires in input *proxyinfo*, a dictionary with two keys ('http' and 'https') and values containing the connection parameters in lists.  
If authentication is not needed, set *username* and *password* to *None*.

Example:
```python
>>> import eurostat
>>> proxyinfo = {'http': ['myuser', 'mypassword', '123.456.789.012:8012'],
                 'https': ['myuser', 'mypassword', 'url:port']}
>>> setproxy(proxyinfo)
```

It always returns *None*.


## Bug reports and feature requests:

Please [open an issue][issue] or send a message to noemi.cazzaniga [at] polimi.it .


## Disclaimer:

Download and usage of Eurostat data is subject to Eurostat's general copyright notice and licence policy (see [Policies][pol]). Please also be aware of the European Commission's [general conditions][cond].


## Data sources:

* Eurostat database: [online catalog][onlinecat] and [bulk download facility][bulkdown].
* Eurostat nomenclatures: [RAMON][ram] metadata.
* Eurostat Interactive Data Explorer: [Data Explorer][expl].
* Eurostat Interactive Tool for Comext Data: [Easy Comext][comext].
* Eurostat PRODCOM website: [PRODCOM][prodcom].
* Eurostat acronyms: [Symbols and abbreviations][abbr].


## References:

* R package [eurostat][es]: R Tools for Eurostat Open Data.
* Python package [pandaSDMX][pandasdmx]: Statistical Data and Metadata eXchange.
* Python package [pandas][pd]: Python Data Analysis Library.


## History:

### version 0.2.2 (31 Mar 2021):

* Bug fix (sdmx non-annual data).

### version 0.2.1 (10 Nov. 2020):

* Bug fix (pandasdmx 0.9).

### version 0.2.0 (22 May 2020):

* Improved SDMX download capability in case of slow internet connections.

### version 0.1.5 (08 Jan. 2020):

* Bug fix (proxy info).
* get_avail_sdmx, get_avail_sdmx_df, subset_avail_sdmx_df added.

### version 0.1.4 (20 Dec. 2019):

* Added support to proxy.

### version 0.1.3 (17 Dec. 2019):

* Bug fix (non-annual data headers).

### version 0.1.2 (25 Nov. 2019):

* Added possibility to download flags.
* get_toc_df, subset_toc_df added.

### verion 0.1.1 (21 Nov. 2019):

* First official release.


[pol]: https://ec.europa.eu/eurostat/web/main/about/our-partners/copyright
[cond]: http://ec.europa.eu/geninfo/legal_notices_en.htm
[onlinecat]: https://ec.europa.eu/eurostat/data/database
[bulkdown]: https://ec.europa.eu/eurostat/estat-navtree-portlet-prod/BulkDownloadListing
[ram]: https://ec.europa.eu/eurostat/ramon/nomenclatures/index.cfm?TargetUrl=LST_NOM&StrGroupCode=SCL&StrLanguageCode=EN
[expl]: http://appsso.eurostat.ec.europa.eu/nui/
[comext]: http://epp.eurostat.ec.europa.eu/newxtweb/
[bulkcomext]: https://ec.europa.eu/eurostat/estat-navtree-portlet-prod/BulkDownloadListing?sort=1&dir=comext%2FCOMEXT_DATA%2FPRODUCTS
[pandasdmx]: https://pandasdmx.readthedocs.io/en/stable/
[pd]: https://pandas.pydata.org/
[es]: http://ropengov.github.io/eurostat/
[issue]: https://bitbucket.org/noemicazzaniga/eurostat/issues/new
[abbr]: https://ec.europa.eu/eurostat/statistics-explained/index.php/Tutorial:Symbols_and_abbreviations
[prodcom]: https://ec.europa.eu/eurostat/web/prodcom
