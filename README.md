[![Build Status](https://travis-ci.org/botswana-harvard/bcpp-export.svg?branch=develop)](https://travis-ci.org/botswana-harvard/bcpp-export)

# bcpp_export

Export analysis datasets of Plot, Household, Enumeration and Participants data from the "BCPP" Edc.

## Subjects
###Usage

Note: If you are also producing the Plot, Household, and Enumeration files, export the Subjects CSV file from the `Households` class instead as the `Household` class calls the `Subjects` class. See section below on `Households`

    from bcpp_export.subjects import Subjects
    
    s = Subjects('bcpp-year-1', merge_on='household_member')
    
    # export to a csv file, default is "~/bcpp_export_subjects.csv"
    s.to_csv()
    
The exported dataframe is `pandas` dataframe:

    s.results
    
The exported dataframe, '`s.results` is a merge of all dataframes. Merge is a LEFT merge on `household_member`.

All dataframes are `pandas` dataframes. All dataframes are prefixed with `df_` with the exception of `results`.

Uses BCPP Edc 1.11.117, see requirements.txt.

## identity256
The dataframes are passed `django` model `values_list`. Encrypted field values (e.g. PII) are not decrypted by `django` for `values_lists`. To include a `sha256` representation of the personal identifier specify `add_identity256=True` when instantiating the `Subjects` class. Ensure the settings attribute `KEYPATH` points to the valid encryption keys. 
    
    IMPORTANT: the value of identity256 is not encrypted so the dataset should be considered sensitive.

    >>> s = Subjects('bcpp-year-1', merge_on='household_member', add_identity256=True)
    >>> s.results['identity256'][0] = '6d24639a6bd16765f3518d2e67146eb5950a7a05b6c8956e639423ac1042da74'
    
## Households
    
### Usage
    
    from bcpp_export.subjects import Households
    
    h = Households('bcpp-year-1', merge_on='household_member', add_identity256=True)

write each dataframe to csv individually

    h.to_csv('plots')  # creates ~/bcpp_export_plots.csv
    h.to_csv('households')  # creates ~/bcpp_export_households.csv
    h.to_csv('members')  # creates ~/bcpp_export_members.csv
    h.to_csv('subjects')  # creates ~/bcpp_export_subjects.csv
    
or write all in one call

    h.to_csv('all')  # creates all listed above

## Examples

### Select data just for CPC communities from pairs 1 to 13

    import os
    import pandas as pd

    communities = [
        'digawana', 'lentsweletau', 'lerala', 'maunatlala', 'mmankgodi', 'oodi', 'otse', 'shoshong', 'tati_siding',
        'nkange', 'mathangwane', 'gweta', 'gumare']
    
    options = dict(
        path_or_buf=os.path.expanduser('~/bcpp_export_{}.csv'.format(name)),
        na_rep='',
        encoding='utf8',
        date_format='%Y-%m-%d %H:%M')

    name = 'plots_cpc_1-13'
    df = pd.read_csv('~/bcpp_export_plots.csv')
    df1 = df[df['community'].isin(communities)]
    df1.to_csv(**options)

    name = 'households_cpc-1-13'
    options.update(path_or_buf=os.path.expanduser('~/bcpp_export_{}.csv'.format(name)))
    df = pd.read_csv('~/bcpp_export_households.csv')
    df1 = df[df['community'].isin(communities)]
    df1.to_csv(**options)

    name = 'members_cpc-1-13'
    options.update(path_or_buf=os.path.expanduser('~/bcpp_export_{}.csv'.format(name)))
    df = pd.read_csv('~/bcpp_export_members.csv')
    df1 = df[df['community'].isin(communities)]
    df1.to_csv(**options)

    name = 'subjects_cpc-1-13'
    options.update(path_or_buf=os.path.expanduser('~/bcpp_export_{}.csv'.format(name)))
    df = pd.read_csv('~/bcpp_export_subjects.csv')
    df1 = df[df['community'].isin(communities)]
    df1.to_csv(**options)
        