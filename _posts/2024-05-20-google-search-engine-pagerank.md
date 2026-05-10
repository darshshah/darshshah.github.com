---
layout: post
title: "The Birth of Google: Search Architecture and PageRank"
date: 2024-05-20
author: Darsh Shah
tags: [search-engines, pagerank, web-crawling, information-retrieval, google]
excerpt: "These two foundational papers introduce the Google search engine and its core ranking algorithm, PageRank. The first paper describes Google's system architecture -- how web pages are crawled, indexed, and stored -- while the second dives deep into the PageRank algorithm itself. Together, they laid the groundwork for what became the dominant search engine on the web."
---

*This post is adapted from a paper review I wrote during CMU's 18-845: Internet Services course, where we studied seminal papers in distributed systems, web architecture, and virtualization. I've converted my reviews into blog posts to share them more broadly.*

> **Paper [1]:** *"The Anatomy of a Large-Scale Hypertextual Web Search Engine"* by Sergey Brin and Larry Page (WWW Conference, 1998)
>
> **Paper [2]:** *"The PageRank Citation Ranking: Bringing Order to the Web"* by Lawrence Page, Sergey Brin, Rajeev Motwani, and Terry Winograd (1998)

## TL;DR

These two foundational papers introduce the Google search engine and its core ranking algorithm, PageRank. The first paper describes Google's system architecture -- how web pages are crawled, indexed, and stored -- while the second dives deep into the PageRank algorithm itself. Together, they laid the groundwork for what became the dominant search engine on the web.

## Summary

Both papers provide an introduction to the Google search engine and its underlying technologies. The first paper describes search trends of the time and argues why a new search engine was needed. The authors then give a detailed description of Google's system architecture and explain how web pages are crawled, indexed, and stored. The second paper extensively discusses the PageRank algorithm used within the Google search engine. Both papers demonstrate why PageRank is a superior algorithm by comparing it against the search engines prevalent at the time. The experimental setup and the sheer volume of data crawled and stored are impressive. Reading both papers feels like witnessing the beginnings of Google, as their idea went on to become the next big thing in the tech world.

## Strengths

- **Scalability as a first-class goal.** The first paper extensively emphasizes that scalability was a primary design goal even before the system was built. This is critical since the web is always growing.
- The diagrams in the second paper regarding PageRank calculation make the algorithm and scoring easy to understand.
- The evaluation and testing in both papers is thorough. The authors extensively benchmarked against existing technologies to demonstrate that their system outperforms the rest.

## Weaknesses

- The first paper covers many different subsystems but only provides an overview of each. More in-depth design details would have been helpful for appreciating the power of the system.
- BigFiles is mentioned in just a few lines. The authors should have provided more information about where it is used and what features it offers.
- There are numerous spelling mistakes in the papers (e.g., "exenral," "Th"). Additionally, the formatting felt more like an article than a formal research paper, as it did not use the standard two-column layout.

## Discussion Questions

- It would be interesting to see results from combining the URLResolver and Indexer into a single subsystem. Since the Indexer sends data to the URLResolver and both dump data into Barrels, could combining them lead to better performance?
- Does the Lexicon support different languages? How would the system perform as the number of Lexicon words increases?
- The papers do not mention the hardware setup used in experiments. Variations in RAM, CPU speed, and other specifications can significantly affect lookup speed. How would the in-memory hashmap compare with today's memory capabilities?
- It would have been interesting to see how query expansion would fit into their search algorithm. They discuss user personalization, but it would be valuable to understand how it integrates with their search model.
- What are the results of rank merging? Whether it improves precision or recall is an important question to answer.
- What is the initial dataset for PageRank -- from which web page does the algorithm start? How many iterations would it require to converge on today's web is a fascinating question.
