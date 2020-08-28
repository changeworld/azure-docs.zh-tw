---
title: 教學課程-升級 Azure Service Fabric 網狀應用程式
description: 本教學課程是系列中的第四部分，說明如何直接從 Visual Studio 升級 Azure Service Fabric Mesh 應用程式。
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 876359b45daa280a1f5701f2490ce505327f309b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010999"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>教學課程：了解如何使用 Visual Studio 升級 Service Fabric 應用程式

本教學課程是系列中的第四部分，說明如何直接從 Visual Studio 升級 Azure Service Fabric Mesh 應用程式。 升級將包含程式碼更新和組態更新。 您會發現 Visual Studio 中的升級和發佈步驟是相同的。

在本教學課程中，您將了解如何：
> [!div class="checklist"]
> * 使用 Visual Studio 升級 Service Fabric Mesh 服務

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * [在 Visual Studio 中建立 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [偵錯在本機開發叢集中執行的 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [部署 Service Fabric Mesh 應用程式](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * 升級 Service Fabric Mesh 應用程式
> * [清除 Service Fabric Mesh 資源](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>必要條件

開始進行本教學課程之前：

* 如果您尚未部署待辦事項應用程式，請依照[發佈 Service Fabric Mesh Web 應用程式](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)中的指示進行操作。

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>使用 Visual Studio 升級 Service Fabric Mesh 服務

本文說明如何升級應用程式內的微服務。 在此範例中，我們將修改 `WebFrontEnd` 服務以顯示工作類別，並增加其提供的 CPU 數量。 然後，升級已部署的服務。

## <a name="modify-the-config"></a>修改組態

當您建立 Service Fabric Mesh 應用程式時，Visual Studio 會為每個部署環境 (雲端和本機) 新增 **parameters.yaml** 檔案。 在這些檔案中，您可以定義參數和其值，然後從 Mesh 的 *.yaml 檔案 (例如，service.yaml 或 network.yaml) 來參照。  Visual Studio 會為您提供一些變數，例如，服務可以使用多少 CPU。

由於預期 **WebFrontEnd** 服務的使用量會變大，我們會更新 `WebFrontEnd_cpu` 參數以將 CPU 資源更新為 `1.5`。

1. 在 **todolistapp** 專案的 [環境]**** > [雲端]**** 底下，開啟 **parameters.yaml** 檔案。 將 `WebFrontEnd_cpu` 值修改為 `1.5`。 最好在參數名稱開頭加上服務名稱 `WebFrontEnd_`，以便此參數能與套用至不同服務的同名參數有所區別。

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. 在 [WebFrontEnd]**** > [服務資源]**** 底下，開啟 **WebFrontEnd** 專案的 **service.yaml** 檔案。

    請注意，在 `resources:` 區段中，`cpu:` 會設定為 `"[parameters('WebFrontEnd_cpu')]"`。 如果正在為雲端建立專案，的值會 `'WebFrontEnd_cpu` 取自**環境**  >  **雲端**  >  **參數 yaml**檔案，而且將會是 `1.5` 。 如果正在建立專案以在本機執行，則會從**環境**的本機 yaml 檔案取得值  >  **Local**  >  **parameters.yaml** ，而且將會是 ' 0.5 '。

> [!Tip]
> 根據預設，系統會使用 profile.yaml 檔案的對等參數檔案，來提供該 profile.yaml 檔案的值。
> 例如，[環境] > [雲端] > parameters.yaml 會提供 [環境] > [雲端] > profile.yaml 的參數值。
>
> 您可以藉由將下列內容新增至 yaml 檔案來覆寫此內容： `parametersFilePath=”relative or full path to the parameters file”` 例如， `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` 或 `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>修改模型

若要引進程式碼變更，請將 `Category` 屬性新增至 `ToDoItem.cs` 檔案中的 `ToDoItem` 類別。

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

然後更新 `Load()` 方法，在相同檔案中，將類別設定為預設的字串：

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>修改服務

`WebFrontEnd` 專案是一個 ASP.NET Core 應用程式，具有會在待辦事項清單項目中顯示的網頁。 在 `WebFrontEnd` 專案中，開啟 `Index.cshtml` 並新增下列兩行，以顯示工作的類別：

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

建立並執行應用程式，以驗證您是否在列出工作的網頁中看到新類別資料行。

## <a name="upgrade-the-app-from-visual-studio"></a>從 Visual Studio 升級應用程式

無論您是要升級程式碼，還是要升級組態 (在此案例中，我們兩者皆進行)，都請升級 Azure 上的 Service Fabric Mesh 應用程式，方法是在 Visual Studio 中的 [todolistapp]**** 按一下滑鼠右鍵，然後選取 [發佈...]****

接著，您將會看見 [發佈 Service Fabric 應用程式] 對話方塊。

使用 [目標寫照]**** 下拉式清單來選取要用於此部署的 profile.yaml 檔案。 我們要升級雲端中的應用程式，因此我們在下拉式清單中選取 **cloud.yaml**，這會使用該檔案中定義的 `WebFrontEnd_cpu` 值 (即 1.0)。

![Visual Studio 的 Service Fabric Mesh 發行對話方塊](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

選取您的 Azure 帳戶和訂用帳戶。 將 [位置]**** 設定為最初將待辦事項應用程式發佈到 Azure 時所使用的位置。 本文使用**美國東部**。

將 [資源群組]**** 設定為最初將待辦事項應用程式發佈至 Azure 時所使用的資源群組。

將 [Azure Container Registry]**** 設定為最初將待辦事項應用程式發佈至 Azure 時所建立的 Azure Container Registry 名稱。

在 [發佈] 對話方塊中，按下 [發佈]**** 按鈕，以升級 Azure 上的待辦事項應用程式。

在 Visual Studio 的 [輸出]**** 視窗中，選取 [Service Fabric 工具]**** 窗格，藉以監視升級的進度。 

建立映像並推送至 Azure Container Registry 之後，[適用狀態]**** 連結會出現在輸出中，您只要按一下就能在 Azure 入口網站中監視部署。

一旦升級完成之後，**Service Fabric 工具**輸出將會以 URL 形式來顯示應用程式的 IP 位址和連接埠。

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

開啟網頁瀏覽器，並瀏覽至 URL 以檢視在 Azure 中執行的網站。 現在，您應該會看到包含類別資料行的網頁。

## <a name="next-steps"></a>後續步驟

在教學課程的這個部分中，您已了解：
> [!div class="checklist"]
> * 如何使用 Visual Studio 升級 Service Fabric Mesh 應用程式

前進到下一個教學課程：
> [!div class="nextstepaction"]
> [清除 Service Fabric Mesh 資源](service-fabric-mesh-tutorial-cleanup-resources.md)