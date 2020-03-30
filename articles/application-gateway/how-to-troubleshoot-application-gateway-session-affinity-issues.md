---
title: 排除會話關聯問題
titleSuffix: Azure Application Gateway
description: 本文提供有關如何在 Azure 應用程式閘道中解決會話關聯問題的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74047977"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>解決 Azure 應用程式閘道會話關聯問題

瞭解如何使用 Azure 應用程式閘道診斷和解決會話關聯問題。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>總覽

當您想要在同一個後端保留使用者工作階段時，以 Cookie 為基礎的工作階段親和性非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。

## <a name="possible-problem-causes"></a>可能的問題原因

維護基於 Cookie 的會話相關性時可能會出現以下原因：

- 未啟用"基於 Cookie 的關聯"設置
- 您的應用程式無法處理基於 Cookie 的關聯
- 應用程式使用基於 Cookie 的關聯，但請求仍在後端伺服器之間來回回跳

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>檢查是否啟用了"基於 Cookie 的關聯"設置

有時，當您忘記啟用"基於 Cookie 的關聯"設置時，可能會出現會話關聯問題。 要確定是否已在 Azure 門戶中的 HTTP 設置選項卡上啟用"基於 Cookie 的關聯"設置，請按照以下說明操作：

1. 登錄到 Azure[門戶](https://portal.azure.com/)。

2. 在**左側導航**窗格中，按一下 **"所有資源**"。 按一下"所有資源"邊欄選項卡中的應用程式閘道名稱。 如果所選訂閱中已具有多個資源，則可以**按名稱在篩選器**中輸入應用程式閘道名稱... 輕鬆地存取應用程式閘道。

3. 在 **"設置"** 下選擇**HTTP 設置**選項卡。

   ![疑難排解會話-關聯問題-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 按一下右側**的 AppGatewayBackendHttpSettings，** 檢查您是否選擇了基於 Cookie 的關聯 **。**

   ![疑難排解會話-關聯問題-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



您還可以使用以下方法之一，檢查 **"Cookie 基於相關性**"的值設置為"**後端HttpSettingsCollection"** 下*啟用*的值：

- 在 PowerShell 中運行[獲取應用閘道後端設置](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting)
- 使用 Azure 資源管理器範本查看 JSON 檔

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>應用程式無法處理基於 Cookie 的關聯

#### <a name="cause"></a>原因

應用程式閘道只能通過使用 Cookie 執行基於會話的關聯。

#### <a name="workaround"></a>因應措施

如果應用程式無法處理基於 Cookie 的關聯，則必須使用外部或內部 Azure 負載等化器或其他協力廠商解決方案。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>應用程式使用基於 Cookie 的關聯，但請求仍在後端伺服器之間來回回跳

#### <a name="symptom"></a>徵狀

當您使用 Internet Explorer 中的短名稱 URL 訪問應用程式閘道時，您啟用了基於 Cookie 的關聯設置，[http://website](http://website/)例如：

要識別此問題，請按照說明操作：

1. 在連接到應用程式閘道後面的應用程式的"用戶端"上獲取 Web 調試器跟蹤（本示例中我們使用 Fiddler）。
    **提示**如果您不知道如何使用 Fiddler，請在底部選中"**我想收集網路流量並使用 Web 調試器對其進行分析**"選項。

2. 檢查並分析會話日誌，以確定用戶端提供的 Cookie 是否具有 ARRAffinity 詳細資訊。 如果您在 Cookie 集中找不到 ARRAffinity 詳細資訊，例如 **"ARRAffinity®** *ARRAffinityValue"，* 則意味著用戶端沒有使用由應用程式閘道提供的 ARRA Cookie 進行回復。
    例如：

    ![疑難排解會話-關聯問題-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![疑難排解會話-關聯問題-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

應用程式繼續嘗試在每個請求上設置 Cookie，直到收到回復。

#### <a name="cause"></a>原因

出現此問題的原因是 Internet Explorer 和其他瀏覽器可能無法存儲或使用帶有短名稱 URL 的 Cookie。

#### <a name="resolution"></a>解決方案

若要修正此問題，建議使用 FQDN 來存取應用程式閘道。 例如，使用[http://website.com](https://website.com/)或[http://appgw.website.com](http://appgw.website.com/)。

## <a name="additional-logs-to-troubleshoot"></a>要疑難排解的其他日誌

您可以收集其他日誌並對其進行分析，以排除與基於 Cookie 的會話相關性相關的問題

### <a name="analyze-application-gateway-logs"></a>分析應用程式閘道日誌

要收集應用程式閘道日誌，請按照說明操作：

透過 Azure 入口網站啟用記錄功能

1. 在[Azure 門戶](https://portal.azure.com/)中，查找資源，然後按一下 **"診斷日誌**"。

   對於應用程式閘道，有三個日誌可用：訪問日誌、性能日誌、防火牆日誌

2. 要開始收集資料，請按一下"**打開診斷**"。

   ![疑難排解會話-關聯問題-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. [診斷設定]**** 刀鋒視窗中提供診斷記錄的設定。 在此範例中，Log Analytics 會儲存記錄。 按一下"**日誌分析**下**配置**"以設置工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。

   ![疑難排解會話-關聯問題-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 確認設置，然後按一下"**保存**"。

   ![疑難排解會話-關聯問題-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>查看和分析應用程式閘道訪問日誌

1. 在應用程式閘道資源檢視下的 Azure 門戶中，在 **"監視**"部分中選擇**診斷日誌**。

   ![疑難排解會話-關聯問題-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右側，在**日誌類別**下拉清單中選擇"**應用程式閘道訪問日誌**"。  

   ![疑難排解會話-關聯問題-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在"應用程式閘道訪問日誌"清單中，按一下要分析並匯出的日誌，然後匯出 JSON 檔。

4. 將步驟 3 中匯出的 JSON 檔轉換為 CSV 檔，並在 Excel、Power BI 或任何其他資料視覺化檢視中查看這些檔。

5. 檢查以下資料：

- **用戶端 IP**= 這是來自連接用戶端的用戶端 IP 位址。
- **用戶端埠**- 這是來自請求的連接用戶端的源埠。
- **請求查詢**– 這指示收到請求的目標伺服器。
- **伺服器路由**：收到請求的後端池實例。
- **X-AzureApplicationGateway-LOG-ID**：要求所使用的相互關聯識別碼。 它可以用來針對後端伺服器上的流量問題進行疑難排解。 例如：X-Azure 應用程式閘道-CACHE-HIT=0&伺服器-路由=10.0.2.4。

  - **SERVER-STATUS**：應用程式閘道從後端收到的 HTTP 回應碼。

  ![疑難排解會話-關聯問題-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果您看到兩個專案來自同一個用戶端IP 和用戶端埠，並且它們發送到同一後端伺服器，則意味著應用程式閘道配置正確。

如果您看到兩個專案來自同一個 ClientIP 和用戶端埠，並且它們發送到不同的後端伺服器，這意味著請求在後端伺服器之間跳轉，請選擇"**應用程式使用基於 Cookie 的關聯，但請求仍在後端伺服器之間反彈**"，請在底部進行故障排除。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 Web 調試器捕獲和分析 HTTP 或 HTTPS 流量

像 Fiddler 這樣的 Web 調試工具可以通過捕獲 Internet 和測試電腦之間的網路流量來説明您調試 Web 應用程式。 這些工具使您能夠在瀏覽器接收/發送傳入和傳出資料時檢查傳入和傳出資料。 在此示例中，Fiddler 具有 HTTP 重播選項，可説明您解決 Web 應用程式的用戶端問題，尤其是身份驗證類型問題。

使用您選擇的 Web 調試器。 在此示例中，我們將使用 Fiddler 捕獲和分析 HTTP 或 HTTPs 流量，請按照說明操作：

1. 下載 Fiddler 工具<https://www.telerik.com/download/fiddler>。

    > [!NOTE]
    > 如果捕獲電腦安裝了 .NET 4，請選擇 Fiddler4。 否則，請選擇 Fiddler2。

2. 按右鍵安裝程式可執行檔，並作為管理員運行以進行安裝。

    ![疑難排解會話-關聯問題-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 打開 Fiddler 時，它應自動開始捕獲流量（請注意左下角的捕獲）。 按 F12 可啟動或停止流量捕獲。

    ![疑難排解會話-關聯問題-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 最有可能的是，您將對解密的 HTTPS 流量感興趣，並且您可以通過選擇**工具** > **Fiddler 選項**來啟用 HTTPS 解密，並選中"解密**HTTPS 流量**"核取方塊。

    ![疑難排解會話-關聯問題-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 您可以通過按一下**X（** 圖示）>**刪除所有**會話（如下圖所示）來重現問題之前刪除以前的不相關的會話： 

    ![疑難排解會話-關聯問題-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 重現問題後，通過選擇"**File** > **檔保存** > **所有會話"** 來保存檔以供審閱。 . 

    ![疑難排解會話-關聯問題-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 檢查並分析會話日誌以確定問題是什麼。

    例如：

- **示例 A：** 您找到一個會話日誌，該會話日誌請求是從用戶端發送的，並且該日誌轉到應用程式閘道的公共 IP 位址，按一下此日誌以查看詳細資訊。  在右側，底部框中的資料是應用程式閘道返回到用戶端的內容。 選擇"RAW"選項卡並確定用戶端是否收到"**設置-Cookie：ARRAffinity=** *ARRAffinity值*"。 如果沒有 Cookie，則未設置會話相關性，或者應用程式閘道未將 Cookie 應用回用戶端。

   > [!NOTE]
   > 此 ARRAffinity 值是 Cookie-id，應用程式閘道為用戶端設置以發送到特定的後端伺服器。

   ![疑難排解會話-關聯問題-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **示例 B：** 下一個會話日誌後跟前一個日誌是用戶端回應回應用程式閘道，該閘道已設置 ARRAAFFINITY。 如果 ARRAffinity Cookie-id 匹配，則資料包應發送到以前使用的同一後端伺服器。 檢查接下來的幾行 HTTP 通信，查看用戶端的 ARRAffinity Cookie 是否正在更改。

   ![疑難排解會話-關聯問題-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 對於同一通信會話，Cookie 不應更改。 選中右側的頂部框，選擇"Cookies"選項卡以查看用戶端是否使用 Cookie 並將其發送回應用程式閘道。 如果沒有，用戶端瀏覽器不會保留和使用 Cookie 進行對話。 有時，用戶端可能會撒謊。

 

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
