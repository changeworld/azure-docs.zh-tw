---
title: 將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看
description: 將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 375191bbe6f45189fba50ea927454c0ec4f64678
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934310"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>將帳單資料上傳至 Azure，並在 Azure 入口網站中加以查看

> [!IMPORTANT] 
>  在預覽期間，使用 Azure Arc 啟用的資料服務不會產生任何費用。 雖然計費系統會以端對端運作，但計費計量會設定為 $0。  如果您遵循此案例，您將會在帳單中看到目前名為「**混合式資料服務**」之服務的專案，以及稱為「microsoft」之類型的資源 **。AzureData/ `<resource type>` **。 您將能夠看到您所建立之每個資料服務 Azure Arc 的記錄，但每筆記錄都將收取 $0 費用。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>連接模式-計費資料的含意

未來將會有兩種模式，可讓您執行 Azure Arc 啟用的資料服務：

- **間接連線** -沒有 Azure 的直接連線。 資料只會透過匯出/上傳程式傳送至 Azure。 所有 Azure Arc 資料服務部署在此模式下都能以預覽形式運作。
- **直接連線** -在此模式中，會相依于已啟用 Azure Arc 的 Kubernetes 服務，以提供 Azure 與 Azure Arc 啟用的資料服務執行所在之 Kubernetes 叢集之間的直接連接。 這會啟用更多的功能，而且也可讓您使用 Azure 入口網站和 Azure CLI 來管理已啟用 Azure Arc 的資料服務，就像是在 Azure PaaS 中管理您的資料服務一樣。  此連線模式尚未提供預覽，但即將推出。

您可以閱讀更多連線 [模式](/docs/connectivity.md)之間的差異。

在間接連線模式中，會定期將帳單資料從 Azure Arc 資料控制器匯出至安全檔案，然後上傳至 Azure 並進行處理。  在即將推出的直接連線模式中，計費資料將會大約 1/小時自動傳送至 Azure，以提供接近即時的方式來查看您的服務成本。 在間接連接模式中匯出和上傳資料的程式也可以使用腳本來自動化，也可以建立服務來為您完成此作業。

## <a name="upload-billing-data-to-azure"></a>將帳單資料上傳至 Azure

若要將帳單資料上傳至 Azure，應先進行下列動作：

1. 如果您還沒有已啟用 Azure Arc 的資料服務，請建立它。 例如，建立下列其中一項：
   - [在 Azure Arc 上建立 Azure SQL 受控實例](create-sql-managed-instance.md)
   - [建立已啟用 Azure Arc 的于 postgresql 超大規模伺服器群組](create-postgresql-hyperscale-server-group.md)
1. [將資源清查、使用量資料、計量和記錄上傳至 Azure 監視器（](upload-metrics-and-logs-to-azure-monitor.md) 如果您尚未這樣做）。
1. 建立資料服務之後，請至少等候2小時，讓計費遙測收集程式能夠收集一些帳單資料。

執行下列命令以匯出帳單資料：

```console
azdata arc dc export -t usage -p usage.json
```

目前，檔案不會加密，因此您可以看到內容。 您可以在文字編輯器中隨意開啟，並查看內容的外觀。

您會發現有兩組資料： `resources` 和 `data` 。 `resources`是資料控制器、于 postgresql 超大規模伺服器群組，以及 SQL 受控實例。 `resources`資料中的記錄會捕捉到資源歷程記錄中的相關事件-建立時間、更新時間，以及刪除資源的時間。 記錄會在 `data` 每個小時內，取得指定實例可使用的核心數。

`resource`專案範例：

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

`data`專案範例：

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

執行下列命令，以將檔案上的 usage.js上傳至 Azure：

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>在 Azure 入口網站中查看帳單資料

遵循下列步驟來查看 Azure 入口網站中的帳單資料：

1. 使用特殊 URL 開啟 Azure 入口網站：  [https://aka.ms/arcdata](https://aka.ms/arcdata) 。
1. 在 [ **成本管理** ] 中螢幕類型頂端的 [搜尋] 方塊中，按一下 [成本管理] 服務。
1. 按一下左邊的 [ **成本分析** ] 索引標籤。
1. 按一下視圖頂端的 [ **依資源的成本** ] 按鈕。
1. 請確定您的範圍已設定為您的資料服務資源建立所在的訂用帳戶。
1. 在接近視圖頂端的範圍選取器旁邊的 [View] 下拉式清單中，選取 [ **依資源的成本** ]。
1. 請確定日期篩選準則設定為 **本月** 或某個其他時間範圍，以在您建立資料服務資源時的時機合理。
1. **Add filter** **Resource type**  =  `microsoft.azuredata/<data service type>` 如果您只想要篩選至一種啟用 Azure Arc 的資料服務，請按一下 [加入篩選]，依資源類型新增篩選。
1. 您現在會看到已建立並上傳至 Azure 的所有資源清單。 由於計費計量是 $0，因此您會看到成本一律是 $0。

## <a name="download-billing-data"></a>下載帳單資料

您可以直接從 Azure 入口網站下載帳單摘要資料。

1. 在 [相同 **成本分析-依據** 您依照上述指示所達成的資源類型視圖] > 視圖中，按一下頂端附近的 [下載] 按鈕。
1. 選擇您的下載檔案類型-Excel 或 CSV-然後按一下 [ **下載資料** ] 按鈕。
1. 根據選取的檔案類型，在適當的編輯器中開啟檔案。

## <a name="export-billing-data"></a>匯出帳單資料

您也可以定期建立帳單匯出工作，以自動將 **詳細** 的使用量和帳單資料匯出至 Azure 儲存體的容器。 如果您想要查看計費的詳細資料，例如帳單期間內指定實例的計費時數，這會很有用。

請遵循下列步驟來設定帳單匯出作業：

1. 按一下左邊的 [匯出]。
1. 按一下 [新增]。
1. 輸入名稱和匯出頻率，然後按 [下一步]。
1. 選擇建立新的儲存體帳戶，或建立新的儲存體帳戶，填寫表單以指定要匯出帳單資料檔案的儲存體帳戶、容器和目錄路徑，然後按 [下一步]。
1. 按一下 [建立]。

帳單資料匯出檔案大約會在4小時內提供，並會依您在建立帳單匯出作業時所指定的排程匯出。

請遵循下列步驟來查看匯出的帳單資料檔案：

您可以驗證 Azure 入口網站中的帳單資料檔案。 

> [!IMPORTANT]
> 建立帳單匯出作業之後，請等候4小時，再繼續進行下列步驟。

1. 在入口網站頂端的 [搜尋] 方塊中，輸入 **儲存體帳戶** ，然後按一下 [ **儲存體帳戶**]。
3. 按一下您在上面建立帳單匯出作業時所指定的儲存體帳戶。
4. 按一下左邊的容器。
5. 按一下您在上面建立帳單匯出作業時所指定的容器。
6. 按一下您在上面建立帳單匯出工作時所指定的資料夾。
7. 向下切入到產生的資料夾和檔案，然後按一下其中一個產生的 .csv 檔案。
8. 按一下 [下載] 按鈕，將檔案儲存到您的本機下載資料夾。
9. 使用 .csv 檔案檢視器（例如 Excel）來開啟檔案。
10. 篩選結果，只顯示具有**資源類型**的資料列  =  `Microsoft.AzureData/<data service resource type` 。
11. 您將會在 UsageQuantity 資料行中的目前24小時內，看到實例使用的時數。
