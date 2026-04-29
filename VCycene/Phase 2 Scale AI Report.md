### **Step-by-Step: What the Pipeline Does**

1. **Ingest Raw Data**
    - Takes images from multiple sources (VCycene’s own dataset + external sets).
    - Brings them into a single, consistent system.
2. **Metadata & Protocol Enforcement**
    - Every image gets a **metadata record** (ID, site, date, contamination flag/type).
    - Ensures data follows a **standard collection protocol** so future images are consistent.
3. **Taxonomy Mapping**
    - Reorganizes all images into the new **26-class taxonomy**:
        - 20 compost-relevant categories (banana peel, eggshell, tea bag, etc.).
        - 5 contamination categories (plastic, paper, metal, glass/ceramic, other).
        - 1 background class.
4. **Annotation Transformation**
    - Converts legacy semantic masks (old PNG masks) into **YOLO polygon format**.
    - Runs scripts to check that masks, classes, and images match correctly.
5. **Standardization**
    - Renames files into a universal format (`YYYYMMDD_siteID_sampleID_class.jpg`).
    - Reorganizes directories into train/validation/test splits.
    - Splits contamination vs. organic waste into separate branches for flexible experiments.
6. **Gold Set Handling**
    - Builds and maintains the **Gold Set** of 1,000+ human-labeled, biologically validated images.
    - Ensures this set has a fixed 80/10/10 split for benchmarking.
7. **Semi-Automated Annotation (SAM)**
    - Runs **Segment Anything Model (SAM)** to auto-generate masks for new/unlabeled images.
    - Humans then review and correct these masks.
    - Cuts down annotation time by ~70–80%.
8. **Data Integrity Checks**
    - Validates that every image has exactly one mask file.
    - Checks class labels against the taxonomy.
    - Ensures polygons are valid (no broken shapes).
9. **Output: Training-Ready Data**
    - At the end, you get a **clean, harmonized, biologically realistic dataset** in YOLO format, ready to plug into training scripts.
    - Because everything is scripted and version-controlled, the process is **repeatable and auditable**.

---

### **In Short**

The pipeline **turns messy, inconsistent raw food waste images into a standardized, biologically valid, YOLO-compatible dataset** — automatically and reproducibly.

This means:
- Every future training run will start with **consistent, high-quality data**.
- Expanding the dataset is scalable (thanks to SAM + metadata structure).
- Training results are reproducible and auditable — critical for commercialization and regulatory approval.