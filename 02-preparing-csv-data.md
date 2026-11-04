# Step 2: Preparing the CSV File & Data Model

Before writing code to load CSV data, you need:
1. A dedicated **`data/`** folder containing a sample CSV file (`data/products.csv`).
2. Project configuration so the CSV file is available when the program runs.
3. A C# class (`Product.cs`) to hold each row of data in memory.

---

## 1. Create a `data` Folder and the Sample CSV File

Keeping data files inside a dedicated **`data/`** folder keeps your project root clean and organised, especially as you add more datasets.

### In Visual Studio:
1. In **Solution Explorer**, right-click the **CsvLoaderApp** project -> **Add** -> **New Folder**, and name it `data`.
2. Right-click the new **`data`** folder -> **Add** -> **New Item...** (`Ctrl+Shift+A`).
3. Select **Text File** (under *General*).
4. Name it `products.csv` and click **Add**.

### In VS Code:
1. In the **Explorer** sidebar, click the **New Folder** icon next to `CSVLOADERAPP`, type `data`, and press **Enter**.
2. Right-click the **`data`** folder (or click the **New File** icon while `data` is selected), type `products.csv`, and press **Enter**.

### Paste the Sample CSV Content:
Copy and paste the following lines into `data/products.csv` and save the file:

```csv
Id,Name,Category,Price,StockQuantity
101,Wireless Mouse,Accessories,24.99,150
102,Mechanical Keyboard,Accessories,89.50,85
103,27-inch 4K Monitor,Displays,349.00,40
104,USB-C Docking Station,Accessories,129.99,60
105,Noise-Cancelling Headphones,Audio,199.95,30
```

> **Note:** Make sure there are no trailing blank lines at the end of the file, and save the file using UTF-8 encoding (the default in both Visual Studio and VS Code).

---

## 2. Copy the CSV File to the Output Directory

When you build and run a C# project, the compiled executable runs from a build output folder (such as `bin/Debug/net10.0/`). By default, Visual Studio runs the app from that `bin/Debug/net10.0/` folder, so if `data/products.csv` is not copied there, your app will throw a `FileNotFoundException`.

Configure your project to automatically copy `data/products.csv` to the output directory whenever you build:

### Option A: In Visual Studio
1. In **Solution Explorer**, expand the **`data`** folder and click on **`products.csv`**.
2. Look at the **Properties** window (press **F4** or right-click `products.csv` and select **Properties**).
3. Find the **Copy to Output Directory** property.
4. Change it from `Do not copy` to **`Copy if newer`** (or `Copy always`).

### Option B: In VS Code (or manually editing `.csproj`)
1. Open **`CsvLoaderApp.csproj`** in the editor.
2. Add an `<ItemGroup>` block inside the `<Project>` tags so your `.csproj` looks like this:

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
  </PropertyGroup>

  <ItemGroup>
    <None Update="data\products.csv">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>

</Project>
```

3. Save `CsvLoaderApp.csproj`. *(Note: Visual Studio's Properties window actually makes this exact change behind the scenes! MSBuild automatically normalises `data\products.csv` or `data/products.csv` across Windows, macOS, and Linux.)*

---

## 3. Create the `Product` Data Model Class

Instead of working with raw strings throughout your application, it is best practice to map each CSV row into a strongly typed C# object.

1. Create a new file named **`Product.cs`** in your project root:
   - **Visual Studio**: Right-click the project in Solution Explorer -> **Add** -> **Class...** -> name it `Product.cs`.
   - **VS Code**: Click **New File** in the Explorer sidebar -> name it `Product.cs`.

2. Replace the contents of `Product.cs` with the following code:

```csharp
namespace CsvLoaderApp;

public class Product
{
    public int Id { get; set; }
    public string Name { get; set; } = string.Empty;
    public string Category { get; set; } = string.Empty;
    public decimal Price { get; set; }
    public int StockQuantity { get; set; }

    public override string ToString()
    {
        return $"[{Id}] {Name,-28} | {Category,-12} | {Price,8:C} | Stock: {StockQuantity}";
    }
}
```

### Why these types?
- `int` for whole numbers (`Id` and `StockQuantity`).
- `string` for text (`Name` and `Category`).
- `decimal` for financial/currency values (`Price`), which avoids floating-point rounding errors.

### Why override ToString()?
We override the `ToString()` method to provide a human-readable representation of the `Product` object. This is useful for debugging and for displaying the data in a user-friendly format. This allows you to write `Console.WriteLine(product);` without any additional formatting and get a nicely formatted output. This is especially useful when we want to print a list of products to the console.

---

**Next Step:** **[Step 3: Reading CSV Data Using Built-in .NET (`System.IO`)](./03-reading-csv-native.md)**

