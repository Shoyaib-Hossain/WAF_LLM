# LLMs as Web Application Firewalls (WAF): SQLi, XSS, SSRF

## Overview

This repository contains an experimental evaluation of whether modern large language models (LLMs) can function as a firewall-like decision layer for web applications by classifying real-world malicious payloads and malicious look alike payloads. The experiment focuses on three common vulnerability categories:

**SQL Injection (SQLi),**
**Cross-Site Scripting (XSS) and**
**Server-Side Request Forgery (SSRF)**

A standardized, structured prompt is used to evaluate three state-of-the-art models:

**ChatGPT-5.2 Thinking,** (OpenAI)
**Gemini 3 Pro** (Google) and
**Claude Sonnet 4.5** (Anthropic)

The goal is to measure how reliably LLMs can detect malicious intent and executability, and whether they can complement or (in some settings) serve as alternatives to traditional Web Application Firewalls (WAFs).

---

## Classification Task

Each payload is classified across **two dimensions**:

1. **Attack resemblance**: Does the input resemble a known attack vector (malicious in form)?
2. **Syntactic validity / executability**: Is it syntactically valid for the relevant execution context (e.g., SQL that could run if referenced objects exist)?

These dimensions produce one of three **mutually exclusive labels**:

**Malicious-Unsafe**
  Resembles a known attack vector and is syntactically valid / executable.

**MaliciousIntended-but-safe**
  Resembles a known attack vector but contains syntax errors, misspellings, incomplete constructs, or context issues that would prevent execution.

**Safe**
  Does not resemble a known attack vector.

---
**Ground Truth Assumption**

For the malicious datasets in this experiment, the ground truth labels all samples as either Malicious-Unsafe or MaliciousIntended-but-safe or Safe.

That means:

Any model output that deviates from the ground-truth label i.e., classifying a sample as MaliciousIntended-but-safe or Safe when the ground truth is Malicious-Unsafe, or vice versa is interpreted as model disagreement, conservative judgment, or a limitation in semantic threat interpretation.

This framing helps highlight where LLM reasoning is strong (e.g., recognizing attack intent) and where it may be inconsistent (e.g., executability judgments, context sensitivity, or over-refusals).

---

## Method Summary

This experiment is designed to be reproducible and comparable across models:

**Structured prompts** ensure consistent classification rules and
**Same payload sets** are used across models for each vulnerability category

---

## Notes on CSV Double-Quote Escaping

Some payloads include literal double-quote characters (`"`), especially in HTML/XML attributes. When these strings are stored in **CSV**, you may see doubled quotes like:

`xmlns=""http://www.w3.org/1999/xhtml""`

This is normal CSV escaping:

CSV fields containing special characters are often wrapped in quotes:
  `" ... "`

If the content itself contains a double-quote (`"`), CSV escapes it by doubling it:
  `"` → `""`

So a value that logically contains:

`<script xmlns="http://www.w3.org/1999/xhtml">`

will appear CSV-encoded as:

`<script xmlns=""http://www.w3.org/1999/xhtml"">`

This does not change the underlying value; most CSV readers will automatically decode it back to normal quotes when parsed. If you want to avoid this representation for human viewing, consider exporting in TSV or JSON instead of CSV.

