# Step 5: Troubleshooting & Best Practices

When loading CSV files in C#, a few common issues can trip up beginners and experienced developers alike. Use this reference to diagnose and resolve them quickly.

---

## 1. `FileNotFoundException: Could not find file '.../data/products.csv'`

### Cause
When you run a C# application:
- **Visual Studio** usually sets the working directory to `bin\Debug\net10.0\`.
- **VS Code (`dotnet run`)** sets the working directory to the project root folder, whereas the VS Code Debugger (`F5`) may use `bin/Debug/net10.0/` depending on `launch.json`.

If `data/products.csv` is not copied to `bin/Debug/net10.0/data/products.csv`, looking for it in `AppContext.BaseDirectory` will fail.

### Fix
1. Ensure `Copy to Output Directory` is set to **Copy if newer** (`PreserveNewest`) in your `.csproj` file (see [Step 2](./02-preparing-csv-data.md#2-copy-the-csv-file-to-the-output-directory)).
2. Always build the path using `AppContext.BaseDirectory` and `"data"`:
   ```csharp
   string filePath = Path.Combine(AppContext.BaseDirectory, "data", "products.csv");
   ```

---

## 2. `IOException: The process cannot access the file because it is being used by another process`

### Cause
The CSV file is currently open in **Microsoft Excel**, which places an exclusive lock on the file.

### Fix
Close the CSV file in Excel before running your C# console application, or open the file in a text editor (like VS Code or Notepad) instead.

---

## 3. `HeaderValidationException` or `ReaderException` in `CsvHelper`

### Cause
The column names in your CSV header row don't match the property names on your C# class (for example, `stock_quantity` in CSV vs `StockQuantity` in C#, or extra whitespace around column headers).

### Fix
Configure `CsvConfiguration` to trim whitespace or ignore header case:

```csharp
using System.Globalization;
using CsvHelper;
using CsvHelper.Configuration;
using CsvLoaderApp;

var config = new CsvConfiguration(CultureInfo.InvariantCulture)
{
    PrepareHeaderForMatch = args => args.Header.Trim(),
    MissingFieldFound = null, // Ignore missing optional columns instead of throwing
    BadDataFound = context =>
    {
        Console.WriteLine($"Warning: Bad data found on row {context.Context.Parser?.Row}: {context.RawRecord}");
    }
};

using StreamReader reader = new(Path.Combine(AppContext.BaseDirectory, "data", "products.csv"));
using CsvReader csv = new(reader, config);

var products = csv.GetRecords<Product>().ToList();
```

---

## 4. Passing a CSV File Path via Command-Line Arguments

To make your console app reusable for any CSV file, accept the file path from `args`:

```csharp
string filePath = args.Length > 0
    ? args[0]
    : Path.Combine(AppContext.BaseDirectory, "data", "products.csv");
```

Then run it from your terminal with a custom file:
```bash
dotnet run -- "C:/data/custom-products.csv"
```

---

**[Back to Overview (README.md)](./README.md)**

