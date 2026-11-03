---
template:
  id: https://www.modelware.io/sierra/system-analysis/anomalies
  name: "Fault & Anomaly Reports"
  rank: 0
  expose:
    - kind: compose
  params:
    - id: ontology
      type: iri
      defaultValue: ${context.ontology}
      required: true
---

# Fault & Anomaly Reporting

Create and manage anomaly reports for Fire Force spacecraft components.

```tree-editor
---
columns: { this: { label: "Anomaly Report" } }
---
@prefix sh: <http://www.w3.org/ns/shacl#> .
@prefix dash: <http://datashapes.org/dash#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix base: <https://www.modelware.io/sierra/base#> .
@prefix component: <https://www.modelware.io/sierra/component#> .
@prefix anomaly: <https://fireforce6.github.io/mission-control/anomaly#> .

anomaly:AnomalyReportShape
    a sh:NodeShape ;
    sh:targetClass anomaly:AnomalyReport ;

    sh:property [
        sh:path anomaly:reportId ;
        sh:name "Report ID" ;
        sh:datatype xsd:string ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path anomaly:affectsComponent ;
        sh:name "Affected Component" ;
        sh:class component:Component ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path base:description ;
        sh:name "Description" ;
        dash:editor dash:TextAreaEditor ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path anomaly:severity ;
        sh:name "Severity" ;
        sh:in ("Minor" "Major" "Critical") ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path anomaly:status ;
        sh:name "Status" ;
        sh:in ("Open" "Under Review" "Resolved") ;
        sh:minCount 1 ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path anomaly:mitigationAction ;
        sh:name "Mitigation Action" ;
        dash:editor dash:TextAreaEditor ;
        sh:maxCount 1 ;
    ] ;

    sh:property [
        sh:path anomaly:evidenceReference ;
        sh:name "Evidence Reference" ;
        sh:maxCount 1 ;
    ] ;
    .
