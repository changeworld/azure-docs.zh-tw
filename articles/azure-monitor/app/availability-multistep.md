---
title: 使用多步驟 Web 測試進行監視 - Azure Application Insights
description: 使用 Azure Application Insights 設定多步驟 Web 測試，以監視您的 Web 應用程式
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 2df8b7450423c901665090608da83f68b43b30e5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024768"
---
# <a name="multi-step-web-tests"></a>多重步驟 Web 測試

您可以透過多步驟 Web 測試，監視一系列記錄的 URL 以及與網站的互動。 本文將逐步引導您完成使用 Visual Studio Enterprise 建立多重步驟 Web 測試的程式。

> [!NOTE]
> 多步驟 Web 測試取決於 Visual Studio Webtest 檔案。 已[宣佈](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) Visual Studio 2019 會是具有 Webtest 功能的最後一個版本。 請務必瞭解，雖然不會新增任何新功能，但目前仍支援 Visual Studio 2019 中的 Webtest 功能，並且會在產品的支援生命週期中繼續受到支援。 Azure 監視器產品小組已解決多步驟可用性測試的相關問題，如[這裡](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)所示。  
> </br>
> [Azure Government](../../azure-government/index.yml) 雲端中**不支援**多步驟 Web 測試。


## <a name="pre-requisites"></a>必要條件

* Visual Studio 2017 Enterprise 或更新版本。
* Visual Studio Web 效能與負載測試工具。

找出必要的測試控管。 啟動 [Visual Studio 安裝程式] > [個別元件] > [調試和測試] > [Web 效能和負載測試工具]。

