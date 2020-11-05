---
title: 使用 .NET 中的 AutoML ONNX 模型進行預測
description: 瞭解如何在 .NET 中使用 AutoML ONNX 模型搭配 ML.NET 來進行預測
titleSuffix: Azure Machine Learning
author: luisquintanilla
ms.author: luquinta
ms.date: 10/30/2020
ms.topic: conceptual
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.custom: how-to, automl
ms.openlocfilehash: 4fb147dc5c57c3a98607a025f566fa583bf87460
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358808"
---
# <a name="make-predictions-with-an-automl-onnx-model-in-net"></a>使用 .NET 中的 AutoML ONNX 模型進行預測

在本文中，您將瞭解如何使用自動化 ML (AutoML) Open Neural Network Exchange (ONNX) 模型，在具有 ML.NET 的 c # .NET Core 主控台應用程式中進行預測。

[ML.NET](/dotnet/machine-learning/) 是適用于 .net 生態系統的開放原始碼、跨平臺的機器學習架構，可讓您使用 c # 或 F # 中的程式碼優先方法，以及透過 [模型](/dotnet/machine-learning/automate-training-with-model-builder) 產生器和 [ML.NET CLI](/dotnet/machine-learning/automate-training-with-cli)等低程式碼工具，來定型和取用自訂機器學習模型。 此架構也是可擴充的，可讓您運用其他熱門的機器學習架構，例如 TensorFlow 和 ONNX。

