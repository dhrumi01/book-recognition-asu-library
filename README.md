# Book Recognition and Retrieval System — ASU Library

> **Snap a photo of a book cover. Get the title, author, availability, and shelf location. Instantly.**

**Team:** Dhrumi Kansara, Devanshi Mehadia, Leticia Iglesias, Satyajith Turlapati, Qinxin Zhang
**Course:** ASU CIS 515 — Arizona State University
**Tools:** Python, EasyOCR, BERT, ResNet-50, PyTorch, Google Colab

---

## The Problem

You are in the ASU library. You recognize a book by its cover but cannot remember the exact title or author. The catalog search needs precise text. You are stuck.

This happens constantly. Students give up. Staff get interrupted. Resources go unused.

We built a system that fixes it.

---

## It Actually Works

Here is a real output from our pipeline:

```
Match Found:
Title:        Big Book of Spelling Tests
Author:       Orin Hargraves
Category:     Reference
Availability: Available
Location:     Noble Library
```

Photo of a book cover goes in. Title, author, availability, and shelf location come out.

---

## How the Pipeline Works

```
Book Cover Photo
       |
  EasyOCR — reads text from the cover image
       |
  ResNet-50 — extracts visual feature vector (2048 dimensions)
       |
  BERT — converts OCR text into semantic embeddings (768 dimensions)
       |
  Cosine Similarity — compares query against ASU library database
       |
  Best Match — title, author, availability, library location
```

Three models. Each handling what it is best at.

**EasyOCR** reads the text on the cover — title fragments, author names, publisher marks. It handles partial and noisy text reasonably well.

**ResNet-50** (pretrained on ImageNet) extracts visual features from the cover image. Two visually similar covers produce similar vectors. Used as a feature extractor without fine-tuning.

**BERT** (bert-base-uncased) converts the OCR text into semantic embeddings. This goes beyond keyword matching — it understands meaning, which matters when OCR extracts partial or slightly garbled text.

**Hybrid matching** combines visual similarity and text similarity scores, then returns the highest-confidence match from the ASU library catalog.

---

## Why Hybrid Matching

We tested text-only and image-only approaches first.

Text-only failed on covers with stylized fonts, partial text, and decorative layouts. Image-only failed when different editions of the same book had completely different cover designs.

Combining both improved accuracy significantly — especially on the edge cases that broke each individual model.

---

## Honest Challenges

**OCR on stylized text is hard.** Book covers use decorative fonts, overlapping elements, and unusual layouts. We cleaned extracted text with regex before passing it to BERT but some covers still produced poor OCR results.

**ResNet was not trained on book covers.** It was pretrained on natural images. Fine-tuning on a dedicated book cover dataset would meaningfully improve visual matching accuracy.

**BERT bias.** Pre-trained language models can reflect biases from training data. For a university library serving a diverse student body, that is worth monitoring and addressing.

**Privacy.** If the system ever processes images of students in a live deployment, on-device OCR would eliminate the need to send images to a server at all.

---

## What I Worked On

My role focused on integrating the end-to-end demo pipeline and mapping model outputs to actionable library responses — translating raw cosine similarity scores into availability lookups, location data, and user-facing results.

---

## What We Would Build Next

- Fine-tune ResNet on a book cover specific dataset for better visual matching
- Add a mobile front end the backend is ready, it just needs a UI
- Explore federated or on-device OCR to address privacy concerns at scale
- Expand to any library system via API integration, not just ASU

---

## Files

| File | Description |
|------|-------------|
| `CIS_515.ipynb` | Full pipeline notebook OCR extraction, ResNet visual features, BERT text embeddings, cosine similarity matching, and live demo |

---

## How to Run

1. Open in Google Colab
2. Mount Google Drive and update IMAGE_DIR and ASU_CSV paths to your dataset
3. Run cells in order — all models download automatically
4. Update query_img path in the final cell to test your own book cover photo

---

Built at Arizona State University CIS 515
Dhrumi Kansara — linkedin.com/in/dhrumikansara — github.com/dhrumi01
