# GEDCOM-X Names in GEDCOM 7

The purpose of this set of extensions to [GEDCOM 7.0](https://gedcom.io) is to port [GEDCOM-X](https://gedcomx.org)'s more versatile name structures in GEDOCM 7.

This document uses prefix notation:
any URI beginning with one of the following short prefixes followed by a colon
is shorthand for a URI beginning with the corresponding URI prefix

| Short Prefix | URI Prefix                                     |
|:-------------|:-----------------------------------------------|
| `g7`         | `https://gedcom.io/terms/v7/`                  |
| `gx`         | `http://gedcomx.org/`                          |
| `ext`        | `https://tychonievich.github.io/gx-g7-names#`  |

This extension consists of a three new extension structure types and a large number of new enumeration values, most taken directly from GEDCOM-X.

## Extension to spec

Extend the `PERSONAL_NAME_PIECES` production [in the GEDCOM 7 specification](https://gedcom.io/specifications/FamilySearchGEDCOMv7.html#PERSONAL_NAME_PIECES) with the following new term:

```gedstruct
n _N_PART <Text>             {0:M}  ext:name-part
  +1 _NP_TYPE <Enum>         {0:1}  ext:name-part-type
  +1 _NP_QUAL <Enum>         {0:M}  ext:name-part-qualifier
```

It is worth noting that, [as with all documented GEDCOM 7 extensions](https://gedcom.io/specifications/FamilySearchGEDCOMv7.html#extension-tags), the tag names above are only recommendations: it is the URIs, not the tag names, that define the meaning of these structures. See [Recommended Tags](#recommended-tags) for more.

### name-part

URI: `https://tychonievich.github.io/gx-g7-names#name-part`

A part of a name.

This structure is intended to be the GEDCOM 7 parallel of the GEDOCM-X element <http://gedcomx.org/v1/NamePart>.

The preferred order of `ext:name-part` structures is the preferred display order of the name parts.

### name-part-type

URI: `https://tychonievich.github.io/gx-g7-names#name-part-type`

The type of a name part.
Only four name part types are defined, and not all name parts need have a type.
Additional, more specific name part descriptors can be found in the `ext:name-part-qualifier`s.

This structure is intended to be the GEDCOM 7 parallel of the `type` property of the GEDOCM-X element <http://gedcomx.org/v1/NamePart>.
The payload of this structure should be an enumerated value documented by GEDCOM-X for that property, namely

| URI | description |
|:----|:------------|
| `http://gedcomx.org/Prefix` | A name prefix. |
| `http://gedcomx.org/Suffix` | A name suffix. |
| `http://gedcomx.org/Given` | A given name. |
| `http://gedcomx.org/Surname` | A surname. |

The `ext:name-part-type` structure is optional, and should only be included if exactly one of the above enumerated values correctly describes the name part.

### ext:name-part-qualifier

URI: `https://tychonievich.github.io/gx-g7-names#name-part-qualifier`

A type-like descriptor of a name part.


This structure is intended to be the GEDCOM 7 parallel of the `qualifiers` property of the GEDOCM-X element <http://gedcomx.org/v1/NamePart>.
The payload of this structure should be an enumerated value [documented by GEDCOM-X for that property](https://github.com/FamilySearch/gedcomx/blob/master/specifications/name-part-qualifiers-specification.md), namely

| URI | description |
|:----|:------------|
| `http://gedcomx.org/Title`|A designation for honorifics (e.g. Dr., Rev., His Majesty, Haji), ranks (e.g. Colonel, General, Knight, Esquire), positions (e.g. Count, Chief, Father, King) or other titles (e.g., PhD, MD). |
| `http://gedcomx.org/Primary`|A designation for the name of most prominent in importance among the names of that type (e.g., the primary given name). |
| `http://gedcomx.org/Secondary`|A designation for a name that is not primary in its importance among the names of that type (e.g., a secondary given name). |
| `http://gedcomx.org/Middle`|A designation useful for cultures that designate a middle name that is distinct from a given name and a surname. |
| `http://gedcomx.org/Familiar`|A designation for one's familiar name. |
| `http://gedcomx.org/Religious`|A designation for a name given for religious purposes. |
| `http://gedcomx.org/Family`|A name that associates a person with a group, such as a clan, tribe, or patriarchal hierarchy. |
| `http://gedcomx.org/Maiden`|A designation given by women to their original surname after they adopt a new surname upon marriage. |
| `http://gedcomx.org/Patronymic`|A name derived from a father or paternal ancestor. |
| `http://gedcomx.org/Matronymic`|A name derived from a mother or maternal ancestor. |
| `http://gedcomx.org/Geographic`|A name derived from associated geography. |
| `http://gedcomx.org/Occupational`|A name derived from one's occupation. |
| `http://gedcomx.org/Characteristic`|A name derived from a characteristic. |
| `http://gedcomx.org/Postnom`|A name mandated by law for populations from Congo Free State / Belgian Congo / Congo / Democratic Republic of Congo (formerly Zaire). |
| `http://gedcomx.org/Particle`|A grammatical designation for articles (a, the, dem, las, el, etc.), prepositions (of, from, aus, zu, op, etc.), initials, annotations (e.g. twin, wife of, infant, unknown), comparators (e.g. Junior, Senior, younger, little), ordinals (e.g. III, eighth), descendancy words (e.g. ben, ibn, bat, bin, bint, bar), and conjunctions (e.g. and, or, nee, ou, y, o, ne, &amp;). |
| `http://gedcomx.org/RootName`|The "root" of a name part as distinguished from prefixes or suffixes. For example, the root of the Polish name "Wilkówna" is "Wilk". |
| `https://tychonievich.github.io/gx-g7-names#hidden`|A name part that should not be displayed. |

Note that this extension adds one qualifier not present in GEDCOM-X:

#### hidden

URI: `https://tychonievich.github.io/gx-g7-names#hidden`

There may be cases where a part of a name should be represented in the data but not displayed when the name is formatted.
For example, there is a Polish surname that is spelled "Kowalski" for men and mixed-gender groups but "Kowalska" for women; a researcher may wish to record both forms, but add the `ext:hidden` qualifier to the one that does not match the gender of the individual.

### Additional constraints

To be fully interoperable with other parts of GEDCOM 7, applications should impose the following limitations on users.

For the purpose of these limitations, a set of name parts are said to be *contiguous* if, after removing any `ext:hidden` parts, they are adjacent in the list of name parts.

* Some applications (and some governments; see, e.g. [rufnames](https://en.wikipedia.org/wiki/Rufname)) require at most one primary given name.
    This means that if there is more than one part with qualifier `gx:Primary` and type `gx:Given`, all such parts must be contiguous.
    
    In most cases, a single `gx:Primary` `gx:Given` part is adequate, but there may be cases when additional details (such as particles, root names, etc) will be added about parts of the primary given name, necessitating breaking it up into several parts.

* The `g7:type-Name` datatype requires at most one primary surname, as identified by slashes in the payload.
    This means that 
    
    * If there is more than one part with qualifier `gx:Primary` and type `gx:Surname`, all such parts must be contiguous.
    * If there is no part with `gx:Primary` and type `gx:Surname`, then all parts with type `gx:Surname` must be contiguous.

    In some cases a single `gx:Primary` `gx:Surname` part is adequate, but there may be cases when additional details (such as particles, root names, etc) will be added about parts of the primary surname name, necessitating breaking it up into several parts.


## Recommended Tags

Per the GEDOCM 7 specification, the tags used for documented extension structures may be changed to avoid tag collisions between independent extensions. However, the following tags are recommended:

```gedcom
0 HEAD
1 GEDC
2 VERS 7.0
1 SCHMA
2 TAG _N_PART https://tychonievich.github.io/gx-g7-names#name-part
2 TAG _NP_TYPE https://tychonievich.github.io/gx-g7-names#name-part-type
2 TAG _NP_QUAL https://tychonievich.github.io/gx-g7-names#name-part-qualifier
2 TAG _GX_PREFIX http://gedcomx.org/Prefix
2 TAG _GX_SUFFIX http://gedcomx.org/Suffix
2 TAG _GX_GIVEN http://gedcomx.org/Given
2 TAG _GX_SURNAME http://gedcomx.org/Surname
2 TAG _GX_TITLE http://gedcomx.org/Title
2 TAG _GX_PRIMARY http://gedcomx.org/Primary
2 TAG _GX_SECONDARY http://gedcomx.org/Secondary
2 TAG _GX_MIDDLE http://gedcomx.org/Middle
2 TAG _GX_FAMILIAR http://gedcomx.org/Familiar
2 TAG _GX_RELIGIOUS http://gedcomx.org/Religious
2 TAG _GX_FAMILY http://gedcomx.org/Family
2 TAG _GX_MAIDEN http://gedcomx.org/Maiden
2 TAG _GX_PATRONYMIC http://gedcomx.org/Patronymic
2 TAG _GX_MATRONYMIC http://gedcomx.org/Matronymic
2 TAG _GX_GEOGRAPHIC http://gedcomx.org/Geographic
2 TAG _GX_OCCUPATIONAL http://gedcomx.org/Occupational
2 TAG _GX_CHARACTERISTIC http://gedcomx.org/Characteristic
2 TAG _GX_POSTNOM http://gedcomx.org/Postnom
2 TAG _GX_PARTICLE http://gedcomx.org/Particle
2 TAG _GX_ROOTNAME http://gedcomx.org/RootName
2 TAG _NPQ_HIDDEN https://tychonievich.github.io/gx-g7-names#hidden
```

## Name parts as the only stored data

It is intended that `ext:name-part` be used in such a way that all other elements of a `g7:INDI-NAME` or `g7:NAME-TRAN` can be reconstructed unambiguously from the `ext:name-part`s alone.

To achieve this, every character in the name's displayed form should be in a `ext:name-part`. This may mean there are `ext:name-part`s with no type or qualifier that store spacing, punctuation, etc.

To populate the other name data from name parts,

* Create the `g7:type-NAME` payload by 
    
    1. Concatenating all `ext:name-part` payloads that do not have the `ext:hidden` qualifier.
    2. Removing any U+002F characters (slash or solidus, `/`), which `g7:type-NAME` cannot represent.
    3. Possibly delimit a part of the string with slashes:
        - If no part has type `gx:Surname`, do not add slashes.
        - Otherwise, if there are name parts with type `gx:Surname` and qualifier `gx:Primary`, put a slash before the first such part and after the last such part.
        - Otherwise, if there are parts with type `gx:Surname`, put a slash before the first such part and after the last such part.
        
* Create other personal name parts as follows:
    - Create a `g7:GIVN` for each part with type `gx:Given`
    - Create a `g7:SURN` for each part with type `gx:Surname`, unless it also has qualifier `gx:Particle` and precedes all `gx:Surname`s that lack `gx:Particle`: in that case, use `g7:SPFX` instead
    - Create a `g7:NPFX` for each part with type `gx:Prefix`
    - Create a `g7:NSFX` for each part with type `gx:Suffix`
    - Create a `g7:NICK` for each part with qualifier `gx:Familiar` that does not also have a type
    - If using a rufname extension, create a rufname structure for the concatenated sequence of parts with type `gx:Given` and qualifier `gx:Primary`

