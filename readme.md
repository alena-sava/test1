This script does entity extraction pipeline

###  Entity Extraction Pipeline

-   **Load Models**
    -   `en_ner_bc5cdr_md`: pretrained NER model for **CHEMICAL**
        entities.
    -   `en_ner_bionlp13cg_md`: pretrained NER model for **SPECIES /
        ORGANISM** entities.
    -   Both are spaCy pipelines; only tokenization + `ner` are enabled
        for speed.
-   **Process Text**
    -   Each row's text is passed through both models → produces two
        `Doc` objects with `doc.ents`.
-   **Filter Entities**
    -   From the first model keep `label_ == "CHEMICAL"`.
    -   From the second keep `label_ in ("SPECIES", "ORGANISM")`.
-   **Merge Overlaps**
    -   Overlapping spans of the same type are merged to avoid
        duplicates.
-   **Context Sentence**
    -   For each entity, find the sentence (`doc_spec.sents`) covering
        its start position.
-   **Clean & Filter**
    -   Remove very short entities (`len < MIN_LEN`).
-   **Return Results**
    -   Each entity is stored as a dict:
        `{entity, type, start, end, sentence}`.
    -   Results from both models are combined into one list.

The results are saved here in your repo:

-data_output/entities_ner.csv (and a Parquet copy at data_output/entities_ner.parquet).

### File structure (CSV schema)

Each row is one extracted entity with its span and context:

-Paper – paper/document ID.

-entity – the extracted string (chemical or species).

-type – CHEMICAL or SPECIES.

-char_start – start character offset in the source text.

-char_end – end character offset (exclusive).

-context_sentence – the sentence that contains the entity.

### Example output


`Paper,entity,type,char_start,char_end,context_sentence``				
/work/eval_papers/PMID_29173234.pdf,non-esterified fatty acids,CHEMICAL,433,459,"- **Stressor Snippet:** The study reported that dietary Î±-lactalbumin intake induced hepatic steatosis, with increased triglycerides (TAG) and non-esterified fatty acids (NEFA) in the liver."				
`