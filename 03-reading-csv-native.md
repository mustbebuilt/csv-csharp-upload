# Step 3: Reading CSV Data Using Built-in .NET (`System.IO`)

For simple CSV files without commas inside quoted text, you can load and parse the file using .NET's built-in `System.IO` namespace without installing any external packages.

Open **`Program.cs`** and delete the default `Console.WriteLine("Hello, World!");` line. We will build the CSV loader step by step.

---

## 1. Import Namespaces and Set Local Currency

At the very top of **`Program.cs`**, import `System.Globalization` (for currency and number formatting) and your project namespace `CsvLoaderApp` (so `Program.cs` can use your `Product` class), then set the culture to UK English (`en-GB`):

```csharp
using System.Globalization;
using CsvLoaderApp;

// Set culture to UK English (en-GB) so currency formatting (:C) uses £
CultureInfo.CurrentCulture = new CultureInfo("en-GB");
```

### What this does:
- **`CultureInfo.CurrentCulture = new CultureInfo("en-GB")`** configures the application to use UK regional settings. Any currency string formatting (`:C` in `Product.ToString()`) will automatically display British Pounds (`£`), and currency parsing will follow UK rules.

---

## 2. Locate and Verify the CSV File Path

Next, add code to build the path to `data/products.csv` and verify that the file exists before trying to open it:

```csharp
// 1. Define the path to the CSV file inside the /data folder
// AppContext.BaseDirectory ensures we look in the build output folder where data/products.csv was copied
string filePath = Path.Combine(AppContext.BaseDirectory, "data", "products.csv");

if (!File.Exists(filePath))
{
    Console.ForegroundColor = ConsoleColor.Red;
    Console.WriteLine($"Error: Could not find CSV file at '{filePath}'");
    Console.ResetColor();
    return;
}
```

### What this does:
- **`AppContext.BaseDirectory`** points to the directory where your compiled `.exe`/`.dll` is running (e.g., `bin/Debug/net10.0/`).
- **`Path.Combine(...)`** safely joins folder and file names using the correct directory separator (`\` on Windows, `/` on macOS and Linux).
- **`File.Exists(filePath)`** checks that `products.csv` is present. If not, it prints a helpful red error message and exits cleanly instead of crashing with an unhandled exception.

---

## 3. Read the File Lines and Loop Over Rows

Below the file check, create a `List<Product>` to store your results, read all lines from the CSV file, and start a `for` loop that skips the header row:

```csharp
List<Product> products = new();

// 2. Read the file line by line
string[] lines = File.ReadAllLines(filePath);

// Skip the first line (index 0) because it contains the column headers:
// Id,Name,Category,Price,StockQuantity
for (int i = 1; i < lines.Length; i++)
{
    string line = lines[i];

    // Skip empty or whitespace-only lines
    if (string.IsNullOrWhiteSpace(line))
    {
        continue;
    }

    // (We will add column splitting and parsing inside this loop next)
}
```

### What this does:
- **`File.ReadAllLines(filePath)`** opens the CSV file, reads every line into a `string[]` array, and automatically closes the file. *(Tip: For huge files with millions of rows, you can use `File.ReadLines(filePath)` to stream lines one by one.)*
- **`for (int i = 1; i < lines.Length; i++)`** starts at index `1` instead of `0` because line `0` is the CSV header (`Id,Name,Category,Price,StockQuantity`).
- **`string.IsNullOrWhiteSpace(line)`** ignores any accidental blank lines at the end of the file.

---

## 4. Split Each Line into Columns

Inside the `for` loop (right after the `IsNullOrWhiteSpace` check), split the current line by the comma character (`,`) and check that all 5 columns are present:

```csharp
    // 3. Split the line by comma
    string[] columns = line.Split(',');

    // Validate that the row has the expected 5 columns
    if (columns.Length < 5)
    {
        Console.WriteLine($"Skipping malformed row on line {i + 1}: {line}");
        continue;
    }
```

### What this does:
- **`line.Split(',')`** breaks a line like `"101,Wireless Mouse,Accessories,24.99,150"` into an array of 5 strings:
  - `columns[0]` -> `"101"`
  - `columns[1]` -> `"Wireless Mouse"`
  - `columns[2]` -> `"Accessories"`
  - `columns[3]` -> `"24.99"`
  - `columns[4]` -> `"150"`
- Checking **`columns.Length < 5`** prevents an `IndexOutOfRangeException` if a row is incomplete.

---

## 5. Parse Numeric Values and Create `Product` Objects

Still inside the `for` loop (right after the column count check), convert the text values into numbers (`int` and `decimal`), create a `Product` instance, and add it to the `products` list:

```csharp
    // 4. Safely parse numeric values
    if (!int.TryParse(columns[0].Trim(), out int id) ||
        !decimal.TryParse(columns[3].Trim(), NumberStyles.Currency, CultureInfo.CurrentCulture, out decimal price) ||
        !int.TryParse(columns[4].Trim(), out int stockQuantity))
    {
        Console.WriteLine($"Skipping row with invalid numbers on line {i + 1}: {line}");
        continue;
    }

    // 5. Create the Product object and add it to the list
    Product product = new()
    {
        Id = id,
        Name = columns[1].Trim(),
        Category = columns[2].Trim(),
        Price = price,
        StockQuantity = stockQuantity
    };

    products.Add(product);
```

### What this does:
- **`.Trim()`** removes any accidental spaces around values in the CSV file.
- **`int.TryParse` & `decimal.TryParse`** safely attempt to convert strings to numbers without crashing if a cell contains invalid text. When conversion succeeds, the parsed value is stored in the `out` variables (`id`, `price`, `stockQuantity`).
- **`NumberStyles.Currency, CultureInfo.CurrentCulture`** allows `decimal.TryParse` to parse both plain decimals (`24.99`) and values with a local currency symbol (`£24.99`).
- **`products.Add(product)`** appends the populated `Product` object to our list.

---

## 6. Display the Loaded Data and Calculate Totals

Finally, **after the closing `}` of the `for` loop**, add the code to print the table of products and calculate the total inventory value using LINQ:

```csharp
// 6. Display the loaded data
Console.WriteLine($"Successfully loaded {products.Count} products:\n");
Console.WriteLine("ID    Name                         | Category     |    Price | Stock");
Console.WriteLine(new string('-', 70));

