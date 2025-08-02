
# 🧬 Disease Ontology Enrichment Analysis

This repository contains an R workflow to identify **Disease Ontology (DO)** associations for a given list of hub genes using the `clusterProfiler` and `DOSE` packages.

---

## 📌 Project Overview

This analysis:

- Maps hub gene symbols to **Entrez IDs**
- Performs Disease Ontology enrichment
- Replaces Entrez IDs with gene symbols for interpretability
- Exports enrichment results to Excel
- Visualizes gene–disease relationships with a simple barplot

---

## 🛠️ Requirements

- R (≥ 4.1 recommended)
- R packages:
  ```r
  BiocManager::install(c("DOSE", "org.Hs.eg.db", "clusterProfiler", "openxlsx"))
  install.packages(c("ggplot2", "tidyr", "readxl"))
````

---

## 📂 Input

* A list of hub genes:

  ```r
  gene_symbols <- c("HGS","PRKDC","MET","PRKCZ","PTK2","PRPF6","CDK8")
  ```

You can modify this list in the R script.

---

## ▶️ Workflow Steps

1. **Map gene symbols to Entrez IDs**

   ```r
   gene_list <- bitr(gene_symbols, fromType = "SYMBOL", toType = "ENTREZID", OrgDb = org.Hs.eg.db)$ENTREZID
   ```

2. **Disease Ontology enrichment**

   ```r
   do_enrich <- enrichDO(gene = gene_list, ont = "DO", pvalueCutoff=0.05)
   ```

3. **Replace Entrez IDs with gene symbols in results**

   ```r
   do_enrich@result$geneID <- sapply(do_enrich@result$geneID, function(ids) {
     symbols <- id_to_symbol$SYMBOL[id_to_symbol$ENTREZID %in% strsplit(ids, "/")[[1]]]
     paste(symbols, collapse = "/")
   })
   ```

4. **Export enrichment table to Excel**

   ```r
   write.xlsx(do_enrich, file = "disease_enrichment.xlsx")
   ```

5. **Read back and reshape for visualization**

   ```r
   do_enrich_filtered <- read_excel("disease_enrichment.xlsx") %>%
     separate_rows(geneID, sep = "/")
   ```

6. **Plot**

   ```r
   ggplot(df_split, aes(y = Description, fill = geneID)) +
     geom_bar() +
     theme_minimal() +
     labs(title = "Disease associations of the hub genes", y = "", x = "Gene")
   ```

---

## 📊 Output

* **disease\_enrichment.xlsx**: the enrichment results
* **Bar plot** showing hub gene–disease relationships

---




If you’d like, I can also help you build the `.gitignore`, a `LICENSE`, or a sample `.R` file structure to upload with this to GitHub — just let me know! 🚀
```
