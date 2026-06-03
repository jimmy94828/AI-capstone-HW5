# Source

This folder contains the headless reasoning + query workflow used to generate
the inferred graph and the SPARQL output.

## `run_reasoning.py`

A single self-contained script that:

1. Merges `ontology/group-ontology.ttl` with the two imported files and strips
   `owl:imports` so the reasoner does not resolve relative IRIs.
2. Runs the **HermiT OWL 2 DL reasoner** (via `owlready2`, which calls the local
   JRE) to classify individuals under `cap:GraspableObject`.
3. Writes the inferred `rdf:type cap:GraspableObject` triples to
   `ontology/inferred-results.ttl`.
4. Executes `queries/graspable_objects.rq` over the asserted + inferred graph and
   saves the formatted result to `results/graspable_objects_output.txt`.

HermiT is a complete OWL 2 DL reasoner, so the `owl:equivalentClass` /
`owl:someValuesFrom` graspability pattern is classified by genuine description-logic
reasoning, not a hand-written rule layer. This satisfies the requirement in the
homework spec (Option C) that a Python workflow must clearly document how
`cap:GraspableObject` membership is derived rather than manually asserted.

### Requirements

- Java (HermiT is a Java reasoner; tested with OpenJDK 21)
- `pip install owlready2 rdflib`

### Run

From the repository root:

```bash
python3 src/run_reasoning.py
```

This regenerates `ontology/inferred-results.ttl` and
`results/graspable_objects_output.txt`.

## `run_validation.py`

Runs SHACL structural validation (pyshacl) of `ontology/shapes.ttl` against the
asserted graph and writes `results/shacl_validation_report.txt`. This is separate
from OWL reasoning: OWL infers `cap:GraspableObject` membership, while SHACL checks
that every object has the expected labels, task roles, and affordances, and that
every manipulation target has a grasping affordance.

```bash
pip install pyshacl rdflib
python3 src/run_validation.py
```

## Alternative GUI workflow

The same result can be reproduced in Protege (open `group-ontology.ttl`, load the
imports, run HermiT or Pellet, inspect the inferred members of
`cap:GraspableObject`, and export the inferred axioms). See the project `README.md`
and `report.md` for that workflow and for optional screenshot verification.
