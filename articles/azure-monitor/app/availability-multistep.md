---
title: 使用多步驟 Web 測試進行監視 - Azure 應用程式見解
description: 設置多步驟 Web 測試，以便使用 Azure 應用程式見解監視 Web 應用程式
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655935"
---
# <a name="multi-step-web-tests"></a>多重步驟 Web 測試

您可以通過多步驟 Web 測試監視錄製的 URL 序列和與網站的交互。 本文將引導您完成使用 Visual Studio 企業版創建多步驟 Web 測試的過程。

> [!NOTE]
> 多步驟 Web 測試依賴于 Visual Studio Web 測試檔案。 據[宣佈](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)，Visual Studio 2019 將是最後一個具有 Web 測試功能的版本。 請務必瞭解，雖然不會添加新功能，但 Visual Studio 2019 中的 Web 測試功能目前仍受支援，並且將繼續在產品的支援生命週期內提供支援。 Azure 監視器產品團隊[已在此處](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)解決了有關多步驟可用性測試的未來問題。  

## <a name="pre-requisites"></a>必要條件

* 視覺工作室 2017 企業或更大.
* 視覺化工作室 Web 性能和負載測試工具。

找到測試控管的先決條件。 啟動**視覺化工作室安裝程式** > **單個元件** > **調試和測試** > **Web 性能和負載測試工具**。

