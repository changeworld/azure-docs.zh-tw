---
title: 使用 CLI 建立 Internet Analyzer 測試 | Microsoft Docs
description: 在此文章中，了解如何建立您的第一個 Internet Analyzer 測試。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184273"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>使用 CLI 建立 Internet Analyzer 測試 (預覽)

有兩種方式可以建立 Internet Analyzer 資源，分別是使用 [Azure 入口網站](internet-analyzer-create-test-portal.md)或使用 CLI。 此節可協助您使用我們的 CLI 體驗，建立新的 Azure Internet Analyzer 資源。 


> [!IMPORTANT]
> 此公開預覽版是在沒有服務等級協定的情況下提供，不得用於生產工作負載。 可能不支援特定功能、可能已經限制功能，或者可能無法在所有 Azure 位置提供使用。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="before-you-begin"></a>開始之前

公開預覽可供全球各區域使用，不過，在預覽期間，資料儲存體限制為*美國西部 2*。

## <a name="object-model"></a>物件模型
Internet Analyzer CLI 會公開下列類型的資源：
* **測試**：測試會比較一段時間後，兩個網際網路端點 (A 與 B) 的終端使用者效能。
* **設定檔**：測試是在 Internet Analyzer 設定檔下建立的。 設定檔允許將相關的測試分組；單一設定檔可以包含一或多個測試。
* **預先設定的端點**：我們已使用各種不同的設定 (區域、加速技術等) 來設定端點。 您可以在測試中使用這些任何預先設定的端點。
* **計分卡**：計分卡可提供測量結果快速且有意義的摘要。 請參閱[解譯您的計分卡](internet-analyzer-scorecard.md)。
* **時間序列**：時間序列會顯示計量隨著時間變更的方式。

## <a name="profile-and-test-creation"></a>設定檔和測試建立
1. 依照 **Azure Internet Analyzer 常見問題集**中的[如何參與預覽？](internet-analyzer-faq.md)指示，取得 Internet Analyzer 預覽存取。
2. [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
3. 執行 `login` 命令以啟動 CLI 工作階段：
    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。
    否則，請在 https://aka.ms/devicelogin 中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

4. 請在瀏覽器中使用您的帳戶認證登入。

5. 選取您的訂用帳戶識別碼 (必須已獲授權可存取 Internet Analyzer 公開預覽)。

    登入之後，您會看到一份與 Azure 帳戶相關聯的訂用帳戶清單。 包含 `isDefault: true` 的訂用帳戶資訊是目前在登入之後會啟動的訂用帳戶。 若要選取另一個訂用帳戶，請使用 [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) 命令搭配訂用帳戶識別碼以進行切換。 如需訂用帳戶選項的詳細資訊，請參閱[使用多個 Azure 訂用帳戶](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)。

    非互動式的登入方法有很多種，[透過 Azure CLI 登入](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)中有詳細說明。

6. **[選擇性]** 建立新的 Azure 資源群組：
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. 安裝 Azure CLI Internet Analyzer 延伸模組：
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. 建立新的 Internet Analyzer 設定檔：
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. 列出新建立的設定檔可用的所有預先設定端點：
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. 在新建立的 InternetAnalyzer 設定檔底下，建立新的測試：
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    上述命令假設 `www.contoso.com` 和 `www.microsoft.com` 都在自訂路徑下裝載一個像素的影像 ([trans.gif](https://fpc.msedge.net/apc/trans.gif))。 如果未明確指定物件路徑，Internet Analyzer 預設會使用 `/apc/trans.gif` 作為物件路徑，這是預先設定的端點裝載一個像素的影像所在。 另請注意，您不需要指定結構描述 (https/http)；Internet Analyzer 僅支援 HTTPS 端點，因此會假設採用 HTTPS。

11. 新的測試應該會出現在 Internet Analyzer 設定檔底下：
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    範例輸出︰
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. 若要開始產生測量，測試的 **scriptFileUri** 所指向的 JavaScript 檔案必須內嵌在您的 Web 應用程式中。 您可以在[內嵌 Internet Analyzer 用戶端](internet-analyzer-embed-client.md)頁面上找到特定的指示。

13. 您可以透過追蹤其「狀態」值來監視測試的進度：
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. 您可以透過產生時間序列或計分卡來檢查測試收集的結果：
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>後續步驟

* 瀏覽 [Internet Analyzer CLI 參考](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest)，以取得支援的命令和使用方式範例的完整清單。
* 請參閱 [Internet Analyzer 常見問題集](internet-analyzer-faq.md)。
* 深入了解如何內嵌 [Internet Analyzer 用戶端](internet-analyzer-embed-client.md)以及如何建立[自訂端點](internet-analyzer-custom-endpoint.md)。 
