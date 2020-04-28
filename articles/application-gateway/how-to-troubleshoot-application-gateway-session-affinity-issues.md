---
title: 排除工作階段關聯問題
titleSuffix: Azure Application Gateway
description: 本文提供有關如何在 Azure 應用程式閘道中解決會話關聯問題的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: aa3617b30fe1ef9b4d4a6c5fe5aac51bff95bb92
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866680"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>解決 Azure 應用程式閘道工作階段關聯問題

瞭解如何使用 Azure 應用程式閘道診斷和解決會話關聯問題。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

當您想要在同一個後端保留使用者工作階段時，以 Cookie 為基礎的工作階段親和性非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。

## <a name="possible-problem-causes"></a>可能的問題原因

維護基於 Cookie 的作業階段相關性時可能會出現以下原因:

- 沒有開啟「基於 Cookie 的關聯」設定
- 您的應用程式無法處理基於 Cookie 的關聯
- 應用程式使用基於 Cookie 的關聯,但請求仍在後端伺服器之間來回回跳

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>檢查是否啟用「基於 Cookie 的關聯」設定

有時,當您忘記啟用"基於 Cookie 的關聯"設置時,可能會出現會話關聯問題。 要確定是否已在 Azure 門戶中的 HTTP 設定選項卡上啟用「基於 Cookie 的關聯」設定,請按照以下說明操作:

