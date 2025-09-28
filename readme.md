AOP Evidence — Entity Extraction Overview
================

## Entity Extraction Pipeline

- **Load Models**
  - `en_ner_bc5cdr_md`: pretrained NER model for **CHEMICAL** entities.
  - `en_ner_bionlp13cg_md`: pretrained NER model for **SPECIES /
    ORGANISM** entities.
  - Both are spaCy pipelines; only tokenization + `ner` are enabled for
    speed.
- **Process Text**
  - Each row’s text is passed through both models → produces two `Doc`
    objects with `doc.ents`.
- **Filter Entities**
  - From the first model keep `label_ == "CHEMICAL"`.
  - From the second keep `label_ in ("SPECIES", "ORGANISM")`.
- **Merge Overlaps**
  - Overlapping spans of the same type are merged to avoid duplicates.
- **Context Sentence**
  - For each entity, find the sentence (`doc_spec.sents`) covering its
    start position.
- **Clean & Filter**
  - Remove very short entities (`len < MIN_LEN`).
- **Return Results**
  - Each entity is stored as a dict:
    `{entity, type, start, end, sentence}`.
  - Results from both models are combined into one list.

## Extracted Entities (CSV)

The full list of extracted entities is saved at:  
[`data_output/entities_ner.csv`](data_output/entities_ner.csv) (and a
Parquet copy at
[`data_output/entities_ner.parquet`](data_output/entities_ner.parquet)).

> On GitHub, large CSVs may show a yellow banner. Use **Raw** to
> download.

## Explanation of fields

| Column | Meaning |
|:---|:---|
| Paper | `/work/eval_papers/PMID_29173234.pdf` → the source document (PDF). |
| entity | `Mouse` → the detected mention in text. |
| type | `SPECIES` → NER label from the `en_ner_bionlp13cg_md` model. |
| char_start | `496` → character offset where the entity begins. |
| char_end | `501` → character offset where it ends. |
| context_sentence | The full sentence containing the mention of the mouse, including context describing what happened to it (adiposity, insulin resistance, steatosis). |

## Example row

``` csv
/work/eval_papers/PMID_29173234.pdf,Mouse,SPECIES,496,501,"Male Balb/c mice fed with diets containing α‑lactalbumin developed abdominal adiposity, insulin resistance, and moderate liver steatosis."
```
