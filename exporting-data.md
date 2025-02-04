# 📄 How to Export Data in Avalonia UI (PDF & Excel)

### 📌 Overview
Exporting data is a crucial feature in many applications, allowing users to **store, share, and analyze data externally**. In this tutorial, you'll learn how to export data from an **Avalonia UI application** into **PDF and Excel** formats.

### ✅ What You Will Learn:
- How to **export data to a PDF file** using `iText7`.
- How to **export data to an Excel file** using `EPPlus`.
- How to **connect export functions to UI buttons**.
- How to **handle file writing operations asynchronously**.

---

## 🛠️ **1. Setting Up Dependencies**
Ensure that the required packages are installed in your project.

```bash
dotnet add package EPPlus
dotnet add package itext7
dotnet restore
```

**EPPlus** is used for handling Excel files, while **iText7** allows us to generate PDFs.

---

## 📄 **2. Exporting Data to PDF**
We'll create a simple method that **generates a PDF file** with structured data.

### **ExportService.cs**
```csharp
using System.Collections.Generic;
using System.IO;
using iText.Kernel.Pdf;
using iText.Layout;
using iText.Layout.Element;
using MyApp.Models;

namespace MyApp.Services
{
    public class ExportService
    {
        private const string PdfPath = "transactions.pdf";

        public void ExportToPdf(IEnumerable<Transaction> transactions)
        {
            using (var writer = new PdfWriter(PdfPath))
            using (var pdf = new PdfDocument(writer))
            using (var doc = new Document(pdf))
            {
                doc.Add(new Paragraph("Exported Transactions"));

                foreach (var t in transactions)
                {
                    doc.Add(new Paragraph($"{t.Date}: {t.Name} - {t.Amount:C}"));
                }
            }
        }
    }
}
```
✔ **Key Points:**
- `PdfWriter` creates a new PDF file.
- `Document` is used to format text in the file.
- We iterate over transactions and write them into the PDF.

---

## 📊 **3. Exporting Data to Excel**
Now, let's implement a method for **generating an Excel file**.

### **ExportService.cs**
```csharp
using System.Collections.Generic;
using System.IO;
using OfficeOpenXml;
using MyApp.Models;

namespace MyApp.Services
{
    public class ExportService
    {
        private const string ExcelPath = "transactions.xlsx";

        public void ExportToExcel(IEnumerable<Transaction> transactions)
        {
            using (var package = new ExcelPackage(new FileInfo(ExcelPath)))
            {
                var sheet = package.Workbook.Worksheets.Add("Transactions");

                sheet.Cells[1, 1].Value = "Date";
                sheet.Cells[1, 2].Value = "Name";
                sheet.Cells[1, 3].Value = "Amount";

                int row = 2;
                foreach (var t in transactions)
                {
                    sheet.Cells[row, 1].Value = t.Date.ToShortDateString();
                    sheet.Cells[row, 2].Value = t.Name;
                    sheet.Cells[row, 3].Value = t.Amount;
                    row++;
                }

                package.Save();
            }
        }
    }
}
```

✔ **Key Points:**
- **EPPlus** is used to create and format an Excel file.
- Data is inserted into specific cells.
- The final Excel file is saved as `transactions.xlsx`.

---

## 🎨 **4. Connecting Export Functionality to the UI**
Now, let's bind these export functions to buttons in Avalonia UI.

### **MainWindow.axaml**
```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="MyApp.Views.MainWindow"
        Title="Data Export Example"
        Width="600" Height="400">
    
    <StackPanel Margin="20">
        <Button Content="📄 Export to PDF" Command="{Binding ExportToPdfCommand}" Margin="5"/>
        <Button Content="📊 Export to Excel" Command="{Binding ExportToExcelCommand}" Margin="5"/>
    </StackPanel>
</Window>
```
✔ **Key Points:**
- Two buttons trigger the export actions.
- The `Command` bindings connect to the ViewModel.

---

## 🏗 **5. Implementing Commands in ViewModel**
Now, let’s create **commands in the ViewModel** to trigger export functions.

### **MainWindowViewModel.cs**
```csharp
using System.Collections.ObjectModel;
using System.Reactive;
using MyApp.Models;
using MyApp.Services;
using ReactiveUI;

namespace MyApp.ViewModels
{
    public class MainWindowViewModel : ReactiveObject
    {
        private readonly ExportService _exportService = new();
        public ObservableCollection<Transaction> Transactions { get; }

        public ReactiveCommand<Unit, Unit> ExportToPdfCommand { get; }
        public ReactiveCommand<Unit, Unit> ExportToExcelCommand { get; }

        public MainWindowViewModel()
        {
            Transactions = new ObservableCollection<Transaction>
            {
                new Transaction { Date = DateTime.Now, Name = "Sample 1", Amount = 50 },
                new Transaction { Date = DateTime.Now, Name = "Sample 2", Amount = 150 }
            };

            ExportToPdfCommand = ReactiveCommand.Create(() => _exportService.ExportToPdf(Transactions));
            ExportToExcelCommand = ReactiveCommand.Create(() => _exportService.ExportToExcel(Transactions));
        }
    }
}
```

✔ **Key Points:**
- **Commands** handle UI interactions.
- Transactions are stored in an **ObservableCollection**.
- The export functions are triggered when buttons are clicked.

---

## 🔥 **6. Testing the Implementation**
### **How to Test**
1️⃣ **Run the application** in Avalonia UI.  
2️⃣ **Click "Export to PDF"** – a new `transactions.pdf` file should appear.  
3️⃣ **Click "Export to Excel"** – an Excel file should be generated.  
4️⃣ **Open the files** and check if the data is correctly saved.  

---

## 🎯 **Conclusion**
🎉 You've successfully added **data export functionality** to your Avalonia UI application!  
- ✅ **Export data to PDF & Excel** with simple C# methods.  
- ✅ **Use ReactiveUI to trigger export commands from UI.**  
- ✅ **Enhance user experience by providing easy data storage options.**  

🚀 Now you can persist and share data in your Avalonia applications!

---

## ✅ **Summary**
- **📤 Export data to PDF with `iText7`**
- **📥 Export data to Excel with `EPPlus`**
- **📂 Save reports and structured data from Avalonia UI**
- **🎨 Bind export functionality to UI with ReactiveUI**
- **🔥 Fully working Avalonia UI data export feature**

Now you can **integrate export functions into your Avalonia UI applications** with ease! 🚀
