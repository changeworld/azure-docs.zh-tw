---
title: 教學課程：使用 WPF 建立翻譯應用程式 (C#) - 翻譯工具
titleSuffix: Azure Cognitive Services
description: 在本教學課程中，您將建立以單一訂用帳戶金鑰來執行文字翻譯、語言偵測和拼字檢查的 WPF 應用程式。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 70550b61354c23889836b48be6f09475569ecd52
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589651"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>教學課程：使用 WPF 建立翻譯應用程式

在本教學課程中，您將建置以單一訂用帳戶金鑰使用 Azure 認知服務進行文字翻譯、語言偵測和拼字檢查的 [Windows Presentation Foundation (WPF)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) 應用程式。 具體來說，您的應用程式將呼叫翻譯工具和 [Bing 拼字檢查](https://azure.microsoft.com/services/cognitive-services/spell-check/)的 API。

什麼是 WPF？ 這是可建立桌面用戶端應用程式的 UI 架構。 WPF 開發平台支援廣泛的應用程式開發功能，包括應用程式模型、資源、控制項、圖形、版面配置、資料繫結、文件和安全性。 它是 .NET Framework 的子集，因此，如果您先前曾使用 ASP.NET 或 Windows Forms 在 .NET Framework 中建置應用程式，即應熟悉其程式設計體驗。 WPF 使用可延伸應用程式標記語言 (XAML) 提供應用程式開發的宣告式模型，我們將在接下來的章節中加以討論。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在 Visual Studio 中建立 WPF 專案
> * 將組件和 NuGet 套件新增至您的專案
> * 使用 XAML 建立應用程式的 UI
> * 使用翻譯工具取得語言、翻譯文字，以及偵測來源語言
> * 使用 Bing 拼字檢查 API 來驗證輸入，並改善翻譯的精確度
> * 執行您的 WPF 應用程式

### <a name="cognitive-services-used-in-this-tutorial"></a>本教學課程中使用的認知服務

此清單包含本教學課程中使用的認知服務。 請依照下列連結瀏覽每項功能的 API 參考。

| 服務 | 功能 | 描述 |
|---------|---------|-------------|
| 轉譯程式 | [取得語言](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | 擷取文字翻譯完整的支援語言清單。 |
| 轉譯程式 | [翻譯](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | 將文字翻譯為 70 多種語言。 |
| 轉譯程式 | [偵測](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | 偵測輸入文字的語言。 包含偵測的信賴分數。 |
| Bing 拼字檢查 | [拼字檢查](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | 更正拼字錯誤以改善翻譯的正確性。 |

## <a name="prerequisites"></a>Prerequisites

繼續操作之前，您將需要下列項目：

* Azure 認知服務訂用帳戶。 [取得認知服務金鑰](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource)。
* 一部 Windows 電腦
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Community 或 Enterprise 版

> [!NOTE]
> 建議您在美國西部區域建立本教學課程所需的訂用帳戶。 否則，您在執行此練習時將必須變更程式碼中的端點和區域。  

## <a name="create-a-wpf-app-in-visual-studio"></a>在 Visual Studio 中建立 WPF 應用程式

我們的首要工作是在 Visual Studio 中設定專案。

1. 開啟 Visual Studio。 選取 [建立新專案]。
1. 在 [建立新專案] 中，找出並選取 [WPF 應用程式 (.NET Framework)]。 您可以從 [語言] 選取 C# 來縮小選項的範圍。
1. 選取 [下一步]，然後將您的專案命名為 `MSTranslatorDemo`。
1. 將 framework 版本設定為 **.NET Framework 4.7.2** 或更新版本，然後選取 [建立]。
   ![在 Visual Studio 中輸入名稱和 framework 版本](media/name-wpf-project-visual-studio.png)

您的專案已建立。 您會發現有兩個開啟的索引標籤：`MainWindow.xaml` 和 `MainWindow.xaml.cs`。 在本教學課程中，我們都將在這兩個檔案中新增程式碼。 我們會針對應用程式的使用者介面修改 `MainWindow.xaml`。 我們會針對我們對翻譯工具和 Bing 拼寫檢查的呼叫修改 `MainWindow.xaml.cs`。
   ![檢查您的環境](media/blank-wpf-project.png)

在下一節中，我們會將組件和 NuGet 套件新增至專案以增添功能，例如 JSON 剖析。

## <a name="add-references-and-nuget-packages-to-your-project"></a>將參考和 NuGet 套件新增至您的專案

我們的專案需要幾個 .NET Framework 組件和 NewtonSoft.Json，我們將使用 NuGet 套件管理員加以安裝。

### <a name="add-net-framework-assemblies"></a>新增 .NET Framework 組件

我們將在專案中新增組件，用以序列化和還原序列化物件，以及管理 HTTP 要求和回應。

1. 在 Visual Studio 的方案總管中找出您的專案。 以滑鼠右鍵按一下您的專案，然後選取 [新增] > [參考]，以開啟 [參考管理員]。
1. [組件] 索引標籤會列出所有可供參考的 .NET Framework 組件。 使用右上方的搜尋列來搜尋參考。
   ![新增組件參考](media/add-assemblies-2019.png)
1. 為您的專案選取下列參考：
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. 將這些參考新增至專案後，您可以按一下 [確定] 以關閉 [參考管理員]。

> [!NOTE]
> 如果您想要深入了解組件參考，請參閱[操作說明：使用參考管理員新增或移除參考](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019)。

### <a name="install-newtonsoftjson"></a>安裝 Newtonsoft.Json

我們的應用程式會使用 NewtonSoft.Json 將 JSON 物件還原序列化。 請依照下列指示來安裝套件。

1. 在 Visual Studio 的方案總管中找出您的專案，並以滑鼠右鍵按一下該專案。 選取 [管理 NuGet 套件]。
1. 找出 [瀏覽] 索引標籤並加以選取。
1. 在搜尋列中輸入 [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)。

    ![找出 NewtonSoft.Json 並加以安裝](media/nuget-package-manager.png)

1. 選取套件並按一下 [安裝]。
1. 安裝完成後，請關閉索引標籤。

## <a name="create-a-wpf-form-using-xaml"></a>使用 XAML 建立 WPF 表單

若要使用您的應用程式，你將需要使用者介面。 我們將使用 XAML，建立可讓使用者選取輸入和翻譯語言、輸入要翻譯的文字，並顯示翻譯輸出的表單。

我們看一下剛才建置的內容。

![WPF XAML 使用者介面](media/translator-text-csharp-xaml.png)

使用者介面包含下列元件：

| 名稱 | 類型 | 描述 |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | 顯示 Microsoft Translator 支援的文字翻譯語言清單。 使用者會選取其來源翻譯語言。 |
| `ToLanguageComboBox` | ComboBox | 顯示與 `FromComboBox` 相同的語言清單，但會用來選取使用者的目標翻譯語言。 |
| `TextToTranslate` | TextBox | 可讓使用者輸入要翻譯的文字。 |
| `TranslateButton` | 按鈕 | 使用此按鈕可翻譯文字。 |
| `TranslatedTextLabel` | 標籤 | 顯示翻譯。 |
| `DetectedLanguageLabel` | 標籤 | 顯示對要翻譯的文字 (`TextToTranslate`) 偵測到的語言。 |

> [!NOTE]
> 我們將使用 XAML 原始程式碼建立此表單，但您也可以使用 Visual Studio 中的編輯器來建立表單。

我們要在專案中新增程式碼。

1. 在 Visual Studio 中，選取 `MainWindow.xaml` 的索引標籤。
1. 將此程式碼複製到您的專案，然後選取 [檔案] > [儲存 mainwindow.xaml]，以儲存您的變更。
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
此時，您應該會在 Visual Studio 中看到應用程式使用者介面的預覽。 看起來應該會類似於下圖。

就這樣，您的表單已準備就緒。 現在，我們要撰寫一些程式碼來使用文字翻譯和 Bing 拼字檢查。

> [!NOTE]
> 您可以依需求調整此表單，或自行建立表單。

## <a name="create-your-app"></a>建立您的應用程式

`MainWindow.xaml.cs` 包含可控制應用程式的程式碼。 在接下來的幾節中，我們將新增程式碼以填入下拉式功能表中，並呼叫翻譯工具和 Bing 拼字檢查所公開的幾個 API。

* 當程式啟動時，`MainWindow` 會具現化，然後呼叫翻譯工具的 `Languages` 方法以擷取並填入我們的語言選取下拉式清單。 此作業只會在每個工作階段開始時執行一次。
* 按一下 [翻譯] 按鈕後，即會擷取使用者選取的語言和文字，然後對輸入進行拼字檢查，並且為使用者顯示翻譯和偵測到的語言。
  * 系統會呼叫翻譯工具的 `Translate` 方法，以翻譯 `TextToTranslate` 中的文字。 此呼叫也包含使用下拉式功能表選取的 `to` 和 `from` 語言。
  * 系統會呼叫翻譯工具的 `Detect` 方法，以判定 `TextToTranslate` 的文字語言。
  * 系統會使用 Bing 拼字檢查來驗證 `TextToTranslate` 並調整拼字錯誤。

我們的整個專案會封裝在 `MainWindow : Window` 類別中。 我們將先新增程式碼以設定訂用帳戶金鑰、宣告翻譯工具和 Bing 拼字檢查的端點，並初始化應用程式。

1. 在 Visual Studio 中，選取 `MainWindow.xaml.cs` 的索引標籤。
1. 將預先填入的 `using` 陳述式取代為以下內容。  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. 找出 `MainWindow : Window` 類別，並將其取代下列程式碼：
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. 新增您的認知服務訂用帳戶，並加以儲存。

在此程式碼區塊中，我們宣告了兩個成員變數，其中包含可用翻譯語言的相關資訊：

| 變數 | 類型 | 描述 |
|----------|------|-------------|
|`languageCodes` | 字串的陣列 |快取語言代碼。 Translator 服務使用短代碼 (例如 `en` 代表英文) 來識別語言。 |
|`languageCodesAndTitles` | 已排序的字典 | 將使用者介面中的「易記」名稱往回對應至 API 中所使用的短代碼。 依字母順序排序，而不考慮大小寫。 |

然後，我們在 `MainWindow` 建構函式中新增了 `HandleExceptions` 進行錯誤處理。 此錯誤處理可確保在例外狀況未獲得處理時會發出警示。 然後會執行檢查，以確認所提供的訂用帳戶金鑰長度為 32 個字元。 如果金鑰的長度小於/大於 32 個字元，則會擲回錯誤。

如果金鑰至少長度正確，`InitializeComponent()` 呼叫就會藉由尋找、載入主應用程式視窗的 XAML 描述並將其具現化，讓使用者介面開始運作。

最後，我們新增了會呼叫適當方法的程式碼，用以擷取翻譯的語言，並填入應用程式使用者介面的下拉式功能表。 別擔心，我們稍後會說明這些呼叫所使用的程式碼。

## <a name="get-supported-languages"></a>取得支援的語言

翻譯工具目前支援 70 餘種語言。 未來將會新增其他語言支援，因此建議您呼叫翻譯工具所公開的語言資源，而不要在您的應用程式中建立硬式編碼的語言清單。

在本節中，我們將建立對語言資源的 `GET` 要求，以指定我們想要翻譯的可用語言清單。

> [!NOTE]
> 語言資源可讓您以下列查詢參數篩選語言支援：音譯、字典和翻譯。 如需詳細資訊，請參閱 [API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)。

在進一步討論之前，我們先看看一個語言資源呼叫的範例輸出：

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

在此輸出中，我們可以擷取出特定語言的語言代碼和 `name`。 我們的應用程式使用 NewtonSoft.Json 將 JSON 物件 ([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm)) 還原序列化。

接續我們在上一節未完成的，我們將新增方法以取得應用程式支援的語言。

1. 在 Visual Studio 中，開啟 `MainWindow.xaml.cs` 的索引標籤。
2. 將下列程式碼新增至您的專案：
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

`GetLanguagesForTranslate()` 方法會建立 HTTP GET 要求，並使用 `scope=translation` 查詢字串參數，將要求的範圍限定於翻譯支援的語言。 系統會新增值為 `en` 的 `Accept-Language` 標頭，讓支援的語言以英文傳回。

JSON 回應經剖析後會轉換為字典。 然後，語言代碼會新增至 `languageCodes` 成員變數。 系統會逐一查看包含語言代碼和易記語言名稱的索引鍵/值組，然後新增至 `languageCodesAndTitles` 成員變數。 表單中的下拉式功能表會顯示易記名稱，但需要以代碼來要求翻譯。

## <a name="populate-language-drop-down-menus"></a>填入語言下拉式功能表

使用者介面都是以 XAML 定義的，因此除了呼叫 `InitializeComponent()` 以外，不需費太多功夫即可完成設定。 您需要做的，就是將易記語言名稱新增至 [翻譯來源] 和 [翻譯目標] 下拉式功能表。 `PopulateLanguageMenus()` 方法會新增名稱。

1. 在 Visual Studio 中，開啟 `MainWindow.xaml.cs` 的索引標籤。
2. 將下列程式碼新增至專案的 `GetLanguagesForTranslate()` 方法下方：
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

此方法會逐一查看 `languageCodesAndTitles` 字典，並將每個索引鍵新增至這兩個功能表。 填入功能表之後，預設的來源和目標語言會分別設為 [偵測] 和 [英文]。

> [!TIP]
> 如果功能表沒有預設選取項目，使用者將無須先選擇 [目標] 或 [來源] 語言，便可按一下 [翻譯]。 如果有預設值，便不需要處理這個問題。

現在，`MainWindow` 已初始化，且使用者介面已建立，必須在按下 [翻譯] 按鈕後，此程式碼才會執行。

## <a name="detect-language-of-source-text"></a>偵測來源文字的語言

現在，我們將建立相關方法，以使用翻譯工具偵測來源文字 (在文字區域中輸入的文字) 的語言。 此要求傳回的值將用於我們後續的翻譯要求中。

1. 在 Visual Studio 中，開啟 `MainWindow.xaml.cs` 的索引標籤。
2. 將下列程式碼新增至專案的 `PopulateLanguageMenus()` 方法下方：
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

此方法會建立對「偵測」資源的 HTTP `POST` 要求。 它會使用單一引數 `text`，而此引數會納入要求本文中一併傳遞。 稍後當我們建立翻譯要求時，在 UI 中輸入的文字將會傳至此方法以進行語言偵測。

此外，此方法也會評估回應的信賴分數。 如果分數高於 `0.5`，則偵測到的語言會顯示在我們的使用者介面中。

## <a name="spell-check-the-source-text"></a>對來源文字進行拼字檢查

現在，我們將建立使用 Bing 拼字檢查 API 對來源文字進行拼字檢查的方法。 拼字檢查可確保翻譯工具能夠產生正確的翻譯。 對來源文字所做的任何校正，都會在我們按下 [翻譯] 按鈕時隨著翻譯要求一起傳遞。

1. 在 Visual Studio 中，開啟 `MainWindow.xaml.cs` 的索引標籤。
2. 將下列程式碼新增至專案的 `DetectLanguage()` 方法下方：

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>經點選時翻譯文字

最後，我們必須建立在使用者介面中的 [翻譯] 按鈕經點選時所叫用的方法。

1. 在 Visual Studio 中，開啟 `MainWindow.xaml.cs` 的索引標籤。
1. 將下列程式碼新增至專案的 `CorrectSpelling()` 方法下方，並加以儲存：  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

第一個步驟是取得「來源」和「目標」語言，以及使用者在表單中輸入的文字。 如果來源語言設為 [偵測]，則會呼叫 `DetectLanguage()` 以判斷來源文字的語言。 該文字有可能屬於翻譯工具不支援的語言。 在此情況下會顯示訊息來通知使用者，並返回而不翻譯文字。

如果來源語言是英文 (不論是指定的，還是偵測到的)，請使用 `CorrectSpelling()` 來檢查文字拼字，並套用任何校正。 校正後的文字會重新新增至文字區域中，讓使用者知道已進行校正。

用來翻譯文字的程式碼看起來會很熟悉：建置 URI、建立要求、加以傳送，以及剖析回應。 JSON 陣列可能包含多個翻譯物件，但我們的應用程式只需要一個。

要求成功後，`TranslatedTextLabel.Content` 會取代為 `translation`，這會更新使用者介面以顯示翻譯的文字。

## <a name="run-your-wpf-app"></a>執行您的 WPF 應用程式

如此，您即有使用 WPF 建置的有效翻譯應用程式。 若要執行應用程式，請按一下 Visual Studio 中的 [啟動] 按鈕。

## <a name="source-code"></a>原始程式碼

您可以在 GitHub 上找到此專案的原始程式碼。

* [瀏覽原始程式碼](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Microsoft Translator 參考](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
