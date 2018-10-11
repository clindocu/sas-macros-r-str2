
sas-macros-r-str2
======================

%str2()
-----

The SAS macro **%str2()** displays the structure of a SAS dataset (in the SAS log).

The macro is similar to R function **utils:::str(object)** that compactly displays the structure of an R object.

All variables in the SAS dataset will be parsed regarding _type_, _real length_ and _display format_ (including no. of _decimal places_)

#### Example

``` sas
%include "gather2.sas";

%str2(ADSL);
```

NOTE: the ```%gather2()``` macro will be invoked within the macro ```%str2()``` and must be available.

#### Result (SAS Log) - Example

``` sas
'dataframe':   180 obs. of   24 variables:
 $ STUDYID : chr ALEA01 ALEA01 ALEA01 ALEA01 ALEA01 ALEA01 ALEA01 ALEA01 ALEA01...
           : SAS Format: $7. (real length: 6)
           : Label: "Study Identifier"
 $ USUBJID : chr ALEA01-01-001 ALEA01-01-002 ALEA01-01-003 ALEA01-01-004 ALEA01...
           : SAS Format: $14. (real length: 13)
           : Label: "Unique Subject Identifier"
 $ SUBJID  : chr 001 002 003 004 005 006 007 008 009 010 011 012 013 014 015 01...
           : SAS Format: $3. (real length: 3)
           : Label: "Subject Identifier for the Study"
 $ SITEID  : chr 01 01 01 01 01 01 01 01 01 01 01 02 02 02 02 02 02 02 02 02 02...
           : SAS Format: $2. (real length: 2)
           : Label: "Study Site Identifier"
 $ AGE     : int 35 85 63 46 42 79 49 51 37 55 44 88 60 60 48 74 37 28 49 67 59...
           : SAS Format: 8. ("integer:", length: 2)
           : Label: "Age"
 $ AGEU    : chr YEARS YEARS YEARS YEARS YEARS YEARS YEARS YEARS YEARS YEARS YE...
           : SAS Format: $5. (real length: 5)
           : Label: "Age Units"
 $ AGEGR1  : chr <65 >=65 <65 <65 <65 >=65 <65 <65 <65 <65 <65 >=65 <65 <65 <65...
           : SAS Format: $4. (real length: 4)
           : Label: "Pooled Age Group 1"
 $ AGEGR1N : int 1 2 1 1 1 2 1 1 1 1 1 2 1 1 1 2 1 1 1 2 1 2 1 1 1 1 1 1 1 1 1...
           : SAS Format: 8. ("integer:", length: 1)
           : Label: "Pooled Age Group 1 (N)"
 $ SEX     : Factor w/ 2 levels "Female", "Male" 2 1 2 NA 2 1 2 1 1 2 2 2 1...
           : SAS Format: $SEX.
           : Label: "Sex"
 $ SEXN    : Factor w/ 2 levels "Female", "Male" 2 1 2 NA 2 1 2 1 1 2 2 2 1...
           : SAS Format: SEXN.
           : Label: "Sex (N)"
 $ RACE    : Factor w/ 4 levels "White", "Black or African... 1 1 1 1 1 1 1 1 1 1 1 1 1...
           : SAS Format: $RACE.
           : Label: "Race"
 $ RACEN   : Factor w/ 4 levels "White", "Black or African... 1 1 1 1 1 1 1 1 1 1 1 1 1...
           : SAS Format: RACEN.
           : Label: "Race (N)"
 $ WEIGHTBL: num 50.8 63.9 63.4 83.5 116.3 58.3 87.2 54.1 66.3 54.8 74.3 77.8 5...
           : SAS Format: BEST12. ("float:", display format: 5.1)
           : Label: "Weight at Baseline (kg)"
 $ L10WTBL : num 1.705864 1.805501 1.802089 1.921686 2.06558 1.765669 1.940516...
           : SAS Format: BEST8. ("float:", display format: 8.6)
           : Label: "Log10(Weight (kg))"
 $ HEIGHT  : int 166 166 172 173 184 162 170 160 177 172 169 175 159 172 159 17...
           : SAS Format: 8. ("integer:", length: 3)
           : Label: "Height (cm)"
 $ BMI     : num 18.435186529 23.189142111 21.430502975 27.899361823 34.3513705...
           : SAS Format: BEST12. ("float:", display format: 12.9)
           : Label: "Body Mass Index (kg/m2)"
```

There are following features:

1. **Numeric variables** will be **parsed** to identify the "real" data type, i.e. **integer** or **float**. The *display format* will be identified, incl. no. of **decimal places** - in addition to the assigned numeric SAS format.

