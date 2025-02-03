# 🏗️ How to Save and Load JSON Data in Avalonia UI

### 📌 Overview
This tutorial will guide you through saving and loading JSON data in an Avalonia UI application. JSON is a lightweight format used for data storage and exchange, making it an excellent choice for persisting application settings, user data, and more.

### ✅ What You Will Learn:
- How to **serialize** and **deserialize** JSON data in Avalonia UI.
- How to **save data to a file** and **load it back**.
- Using `System.Text.Json` for JSON manipulation.
- Implementing a **simple data model and ViewModel** for JSON storage.

---

## 🛠️ **1. Setting Up the Dependencies**
Ensure you have the required package in your project:

```bash
dotnet add package System.Text.Json
dotnet restore
```

---

## 📂 **2. Creating the Data Model**
Let's define a simple data model that we want to save in JSON format.

```csharp
using System;

namespace MyApp.Models
{
    public class UserSettings
    {
        public string Username { get; set; }
        public bool DarkModeEnabled { get; set; }
        public DateTime LastLogin { get; set; }
    }
}
```

---

## 📥 **3. Saving Data to a JSON File**
To save the object as JSON, we use `JsonSerializer` and write the output to a file.

```csharp
using System;
using System.IO;
using System.Text.Json;
using System.Threading.Tasks;
using MyApp.Models;

namespace MyApp.Services
{
    public class JsonStorageService
    {
        private const string FilePath = "settings.json";

        public async Task SaveSettingsAsync(UserSettings settings)
        {
            var json = JsonSerializer.Serialize(settings, new JsonSerializerOptions { WriteIndented = true });
            await File.WriteAllTextAsync(FilePath, json);
        }
    }
}
```

✔ **Key Points:**
- `JsonSerializer.Serialize()` converts the `UserSettings` object to JSON.
- `WriteIndented = true` makes the JSON file more readable.
- `File.WriteAllTextAsync()` ensures the data is stored asynchronously.

---

## 📤 **4. Loading Data from a JSON File**
Now, let's implement a method to **read JSON data** and convert it back into a C# object.

```csharp
public async Task<UserSettings?> LoadSettingsAsync()
{
    if (!File.Exists(FilePath))
        return null; // Return null if file does not exist

    var json = await File.ReadAllTextAsync(FilePath);
    return JsonSerializer.Deserialize<UserSettings>(json);
}
```

✔ **Key Points:**
- Checks if the file exists before reading.
- Uses `JsonSerializer.Deserialize<T>()` to parse JSON into a `UserSettings` object.

---

## 🏗 **5. Integrating with a ViewModel**
Let's create a **ViewModel** to bind the settings data to the UI.

```csharp
using System;
using System.Threading.Tasks;
using MyApp.Models;
using MyApp.Services;
using ReactiveUI;

namespace MyApp.ViewModels
{
    public class SettingsViewModel : ReactiveObject
    {
        private readonly JsonStorageService _storageService = new();
        private UserSettings _userSettings = new();

        public string Username
        {
            get => _userSettings.Username;
            set => this.RaiseAndSetIfChanged(ref _userSettings.Username, value);
        }

        public bool DarkModeEnabled
        {
            get => _userSettings.DarkModeEnabled;
            set => this.RaiseAndSetIfChanged(ref _userSettings.DarkModeEnabled, value);
        }

        public async Task SaveSettingsAsync() => await _storageService.SaveSettingsAsync(_userSettings);
        public async Task LoadSettingsAsync() => _userSettings = await _storageService.LoadSettingsAsync() ?? new UserSettings();
    }
}
```

✔ **Key Points:**
- `RaiseAndSetIfChanged` ensures changes update the UI.
- `SaveSettingsAsync()` and `LoadSettingsAsync()` interact with the storage service.

---

## 🎨 **6. Creating the UI (XAML)**
Here’s a simple UI to modify and save user settings.

```xml
<Window xmlns="https://github.com/avaloniaui"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        x:Class="MyApp.Views.SettingsWindow"
        Title="User Settings"
        Width="400" Height="300">
    <StackPanel Margin="20">
        <TextBlock Text="Username:" />
        <TextBox Text="{Binding Username}" />
        
        <CheckBox Content="Enable Dark Mode" IsChecked="{Binding DarkModeEnabled}" />
        
        <Button Content="Save Settings" Command="{Binding SaveSettingsAsync}" />
        <Button Content="Load Settings" Command="{Binding LoadSettingsAsync}" />
    </StackPanel>
</Window>
```

✔ **Key Points:**
- **Two-way binding** (`Text="{Binding Username}"`) allows live updates.
- The **CheckBox** binds to `DarkModeEnabled`.
- **Buttons** trigger `SaveSettingsAsync` and `LoadSettingsAsync`.

---

## 🔥 **7. Testing the Implementation**
### 🛠 **How to Test**
1. **Run the application.**
2. **Enter a username and toggle dark mode.**
3. **Click "Save Settings" – JSON file is created.**
4. **Restart the app, click "Load Settings" – values are restored.**

---

## 🎯 **Conclusion**
🎉 You’ve successfully implemented **JSON storage in Avalonia UI!**  
- ✅ **Saving and loading settings.**
- ✅ **Asynchronous file handling.**
- ✅ **ViewModel binding to UI.**

For further improvements, consider **encrypting JSON** for sensitive data or using **async data validation**.

---

### 📌 **Next Steps**
- 🔹 Add **error handling** for corrupt JSON files.
- 🔹 Implement **multiple user profiles** with different JSON files.
- 🔹 Explore **saving data in a database** (e.g., SQLite with Entity Framework).

---

## 🔗 **Related Resources**
- 📖 [Avalonia UI Documentation](https://docs.avaloniaui.net/)
- 📖 [Microsoft JSON Documentation](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json-overview)

---

## **✅ Summary**
- **📤 Saving JSON with `JsonSerializer.Serialize()`**
- **📥 Loading JSON with `JsonSerializer.Deserialize<T>()`**
- **🎨 Binding JSON data to UI using a ViewModel**
- **📂 Storing user preferences and settings**
- **🔥 Fully working Avalonia UI JSON implementation**

🚀 Now you can persist data in your Avalonia applications! Happy coding! 🎉 
