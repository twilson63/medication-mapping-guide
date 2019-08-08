# Medication Mapping

## Summary

This document provides guidance on how to take a medication list with no cross-reference identifier and match it with a RxCUI identifier for a semantic clinical drug or a semantic brand drug. Which can be used in clinical decision support systems to provide insight into medication safety.

> NOTE: this document uses some medication specific terminology, please see the glossary below for definitions and links to further explainations of each concept.

In order to create a mapping you will need the following:

* English Generic Name of the medication
* Strength of the medication
* Strength units of the medication
* Dose form of the medication


## Using the RxNorm API

The RxNorm REST API is published online at https://rxnav.nlm.nih.gov/RxNormAPIs.html and contains an endpoint that will try to match the medication name, strength, strength units and form to the best rxcui. You can see some background information on how they try to find the right match.  https://rxnav.nlm.nih.gov/RxNormApproxMatch.html

## Comments to help

If there is not a high signal of results, the response will provide comments giving the requestor information about what could be a issue with the matching:

* Mispellings
* No drugs identified

## Getting to SCD or SBD 

Once you have an RxCUI, you can check the rxcui aganist the following `/rxcui/{rxcui}/properties` endpoint:

https://rxnav.nlm.nih.gov/RxNormAPIREST.html#uLink=RxNorm_REST_getRxConceptProperties

This request will give you the term-type, if it is not SCD or SBD, then you will need to get the related types.

You can get the related types by calling the the related endpoint:

https://rxnav.nlm.nih.gov/RxNormAPIREST.html#uLink=RxNorm_REST_getRelatedByType

## JSON Data

You can choose between working with xml or json, in order to use json, simply add a .json at the end of each url path. Note the examples in the documentation.

## Examples

> HTTPS RESTful endpoints can be access using any program language that supports an http client and json or xml parsers. In these examples we chose to provide the examples using `curl` which is a command line utility that shows the resource, headers, methods and responses. For more information about curl - https://curl.haxx.se/, for more information about RESTful Services - https://en.wikipedia.org/wiki/Representational_state_transfer

### Example 1 - Simple Match

Product: nifedipine 30 mg tablet

```
curl https://rxnav.nlm.nih.gov/REST/approximateTerm.json?term=nifedipine%2030%20mg
```

Results

```
approximateGroup: 
  inputTerm: nifedipine 30 mg
  comment:   
  candidate: 
    - 
      rxcui: 317655
      rxaui: 1482815
      score: 100
      rank:  1
```

Raw Json

```
{
  "approximateGroup": {
    "inputTerm": "nifedipine 30 mg",
    "comment": "",
    "candidate": [
      {
        "rxcui": "317655",
        "rxaui": "1482815",
        "score": "100",
        "rank": "1"
      },
      {
        "rxcui": "317655",
        "rxaui": "3724139",
        "score": "100",
        "rank": "1"
      },
      ...
    ]
  }
}
```

The results will come back sorted based on rank and score:

You can use the rxcui to get the properties of the candidate:

```
curl https://rxnav.nlm.nih.gov/REST/rxcui/317655/properties.json
```

Response

```
{
  "properties": {
    "rxcui": "317655",
    "name": "Nifedipine 30 MG",
    "synonym": "",
    "tty": "SCDC",
    "language": "ENG",
    "suppress": "N",
    "umlscui": "C0993433"
  }
}
```

This shows that this rxcui has tty of SCDC or Semantic Clinical Drug Concept. If a match is not a SCD or SBD, then you can use the `/rxcui/{rxcui}/related?tty` endpoint

```
curl https://rxnav.nlm.nih.gov/REST/rxcui/317655/related.json?tty=SBD+SCD
```

Response