2. **Character variables:** the "real" **length** based on the actual values will be identified (NOTE: also leading blanks will be counted).

3. The number of **missing/blank values** will be identified

4. **Data definition tables** (with format decodes) will be generated

Data Definition Tables: **DDT_FINAL**, **DDT_FINAL_SHORT**, **DDT_STR**

NOTE: character variables will not be converted to factors, equivalent to R option ```stringsAsFactors = FALSE``` 

#### Limitations

* Character variables in database may not be longer than <= 400 characters (performance issue; can be changed, see below)
* Some variables/formats will **not** be parsed regarding number of **decimal places** (e.g., DOLLAR., COMMA., COMMAX., NUMX., PERCENT.)
* Number of **decimal places**: also zeros at the end of a number will be counted (e.g., the value 1.00 will be identified as a float number with 2 decimal places)
* Format code, format level and decode will be truncated after 4.000 characters ($4000.); can be adjusted (performance issue)
* Does not work correct in case of truncated formats, e.g., when $SEX. "F"=Female" is formatted as $SEX4. and the values will be truncated to "Fema" (4 characters)
* SAS version 9.4 above required to execute ```%gather2()```
* Input dataset must not contain variables: _ColFormat _ColTyp _ColTypVar _UserDef _IsRealNum _SASFormat _IsMissing 
* Low performance: the whole dataset will be transposed from wide to long and all variable values will be parsed

NOTE: If there are character variables in the SAS dataset with a length > 400 char. then change twice within the Macro: 

* ```ValFormat=$400.``` to ```ValFormat=$[YourMaxlength].```

#### Usage

``` sas
%include "gather2.sas";

%str2(DbIn, DDTOUT=Y); * with output of data definition table in SAS output window;

%str2(DbIn);

```

``` 
DbIn:           Input dataset
```

``` 
DDTOUT:         If Y: Output of a Data Definition Table in the SAS output window: 
                With "real" data type, i.e. 'integer' or 'float', number of decimal places, 
                "real" length of character variables, no. of missing values, format decodes
```

<span style="color: green"> SAS output files: DDT_FINAL, DDT_FINAL_SHORT, DDT_STR </span>


%gather2()
---------

A **gather** Macro with following features:

The SAS macro **%gather2()** transposes a dataset from *fat* to *skinny* (normalization) or from a *wide* to a *long* dataset.

The macro is similar to R function **tidyr:::gather()**.

The macro identifies the "real" *SAS format*, generates a flag for *missing values* (removes "."), generates a flag for *numeric formats* (*BEST.* or *w.d.*) and outputs *variable labels*.


#### Usage

``` sas
%gather2(DbIn, Key, Value, Exclude, DbOut, ValFormat=, WithFormats=N, WithLabels=N, SASFormats=N);
```

NOTE: Option *WithFormats* executes the SAS function *fmtinfo()* that requires **SAS version 9.4** above!

```
DbIn:        Input dataset
```

```
Key:         Name of key variable in output (should not be a column name!)
```

```
Value:       Name of variable with values in output (should not be a column name!)
             NOTE: character variables: leading blanks will not be removed!
             NOTE: Missing "." will be removed (set to blank)
```

```
Exclude:     Is the -exclude variable (ID variable) - must be a column name
             (or more column names/primary keys, or empty)
```

```
DbOut:       Output dataset
```

```
ValFormat:   if Y, Character format of variable with values (default character $200.)
             not possibe for (numeric) dates (dates as character only)
             Numeric Format, e.g. 8. only possible if all values in dataset are numeric;
```

```
WithFormats: if Y, Output of the associated SAS Formats (e.g. char, num, time, date, datetime) in 
             Variables: _ColFormat, _ColFor2, _ColTyp, _ColTypVar (N, C)
             Please Note: SAS function fmtinfo() requires SAS 9.4 above!
```

```
WithLabels:  if Y, Output of the Variable Labels 
             Variable _ColLab
             (additional feature)
```
             
```
SASFormats:  if Y, Output of (real) SAS Formats, Identification of missing Values 
             Variables: _UserDef, _IsRealNum (i.e. BEST. or w.d Format), _SASFormat, _IsMissing 
```

#### Limitations
* Input dataset must not contain Variables: _ColFormat _ColTyp _ColTypVar _UserDef _IsRealNum _SASFormat _IsMissing 
* Does not identify "special" missing values (e.g. .Z) as missing
* SAS v. 9.4 above required

#### Example

``` sas
%gather2(VS_WIDE, VSTESTCD, VSORRES, SUBJID VISITNUM, VS_LONG, ValFormat=$10., WithLabels=Y, SASFormats=Y);
```