1. 登入 Azure[門戶](https://portal.azure.com/)。

2. 在**左側導航**窗格中,按兩下 **「所有資源**」。。 按下「所有資源」邊欄選項卡中的應用程式閘道名稱。 如果所選訂閱中已具有多個資源,則可以**按名稱在篩選器**中輸入應用程式閘道名稱... 輕鬆地存取應用程式閘道。

3. 在 **「設定」** 選擇**HTTP 設定**選項卡。

   ![疑難解答會話-關聯問題-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 點選右**方的 AppGatewayBackendHttpSettings,** 檢查您是否選擇了基於 Cookie 的關聯 **。**

   ![疑難解答會話-關聯問題-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



您還可以使用以下方法之一,檢查 **「Cookie」 基於相關性**「的值設定為「**後端HttpSettingsCollection」***開啟*的值:

- 在 PowerShell 執行到 PowerShell 中執行[取得應用閘道後端設定](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting)
- 使用 Azure 資源管理員樣本檢視 JSON 檔案

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>應用程式無法處理基於 Cookie 的關聯

#### <a name="cause"></a>原因

應用程式閘道只能透過使用 Cookie 執行基於會話的關聯。

#### <a name="workaround"></a>因應措施

如果應用程式無法處理基於 Cookie 的關聯,則必須使用外部或內部 Azure 負載均衡器或其他第三方解決方案。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>應用程式使用基於 Cookie 的關聯,但請求仍在後端伺服器之間來回回跳

#### <a name="symptom"></a>徵狀

當您使用 Internet Explorer 中的短名稱網址存取應用程式閘道時,您啟用了基於 Cookie 的關聯設定,[http://website](http://website/)例如:

要識別此問題,請按照說明操作:

1. 在連接到應用程式閘道後面的應用程式的「用戶端」上獲取 Web 除錯器追蹤(本範例中我們使用 Fiddler)。
    **提示**如果您不知道如何使用 Fiddler,請在底部選中「**我想收集網路流量並使用 Web 除錯器對其進行分析**」選項。

2. 檢查並分析會話日誌,以確定用戶端提供的 Cookie 是否具有 ARRAffinity 詳細資訊。 如果您在 Cookie 集中找不到 ARRAffinity 詳細資訊,例如 **「ARRAffinity®** *ARRAffinityValue」,* 則意味著客戶端沒有使用由應用程式閘道提供的 ARRA Cookie 進行回復。
    例如：

    ![疑難解答會話-關聯問題-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![疑難解答會話-關聯問題-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

應用程式繼續嘗試在每個請求上設置 Cookie,直到收到回復。

#### <a name="cause"></a>原因

出現此問題的原因是 Internet Explorer 和其他瀏覽器可能無法儲存或使用帶有短名稱 URL 的 Cookie。

#### <a name="resolution"></a>解決方案

若要修正此問題，建議使用 FQDN 來存取應用程式閘道。 例如,[http://website.com](https://website.com/)使用[http://appgw.website.com](http://website.com/)或 。

## <a name="additional-logs-to-troubleshoot"></a>要排除故障的其他紀錄

您可以收集其他日誌並對其進行分析,以排除與基於 Cookie 的工作階段相關性相關的問題

### <a name="analyze-application-gateway-logs"></a>分析應用程式閘道紀錄

要收集應用程式閘道紀錄,請按照說明操作:

透過 Azure 入口網站啟用記錄功能

1. 在[Azure 門戶](https://portal.azure.com/)中,查找資源,然後單擊 **「診斷日誌**」。。

   對於應用程式閘道,有三個日誌可用:訪問日誌、性能日誌、防火牆日誌

2. 要開始收集數據,請單擊「**打開診斷**」。

   ![疑難解答會話-關聯問題-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. [診斷設定]**** 刀鋒視窗中提供診斷記錄的設定。 在此範例中，Log Analytics 會儲存記錄。 按下「**日誌分析**下**設定**」以設置工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。

   ![疑難解答會話-關聯問題-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 確認設置,然後單擊"**保存**"。

   ![疑難解答會話-關聯問題-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>檢視並分析應用程式閘道存取紀錄

1. 在應用程式閘道資源檢視下的 Azure 門戶中,在 **「監視**」部分中選擇**診斷日誌**。

   ![疑難解答會話-關聯問題-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右側,在**日誌類別**下拉清單中選擇"**應用程式閘道訪問日誌**"。  

   ![疑難解答會話-關聯問題-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在「應用程式閘道存取日誌」清單中,按一下要分析並匯出的日誌,然後匯出 JSON 檔。

4. 將步驟 3 中導出的 JSON 檔轉換為 CSV 檔,並在 Excel、Power BI 或任何其他數據可視化工具中查看這些檔。

5. 檢查以下資料:

- **用戶端 IP**= 這是來自連接用戶端的用戶端 IP 位址。
- **用戶端連接埠**- 這是來自請求的連接用戶端的源埠。
- **請求查詢**– 這指示收到請求的目標伺服器。
- **伺服器路由**:收到請求的後端池實例。
- **X-AzureApplicationGateway-LOG-ID**：要求所使用的相互關聯識別碼。 它可以用來針對後端伺服器上的流量問題進行疑難排解。 例如:X-Azure 應用程式閘道-CACHE-HIT=0&服务器-路由=10.0.2.4。

  - **SERVER-STATUS**：應用程式閘道從後端收到的 HTTP 回應碼。

  ![疑難解答會話-關聯問題-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果您看到兩個項目來自同一個用戶端IP和用戶端埠,並且它們發送到同一後端伺服器,則意味著應用程式網關配置正確。

如果您看到兩個項目來自同一個 ClientIP 和用戶端埠,並且它們發送到不同的後端伺服器,這意味著請求在後端伺服器之間跳轉,請選擇「**應用程式使用基於 Cookie 的關聯,但請求仍在後端伺服器之間反彈**」,請在底部進行故障排除。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 Web 除錯器擷取及分析 HTTP 或 HTTPS 流量

像 Fiddler 這樣的 Web 除錯工具可以透過擷取網路網路與測試電腦之間的網路流量來説明您除錯 Web 應用程式。 這些工具使您能夠在瀏覽器接收/發送傳入和傳出數據時檢查傳入和傳出數據。 在此範例中,Fiddler 具有 HTTP 重播選項,可説明您解決 Web 應用程式的用戶端問題,尤其是身份驗證類型問題。

使用您選擇的 Web 除錯器。 此範例中,我們將使用 Fiddler 擷取和分析 HTTP 或 Ht 流量,請按照說明操作:

1. 下載 Fiddler<https://www.telerik.com/download/fiddler>工具 。

    > [!NOTE]
    > 如果捕獲電腦安裝了 .NET 4,請選擇 Fiddler4。 否則,請選擇 Fiddler2。

2. 右鍵單擊安裝程式可執行檔,並作為管理員運行以進行安裝。

    ![疑難解答會話-關聯問題-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 打開 Fiddler 時,它應自動開始捕獲流量(請注意左下角的捕獲)。 按 F12 可啟動或停止流量捕獲。

    ![疑難解答會話-關聯問題-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 最有可能的是,您將對解密的 HTTPS 流量感興趣,並且您可以通過選擇**工具** > **Fiddler 選項**來啟用 HTTPS 解密,並選中「解密**HTTPS 流量**」複選框。

    ![疑難解答會話-關聯問題-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 您可以透過按下**X(** 圖示) >**刪除所有**工作階段(如下圖所示)來重現問題之前刪除以前的不相關的工作階段: 

    ![疑難解答會話-關聯問題-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 重現問題後,通過選擇「**File** > **檔儲存** > **所有會話」** 來儲存檔以供審閱。 . 

    ![疑難解答會話-關聯問題-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 檢查並分析會話日誌以確定問題是什麼。

    例如：

- **範例 A:** 您找到一個工作階段日誌,該會話日誌請求是從用戶端發送的,並且該日誌轉到應用程式閘道的公共 IP 位址,按一下此日誌以詳細資訊。  在右側,底部框中的數據是應用程式閘道返回到客戶端的內容。 選擇「RAW」選項卡並確定用戶端是否收到「**設定-Cookie:ARRAffinity=** *ARRAffinity 值*」。 如果沒有 Cookie,則未設置會話相關性,或者應用程式閘道未將 Cookie 應用回用戶端。

   > [!NOTE]
   > 此 ARRAffinity 值是 Cookie-id,應用程式閘道為用戶端設置以發送到特定的後端伺服器。

   ![疑難解答會話-關聯問題-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **範例 B:** 下一個工作階段日誌後跟前一個日誌是用戶端回應回應用程式閘道,該閘道已設置 ARRAAFFINITY。 如果 ARRAffinity Cookie-id 匹配,則數據包應發送到以前使用的同一後端伺服器。 檢查接下來的幾行 HTTP 通訊,查看用戶端的 ARRAffinity Cookie 是否正在更改。

   ![疑難解答會話-關聯問題-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 對於同一通信會話,Cookie 不應更改。 選中右側的頂部框,選擇"Cookies"選項卡以查看用戶端是否使用 Cookie 並將其發送回應用程式閘道。 如果沒有,客戶端瀏覽器不會保留和使用 Cookie 進行對話。 有時,用戶端可能會撒謊。

 

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