foreach (Product product in products)
{
    Console.WriteLine(product);
}

// 7. Example: Perform a simple LINQ calculation on the loaded data
decimal totalInventoryValue = products.Sum(p => p.Price * p.StockQuantity);
Console.WriteLine(new string('-', 70));
Console.WriteLine($"Total Inventory Value: {totalInventoryValue:C}");
```

### What this does:
- **`Console.WriteLine(product)`** automatically calls the `ToString()` method you created on the `Product` class in Step 2, printing each product in aligned columns with `£` currency formatting.
- **`products.Sum(p => p.Price * p.StockQuantity)`** uses LINQ to multiply each product's price by its stock quantity and sum the total across all products.

---

## 7. Run the Application

### In Visual Studio:
- Press **F5** (or **Ctrl+F5** to run without debugging).

### In VS Code:
- Open the terminal and run:
  ```bash
  dotnet run
  ```

### Expected Console Output:
```text
Successfully loaded 5 products:

ID    Name                         | Category     |    Price | Stock
----------------------------------------------------------------------
[101] Wireless Mouse               | Accessories  |   £24.99 | Stock: 150
[102] Mechanical Keyboard          | Accessories  |   £89.50 | Stock: 85
[103] 27-inch 4K Monitor           | Displays     |  £349.00 | Stock: 40
[104] USB-C Docking Station        | Accessories  |  £129.99 | Stock: 60
[105] Noise-Cancelling Headphones  | Audio        |  £199.95 | Stock: 30
----------------------------------------------------------------------
Total Inventory Value: £34,129.40
```

---

## Limitations of `string.Split(',')`

While `string.Split(',')` is great for learning and simple datasets, real-world CSV files often include:
- Values containing commas wrapped in quotes: `106,"Monitor, 32-inch Curved",Displays,499.00,15`
- Escaped double quotes inside fields: `107,"27"" Monitor",Displays,299.00,20`
- Columns in a different order than expected.

In **Step 4**, you will learn how to handle all of these automatically using the **`CsvHelper`** library.

---

**Next Step:** **[Step 4: Reading CSV Data Using `CsvHelper`](./04-reading-csv-csvhelper.md)**
