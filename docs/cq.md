## Competency Questions

PSO can be used for answering several questions related to the various statuses a publication entity goes through during its publication process.
In the following subsections, some of them are introduced together with their respective SPARQL queries. 

The prefixes that are used in all the SPARQL queries provided below are defined as follows:

    PREFIX dcterms: <http://purl.org/dc/terms/>
    PREFIX part: <http://www.ontologydesignpatterns.org/cp/owl/participation.owl#>
    PREFIX pso: <http://purl.org/spar/pso/>
    PREFIX ti: <http://www.ontologydesignpatterns.org/cp/owl/timeinterval.owl#>
    PREFIX tvc: <http://www.essepuntato.it/2012/04/tvc/>
    PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

### CQ1

Which statuses in time and status types are associated with a document?

    SELECT ?document ?statusInTime ?status
    WHERE {
        ?document pso:holdsStatusInTime ?statusInTime .
        ?statusInTime a pso:StatusInTime ;
            pso:withStatus ?status .
    }

### CQ2

What are the start and end dates of the time intervals during which a document holds a particular status?

    SELECT ?document ?status ?timeInterval ?startDate ?endDate
    WHERE {
        ?document pso:holdsStatusInTime ?statusInTime .
        ?statusInTime pso:withStatus ?status ;
            tvc:atTime ?timeInterval .
        OPTIONAL { ?timeInterval ti:hasIntervalStartDate ?startDate . }
        OPTIONAL { ?timeInterval ti:hasIntervalEndDate ?endDate . }
    }

### CQ3

Which event caused the acquisition of a status for a document, along with its description?

    SELECT ?document ?status ?acquisitionEvent ?eventDescription
    WHERE {
        ?document pso:holdsStatusInTime ?statusInTime .
        ?statusInTime pso:withStatus ?status ;
            pso:isAcquiredAsConsequenceOf ?acquisitionEvent .
        OPTIONAL { ?acquisitionEvent dcterms:description ?eventDescription . }
    }

### CQ4

Which event caused the termination or loss of a status, along with its description?

    SELECT ?document ?status ?lossEvent ?eventDescription
    WHERE {
        ?document pso:holdsStatusInTime ?statusInTime .
        ?statusInTime pso:withStatus ?status ;
            pso:isLostAsConsequenceOf ?lossEvent .
        OPTIONAL { ?lossEvent dcterms:description ?eventDescription . }
    }

### CQ5

What is the complete lifecycle of a document's status in time (status, validity interval, triggering event, and terminating event)?

    SELECT ?document ?status ?startDate ?endDate ?acquiredEvent ?lostEvent
    WHERE {
        ?document pso:holdsStatusInTime ?statusInTime .
        ?statusInTime pso:withStatus ?status .
        OPTIONAL {
            ?statusInTime tvc:atTime ?timeInterval .
            OPTIONAL { ?timeInterval ti:hasIntervalStartDate ?startDate . }
            OPTIONAL { ?timeInterval ti:hasIntervalEndDate ?endDate . }
        }
        OPTIONAL { ?statusInTime pso:isAcquiredAsConsequenceOf ?acquiredEvent . }
        OPTIONAL { ?statusInTime pso:isLostAsConsequenceOf ?lostEvent . }
    }