# 🤖 Autonomous Job Application AI Agent (Goal → Plan → Act)

## 📌 Project Description

This project implements an **Autonomous Job Application AI Agent** that mimics a real-world agent operating with a **goal-oriented workflow**: it plans steps, acts using external tools, and decides based on results. Unlike a chatbot, this agent **executes tasks autonomously**, from searching jobs to analyzing JDs, matching CVs, generating cover letters, and making decisions to apply or skip.

The system leverages **LangGraph** for stateful orchestration, conditional branching for decision-making, and external tools/APIs to interact with job platforms and documents.

---

## 🎯 Problem Statement

Job hunting is repetitive, time-consuming, and requires context-aware decision-making:

* Searching jobs across platforms
* Reading and understanding JDs
* Matching CV to JD requirements
* Writing personalized cover letters
* Deciding which jobs to apply for

Existing tools are static or manual; this project automates the **entire goal-driven process**.

---

## 💡 Solution Overview

The **Autonomous Job Application Agent** works in a **Goal → Plan → Act** cycle:

### 1️⃣ Goal

* Apply for AI jobs aligned with user preferences

### 2️⃣ Plan

* Search relevant job postings
* Extract key information from JD (skills, experience, location, salary)
* Match CV skills to JD requirements
* Decide if the job is a good fit

### 3️⃣ Act

* Generate a customized cover letter
* Apply (or skip)
* Log decisions for memory and future reference

---

## 🏗️ System Architecture

```
User CV & Preferences
        ↓
      Goal
        ↓
    LangGraph Planner
        ↓
Conditional Branching / Tools
    ├── Job Search Tool
    ├── JD Analyzer Tool
    ├── CV Matcher Tool
    ├── Cover Letter Generator Tool
        ↓
Decision: Apply / Skip
        ↓
Memory Logging & Reporting
```

**Key Concepts:**

* **LangGraph:** Orchestrates tasks as nodes in a graph; handles conditional branching.
* **Tools:** Specialized LLM functions or external APIs for each sub-task.
* **Decision Memory:** Logs why a job was applied or skipped for future reasoning.

---

## 🧠 Code Explanation (Step by Step)

### 1️⃣ Define Tools / Functions

```python
# Job search
def search_jobs(query):
    # Placeholder: could integrate LinkedIn API or scraping
    return ["AI Research Engineer - Company X", "ML Engineer - Company Y"]

# JD Analyzer
def analyze_jd(job_posting):
    # Extract key requirements
    return {"skills": ["Python", "PyTorch", "NLP"], "level": "Mid-Senior"}

# CV Matcher
def match_cv(cv, jd_requirements):
    # Compare CV skills to JD skills
    matched_skills = set(cv["skills"]).intersection(set(jd_requirements["skills"]))
    score = len(matched_skills)/len(jd_requirements["skills"])
    return score

# Cover letter generator
def generate_cover_letter(cv, jd):
    return f"Dear Hiring Manager, I am excited to apply for {jd['title']}..."
```

---

### 2️⃣ LangGraph Nodes / Planner

```python
from langgraph import Graph, Node

# Define nodes
search_node = Node(name="Search Jobs", func=search_jobs)
analyze_node = Node(name="Analyze JD", func=analyze_jd)
match_node = Node(name="Match CV", func=match_cv)
cover_letter_node = Node(name="Generate Cover Letter", func=generate_cover_letter)

# Graph
agent_graph = Graph()
agent_graph.add_nodes([search_node, analyze_node, match_node, cover_letter_node])

# Define edges / plan flow
search_node.add_edge(analyze_node)
analyze_node.add_edge(match_node)
match_node.add_edge(cover_letter_node)
```

---

### 3️⃣ Conditional Branching (Decide Apply / Skip)

```python
def decide_apply(match_score, threshold=0.6):
    return "Apply" if match_score >= threshold else "Skip"
```

---

### 4️⃣ Agent Execution

```python
cv_data = {"skills": ["Python", "PyTorch", "NLP", "Deep Learning"]}
job_query = "AI jobs"

# Plan + Act
jobs = search_node.execute(job_query)
for job in jobs:
    jd = analyze_node.execute(job)
    score = match_node.execute(cv_data, jd)
    decision = decide_apply(score)
    if decision == "Apply":
        letter = cover_letter_node.execute(cv_data, jd)
        print(f"Applying to {job} with letter:\n{letter}\n")
    else:
        print(f"Skipping {job} due to low match score\n")
```

---

## 🧰 Tech Stack

* **LangChain / LangGraph:** Orchestrating goal → plan → act nodes
* **LLM:** Ollama / Llama 3
* **Tools:** Python functions for job search, CV matching, cover letter generation
* **Memory:** Optional ChromaDB for storing decisions
* **Environment:** Google Colab / Local Python

---

### Autonomous Job Application AI Agent

A **goal-oriented autonomous agent** that executes the full job application workflow:

* Searches AI job postings
* Analyzes Job Descriptions
* Matches user CV to requirements
* Generates personalized cover letters
* Decides to apply or skip based on suitability

**Key Features:**

* Goal → Plan → Act architecture
* LangGraph orchestration with conditional branching
* Tool-driven execution
* Decision logging and memory for future reasoning

**Use Cases:**

* Automate AI job applications
* Personalized job recommendation system
* Research on autonomous LLM agents

---

## 🚀 Future Improvements

* Integrate real job search APIs (LinkedIn, Indeed)
* Add feedback loop to improve match threshold
* Persistent memory of past applications
* Multi-agent collaboration to diversify applications
