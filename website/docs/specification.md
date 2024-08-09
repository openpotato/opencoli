# OpenCoLi Specification

#### Version 0.1.2

The key words "MUST", "REQUIRED", "SHOULD", and "MAY" in this document are to be interpreted as described in [RFC2119 and RFC8174](https://tools.ietf.org/html/bcp14), when, and only when, they appear in all capitals, as shown here.

This specification is licensed under the [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Introduction

The Open Code List Representation Format (OpenCoLi) defines a generic standard data format for representing code lists. Based on the [JSON Standard](https://datatracker.ietf.org/doc/html/rfc8259), this format can be easily generated and read by almost any programming language. Documents in the OpenCoLi format can be validated for syntactic correctness using the [OpenCoLi Document Schema](https://github.com/openpotato/opencoli/tree/main/schemas/v0.1/schema.json).

OpenCoLi can be used for exchanging code lists between services or applications, as a representation format for official code lists, or as a response format for API requests (e.g., for RESTful web services).

### What are Code Lists?

Code lists are structured collections of codes or keys used for the identification and classification of data. These code lists are essential in numerous fields such as databases, management systems, scientific research, and industrial applications. They help in organising, storing, and retrieving data consistently and efficiently.

Code lists play a crucial role in the standardisation and harmonisation of data. By using standardised codes, different systems and organisations can interpret and exchange data uniformly.

Examples of code lists include:

+ International codes for countries, languages, and currencies by the International Organization for Standardization (ISO).

+ National area codes (e.g., municipal codes of the Federal Republic of Germany).

+ National and subnational codes in the public sector (e.g., statistical codes from statistical authorities).

In principle, any data selection can be mapped to a code list; even a simple Boolean value can be represented by the codes *No* and *Yes*.

### How are Code Lists structured?

The simplest form of a code list consists of two columns: key and description.

Here is an example of a country code list:

Code | Description |
---- | ----------- |
AT   | Austria     |
CH   | Switzerland |
DE   | Germany     |

Code lists can potentially contain an unlimited number of columns. Here is an example of a country code list with three columns:

Code  | Short Name  | Long Name                     
----- | ----------- | ------------------------------
AT    | Austria     | Republic of Austria           
CH    | Switzerland | Swiss Confederation           
DE    | Germany     | Federal Republic of Germany   

Code lists can refer to each other. Here is an example of a country code list referring to another continents code list:

Code | Short Name  | Long Name                      | Continent
---- | ----------- | ------------------------------ | ---------
AT   | Austria     | Republic of Austria            | EU
CH   | Switzerland | Swiss Confederation            | EU
DE   | Germany     | Federal Republic of Germany    | EU
MA   | Morocco     | Kingdom of Morocco             | AF

The corresponding continents code list might look like this:

Code | Name     
---- | -------- 
AF   | Africa
AM   | Americas
EU   | Europe
OC   | Oceania

Code lists can also have more than one key. Here is an example of a country code list with different ISO3166 codes:

Alpha2Code | Alpha3Code | NumericCode | Name       
---------- | ---------- | ----------- | ----------- 
AT         | AUT        | 040         | Austria    
CH         | CHE        | 756         | Switzerland
DE         | DEU        | 276         | Germany    

Let’s go a step further with this multilingual country code list:

Code | Language | Name       
---- | -------- | -----------
AT   | en       | Austria    
AT   | de       | Österreich 
CH   | en       | Switzerland
CH   | de       | Schweiz    
DE   | en       | Germany    
DE   | de       | Deutschland

Here, it is the combination of key and language (also represented by a language key) that defines uniqueness.

The structure of code lists can thus be simple or somewhat complex.

### Are There Already Standards for Code Lists?

Yes, there are: The [Organization for the Advancement of Structured Information Standards (OASIS)](https://www.oasis-open.org) has defined an XML-based standard for code lists with [Code List Representation (genericode)](https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html).

> The OASIS Code List Representation format, “genericode”, is a single model and XML format (with a W3C XML Schema) that can encode a broad range of code list information. The XML format is designed to support interchange or distribution of machine-readable code list information between systems.  Note that genericode is not designed as a run-time format for accessing code list information, and is not optimized for such usage.  Rather, it is designed as an interchange format that can be transformed into formats suitable for run-time usage, or loaded into systems that perform run-time processing using code list information.

This sounds good, but there’s a catch. There is no JSON representation of "genericode".

> Recognising the custom use of JSON in a tight binding between user-defined processes, the committee sees no purpose served by standardising a JSON syntax for the genericode vocabulary.

This means there is no official support for JSON as a data format and thus no official JSON schema.

So, if you want to represent code lists in XML format, the OASIS standard is recommended. However, if you want to work with JSON, OpenCoLi provides a suitable alternative. OpenCoLi is significantly influenced by the *OASIS Code List Representation Format* and strives to be largely compatible with it.

## Definitions

### OpenCoLi Document

An OpenCoLi document is a self-contained resource that defines and describes either a code list or a collection of code lists. It MUST contain at least the `opencoli` field and either `codeList` or `codeListSet`, but not both. An OpenCoLi document uses and conforms to the OpenCoLi specification.

### Code List

A code list is a classic relational table with columns and data rows, where at least one column should serve as the key (code). OpenCoLi allows for defining generic code lists.

### Code List Collection

A code list collection is a list of references to external code lists. A code list collection can be used to group different versions of a code list.

## Specification

### Versioning

The OpenCoLi specification is versioned according to the `major.minor.patch` scheme. The major-minor part of the version number (e.g., `0.1`) MUST indicate the functional set of the specification. Patch versions address errors in this document or provide clarifications to this document, not to the functionality. Tools that support OpenCoLi version `0.1` MUST be compatible with all `0.1.*` versions of OpenCoLi. The patch version SHOULD NOT be considered by the tools, so that no distinction is made between `0.1.0` and `0.1.1`.

An OpenCoLi document always contains a mandatory `opencoli` field that specifies the version of the OpenCoLi specification being used.

### Format

An OpenCoLi document that conforms to the OpenCoLi specification is itself a JSON object that can be represented in JSON format.

All field names in the specification are case-sensitive. The schema defines two types of fields: fixed fields that have a declared name, and free fields whose names MUST conform to a specific pattern. Additional fields MUST have unique names within the contained JSON object.

#### JSON Schema

[JSON Schema](https://json-schema.org/) is a specification for defining JSON data structures. A JSON schema itself is expressed declaratively using [JSON](https://www.json.org/). The [OpenCoLi Document Schema](https://github.com/openpotato/opencoli/tree/main/schemas/v0.1/schema.json) is a JSON schema for OpenCoLi documents.

#### Multilingual Support

OpenCoLi supports multilingualism, meaning text strings can optionally be provided with one or more [IETF BCP 47 language tags](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). Each language tag can then be followed by the corresponding translation.

Example:

```json
"description": [
  {
    "language": "en",
    "text": "Currency codes of the International Standard ISO 4217"
  },
  {
    "language": "de",
    "text": "Währungscodes der internationalen Norm ISO 4217"
  }
]
```

### Language Tags

IETF BCP 47 (Best Current Practice 47) is a document that defines the rules and procedures for creating and using language tags. These tags are used to identify the language of content on the Internet. BCP 47 is maintained by the Internet Engineering Task Force (IETF) and consists of two RFCs (Request for Comments): [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646) and [RFC 4647](https://datatracker.ietf.org/doc/html/rfc4647).

Language tags are necessary to ensure proper localisation and internationalisation of web content and software. They allow applications and services to provide the correct language content to users and support the accurate display of language-specific data such as date formats, numbers, and text direction.

A BCP 47 language tag consists of a series of subtags separated by hyphens. These subtags define various aspects of the language and its variants. The most basic components are:

+ **Primary language subtag**: This is a mandatory subtag consisting of a two- or three-letter code that denotes the main language (e.g., `en` for English, `de` for German).

+ **Region subtag**: An optional subtag that specifies a country or region (e.g., `US` for the United States in `en-US`).

+ **Script subtag**: An optional subtag that indicates the writing system (e.g., `Latn` for the Latin alphabet in `zh-Latn`).

+ **Variant subtag**: An optional subtag that describes language variants or dialects (e.g., `1901` for traditional German orthography in `de-1901`).

+ **Extension subtag**: Extensions for specifying additional information.

+ **Private use subtag**: A range reserved for private use and not standardised.

### Dates and Times

The formatting of dates and times in OpenCoLi, as specified by [JSON Schema](https://json-schema.org/understanding-json-schema/reference/string.html#dates-and-times), is defined by [RFC 3339, Section 5.6](https://tools.ietf.org/html/rfc3339#section-5.6).

Examples:

+ **`date-time`**: Date and time together, e.g., `2024-11-13T20:20:39` or `2024-11-13T20:20:39+00:00`.
+ **`time`**: Time only, e.g., `20:20:39` or `20:20:39+00:00`.
+ **`date`**: Date only, e.g., `2024-11-13`.

### URIs

When a [Uniform Resource Identifier (URI)](https://tools.ietf.org/html/rfc3986) is required, it is important to distinguish between two JSON string formats depending on the context:

+ The `uri` format expects the JSON string to be an absolute URI. An absolute URI contains all the necessary information to identify the resource independently of its context. This means a `uri`:
    
    + begins with a scheme (like http, https, etc.),
    + may contain a hostname or an IP address,
    + and may optionally include a path, a query, and a fragment.

+ The `uri-reference` format is more flexible and allows both absolute and relative URIs. `uri-reference` can be a complete URI as described above, or it can be a relative reference that needs to be interpreted in a specific context. A relative URI might include only a path or even just a fragment.

Examples:

+ **`uri`**:
    + `https://example.com/path/to/resource?query=param#fragment`
+ **`uri-reference`**:
    + `https://example.com/path/to/resource?query=param#fragment`
    + `/path/to/resource`
    + `#fragment`
  
### Schema

#### OpenCoLi Object

This is the root object of an OpenCoLi document and contains the following fields:

**`opencoli`**

:   A JSON string with the version number of the OpenCoLi specification. **This field is REQUIRED**.

**`codeList`**

:   A `codeList` object that includes the column definitions and data content of a code list.

**`codeListSet`**

:   A `codeListSet` object that defines a collection of code lists.

The `codeList` and `codeListSet` fields are mutually exclusive. **One of these fields is REQUIRED**.

#### codeList Object

The `codeList` object defines a complete code list along with its data:

**`info`**

:   A `codeListInfo` object containing metadata about the code list. **This field is REQUIRED**.

**`columnSet`**

:   A `columnSet` object that defines the columns and unique keys of the code list. **This field is REQUIRED**.

**`dataSet`**

:   A `dataSet` object that contains the data rows of the code list.

#### codeListInfo Object

The `codeListInfo` object contains metadata about the code list:

**`shortName`**

:   A JSON string with the short name of the code list.

**`longName`**

:   The long name of the code list. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`description`**

:   A detailed description of the code list. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`notes`**

:   Additional notes about the code list. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`changeLog`**

:   Documents the changes compared to previous versions of this code list. It MUST be a JSON array of JSON strings.

**`agency`**

:   An `agency` object with information about the organisation responsible for the publication and/or maintenance of the codes.

**`version`**

:   A JSON string with the version of the code list.

**`validFrom`**

:   A JSON string in `date-time` format that defines the start date from which this code list is valid.

**`validTo`**

:   A JSON string in `date-time` format that defines the end date up to which this code list is valid.

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions (together) of this code list. **This field is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI uniquely identifies a specific version of this code list.

**`locationUri`**

:   Either a JSON string in `uri` format or a JSON array with JSON string values in `uri` format. These URId are suggested retrieval locations for the referenced code list, in OpenCoLi format.

**`publishedAt`**

:   A JSON string in `date-time` format with the publication date of this code list.

**`publishedFrom`**

:   A `publisher` object with information about the publisher of this code list.

**`language`**

:   A JSON string with the language of this code list. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

This object MAY be extended.

Example:

```json
"info": {
  "shortName": "GermanFederalStateCodes",
  "longName": "ISO 3166-2 Codes for Germany",
  "description": "ISO 3166-2 Codes for the federal states of Germany",
  "agency": {
    "shortName": "ISO",
    "longName": "International Organization for Standardization",
    "url": "https://www.iso.org/"
  },
  "version": "2024-07-12",
  "canonicalUri": "urn:iso:std:iso:3166-2",
  "canonicalVersionUri": "urn:iso:std:iso:3166-2:2024-07-12",
  "locationUri": "https://raw.githubusercontent.com/openpotato/opencoli/main/samples/germany.federal-state-codes-2024-07-12.json",
  "publishedAt": "2024-07-12T10:00:00",
  "publishedFrom": {
    "shortName": "OpenCoLi",
    "url": "https://openpotato.github.io/opencoli/"
  },
  "language": "en"
}
```

#### codeListSet Object

The `codeListSet` object defines a collection of code lists:

**`info`**

:   A `codeListSetInfo` object containing metadata about the code list collection. **This field is REQUIRED**.

**`codeLists`**

:   A list of code lists. It MUST be a JSON array of `codeListRef` objects. **This field is REQUIRED**.

#### codeListSetInfo Object

The `codeListSetInfo` object contains metadata about a code list collection:

**`shortName`**

:   A JSON string with the short name of the code list collection.

**`longName`**

:   The long name of the code list collection. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`description`**

:   A short description of the code list collection. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`summary`**

:   A longer summary of the content of the code list collection. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`language`**

:   A JSON string with the language of this code list collection. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

This object MAY be extended.

Example:

```json
"info": {
  "shortName": "GermanFederalStateCodeListSet",
  "longName": "ISO 3166-2 Code Lists for Germany",
  "description": "All versions of the ISO 3166-2 Code Lists for the federal states of Germany",
  "language": "en"
}
```

#### codeListRef Object

The `codeListRef` object defines a reference to an external code list:

**`canonicalUri`**

:   A JSON string in `uri` format. This URI uniquely identifies all versions (together) of the referenced code list. **This field is REQUIRED**.

**`canonicalVersionUri`**

:   A JSON string in `uri` format. This URI uniquely identifies a specific version of the referenced code list.

**`locationUri`**

:   Either a JSON string in `uri` format or a JSON array with JSON string values in `uri` format. These URId are suggested retrieval locations for the referenced code list, in OpenCoLi format.

#### columnSet Object

The `columnSet` object defines columns and unique keys of a code list:

**`columns`**

:   Defines the columns of the code list. It MUST be a JSON array of `column` objects. **This field is REQUIRED**.

**`keys`**

:   Defines the unique keys of the code list. It MUST be a JSON array of `key` objects.

**`defaultKey`**

:   A JSON string with an ID that refers to a `key` object. This can be used to define the default key, that is, the preferred key from `keys` to be used as the code source.

**`references`**

:   Defines internal references as well as external references to other code lists. It MUST be a JSON array of `reference` objects.

#### column Object

The `column` object defines a column for a code list:

**`id`**

:   A JSON string with the ID of the column. **This field is REQUIRED**.

**`name`**

:   The name of the column. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`description`**

:   A short description of the column. This can be either a regular JSON string or a JSON array of `localizableString` objects.

**`type`**

:   Defines the data type of the column. **This field is REQUIRED**. It MUST be a JSON string with one of these values:
    + `string`
    + `enum`
    + `enum-set`
    + `integer`
    + `number`
    + `bool`
    + `time`
    + `date`
    + `date-time`
    + `object`

**`nullable`**

:   A JSON boolean that defines whether this column can also contain JSON NULLs.

Depending on the value in `type`, further properties are available.

##### string

Represents a JSON string. The following additional schema properties are available:

+ `minLength` : A JSON number in `integer` format with the minimum allowable number of characters
+ `maxLength` : A JSON number in `integer` format with the maximum allowable number of characters
+ `pattern` : A JSON string with a regular expression that must always match the values in this column.
+ `language` : A JSON string with the language for the contents of this column. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt).

##### enum

A JSON string representing an enumeration. The following additional schema properties are available:

+ `members` : Defines the possible values of the enumeration. It MUST be a JSON array of `enumMember` objects. **This field is REQUIRED**.

##### enum-set

A JSON string representing an enumeration set, formatted as a CSV string. The following additional schema properties are available:

+ `delimiterChar` : A JSON string defining the CSV delimiter.
+ `quoteChar` : A JSON string defining the CSV quote character.
+ `members` : Defines the possible values of the enumeration. It MUST be a JSON array of `enumMember` objects. **This field is REQUIRED**.

##### integer

Represents a JSON number in `integer` format. The following additional schema properties are available:

+ `minValue` : A JSON number in `integer` format with the minimum allowable value
+ `maxValue` : A JSON number in `integer` format with the maximum allowable value

##### number

Represents a JSON number in `number` format. The following additional schema properties are available:

+ `minValue` : A JSON number in `number` format with the minimum allowable value
+ `maxValue` : A JSON number in `number` format with the maximum allowable value

##### date

Represents a JSON string in `date` format. The following additional schema properties are available:

+ `minValue` : A JSON string in `date` format with the minimum allowable value
+ `maxValue` : A JSON string in `date` format with the minimum allowable value

Here’s the translation into British English:

##### date-time

Represents a JSON string in the `date-time` format. The following additional schema properties are available:

+ **`minValue`**: A JSON string in the `date-time` format specifying the allowable minimum value.
+ **`maxValue`**: A JSON string in the `date-time` format specifying the allowable maximum value.

##### time

Represents a JSON string in the `time` format. The following additional schema properties are available:

+ **`minValue`**: A JSON string in the `time` format specifying the allowable minimum value.
+ **`maxValue`**: A JSON string in the `time` format specifying the allowable maximum value.

##### object

Represents a JSON object. The following additional schema properties are available:

+ **`schemaUri`**: A JSON string in the `uri` format. This URI is the location for retrieving the JSON schema for this column.

#### enumMember Object

The `enumMember` object defines a value in an enumeration:

**`value`**

:   A JSON string with the enumeration value. **This field is REQUIRED**.

**`description`**

:   The description of the enumeration value. This can be either a standard JSON string or a JSON array with `localizableString` objects.

#### key Object

The `key` object defines a unique key for the code list:

**`id`**

:   A JSON string with the unique ID of the key. **This field is REQUIRED**.

**`name`**

:   The name of the key. This can be either a standard JSON string or a JSON array with `localizableString` objects.

**`description`**

:   A brief description of the key. This can be either a standard JSON string or a JSON array with `localizableString` objects.

**`columnRefs`**

:   A JSON array of strings with IDs that each reference a `column` object. **This field is REQUIRED**.

#### reference Object

The `reference` object defines a reference to either an internal or external code list:

**`codeListRef`**

:   A `codeListRef` object that points to an external code list. If `codeListRef` is defined, `keyRef` refers to a `key` object from the referenced code list; otherwise, it refers to a `key` object from this code list.

**`keyRef`**

:   A JSON string with an ID that references a `key` object. **This field is REQUIRED**.

#### dataSet Object

The `dataSet` object contains the data of a code list:

**`rows`**

:   Defines the data rows of the code list. It MUST be a JSON array of `row` objects. **This field is REQUIRED**.

#### row Object

The `row` object defines a data row in a code list:

**`cells`**

:   Defines the data cells of the data row. It MUST be a JSON array of `cell` objects. **This field is REQUIRED**.

#### cell Object

The `cell` object defines the content of a data cell:

**`columnRef`**

:   A reference to a code list column. A JSON string with an ID that references a `column` object.

**`value`**

:   Defines the actual value of the data cell. Depending on the column definition, this can be a JSON null value, a JSON string, a JSON number, a JSON object, or a JSON array of `localizableString` objects. **This field is REQUIRED**.

#### localizableString Object

The `localizableString` object is a text string associated with a language code:

**`language`**

:   A JSON string with a language tag. This MUST be an [IETF BCP 47 language tag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt). **This field is REQUIRED**.

**`text`**

:   The actual text. **This field is REQUIRED**.

Example:

``` json
"longName": [
  {
    "language": "en",
    "text": "ISO 3166-2 Codes for Germany"
  },
  {
    "language": "de",
    "text": "ISO 3166-2 Codes für Deutschland"
  }
]
```

### Extension of the Specification

The OpenCoLi specification can be extended with additional data at certain points.

The properties of these extensions are implemented as free fields, which must always be prefixed with `x-` (e.g., `x-external-id`). The value can be a string, a number, a boolean value, null, an object, or an array.

The extensions may or may not be supported by the available tools. Ideally, these tools can be extended to add the desired support (e.g., in Open Source projects).

Example:

```json
"info": {
  "shortName": "GermanFederalStateCodes",
  "agency": {
    "shortName": "ISO",
    "longName": "International Organization for Standardization",
    "x-contact-name": "ISO Central Secretariat",
    "x-contact-address": "Chemin de Blandonnet 8, 1214 Geneva, Switzerland",
    "x-contact-email": "central@iso.org"
  }
}
```