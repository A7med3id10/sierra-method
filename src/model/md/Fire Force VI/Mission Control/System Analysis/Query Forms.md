---
ontology: https://fireforce6.github.io/mission-control/system-analysis/components
title: Fire Force VI — SPARQL Query Forms
---

# Fire Force VI — SPARQL Query Forms

## Engineering Question

How is the FireSat component architecture organized,
and are any components missing a parent?

## 1. SELECT — Find Orphan Components

The SELECT query identifies components that have no
recorded parent, excluding the top-level FireSat system.

```table
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX component: <https://www.modelware.io/sierra/component#>
PREFIX base: <https://www.modelware.io/sierra/base#>
PREFIX components: <https://fireforce6.github.io/mission-control/system-analysis/components#>

SELECT DISTINCT ?component
WHERE {
    ?component rdf:type ?type .
    ?type rdfs:subClassOf* component:Component .

    FILTER(?component != components:FireSat)

    FILTER NOT EXISTS {
        ?component base:isContainedBy ?parent .
    }
}
ORDER BY ?component
```

## ASK — Are Any Components Missing a Parent?

The ASK query returns `true` when at least one component
other than the top-level FireSat system has no recorded parent.

```table
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX component: <https://www.modelware.io/sierra/component#>
PREFIX base: <https://www.modelware.io/sierra/base#>
PREFIX components: <https://fireforce6.github.io/mission-control/system-analysis/components#>

ASK
WHERE {
    ?component rdf:type ?type .
    ?type rdfs:subClassOf* component:Component .

    FILTER(?component != components:FireSat)

    FILTER NOT EXISTS {
        ?component base:isContainedBy ?parent .
    }
}
```

### ASK — Are Any Components Missing a Parent?

The original ASK query returns a Boolean. This table
uses an equivalent SELECT expression for notebook display.

```table
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX component: <https://www.modelware.io/sierra/component#>
PREFIX base: <https://www.modelware.io/sierra/base#>
PREFIX components: <https://fireforce6.github.io/mission-control/system-analysis/components#>

SELECT
    (IF(COUNT(DISTINCT ?component) > 0,
        "TRUE", "FALSE") AS ?orphanExists)
WHERE {
    OPTIONAL {
        ?component rdf:type ?type .
        ?type rdfs:subClassOf* component:Component .

        FILTER(?component != components:FireSat)

        FILTER NOT EXISTS {
            ?component base:isContainedBy ?parent .
        }
    }
}
```


## 2. CONSTRUCT — Component Containment Graph

This query creates an explicit parent-to-child RDF
graph derived from the component containment facts.

```tree
---
containment: [ base:contains ]
---
PREFIX base: <https://www.modelware.io/sierra/base#>

CONSTRUCT {
    ?parent base:contains ?child .
}
WHERE {
    ?child base:isContainedBy ?parent .
}
```

## 3. DESCRIBE — FireSat

The DESCRIBE query retrieves RDF statements associated with
the top-level FireSat component.

```table
PREFIX components: <https://fireforce6.github.io/mission-control/system-analysis/components#>

DESCRIBE components:FireSat
```

### DESCRIBE — Explore the FireSat Component

A DESCRIBE query returns an RDF graph. The following
SELECT query displays FireSat's outgoing RDF statements
as a table for notebook inspection.

```table
PREFIX components: <https://fireforce6.github.io/mission-control/system-analysis/components#>

SELECT ?predicate ?value
WHERE {
    components:FireSat ?predicate ?value .
}
ORDER BY ?predicate ?value
```
## Engineering Interpretation

SELECT returns individual components that may require
an architecture review.

CONSTRUCT returns a graph that can be visualized as
a system decomposition.

An empty SELECT result does not by itself prove that
the entire architecture is complete.
