# Code Examples for Software Development I Course

## Introduction

This repository contains code examples and documentation you will see during practice lectures (2026 Fall).

All examples are provided as-is and are intended only for learning purposes. Some examples are designed to display "bad code" or "bad practices", while others might be correct but provide overly simplified solutions. 

⚠️ **Important**: **Never** use the provided examples as production-ready code, as they are not intended for production use.

## Repository Structure

This repository contains a separate folder for each practice lecture (e.g., "Lecture01" for the first lecture).

Each practice lecture folder contains one or more subfolders for the topics that were discussed during that session.

## Getting Started

### File Formats & Tools

#### Quarto Files (*.qmd)
Many examples are provided as `*.qmd` files - [Quarto](https://quarto.org/) documents (plain markdown with executable code blocks). Polyglot Notebooks (`*.dib`/`*.ipynb`) were deprecated by Microsoft in 2026, so examples previously authored as notebooks now use this format instead.

**Requirements**: 
- [Quarto CLI](https://quarto.org/docs/get-started/) - renders pages and slides
- Visual Studio Code with the [Quarto extension](https://marketplace.visualstudio.com/items?itemName=quarto.quarto)

Live-reloading preview of a `.qmd` file while editing:

```
quarto preview <file>.qmd
```

#### LINQ Files (*.linq)
LINQ examples are provided as `*.linq` files for use with LINQPad.

**Requirements**: 
- [LINQPad tool](https://www.linqpad.net/Download.aspx) - for creating, testing, and running LINQ queries

#### HTTP Request Files (*.http)
Some lectures include `*.http` files - plain-text HTTP request definitions (`###`-separated requests) used to test REST APIs directly, without typing curl commands or clicking through a separate app by hand.

**Requirements**:
- Visual Studio Code with the [REST Client extension](https://marketplace.visualstudio.com/items?itemName=humao.rest-client) - adds a "Send Request" link above each request in a `.http` file; the response opens in a side panel.
- Visual Studio 2022 (17.6+) and JetBrains Rider both have built-in equivalents (Visual Studio's own `.http` editor with Endpoints Explorer; Rider's bundled HTTP Client), so no extension is required with either.

**Alternative**: [Postman](https://www.postman.com/downloads/) is a popular standalone GUI app for testing APIs - request collections, environments, and a full request-builder interface, at the cost of a separate app instead of living in your editor next to the code. `.http` files stay in plain text and version-controlled alongside your project; if your team prefers Postman instead, its collections can be exported/imported to share with teammates the same way.

#### Visual Studio Solutions (*.sln)
All code examples provided as Visual Studio solutions were tested and compiled using **Visual Studio 2022** with the following workloads:

![VS Workloads](images/vs_install.png)

**Alternative**: All examples should work with **Visual Studio Code** if you install:
- [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
- [C# Dev Kit extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)

The examples can also be opened and run using [JetBrains Rider](https://www.jetbrains.com/rider/).

## Project Ideas

For team project inspiration, see [PROGRAMS.md](Lecture00/PROGRAMS.md) which contains project ideas and information about the current and previous year's themes.

## Theory Lecture Materials

📖 **Theory Slides**: Access slides from theory lectures at [software-engineering repository](https://github.com/smagurauskas/software-engineering/tree/main)

## Demo Project

🚀 **Live Demonstrations**: Follow along with language features and development practices using the [PSI2026-Playground repository](https://github.com/niku-live/teaching-university-psi-2026-playground)

This repository contains code examples that will be used during lectures to demonstrate various programming concepts, language features, and development techniques.

## Lectures

- [Lecture 00](Lecture00/README.md) - Course Introduction, Teams & Evaluation Process
- [Lecture 01](Lecture01/README.md) - Reviewing Theory Lecture 1 (.NET/C# Fundamentals & Pull Requests), Turning Your Template Into a Product
- [Lecture 02](Lecture02/README.md) - Reviewing Theory Lecture 2 (Web Services & ASP.NET APIs), Continuing StudySpot
- [Lecture 03](Lecture03/README.md) - Reviewing Theory Lecture 3 (Web UI & Agile), Validating StudySpot