```
{
  "relatedGroup": {
    "rxcui": "317655",
    "termType": [
      "SBD",
      "SCD"
    ],
    "conceptGroup": [
      {
        "tty": "SBD",
        "conceptProperties": [
          {
            "rxcui": "207772",
            "name": "Osmotic 24 HR Nifedipine 30 MG Extended Release Oral Tablet [Procardia]",
            "synonym": "Procardia 30 MG Osmotic 24 HR Extended Release Oral Tablet",
            "tty": "SBD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C0709170"
          },
          {
            "rxcui": "360394",
            "name": "Osmotic 24 HR Nifedipine 30 MG Extended Release Oral Tablet [Nifedical]",
            "synonym": "Nifedical 30 MG Osmotic 24 HR Extended Release Oral Tablet",
            "tty": "SBD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C1178599"
          },
          {
            "rxcui": "672916",
            "name": "24 HR Nifedipine 30 MG Extended Release Oral Tablet [Adalat]",
            "synonym": "24 HR Adalat 30 MG Extended Release Oral Tablet",
            "tty": "SBD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C0978987"
          },
          {
            "rxcui": "672920",
            "name": "24 HR Nifedipine 30 MG Extended Release Oral Tablet [Afeditab CR]",
            "synonym": "Afeditab CR 30 MG 24 HR Extended Release Oral Tablet",
            "tty": "SBD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C1585734"
          }
        ]
      },
      {
        "tty": "SCD",
        "conceptProperties": [
          {
            "rxcui": "198034",
            "name": "24 HR Nifedipine 30 MG Extended Release Oral Tablet",
            "synonym": "nifedipine 30 MG 24 HR Extended Release Oral Tablet",
            "tty": "SCD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C0689768"
          },
          {
            "rxcui": "227058",
            "name": "24 HR Nifedipine 30 MG Extended Release Oral Capsule",
            "synonym": "nifedipine 30 MG 24 HR Extended Release Oral Capsule",
            "tty": "SCD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C0732973"
          },
          {
            "rxcui": "391980",
            "name": "Nifedipine 30 MG Oral Tablet",
            "synonym": "",
            "tty": "SCD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C1298361"
          },
          {
            "rxcui": "1812011",
            "name": "Osmotic 24 HR Nifedipine 30 MG Extended Release Oral Tablet",
            "synonym": "nifedipine 30 MG Osmotic 24 HR Extended Release Oral Tablet",
            "tty": "SCD",
            "language": "ENG",
            "suppress": "N",
            "umlscui": "C4282228"
          }
        ]
      }
    ]
  }
}
```


## Legal Compliance

This document is for information purposes only and is intended to provide a high level overview of some key RxNorm API endpoints that can help a developer map a medication drug list without a standard identifier.  The RxNorm API is a public resource provided by the National Library of Medicine.  The dataset that is used to drive this API and the API itself are available for download.  This document does not constitute legal advice.  Developers using this guidance are responsible for compliance with all applicable local, regional, and international laws, ordinances, regulations, codes and standards.

## Download API

https://mor.nlm.nih.gov/download/rxnav/RxNav-in-a-Box.html

## Thank you 

Thank you for taking the time to review this document and the RxNorm API, the National Institute of Medicine deserves all the credit for this fantastic resource.

## Glossary

| Term                   |  Definition                                                |
| --- | ---- |
| RxNorm | RxNorm is a clinical database created by the United States National Institute of Medicine, it contains cross references to several clinical databases in an effort to normalize medication identifiers across healthcare |
| Generic Medication Name | The generic medication name is the ingredient or list of major ingredients to a medication in english |
| Strength | Is the number that represents the amount of a given unit that this ingredient exists in the medication |
| Strength Units | Is the unit or measurement of the given ingredient in the medication |
| Dose Form | Is the form of the medication. e.g. Tablet, Patch, etc |
| API | Application Program Interface or a bridge that any software application can call to get results |
| RxCUI | Is an rx norm identifier that spreads across several classification systems, from ingredient to product. |
| SCD | Semantic Clinical Drug - [Generic Medication Name] [Strength] [Strength Units] [Dose Form] [Dose Route] |
| SBC | Semantic Brand Drug - [Brand Medication Name] [Strength] [Strength Units] [Dose Form] [Dose Route] |


<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.