ONNX 是 AI 模型的開放原始碼格式。 ONNX 支援架構間的互通性。 這表示您可以在許多熱門的機器學習架構（例如 PyTorch）中訓練模型、將它轉換成 ONNX 格式，然後在不同的架構（例如 ML.NET）中使用 ONNX 模型。 若要深入了解，請前往 [ONNX 網站](https://onnx.ai/)。

## <a name="prerequisites"></a>先決條件

- [.NET Core SDK 3.1 或更高版本](https://dotnet.microsoft.com/download)
- 文字編輯器或 IDE (例如 [Visual Studio](https://visualstudio.microsoft.com/vs/) 或 [Visual Studio Code](https://code.visualstudio.com/Download)) 
- ONNX 模型。 若要瞭解如何定型 AutoML ONNX 模型，請參閱下列 [銀行行銷分類筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。
- [Netron](https://github.com/lutzroeder/netron) (選擇性)

## <a name="create-a-c-console-application"></a>建立 C# 主控台應用程式

在此範例中，您會使用 .NET Core CLI 來建立您的應用程式，但您可以使用 Visual Studio 來執行相同的工作。 深入瞭解 [.NET Core CLI](/dotnet/core/tools/)。

1. 開啟終端機，然後建立新的 c # .NET Core 主控台應用程式。 在此範例中，應用程式的名稱是 `AutoMLONNXConsoleApp` 。 目錄是以與您的應用程式內容相同的名稱建立。

    ```dotnetcli
    dotnet new console -o AutoMLONNXConsoleApp
    ```

1. 在終端機中，流覽至 *AutoMLONNXConsoleApp* 目錄。

    ```bash
    cd AutoMLONNXConsoleApp
    ```

## <a name="add-software-packages"></a>新增軟體套件

1. 使用 .NET Core CLI 來安裝 **Microsoft.ML** 、 **OnnxRuntime** 和 **OnnxTransformer** NuGet 套件中。

    ```dotnetcli
    dotnet add package Microsoft.ML
    dotnet add package Microsoft.ML.OnnxRuntime
    dotnet add package Microsoft.ML.OnnxTransformer
    ```

    這些套件包含在 .NET 應用程式中使用 ONNX 模型所需的相依性。 ML.NET 提供使用 [ONNX 運行](https://github.com/Microsoft/onnxruntime) 時間進行預測的 API。

1. 開啟 *Program.cs* 檔案，並 `using` 在頂端加入下列語句，以參考適當的封裝。

    ```csharp
    using System.Linq;
    using Microsoft.ML;
    using Microsoft.ML.Data;
    using Microsoft.ML.Transforms.Onnx;
    ```

## <a name="add-a-reference-to-the-onnx-model"></a>加入 ONNX 模型的參考

主控台應用程式存取 ONNX 模型的方式，就是將它新增至組建輸出目錄。  若要深入瞭解 MSBuild 一般專案，請參閱 [msbuild 指南](/visualstudio/msbuild/common-msbuild-project-items)。

在您的應用程式中新增 ONNX 模型檔案的參考

1. 將您的 ONNX 模型複製到應用程式的 *AutoMLONNXConsoleApp* 根目錄。
1. 開啟 *AutoMLONNXConsoleApp .csproj* 檔案，並在節點內新增下列內容 `Project` 。

    ```xml
    <ItemGroup>
        <None Include="automl-model.onnx">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ```

    在此情況下，ONNX 模型檔的名稱為 *automl ONNX* 。

1. 開啟 *Program.cs* 檔案，並在類別內新增下列程式程式碼 `Program` 。

    ```csharp
    static string ONNX_MODEL_PATH = "automl-model.onnx";
    ```

## <a name="initialize-mlcontext"></a>初始化 MLCoNtext

在 `Main` 您類別的方法內 `Program` ，建立的新實例 [`MLContext`](xref:Microsoft.ML.MLContext) 。

```csharp
MLContext mlContext = new MLContext();
```

[`MLContext`](xref:Microsoft.ML.MLContext)類別是所有 ML.NET 作業的起點，而初始化則會 `mlContext` 建立可跨模型生命週期共用的新 ML.NET 環境。 它在概念上類似于 Entity Framework 中的 DbCoNtext。

## <a name="define-the-model-data-schema"></a>定義模型資料架構

您的模型需要特定格式的輸入和輸出資料。 ML.NET 可讓您透過類別定義資料的格式。 有時您可能已經知道該格式的樣子。 在不知道資料格式的情況下，您可以使用 Netron 之類的工具來檢查您的 ONNX 模型。

此範例中使用的模型會使用來自 NYC TLC 計程車行程資料集的資料。 您可以在下方看到資料的範例：

|vendor_id|rate_code|passenger_count|trip_time_in_secs|trip_distance|payment_type|fare_amount|
|---|---|---|---|---|---|---|
|VTS|1|1|1140|3.75|Crd|15.5|
|VTS|1|1|480|2.72|Crd|10.0|
|VTS|1|1|1680|7.8|CSH|26.5|

### <a name="inspect-the-onnx-model-optional"></a>檢查 ONNX 模型 (選擇性) 

使用 Netron 之類的工具來檢查模型的輸入和輸出。

1. 開啟 Netron。
1. 在頂端功能表列中，選取 [檔案] **> 開啟** ，然後使用 [檔案瀏覽器] 來選取您的模型。
1. 您的模型隨即開啟。 例如， *automl 模型 onnx* 模型的結構如下所示：

    :::image type="content" source="media/how-to-use-automl-onnx-model-dotnet/netron-automl-onnx-model.png" alt-text="Netron AutoML ONNX 模型":::

1. 選取圖表底部的最後一個節點 (`variable_out1` 在此案例中，) 顯示模型的中繼資料。 提要欄位上的輸入和輸出會顯示模型的預期輸入、輸出和資料類型。 您可以使用這項資訊來定義模型的輸入和輸出架構。

### <a name="define-model-input-schema"></a>定義模型輸入架構

`OnnxInput`使用 *Program.cs* 檔案中的下列屬性，建立名為的新類別。

```csharp
public class OnnxInput
{
    [ColumnName("vendor_id")]
    public string VendorId { get; set; }

    [ColumnName("rate_code"),OnnxMapType(typeof(Int64),typeof(Single))]
    public Int64 RateCode { get; set; }

    [ColumnName("passenger_count"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 PassengerCount { get; set; }

    [ColumnName("trip_time_in_secs"), OnnxMapType(typeof(Int64), typeof(Single))]
    public Int64 TripTimeInSecs { get; set; }

    [ColumnName("trip_distance")]
    public float TripDistance { get; set; }

    [ColumnName("payment_type")]
    public string PaymentType { get; set; }
}
```

每個屬性都會對應到資料集中的資料行。 屬性會進一步批註屬性。

[`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute)屬性可讓您指定在運算元據時，ML.NET 應該如何參考資料行。 例如，雖然 `TripDistance` 屬性遵循標準的 .net 命名慣例，但模型只知道稱為的資料行或功能 `trip_distance` 。 為了解決此命名差異， [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 屬性會 `TripDistance` 依名稱將屬性對應至資料行或功能 `trip_distance` 。
  
針對數值，ML.NET 只會在實 [`Single`](xref:System.Single) 數值型別上運作。 不過，某些資料行的原始資料類型是整數。 屬性會在 [`OnnxMapType`](xref:Microsoft.ML.Transforms.Onnx.OnnxMapTypeAttribute) ONNX 和 ML.NET 之間對應類型。

若要深入瞭解資料屬性，請參閱 [ML.NET load data guide](/dotnet/machine-learning/how-to-guides/load-data-ml-net)。

### <a name="define-model-output-schema"></a>定義模型輸出架構

處理資料之後，它會產生特定格式的輸出。 定義您的資料輸出架構。 `OnnxOutput`使用 *Program.cs* 檔案中的下列屬性，建立名為的新類別。

```csharp
public class OnnxOutput
{
    [ColumnName("variable_out1")]
    public float[] PredictedFare { get; set; }
}
```

類似于 `OnnxInput` ，請使用 [`ColumnName`](xref:Microsoft.ML.Data.ColumnNameAttribute) 屬性將輸出對應 `variable_out1` 至更具描述性的名稱 `PredictedFare` 。

## <a name="define-a-prediction-pipeline"></a>定義預測管線

ML.NET 中的管線通常是一系列的連鎖轉換，可操作輸入資料來產生輸出。 若要深入瞭解資料轉換，請參閱 [ML.NET 資料轉換指南](/dotnet/machine-learning/resources/transforms)。

1. 在類別內建立名為的新方法 `GetPredictionPipeline` `Program`

    ```csharp
    static ITransformer GetPredictionPipeline(MLContext mlContext)
    {

    }
    ```

1. 定義輸入和輸出資料行的名稱。 將下列程式碼新增至 `GetPredictionPipeline` 方法內。

    ```csharp
    var inputColumns = new string []
    {
        "vendor_id", "rate_code", "passenger_count", "trip_time_in_secs", "trip_distance", "payment_type"
    };

    var outputColumns = new string [] { "variable_out1" };
    ```

1. 定義您的管線。 [`IEstimator`](xref:Microsoft.ML.IEstimator%601)提供管線之作業、輸入和輸出架構的藍圖。

    ```csharp
    var onnxPredictionPipeline =
        mlContext
            .Transforms
            .ApplyOnnxModel(
                outputColumnNames: outputColumns,
                inputColumnNames: inputColumns,
                ONNX_MODEL_PATH);
    ```

    在此情況下， [`ApplyOnnxModel`](xref:Microsoft.ML.OnnxCatalog.ApplyOnnxModel%2A) 是管線中唯一的轉換，它會採用輸入和輸出資料行的名稱，以及 ONNX 模型檔的路徑。

1. [`IEstimator`](xref:Microsoft.ML.IEstimator%601)只會定義要套用至資料的一組作業。 在您的資料上操作的是所謂的 [`ITransformer`](xref:Microsoft.ML.ITransformer) 。 `Fit`您可以使用方法，從建立一個 `onnxPredictionPipeline` 。

    ```csharp
    var emptyDv = mlContext.Data.LoadFromEnumerable(new OnnxInput[] {});

    return onnxPredictionPipeline.Fit(emptyDv);
    ```

    [`Fit`](xref:Microsoft.ML.IEstimator%601.Fit%2A)方法需要 [`IDataView`](xref:Microsoft.ML.IDataView) 作為輸入來執行的作業。 [`IDataView`](xref:Microsoft.ML.IDataView)是使用表格格式來表示 ML.NET 中資料的一種方式。 因為在此情況下，管線只用于預測，所以您可以提供空白， [`IDataView`](xref:Microsoft.ML.IDataView) 以提供 [`ITransformer`](xref:Microsoft.ML.ITransformer) 必要的輸入和輸出架構資訊。 [`ITransformer`](xref:Microsoft.ML.ITransformer)然後會傳回檔整，以便在您的應用程式中進一步使用。

    > [!TIP]
    > 在此範例中，管線是在相同的應用程式中定義和使用。 不過，建議您使用個別的應用程式來定義和使用您的管線來進行預測。 在 ML.NET 中，您的管線可以進行序列化和儲存，以便進一步用於其他 .NET 終端使用者應用程式。 ML.NET 支援各種不同的部署目標，例如桌面應用程式、web 服務、WebAssembly 應用程式 * 等等。 若要深入瞭解如何儲存管線，請參閱 [ML.NET 儲存和載入定型的模型指南](/dotnet/machine-learning/how-to-guides/save-load-machine-learning-models-ml-net)。
    >
    > * 僅 .NET Core 5 或更新版本才支援 WebAssembly

1. 在 `Main` 方法中， `GetPredictionPipeline` 使用必要的參數來呼叫方法。

    ```csharp
    var onnxPredictionPipeline = GetPredictionPipeline(mlContext);
    ```

## <a name="use-the-model-to-make-predictions"></a>使用模型進行預測

現在您已有管線，現在可以使用它來進行預測。 ML.NET 提供便利的 API，可在名為的單一資料實例上進行預測 [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) 。

1. 在 `Main` 方法中， [`PredictionEngine`](xref:Microsoft.ML.PredictionEngine%602) 使用方法建立 [`CreatePredictionEngine`](xref:Microsoft.ML.ModelOperationsCatalog.CreatePredictionEngine%2A) 。

    ```csharp
    var onnxPredictionEngine = mlContext.Model.CreatePredictionEngine<OnnxInput, OnnxOutput>(onnxPredictionPipeline);
    ```

1. 建立測試資料輸入。

    ```csharp
    var testInput = new OnnxInput
    {
        VendorId = "CMT",
        RateCode = 1,
        PassengerCount = 1,
        TripTimeInSecs = 1271,
        TripDistance = 3.8f,
        PaymentType = "CRD"
    };
    ```

1. 使用， `predictionEngine` 根據使用方法的新資料進行預測 `testInput` [`Predict`](xref:Microsoft.ML.PredictionEngineBase%602.Predict%2A) 。

    ```csharp
    var prediction = onnxPredictionEngine.Predict(testInput);
    ```

1. 將您的預測結果輸出至主控台。

    ```csharp
    Console.WriteLine($"Predicted Fare: {prediction.PredictedFare.First()}");
    ```

1. 使用 .NET Core CLI 來執行您的應用程式。

    ```dotnetcli
    dotnet run
    ```

    結果看起來應該類似下列輸出：

    ```text
    Predicted Fare: 15.621523
    ```

若要深入瞭解如何在 ML.NET 中進行預測，請參閱 [使用模型進行預測指南](/dotnet/machine-learning/how-to-guides/machine-learning-model-predictions-ml-net)。

## <a name="next-steps"></a>後續步驟

- [將您的模型部署為 ASP.NET Core Web API](/dotnet/machine-learning/how-to-guides/serve-model-web-api-ml-net)
- [將您的模型部署為無伺服器 .NET Azure 函數](/dotnet/machine-learning/how-to-guides/serve-model-serverless-azure-functions-ml-net)