![Visual Studio 安裝程式 UI 的螢幕截圖，其中選擇了單個元件，並在專案旁邊選中了 Web 性能和負載測試工具的核取方塊](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> 多步驟 Web 測試具有與其相關的額外費用。 要瞭解更多資訊，請參閱[官方定價指南](https://azure.microsoft.com/pricing/details/application-insights/)。

## <a name="record-a-multi-step-web-test"></a>記錄多步驟 Web 測試 

> [!WARNING]
> 我們不再建議使用多步驟記錄器。 記錄器是為具有基本交互的靜態 HTML 頁面開發的，不為現代網頁提供功能體驗。

有關創建 Visual Studio 網路測試的指導，請參閱[官方 Visual Studio 2019 文檔](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)。

## <a name="upload-the-web-test"></a>上傳 Web 測試

1. 在"可用性"窗格上的"應用程式見解"門戶中，選擇 **"創建測試** > **測試類型** > **多步驟 Web 測試**"。

2. 設置測試位置、頻率和警報參數。

### <a name="frequency--location"></a>頻率&位置

|設定| 說明
|----|----|----|
|**測試頻率**| 設定從每個測試位置執行測試的頻率。 預設頻率為 5 分鐘且有五個測試位置，則您的網站平均每一分鐘會執行測試。|
|**測試位置**| 是我們的伺服器向您的 URL 發送 Web 請求的地方。 **我們推薦的測試位置最少為 5**個，以確保您可以將網站中的問題與網路問題區分開來。 您最多可以選取 16 個位置。

### <a name="success-criteria"></a>成功準則

|設定| 說明
|----|----|----|
| **測試超時** |降低此值可收到有關回應變慢的警示。 如果未在這段時間內收到您網站的回應，則測試會視為失敗。 如果已選取 [剖析相依要求] ****，則必須在這段時間內收到所有映像、樣式檔、指令碼和其他相依資源。|
| **HTTP 回應** | 視為成功的傳回狀態碼。 200 是表示已傳回標準 Web 網頁的代碼。|
| **內容相符** | 一個字串，像"歡迎！ 我們會測試每個回應中的區分大小寫完全相符。 必須是單純字串，不含萬用字元。 別忘了，如果頁面內容變更，則可能需要更新。 **僅支援包含內容匹配的英語字元** |

### <a name="alerts"></a>警示

|設定| 說明
|----|----|----|
|**近即時（預覽）** | 我們建議使用近即時警報。 配置這種類型的警報是在創建可用性測試後完成的。  |
|**經典** | 我們不再建議對新的可用性測試使用經典警報。|
|**警報位置閾值**|建議至少為位置數的 3/5。 警報位置閾值與測試位置數之間的最優關係是測試位置的**警報位置閾值** = **數 - 2，至少為 5 個測試位置。**|

## <a name="configuration"></a>組態

### <a name="plugging-time-and-random-numbers-into-your-test"></a>將時間和亂數插入測試

假設您要測試的工具會從外部來源取得與時間相關的資料 (例如股票)。 當您記錄 Web 測試時，您必須使用特定的時間，但您將它們設為測試的參數：StartTime 和 EndTime。

![我真棒股票應用程式截圖](./media/availability-multistep/app-insights-72webtest-parameters.png)

當您執行測試時，希望 EndTime 永遠為目前時間，而 StartTime 為 15 分鐘前。

Web 測試日期時間外掛程式提供了處理參數化時間的方法。

1. 針對您想要的每個變數參數值，各加入一個 Web 測試外掛程式。 在 Web 測試工具列中，選擇 [加入 Web 測試外掛程式] ****。
    
    ![添加 Web 測試外掛程式](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    在此範例中，我們會使用兩個日期時間外掛程式執行個體。 一個執行個體設定為 "15 minutes ago"，另一個則設定為 "now"。

2. 開啟每個外掛程式的屬性。 為屬性命名，然後將它設為使用目前時間。 對其中一個，設定 [加入分鐘] = -15。

    ![內容參數](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. 在 Web 測試參數中，使用 {{plug-in name}} 來參考外掛程式名稱。

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

現在將您的測試上傳至入口網站。 在每次執行測試時，它會使用動態值。

### <a name="dealing-with-sign-in"></a>處理登入

如果使用者登入您的應用程式，您有許多模擬登入的選項，以便在登入後方測試頁面。 您使用的方法取決於應用程式所提供的安全性類型。

在所有情況下，您應該只為了測試用途在您的應用程式中建立帳戶。 可能的話，限制此測試帳戶的權限，讓 Web 測試不可能影響實際使用者。

**簡單的使用者名和密碼**以通常的方式記錄 Web 測試。 先刪除 Cookie。

**SAML 身份驗證**

|屬性名稱| 描述|
|----|-----|
| 受眾 Uri | SAML 權杖的受眾 URI。  這是存取控制服務 （ACS） 的 URI ， 包括 ACS 命名空間和主機名稱。 |
| 證書密碼 | 用戶端憑證的密碼，它將授予對嵌入私密金鑰的存取權限。 |
| 用戶端憑證  | 具有 Base64 編碼格式私密金鑰的用戶端憑證值。 |
| 名稱識別碼 | 權杖的名稱識別碼 |
| 不能晚於 | 權杖有效的時間跨度。  預設值是 5 分鐘。 |
| 生效時間 | 過去創建權杖的有效期（用於解決時間偏差）。  預設值為（負）5 分鐘。 |
| 目標上下文參數名稱 | 將接收生成的斷言的上下文參數。 |


**用戶端機密**如果應用具有涉及用戶端機密的登錄路由，請使用該路由。 Azure Active Directory (AAD) 是可提供用戶端密碼登入的服務範例。 在 AAD 中，用戶端密碼是應用程式金鑰。

以下是使用應用程式金鑰之 Azure Web 應用程式的 Web 測試範例︰

![示例螢幕截圖](./media/availability-multistep/client-secret.png)

從使用用戶端密鑰 (AppKey) 的 AAD 取得權杖。
從回應中擷取持有人權杖。
使用授權標頭中的持有人權杖呼叫 API。
確保 Web 測試是實際用戶端-也就是說，它在 AAD 中有自己的應用程式 - 並使用其用戶端 Id + 應用金鑰。 您測試中的服務在 AAD 中也有自己的應用程式︰此應用程式的 appID URI 會反映於 [資源] 欄位中的 Web 測試。

### <a name="open-authentication"></a>開放驗證
使用您的 Microsoft 或 Google 帳戶登入即是開放驗證的範例。 許多使用 OAuth 的應用程式都提供替代用戶端密碼，第一個技巧就是調查該可能性。

如果您的測試必須使用 OAuth 登入，則常用的方式是：

使用 Fiddler 等工具來檢查網頁瀏覽器、驗證網站及您的應用程式之間的流量。
使用不同的電腦或瀏覽器，或以較長時間間隔 (讓權杖過期) 執行兩次以上的登入。
藉由比較不同的工作階段，識別從驗證網站傳回的權杖，登入之後此權杖會傳遞至您的應用程式伺服器。
使用 Visual Studio 記錄 Web 測試。
將權杖參數化，當驗證器傳回權杖時設定參數，然後在查詢網站時使用參數。 (Visual Studio 會嘗試將測試參數化，但不會正確地將權杖參數化。)

## <a name="troubleshooting"></a>疑難排解

專用[故障排除文章](troubleshoot-availability.md)。

## <a name="next-steps"></a>後續步驟

* [可用性警報](availability-alerts.md)
* [Url ping Web 測試](monitor-web-app-availability.md)
