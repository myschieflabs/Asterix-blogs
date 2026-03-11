# Build a Resume Screening AI: A Simple Tutorial

Recruiters often review hundreds of resumes for a single job opening. Manually scanning each one is slow and inefficient, and strong candidates can easily be missed simply because the volume is too high. AI can assist by automatically analyzing resumes and highlighting the most relevant candidates.

In this tutorial, we show how students can build a **simple AI powered resume screening system**. The goal is not to create a production hiring platform, but to demonstrate how modern natural language processing techniques can compare resumes with job descriptions.

---

## What We Are Building

The system takes two inputs:

* a **job description**
* a collection of **candidate resumes**

Using language embeddings, the system measures how closely each resume matches the job description and produces a ranked list of candidates.

![AI pipeline diagram showing resumes and job description processed by a model that ranks candidates by similarity](https://images.unsplash.com/photo-1581092588429-4a5f9b8d87f5)

The workflow is simple:

1. Convert text into embeddings
2. Compare resumes with the job description
3. Rank candidates by similarity score

This allows the system to understand **semantic meaning**, not just keywords.

---

## Tools and Libraries

This tutorial uses Python and several commonly used libraries:

* **sentence-transformers** for semantic embeddings
* **scikit-learn** for similarity calculations
* **pdfminer.six** for extracting text from PDF resumes

Install the required dependencies:

```bash
pip install sentence-transformers scikit-learn pdfminer.six
```

These tools provide everything needed to build the basic screening system.

---

## Step 1: Prepare the Job Description

Start with a job description that defines the role and the required skills. This will act as the reference text for comparing resumes.

Example:

```python
job_description = """
We are looking for a Machine Learning Engineer with experience in Python,
data analysis, deep learning, and model deployment.
"""
```

The more clearly the role is described, the more meaningful the matching results will be.

---

## Step 2: Load an Embedding Model

Computers cannot interpret language directly. Instead, we convert text into **numerical vectors (embeddings)** that represent meaning.

Sentence Transformers provides pretrained models that do this efficiently.

```python
from sentence_transformers import SentenceTransformer

model = SentenceTransformer('all-MiniLM-L6-v2')
```

This model converts sentences into embeddings where similar meanings produce similar vectors.

---

## Step 3: Convert Text into Embeddings

Next, convert the job description and resume text into embeddings.

```python
job_embedding = model.encode(job_description)
resume_embedding = model.encode(resume_text)
```

Once converted into vectors, the texts can be compared mathematically.

---

## Step 4: Calculate Resume Similarity

To measure how well a resume matches the job description, we calculate cosine similarity.

```python
from sklearn.metrics.pairwise import cosine_similarity

similarity_score = cosine_similarity(
    [job_embedding],
    [resume_embedding]
)

print(similarity_score)
```

The score ranges between **0 and 1**.

* closer to **1** indicates a strong match
* closer to **0** indicates weaker relevance

---

## Step 5: Rank Multiple Resumes

To screen multiple candidates, compute similarity scores for each resume and sort the results.

```python
scores = []

for resume in resumes:
    embedding = model.encode(resume)
    score = cosine_similarity([job_embedding], [embedding])[0][0]
    scores.append((resume, score))

ranked_resumes = sorted(scores, key=lambda x: x[1], reverse=True)
```

Example output:

| Candidate | Match Score |
| --------- | ----------- |
| Resume A  | 0.91        |
| Resume B  | 0.82        |
| Resume C  | 0.67        |

Recruiters can review the highest ranked candidates first.

![Illustration of AI ranking resumes with similarity scores](https://images.unsplash.com/photo-1551288049-bebda4e38f71)

---

## Important Note: This Is a Learning Prototype

This tutorial demonstrates the **core concept behind AI resume screening**, but real world systems are more advanced.

The basic approach compares entire documents, which may miss details such as years of experience or specific skill requirements.

Production systems typically combine multiple techniques.

### Skill Extraction

AI models identify technologies such as Python, SQL, React, TensorFlow, or Kubernetes directly from resumes.

### Experience Detection

Natural language processing can extract structured information such as years of experience, certifications, and education.

### Section Parsing

Resumes can be divided into sections like skills, education, and work experience so that important sections receive more weight.

### Rule Based Filters

Recruitment platforms often apply rules such as:

* minimum years of experience
* required certifications
* mandatory skills

Combining these techniques with semantic similarity improves accuracy.

---

## Extending the Project

Students can expand this tutorial in several ways:

* build a **web interface using Streamlit or Flask**
* add **skill extraction using spaCy**
* store resume embeddings in a **vector database such as FAISS**
* implement filters for **experience level or education**

These improvements transform a simple prototype into a more complete AI application.

---

## Complete Working Example (Using PDF Resumes)

The following script reads **PDF resumes from a folder**, extracts their text, compares them with the job description, and ranks candidates.

![Example workflow showing PDF resumes being parsed and ranked by an AI system](https://images.unsplash.com/photo-1519389950473-47ba0277781c)

This example assumes that resumes are stored inside a folder called `resumes`.

```python
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
from pdfminer.high_level import extract_text
import os

# Load embedding model
model = SentenceTransformer('all-MiniLM-L6-v2')

# Job description
job_description = """
We are looking for a Machine Learning Engineer with experience in Python,
data analysis, deep learning, and model deployment.
"""

job_embedding = model.encode(job_description)

resume_folder = "resumes"
scores = []

for file in os.listdir(resume_folder):

    if file.endswith(".pdf"):

        path = os.path.join(resume_folder, file)

        # Extract text from PDF
        resume_text = extract_text(path)

        # Convert resume to embedding
        resume_embedding = model.encode(resume_text)

        # Calculate similarity
        score = cosine_similarity(
            [job_embedding],
            [resume_embedding]
        )[0][0]

        scores.append((file, score))

# Rank candidates
ranked_resumes = sorted(scores, key=lambda x: x[1], reverse=True)

print("\nCandidate Rankings:\n")

for name, score in ranked_resumes:
    print(f"{name}: {score:.3f}")
```

---

## How to Run the Example

1. Create a folder called **resumes**
2. Add several **PDF resumes** to that folder
3. Install dependencies

```bash
pip install sentence-transformers scikit-learn pdfminer.six
```

4. Run the script.

The program will output a ranked list of candidates based on how closely their resumes match the job description.

---

## Final Thoughts

Many modern AI systems rely on a simple but powerful concept. Language can be represented numerically and compared mathematically. This principle powers search engines, recommendation systems, and document retrieval tools.

A resume screening AI demonstrates how these ideas can be applied to a real world problem. Even a small prototype shows how AI can assist in tasks that traditionally require extensive manual effort.

For students exploring AI engineering, projects like this provide a practical introduction to **natural language processing, embeddings, and intelligent ranking systems**.
