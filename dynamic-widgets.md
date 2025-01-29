# 📖 Dynamic Widgets in Avalonia UI  

This guide will teach you how to create **interactive and customizable UI components** for desktop applications using **Avalonia UI and .NET Core 6+**.  

## ✅ Topics Covered  
- **Creating and managing widgets**  
- **Drag-and-drop functionality for widgets**  
- **Resizable UI components**  
- **Saving and loading widget layouts using JSON**  
- **Real-time financial data updates**  

---

## 🛠 Prerequisites  
Before starting, make sure you have the following installed:
- **.NET Core 6+**
- **Avalonia UI Toolkit**
- **ReactiveUI** for state management  
- **Newtonsoft.Json** for JSON layout persistence  

---

## 📌 Setting Up Avalonia UI  
To create an Avalonia UI application, install the required packages:  

```bash
dotnet new avalonia.app -n DynamicWidgetsApp
cd DynamicWidgetsApp
dotnet add package Avalonia.ReactiveUI
dotnet add package Newtonsoft.Json
dotnet restore
```

---

# 🛠 1. Creating a Basic Widget
A simple financial tracking widget using XAML:

📌 **File: `Views/WidgetView.axaml`**
```xml
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.Views.WidgetView">
    <Border BorderThickness="2" CornerRadius="10" Padding="10" Background="LightGray">
        <StackPanel>
            <TextBlock Text="{Binding Title}" FontSize="16" FontWeight="Bold"/>
            <TextBlock Text="{Binding Content}" FontSize="12"/>
        </StackPanel>
    </Border>
</UserControl>
```

## 🔹 ViewModel for Widget
Each widget is represented by a **ViewModel**:

📌 **File: `ViewModels/WidgetViewModel.cs`**
```csharp
using ReactiveUI;

public class WidgetViewModel : ReactiveObject
{
    private string _title;
    private string _content;

    public string Title
    {
        get => _title;
        set => this.RaiseAndSetIfChanged(ref _title, value);
    }

    public string Content
    {
        get => _content;
        set => this.RaiseAndSetIfChanged(ref _content, value);
    }

    public WidgetViewModel(string title, string content)
    {
        Title = title;
        Content = content;
    }
}
```

---

# 🛠 2. Implementing Dynamic Widget Display  
To dynamically display widgets, we need to **create a container for them** in the main application.

📌 **File: `Views/MainWindow.axaml`**
```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="clr-namespace:MyApp.Views"
        x:Class="MyApp.Views.MainWindow"
        Width="800" Height="600" Title="Dynamic Widgets">

    <StackPanel>
        <Button Content="Add Widget" Command="{Binding AddWidgetCommand}" Margin="10"/>
        <ItemsControl Items="{Binding Widgets}">
            <ItemsControl.ItemTemplate>
                <DataTemplate>
                    <local:WidgetView DataContext="{Binding}" />
                </DataTemplate>
            </ItemsControl.ItemTemplate>
        </ItemsControl>
    </StackPanel>
</Window>
```

## 🔹 ViewModel for Main Window
📌 **File: `ViewModels/MainWindowViewModel.cs`**
```csharp
using System.Collections.ObjectModel;
using System.Reactive;
using ReactiveUI;

public class MainWindowViewModel : ReactiveObject
{
    public ObservableCollection<WidgetViewModel> Widgets { get; } = new();
    public ReactiveCommand<Unit, Unit> AddWidgetCommand { get; }

    public MainWindowViewModel()
    {
        AddWidgetCommand = ReactiveCommand.Create(AddWidget);
    }

    private void AddWidget()
    {
        Widgets.Add(new WidgetViewModel("New Widget", "This is a dynamic widget."));
    }
}
```

👉 **How it works?**  
✅ The "Add Widget" button dynamically adds a **new widget** to the UI.  
✅ **ItemsControl** dynamically binds to the list of widgets in the ViewModel.

---

# 🛠 3. Saving & Loading Widget Layouts
To persist widget positions and settings, save them as JSON.

📌 **File: `Services/WidgetLayoutService.cs`**
```csharp
using System.Collections.Generic;
using System.IO;
using Newtonsoft.Json;

public class WidgetLayoutService
{
    public void SaveLayout(IEnumerable<WidgetViewModel> widgets)
    {
        var json = JsonConvert.SerializeObject(widgets);
        File.WriteAllText("widgets_layout.json", json);
    }

    public List<WidgetViewModel> LoadLayout()
    {
        if (File.Exists("widgets_layout.json"))
        {
            var json = File.ReadAllText("widgets_layout.json");
            return JsonConvert.DeserializeObject<List<WidgetViewModel>>(json);
        }
        return new List<WidgetViewModel>();
    }
}
```

---

# 🛠 4. Real-Time Data Updates
To simulate live financial tracking, we use **ReactiveUI**.

📌 **File: `ViewModels/FinancialWidgetViewModel.cs`**
```csharp
using System;
using System.Reactive.Linq;
using ReactiveUI;

public class FinancialWidgetViewModel : ReactiveObject
{
    private decimal _balance;
    public decimal Balance
    {
        get => _balance;
        set => this.RaiseAndSetIfChanged(ref _balance, value);
    }

    public FinancialWidgetViewModel()
    {
        Observable.Interval(TimeSpan.FromSeconds(5))
            .Subscribe(_ => Balance = new Random().Next(1000, 5000));
    }
}
```

👉 **How it works?**  
✅ The **Balance** updates **every 5 seconds**, simulating real-time data changes.  

---

# 🛠 5. UI Enhancements & Customization  

To improve **UI customization**, we add:

- **Light/Dark Mode Support**
- **Resizable Widgets**
- **User-defined layouts**

### 🔹 Adding Theme Support in `App.axaml`
📌 **File: `App.axaml`**
```xml
<Application xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             x:Class="MyApp.App">
    <Application.Styles>
        <FluentTheme Mode="Light"/>
    </Application.Styles>
</Application>
```

### 🔹 Making Widgets Resizable  

```xml
<GridSplitter HorizontalAlignment="Stretch" VerticalAlignment="Stretch"/>
```

---

👨‍💻 **Follow this repository for more Avalonia UI tutorials!**  
