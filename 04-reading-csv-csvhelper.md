# Step 4: Reading CSV Data Using `CsvHelper` (Recommended)

For real-world applications, writing your own CSV parser is error-prone because CSV fields can contain commas inside quotes (`"Keyboard, Wireless"`), line breaks, or custom column names.

**`CsvHelper`** is the most popular, high-performance open-source NuGet library for reading and writing CSV files in .NET.

---

## 1. Install the `CsvHelper` NuGet Package

Follow the instructions for your IDE to add `CsvHelper` to your project.

### Option A: In Visual Studio
1. In **Solution Explorer**, right-click on the **`CsvLoaderApp`** project and select **Manage NuGet Packages...**.
2. Click the **Browse** tab at the top left.
3. In the search bar, type **`CsvHelper`**.
4. Select **CsvHelper** (by Josh Close) from the results.
5. Click **Install** on the right pane, and click **Accept** if prompted.

*(Alternatively, open **Tools > NuGet Package Manager > Package Manager Console** and run `Install-Package CsvHelper`.)*

---

### Option B: In VS Code

**Using the Integrated Terminal (.NET CLI):**
1. Open the VS Code terminal (`Ctrl+\``).
2. Run the following command in your project directory:
   ```bash
   dotnet add package CsvHelper
   ```

**Or Using the C# Dev Kit Command Palette:**
1. Open the Command Palette (`Ctrl+Shift+P` on Windows/Linux, `Cmd+Shift+P` on macOS).
2. Select **NuGet: Add NuGet Package...**.
3. Type `CsvHelper`, select it from the list, and choose the latest stable version.

---

## 2. Test with Quoted Commas in `data/products.csv`

Let's add a row to **`data/products.csv`** that includes a comma inside quotes to see how `CsvHelper` handles it effortlessly:

```csv
Id,Name,Category,Price,StockQuantity
101,Wireless Mouse,Accessories,24.99,150
102,Mechanical Keyboard,Accessories,89.50,85
103,27-inch 4K Monitor,Displays,349.00,40
104,USB-C Docking Station,Accessories,129.99,60
105,Noise-Cancelling Headphones,Audio,199.95,30
106,"Monitor Stand, Dual Arm",Accessories,59.99,45
```

---

## 3. Read the CSV with `CsvHelper` in `Program.cs`

Open **`Program.cs`** and replace its contents with the following code:

```csharp
using System.Globalization;
using CsvHelper;
using CsvLoaderApp;

// Set culture to UK English (en-GB) so currency formatting (:C) uses £
CultureInfo.CurrentCulture = new CultureInfo("en-GB");

string filePath = Path.Combine(AppContext.BaseDirectory, "data", "products.csv");

if (!File.Exists(filePath))
{
    Console.ForegroundColor = ConsoleColor.Red;
    Console.WriteLine($"Error: Could not find CSV file at '{filePath}'");
    Console.ResetColor();
    return;
}

// 1. Open the file stream and pass it to CsvReader
using StreamReader reader = new(filePath);
using CsvReader csv = new(reader, CultureInfo.CurrentCulture);

// 2. Automatically read and map rows to Product objects
List<Product> products = csv.GetRecords<Product>().ToList();

// 3. Display the loaded products
Console.WriteLine($"Successfully loaded {products.Count} products using CsvHelper:\n");
Console.WriteLine("ID    Name                         | Category     |    Price | Stock");
Console.WriteLine(new string('-', 70));

foreach (Product product in products)
{
    Console.WriteLine(product);
}

// 4. Example: Perform a simple LINQ calculation on the loaded data
decimal totalInventoryValue = products.Sum(p => p.Price * p.StockQuantity);
Console.WriteLine(new string('-', 70));
Console.WriteLine($"Total Inventory Value: {totalInventoryValue:C}");
```

### How `CsvHelper` Works Here:
- **`using StreamReader reader = new(filePath);`**: Opens the file for reading and automatically disposes of the file handle when finished.
- **`csv.GetRecords<Product>()`**: Reads the CSV header row (`Id,Name,Category,Price,StockQuantity`), matches each column name to the property names on your `Product` class, converts each value to its target type (`int`, `string`, `decimal`), and yields `Product` instances.
- **`.ToList()`**: Materialises all records into a `List<Product>` in memory.

---

## 4. Optional: Customising Column Headers with Attributes

What if your CSV file uses column headers with spaces or different names (for example, `Product ID` or `Stock_Count`)? You can use `[Name]` attributes in **`Product.cs`**:

```csharp
using CsvHelper.Configuration.Attributes;

namespace CsvLoaderApp;

public class Product
{
    [Name("Id")]
    public int Id { get; set; }

    [Name("Name")]
    public string Name { get; set; } = string.Empty;

    [Name("Category")]
    public string Category { get; set; } = string.Empty;

    [Name("Price")]
    public decimal Price { get; set; }

    [Name("StockQuantity")]
    public int StockQuantity { get; set; }

    public override string ToString()
    {
        return $"[{Id}] {Name,-28} | {Category,-12} | {Price,8:C} | Stock: {StockQuantity}";
    }
}
```

---

## 5. Run the Application

- **Visual Studio**: Press **F5**.
- **VS Code**: Run `dotnet run` in the terminal.

Notice that row `106` (`"Monitor Stand, Dual Arm"`) is parsed accurately without splitting on the comma inside the product name!

---

**Next Step:** **[Step 5: Troubleshooting & Best Practices](./05-troubleshooting-and-best-practices.md)**

