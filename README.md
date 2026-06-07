# 2026 Spring Tsinghua Quantum Computing Seminar

This repository contains course-related materials for the 2026 Spring offering of **Topics in Quantum Computing / 量子计算研讨课** at Tsinghua University.

## Contents

| Directory | Description |
|---|---|
| `Lectures/` | Lecture slides and course handouts. |
| `Homework/` | Homework statements and selected submissions. |
| `FinalProject/` | Final project materials, including the SABRE presentation and report. |
| `Quantum_Project/` | External course project repository by `zhaochenyang20`, included as a Git submodule. |

## Final Project

The final project focuses on the paper:

> Gushu Li, Yufei Ding, Yuan Xie. *Tackling the Qubit Mapping Problem for NISQ-Era Quantum Devices*. arXiv:1809.02573, 2018.

Related files are under `FinalProject/`, including:

- `SABRE_Final_Presentation.pptx`
- `SABRE_qubit_mapping_report.md`
- `SABRE_qubit_mapping_report.pdf`
- supporting figures under `FinalProject/report_assets/`

## External Project

`Quantum_Project/` is intentionally tracked as a submodule so that opening it on GitHub points to the original repository:

<https://github.com/zhaochenyang20/Quantum_Project>

To clone this repository with submodules:

```bash
git clone --recurse-submodules <repo-url>
```

If the repository has already been cloned:

```bash
git submodule update --init --recursive
```

## Privacy Note

Student names may appear where they are part of submitted coursework or author information. Student ID numbers are intentionally omitted from this repository.

## Disclaimer

This is a personal course-material repository and is not an official course website.
