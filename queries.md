# SPARQL Queries

## Query 1 — Get all cars (any type)

Uses `rdfs:subClassOf*` so Sedans, SUVs, and Coupes are all included automatically.

```sparql
PREFIX :     <http://example.org/cars#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT ?car ?label ?year
WHERE {
  ?subClass rdfs:subClassOf* :Car .
  ?car a ?subClass ;
       skos:prefLabel ?label ;
       :year ?year .
}
ORDER BY ?year
```

**Result:** All 10 car models, from oldest (BMW 3 Series 1975) to newest (Tesla Model Y 2020).

---

## Query 2 — Search by label OR nickname

Finds cars using either the formal name or the informal one (e.g. "Saloon" finds Sedans).

```sparql
PREFIX :     <http://example.org/cars#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT ?car ?carLabel ?year
WHERE {
  ?type a rdfs:Class .
  { ?type skos:prefLabel "Saloon"@en }
  UNION
  { ?type skos:altLabel  "Saloon"@en }

  ?subClass rdfs:subClassOf* ?type .
  ?car a ?subClass ;
       skos:prefLabel ?carLabel ;
       :year ?year .
}
ORDER BY ?year
```

**Result:** Searching "Saloon" returns Toyota Camry, Honda Accord, and BMW 3 Series — because "Saloon" is the `altLabel` on `:Sedan`.

---

## Query 3 — Which brands are oldest, and where are they from?

```sparql
PREFIX :     <http://example.org/cars#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT ?brandName ?country ?founded (COUNT(?car) AS ?numModels)
WHERE {
  ?brand a :Brand ;
         skos:prefLabel ?brandName ;
         :country ?country ;
         :founded ?founded .
  OPTIONAL { ?car :brand ?brand . }
}
GROUP BY ?brandName ?country ?founded
ORDER BY ?founded
```

**Result:**

| Brand   | Country | Founded | Models in ontology |
|---------|---------|---------|--------------------|
| Ford    | USA     | 1903    | 1 |
| BMW     | Germany | 1916    | 3 |
| Porsche | Germany | 1931    | 1 |
| Toyota  | Japan   | 1937    | 2 |
| Honda   | Japan   | 1948    | 1 |
| Tesla   | USA     | 2003    | 2 |

Ford has been making cars for 120+ years. Tesla for ~20. The `:founded` property makes that visible at a glance.