![Visual Studio 安裝程式 UI 的螢幕擷取畫面，其中已選取個別元件，而且 Web 效能和負載測試工具項目旁邊有核取方塊](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 多步驟 Web 測試有相關聯的額外成本。 若要深入瞭解，請參閱[官方價格指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="record-a-multi-step-web-test"></a>記錄多重步驟的 Web 測試 

> [!WARNING]
> 我們不再建議使用多步驟錄製器。 錄製器是針對具有基本互動的靜態 HTML 網頁所開發，並不提供現代化網頁的功能體驗。

如需建立 Visual Studio Web 測試的指引，請參閱[官方 Visual Studio 2019 文件](/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)。

## <a name="upload-the-web-test"></a>上傳 Web 測試

1. 在 [可用性] 窗格的 Application Insights 入口網站中，選取 [建立測試] > [測試類型] > [多步驟 Web 測試]。

2. 設定測試位置、頻率及警示參數。

### <a name="frequency--location"></a>頻率和位置

|設定| 說明
|----|----|----|
|**測試頻率**| 設定從每個測試位置執行測試的頻率。 預設頻率為 5 分鐘且有五個測試位置，則您的網站平均每一分鐘會執行測試。|
|**測試位置**| 是我們的伺服器將 Web 要求傳送至您的 URL 的位置。 **建議的測試位置數目下限為五個**，以確保您可以區分網站問題與網路問題。 您最多可以選取 16 個位置。

### <a name="success-criteria"></a>成功準則

|設定| 說明
|----|----|----|
| **測試逾時** |降低此值可收到有關回應變慢的警示。 如果未在這段時間內收到您網站的回應，則測試會視為失敗。 如果已選取 [剖析相依要求] ，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。|
| **HTTP 回應** | 視為成功的傳回狀態碼。 200 是表示已傳回標準 Web 網頁的代碼。|
| **內容比對** | 字串，例如「歡迎！」 我們會測試每個回應中的區分大小寫完全相符。 必須是單純字串，不含萬用字元。 別忘了，如果頁面內容變更，則可能需要更新。 **內容相符僅支援英文字元** |

### <a name="alerts"></a>警示

|設定| 說明
|----|----|----|
|**近乎即時 (預覽)** | 我們建議使用近乎即時的警示。 您的可用性測試建立後，此類型的警示會設定完成。  |
|**傳統** | 我們不再建議使用傳統警示來進行新的可用性測試。|
|**警示位置閾值**|建議至少為位置數的 3/5。 警示位置閾值與測試位置數目之間的最佳關聯性，就是**警示位置閾值** = **測試位置數目 - 2 (最少五個測試位置)。**|

## <a name="configuration"></a>組態

### <a name="plugging-time-and-random-numbers-into-your-test"></a>在您的測試中插入時間和隨機數字

假設您要測試的工具會從外部來源取得與時間相關的資料 (例如股票)。 當您記錄 Web 測試時，您必須使用特定的時間，但您將它們設為測試的參數：StartTime 和 EndTime。

![我的絕佳股票應用程式螢幕擷取畫面](./media/availability-multistep/app-insights-72webtest-parameters.png)

當您執行測試時，希望 EndTime 永遠為目前時間，而 StartTime 為 15 分鐘前。

Web 測試日期時間外掛程式提供處理參數化時間的方式。

1. 針對您想要的每個變數參數值，各加入一個 Web 測試外掛程式。 在 Web 測試工具列中，選擇 [加入 Web 測試外掛程式] 。
    
    ![加入 Web 測試外掛程式](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    在此範例中，我們會使用兩個日期時間外掛程式執行個體。 一個執行個體設定為 "15 minutes ago"，另一個則設定為 "now"。

2. 開啟每個外掛程式的屬性。 為屬性命名，然後將它設為使用目前時間。 對其中一個，設定 [加入分鐘] = -15。

    ![內容參數](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 測試參數中，使用 {{plug-in name}} 來參考外掛程式名稱。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

現在將您的測試上傳至入口網站。 在每次執行測試時，它會使用動態值。

### <a name="dealing-with-sign-in"></a>處理登入

如果使用者登入您的應用程式，您有許多模擬登入的選項，以便在登入後方測試頁面。 您使用的方法取決於應用程式所提供的安全性類型。

在所有情況下，您應該只為了測試用途在您的應用程式中建立帳戶。 可能的話，限制此測試帳戶的權限，讓 Web 測試不可能影響實際使用者。

**簡單的使用者名稱和密碼**以一般方式記錄 Web 測試。 先刪除 Cookie。

**SAML 驗證**

|屬性名稱| 描述|
|----|-----|
| 對象 URI | SAML 權杖的對象 URI。  這是存取控制服務 (ACS) 的 URI - 包括 ACS 命名空間和主機名稱。 |
| 憑證密碼 | 將授與內嵌私密金鑰存取權之用戶端憑證的密碼。 |
| 用戶端憑證  | 具有 Base64 編碼格式之私密金鑰的用戶端憑證值。 |
| 名稱識別碼 | Token 的名稱識別碼 |
| 不晚於 | 權杖將持續有效的時間範圍。  預設值是 5 分鐘。 |
| 生效時間 | 在過去建立的權杖將持續有效的時間範圍 (以處理時間扭曲)。  預設值為 (負) 5分鐘。 |
| 目標內容參數名稱 | 將接收所產生之判斷提示的內容參數。 |


**用戶端密碼**如果應用程式的登入路由牽涉到用戶端密碼，請使用此路由。 Azure Active Directory (AAD) 是可提供用戶端密碼登入的服務範例。 在 AAD 中，用戶端密碼是應用程式金鑰。

以下是使用應用程式金鑰之 Azure Web 應用程式的 Web 測試範例︰

![範例螢幕擷取畫面](./media/availability-multistep/client-secret.png)

從使用用戶端密鑰 (AppKey) 的 AAD 取得權杖。
從回應中擷取持有人權杖。
使用授權標頭中的持有人權杖呼叫 API。
請確定 Web 測試是實際的用戶端 (也就是，在 AAD 中具有自己的應用程式) 並使用其 clientId + appkey。 您測試中的服務在 AAD 中也有自己的應用程式︰此應用程式的 appID URI 會反映於 [資源] 欄位中的 Web 測試。

### <a name="open-authentication"></a>開放驗證
使用您的 Microsoft 或 Google 帳戶登入即是開放驗證的範例。 許多使用 OAuth 的應用程式都提供替代用戶端密碼，第一個技巧就是調查該可能性。

如果您的測試必須使用 OAuth 登入，則常用的方式是：

使用 Fiddler 等工具來檢查網頁瀏覽器、驗證網站及您的應用程式之間的流量。
使用不同的電腦或瀏覽器，或以較長時間間隔 (讓權杖過期) 執行兩次以上的登入。
藉由比較不同的工作階段，識別從驗證網站傳回的權杖，登入之後此權杖會傳遞至您的應用程式伺服器。
使用 Visual Studio 記錄 Web 測試。
將權杖參數化，當驗證器傳回權杖時設定參數，然後在查詢網站時使用參數。 (Visual Studio 會嘗試將測試參數化，但不會正確地將權杖參數化。)

## <a name="troubleshooting"></a>疑難排解

專用的[疑難排解文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [可用性警示](availability-alerts.md)
* [URL ping Web 測試](monitor-web-app-availability.md)
