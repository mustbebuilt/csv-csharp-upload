# Step 1: Project Setup (Visual Studio & VS Code)

In this step, you will create a new C# Console Application named `CsvLoaderApp`. Follow the instructions below for either **Visual Studio** or **Visual Studio Code (VS Code)**.

---

## Option A: Using Visual Studio (Windows)

### 1. Verify Prerequisites
1. Open **Visual Studio Installer**.
2. Ensure **Visual Studio** is installed with the **.NET desktop development** workload ticked and the **.NET 10.0 Runtime/SDK** included.

### 2. Create a New Console Project
1. Launch **Visual Studio**.
2. On the start window, click **Create a new project**.
3. In the search box at the top, type **Console** and filter by:
   - **Language**: `C#`
   - **Platform**: `All platforms`
   - **Project type**: `Console`
4. Select **Console App** (make sure it says *A project for creating a command-line application that can run on .NET on Windows, Linux and macOS*, and **not** *Console App (.NET Framework)*).
5. Click **Next**.

### 3. Configure Your Project
1. **Project name**: Enter `CsvLoaderApp`.
2. **Location**: Choose a folder on your computer where you want to save the project.
3. **Solution name**: Leave as `CsvLoaderApp`.
4. Click **Next**.

### 4. Additional Information
1. **Framework**: Select **.NET 10.0 (Long Term Support)** (or the latest available .NET version).
2. **Do not use top-level statements**: You can leave this **unticked** (uses modern top-level statements) or **ticked** (uses the classic `Program` class and `Main` method). *Note: Our code examples show full class definitions so they work seamlessly in both modes.*
3. Click **Create**.

### 5. Run the Starter Project
1. Press **F5** (or click the green **Run / CsvLoaderApp** button in the top toolbar).
2. Verify that a console window opens and displays `Hello, World!`.

---

## Option B: Using Visual Studio Code (Windows, macOS, Linux)

With VS Code, you can create the project either using the **C# Dev Kit GUI** or the **Integrated Terminal (.NET CLI)**.

### 1. Verify Prerequisites
1. Open **VS Code**.
2. Go to the **Extensions** view (`Ctrl+Shift+X` on Windows/Linux, `Cmd+Shift+X` on macOS).
3. Search for and install **C# Dev Kit** (published by Microsoft).
4. Verify the .NET SDK is installed by opening the VS Code Terminal (`Ctrl+\`` or **Terminal > New Terminal**) and running:
   ```bash
   dotnet --version
   ```
   *(If the command is not found, download and install the [.NET SDK](https://dotnet.microsoft.com/download) and restart VS Code.)*

---

### Method 1: Create Project Using the Command Palette (C# Dev Kit)
1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux, `Cmd+Shift+P` on macOS).
2. Type and select **.NET: New Project...**.
3. Select **Console App** from the list of templates.
4. Choose the parent folder where your project folder should be created.
5. Type `CsvLoaderApp` as the project name and press **Enter**.
6. Select **.sln** or **.slnx** if prompted for solution file format, then click **Create Project**.
7. When prompted, click **Open** to open the new project folder in VS Code.

---

### Method 2: Create Project Using the Terminal (.NET CLI)
1. Open a terminal and navigate to the directory where you want to create your project:
   ```bash
   mkdir CsvLoaderApp
   cd CsvLoaderApp
   ```
2. Run the `dotnet new` command to scaffold a .NET 10 C# Console Application:
   ```bash
   dotnet new console --framework net10.0
   ```
3. Open the folder in VS Code:
   ```bash
   code .
   ```

---

### Run the Starter Project in VS Code
1. Open the integrated terminal in VS Code (`Ctrl+\``).
2. Run the project using the CLI:
   ```bash
   dotnet run
   ```
   *(Alternatively, open `Program.cs` and press **F5** or click **Run and Debug** in the sidebar).*
3. Verify that the terminal prints `Hello, World!`.

---

## Understand Your Project Files

Regardless of whether you used Visual Studio or VS Code, your project folder now contains two key files:

- **`CsvLoaderApp.csproj`**: The XML project configuration file that tracks target .NET version, included files, and NuGet packages.
- **`Program.cs`**: The main C# source code entry point where your application starts.

---

**Next Step:** **[Step 2: Preparing the CSV File & Data Model](./02-preparing-csv-data.md)**

