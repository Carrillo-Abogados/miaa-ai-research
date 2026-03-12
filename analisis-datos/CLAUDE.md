# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## About This Repository

This is a course materials repository for **Fundamentos de Análisis de Datos I (Análisis Exploratorio de Datos)** at Universidad ICESI, taught by Prof. Jose Armando Ordoñez Cordoba. It belongs to the Maestría en Ciencia de Datos / Maestría en IA Aplicada / Ingeniería de Datos program (period 2026-1).

The course follows the **CRISP-DM** framework (Cross-Industry Standard Process for Data Mining) and covers the full data project lifecycle: business understanding, data understanding, data preparation, modeling, evaluation, and deployment.

## Repository Structure

- **Jupyter notebooks** (`.ipynb`): Course labs and exercises, designed for Google Colab
  - `5. Taller_1_vino.ipynb` — Full EDA workflow on sklearn wine dataset (univariate, bivariate analysis, KNN classification)
  - `12. 2.2.1-carga-dataset-parquet.ipynb` — Loading datasets in parquet format (large notebook)
  - `14. 2.3-merge_join-datasets.ipynb` — Pandas merge/join operations tutorial
  - `15. 2-quizz-ejercicios-de-merge.ipynb` — Merge exercises using US states data (has "Completar" placeholders for students)
- **PDFs**: Lecture slides, readings, and reference documents
- **Datasets**: `SABERTYT20162.csv` (ICFES Saber TyT), `Dataset Riesgo Cardiovascular.xlsx`
- **`Documentación/`**: Supplementary session slides and readings
- **`Grabaciones de Clase/`**: Class recordings (`.mp4`)
- **`eda_couse-main.zip`**: Source repository from `armandoordonez/eda_couse` on GitHub

## Tech Stack

- **Language**: Python 3
- **Environment**: Google Colab (notebooks have Colab badges/links)
- **Core libraries**: pandas, numpy, matplotlib, seaborn, scikit-learn
- **File formats**: CSV, XLSX, Parquet, PDF

## Course Units

1. **Ciclo de vida de proyectos de datos e IA** — CRISP-DM, business problem formulation, analytics types (descriptive, diagnostic, predictive, prescriptive)
2. **Introducción a ingeniería de datos** — Data types, sources, ingestion, integration, data architectures (warehouse, lake, lakehouse)
3. **Análisis exploratorio de datos** — Descriptive statistics, visualization, anomalies/outliers, missing values, feature engineering

## Evaluation

- Talleres (workshops): 40%
- Proyecto final: 60%

## AI Usage Policy

The course uses **IAG Nivel 4: IAG Completa** — full use of generative AI is permitted. Each activity must include an 800-word technical/creative report explaining how AI tools were used and the ethical, stylistic, and technical decisions made.

## Conventions When Working with Notebooks

- Notebooks are in Spanish; keep comments and markdown in Spanish
- Some notebooks contain `"Completar"` placeholders — these are student exercises; do not fill them in unless explicitly asked
- Notebooks are designed for Colab; use `!pip install` for dependencies not in Colab's default environment
- EDA pattern: general understanding → preprocessing → univariate analysis → bivariate analysis → modeling
