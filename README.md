# Car Ontology — M9A Stretch

## What is this?

A small knowledge graph about cars — their **type** (Sedan, SUV, Coupe), their **brand**, and the **year** the model was first made.

---

## Domain

Cars. Simple and familiar. Every car has a type, belongs to a brand, and was introduced in a specific year. Brands have a home country and a founding year.

---

## Class Structure

```
Car
├── Sedan   (e.g. Toyota Camry, BMW 3 Series)
├── SUV     (e.g. Ford Bronco, Tesla Model Y)
└── Coupe   (e.g. Porsche 911, BMW 4 Series)

Brand       (Toyota, Honda, Ford, BMW, Porsche, Tesla)
```

Each car connects to its brand like this:

```
:Camry  a :Sedan ;
    :brand  :Toyota ;
    :model  "Camry" ;
    :year   "1982" .
```

---

## Properties

| Property      | Applies to | Example value     | Required? |
|---------------|------------|-------------------|-----------|
| `:brand`      | Car        | Toyota            | ✅ Yes |
| `:model`      | Car        | "Camry"           | ✅ Yes |
| `:year`       | Car        | 1982              | ✅ Yes |
| `:country`    | Brand      | "Japan"           | ✅ Yes |
| `:founded`    | Brand      | 1937              | ✅ Yes |
| `:horsepower` | Car        | 203               | ⬜ Optional |
| `:isElectric` | Car        | true              | ⬜ Optional |

**OPTIONAL properties explained:**
- `:horsepower` — Tesla Model S does not have it (electric motor, no single HP figure). Tesla Model Y also omits it for the same reason.
- `:isElectric` — Only electric cars carry this. Gasoline cars simply don't have the property at all (absence = gas engine).

---

## One Modeling Decision Worth Explaining

**`Sedan`, `SUV`, and `Coupe` are subclasses of `Car` — not just a text label.**

This means a SPARQL query asking for *"all Cars"* automatically includes Sedans, SUVs, and Coupes without listing them one by one. If we add `:Hatchback` later, it just works. Using a text label like `type = "SUV"` would require every query to know every possible type string.

---

## Where `skos:altLabel` and `rdfs:subClassOf` Appear

### `rdfs:subClassOf`
```
:Sedan  rdfs:subClassOf :Car
:SUV    rdfs:subClassOf :Car
:Coupe  rdfs:subClassOf :Car
```
Enables: "give me all Cars" → returns everything.

### `skos:prefLabel` + `skos:altLabel`
| Thing       | prefLabel | altLabel             | Why |
|-------------|-----------|----------------------|-----|
| `:Sedan`    | "Sedan"   | "Saloon"             | British English calls it a Saloon |
| `:SUV`      | "SUV"     | "Sport Utility Vehicle" | Either term should find it |
| `:Coupe`    | "Coupe"   | "Sports Car"         | Common casual name |
| `:Porsche911`| "Porsche 911" | "911"           | Fans just say "the 911" |
