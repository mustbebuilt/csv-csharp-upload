# Loading CSV Data in a C# Console Application

Welcome! This guide walks you step-by-step through creating a C# Console Application that reads, parses, and displays data from a CSV (Comma-Separated Values) file.

Whether you are using **Visual Studio** or **Visual Studio Code (VS Code)**, each guide includes dedicated instructions for your preferred development environment.

---

## Guide Contents

1. **[Step 1: Project Setup (Visual Studio & VS Code)](./01-project-setup.md)**
   - Installing prerequisites (.NET SDK, IDE extensions).
   - Creating a new C# Console Application in **Visual Studio**.
   - Creating a new C# Console Application in **VS Code** (using C# Dev Kit or the .NET CLI).

2. **[Step 2: Preparing the CSV File & Data Model](./02-preparing-csv-data.md)**
   - Organising data files in a dedicated `data/` folder (`data/products.csv`).
   - Ensuring the CSV file is copied to the build output directory in **Visual Studio** and **VS Code**.
   - Creating a C# `Product` class to represent each row.

3. **[Step 3: Reading CSV Data Using Built-in .NET (`System.IO`)](./03-reading-csv-native.md)**
   - Reading lines with `File.ReadAllLines` and `StreamReader`.
   - Splitting columns and safely parsing data types (`int`, `decimal`).
   - Displaying formatted results in the console.

4. **[Step 4: Reading CSV Data Using `CsvHelper` (Recommended for Production)](./04-reading-csv-csvhelper.md)**
   - Installing the `CsvHelper` NuGet package in **Visual Studio** and **VS Code**.
   - Automatically mapping CSV columns to C# objects.
   - Handling quoted fields, commas inside values, and custom headers.

5. **[Step 5: Troubleshooting & Best Practices](./05-troubleshooting-and-best-practices.md)**
   - Fixing `FileNotFoundException` and working directory issues.
   - Handling missing values, bad rows, and cultural number/date formats.

---

## Prerequisites

Before starting, make sure you have the following installed:

- **.NET SDK (10.0 recommended)**: [Download .NET](https://dotnet.microsoft.com/download)
- **Choose your editor**:
  - **Visual Studio** (Community, Professional, or Enterprise) with the **.NET desktop development** workload installed.
  - **Visual Studio Code** with the **[C# Dev Kit](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)** extension installed.

---

## Quick Start

Begin with **[Step 1: Project Setup](./01-project-setup.md)** to create your project.

