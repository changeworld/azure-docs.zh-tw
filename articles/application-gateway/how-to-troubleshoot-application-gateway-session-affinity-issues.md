---
title: 針對會話親和性問題進行疑難排解
titleSuffix: Azure Application Gateway
description: 本文提供有關如何針對 Azure 應用程式閘道中的會話親和性問題進行疑難排解的資訊
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 02d1d78dae4f02ac53d535f6c404b15f8d98f008
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90563738"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>針對 Azure 應用程式閘道會話親和性問題進行疑難排解

瞭解如何診斷和解決 Azure 應用程式閘道的會話親和性問題。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概觀

當您想要在同一個後端保留使用者工作階段時，以 Cookie 為基礎的工作階段親和性非常有用。 使用受閘道管理的 Cookie，應用程式閘道即可將來自使用者工作階段的後續流量導向至同一部伺服器進行處理。 當使用者工作階段的工作階段狀態儲存在伺服器本機時，這項功能很重要。

## <a name="possible-problem-causes"></a>可能的問題原因

維護以 cookie 為基礎的會話親和性的問題可能是因為下列主要原因所致：

- 未啟用「以 Cookie 為基礎的親和性」設定
- 您的應用程式無法處理以 cookie 為基礎的親和性
- 應用程式正在使用以 cookie 為基礎的親和性，但仍在後端伺服器之間跳動的要求

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>檢查是否已啟用 [以 Cookie 為基礎的親和性] 設定

有時候，當您忘記啟用「以 Cookie 為基礎的親和性」設定時，可能會發生會話親和性問題。 若要判斷您是否已在 Azure 入口網站的 [HTTP 設定] 索引標籤上啟用 [以 Cookie 為基礎的親和性] 設定，請遵循下列指示：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在 **左側流覽** 窗格中，按一下 [ **所有資源**]。 在 [所有資源] 分頁中，按一下應用程式閘道名稱。 如果您選取的訂用帳戶中已經有數個資源，您可以在 [**依名稱篩選**] 中輸入應用程式閘道名稱 .。。 輕鬆地存取應用程式閘道。

3. 在 [**設定**] 底下選取 [ **HTTP 設定**] 索引標籤。

   ![螢幕擷取畫面顯示已選取 H T P 設定的設定。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 按一下右側的 [ **appGatewayBackendHttpSettings** ]，以檢查您是否已針對以 Cookie 為基礎的親和性選取 **了 [啟用** ]。

   ![螢幕擷取畫面顯示應用程式閘道的閘道設定，inlcuidng 是否已選取以 Cookie 為基礎的親和性。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



您也可以使用下列其中一種方法，檢查 "**backendHttpSettingsCollection**" 下的 "**CookieBasedAffinity**" 值是否已設定為*啟用*：

- 在 PowerShell 中執行[AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting)
- 使用 Azure Resource Manager 範本查看 JSON 檔案

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>應用程式無法處理以 cookie 為基礎的親和性

#### <a name="cause"></a>原因

應用程式閘道只能使用 cookie 來執行以會話為基礎的親和性。

#### <a name="workaround"></a>因應措施

如果應用程式無法處理以 cookie 為基礎的親和性，您必須使用外部或內部的 azure 負載平衡器或另一個協力廠商解決方案。

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>應用程式正在使用以 cookie 為基礎的親和性，但仍在後端伺服器之間跳動的要求

#### <a name="symptom"></a>徵狀

當您使用 Internet Explorer 中的簡短名稱 URL 存取應用程式閘道時，您已啟用以 Cookie 為基礎的親和性設定，例如： `http://website` ，要求仍會在後端伺服器之間彈跳。

若要識別此問題，請遵循下列指示：

1. 在「用戶端」上進行 web 偵錯工具追蹤，以連線至應用程式閘道後方的應用程式 (我們在此範例) 中使用 Fiddler。
    **秘訣** 如果您不知道如何使用 Fiddler，請勾選底部的 [**我要收集網路流量，並使用 web 偵錯工具分析它**] 選項。

2. 檢查並分析會話記錄，以判斷用戶端所提供的 cookie 是否有 ARRAffinity 詳細資料。 如果您在 cookie 集合中找不到 ARRAffinity 詳細資料，例如 "**ARRAffinity =** *ARRAffinityValue*"，這表示用戶端不會使用應用程式閘道所提供的 ARRA cookie 來回複。
    例如：

    ![螢幕擷取畫面顯示已反白顯示單一專案的會話記錄。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![螢幕擷取畫面顯示 H T P 的要求標頭，包括 cookie 資訊。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

應用程式會繼續嘗試在每個要求上設定 cookie，直到收到回復為止。

#### <a name="cause"></a>原因

發生此問題的原因是 Internet Explorer 和其他瀏覽器可能不會以簡短名稱 URL 儲存或使用 cookie。

#### <a name="resolution"></a>解決方案

若要修正此問題，建議使用 FQDN 來存取應用程式閘道。 例如，請使用 [http://website.com](https://website.com/) 或 [http://appgw.website.com](http://website.com/) 。

## <a name="additional-logs-to-troubleshoot"></a>要疑難排解的其他記錄檔

您可以收集其他記錄並加以分析，以針對以 cookie 為基礎的會話親和性相關的問題進行疑難排解

### <a name="analyze-application-gateway-logs"></a>分析應用程式閘道記錄

若要收集應用程式閘道記錄檔，請遵循下列指示：

透過 Azure 入口網站啟用記錄功能

1. 在 [ [Azure 入口網站](https://portal.azure.com/)中，尋找您的資源，然後按一下 [ **診斷記錄**]。

   針對應用程式閘道，有三個可用的記錄：存取記錄檔、效能記錄檔、防火牆記錄檔

2. 若要開始收集資料，請按一下 [ **開啟診斷**]。

   ![螢幕擷取畫面顯示已選取診斷記錄的應用程式閘道。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. [診斷設定]**** 刀鋒視窗中提供診斷記錄的設定。 在此範例中，Log Analytics 會儲存記錄。 按一下 [ **Log Analytics** ] 下的 **[設定]** 以設定您的工作區。 您也可以使用事件中樞和儲存體帳戶來儲存診斷記錄。

   ![螢幕擷取畫面顯示已選取 Log Analytics 設定的 [診斷設定] 窗格。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 確認設定，然後按一下 [ **儲存**]。

   ![螢幕擷取畫面顯示 [診斷設定] 窗格，其中已選取 [儲存]。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>查看並分析應用程式閘道存取記錄

1. 在 [應用程式閘道資源] 視圖下的 Azure 入口網站中，選取 [**監視**] 區段中的 [**診斷記錄**]。

   ![螢幕擷取畫面顯示已選取診斷記錄的監視。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 在右側的 [**記錄檔類別**] 底下的下拉式清單中，選取 [**ApplicationGatewayAccessLog**]。  

   ![螢幕擷取畫面顯示已選取 ApplicationGatewayAccessLog 的 [記錄類別] 下拉式清單。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. 在 [應用程式閘道存取記錄] 清單中，按一下您想要分析和匯出的記錄檔，然後匯出 JSON 檔案。

4. 將您在步驟3中匯出的 JSON 檔案轉換為 CSV 檔案，並在 Excel、Power BI 或任何其他資料視覺化檢視中加以查看。

5. 檢查下列資料：

- **ClientIP**–這是來自連接用戶端的用戶端 IP 位址。
- **ClientPort** -這是來自連接用戶端要求的來源埠。
- **RequestQuery** –這表示收到要求的目的地伺服器。
- **伺服器路由**：收到要求的後端集區實例。
- **X-AzureApplicationGateway-LOG-ID**：要求所使用的相互關聯識別碼。 它可以用來針對後端伺服器上的流量問題進行疑難排解。 例如： >new-azureapplicationgateway-CACHE-命中 = 0&伺服器路由 = 10.0.2.4。

  - **SERVER-STATUS**：應用程式閘道從後端收到的 HTTP 回應碼。

  ![螢幕擷取畫面會以純文字顯示伺服器狀態，大部分是隱藏的，clientPort 和伺服器路由會反白顯示。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

如果您看到兩個專案來自相同的 ClientIP 和用戶端埠，則會傳送至相同的後端伺服器，這表示應用程式閘道已正確設定。

如果您看到兩個專案來自相同的 ClientIP 和用戶端埠，而且這些專案會傳送至不同的後端伺服器，這表示要求會在後端伺服器之間跳動，請選取 [**應用程式正在使用以 cookie 為基礎的親和性，但仍在後端伺服器之間跳動的要求**]，以進行疑難排解。

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>使用 web 偵錯工具來捕獲及分析 HTTP 或 HTTPS 流量

Web 偵錯工具（如 Fiddler）可協助您藉由捕捉網際網路與測試電腦之間的網路流量，來進行 web 應用程式的偵錯工具。 這些工具可讓您在瀏覽器接收/傳送資料時，檢查傳入和傳出的資料。 在此範例中，Fiddler 具有可協助您疑難排解 web 應用程式用戶端問題的 HTTP 重新執行選項，特別是針對驗證種類的問題。

使用您選擇的 web 偵錯工具。 在此範例中，我們將使用 Fiddler 來捕捉及分析 HTTP 或 HTTPs 流量，請遵循下列指示：

1. 下載 Fiddler 工具 <https://www.telerik.com/download/fiddler> 。

    > [!NOTE]
    > 如果捕捉電腦已安裝 .NET 4，請選擇 [Fiddler4]。 否則，請選擇 [Fiddler2]。

2. 以滑鼠右鍵按一下安裝程式可執行檔，並以系統管理員身分執行安裝。

    ![螢幕擷取畫面顯示 Fiddler 工具安裝程式，其中已選取 [以系統管理員身分執行] 的內容功能表。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. 當您開啟 Fiddler 時，它應該會自動開始捕捉流量 (請注意位於左下角) 的捕獲。 按 F12 以啟動或停止流量捕捉。

    ![螢幕擷取畫面顯示 Fiddler Web 偵錯工具，其中已醒目提示 [捕捉] 指示器。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 最可能的情況是，您會對解密的 HTTPS 流量有興趣，而且可以選取 [**工具**  >  **Fiddler 選項**]，然後選取 [**解密 HTTPs 流量**] 方塊來啟用 HTTPs 解密。

    ![螢幕擷取畫面顯示已選取 H T T P 的 Fiddler 選項，並將選取的 HTTPS 流量解密。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 您可以按一下 X (圖示，以移除先前未相關的會話，方法是按一下  **X** 圖示) > **全部移除** （如以下螢幕擷取畫面）： 

    ![螢幕擷取畫面顯示選取的 X 圖示，它會顯示 [全部移除] 選項。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 重現問題之後，**請選取 [**  >  **儲存**  >  **所有會話**]，以儲存要審核的檔案 .。。 

    ![螢幕擷取畫面顯示已選取 [儲存所有會話] 選項。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 檢查並分析會話記錄，以判斷問題是什麼。

    例如：

- **範例 A：** 您會發現從用戶端傳送要求的會話記錄，並移至應用程式閘道的公用 IP 位址，請按一下此記錄以查看詳細資料。  底部方塊中的資料是應用程式閘道傳回給用戶端的資料。 選取 [原始] 索引標籤，然後判斷用戶端是否收到「**設定 Cookie： ARRAffinity =** *ARRAffinityValue*」。 如果沒有 cookie，就不會設定會話親和性，或應用程式閘道不會將 cookie 套用回用戶端。

   > [!NOTE]
   > 此 ARRAffinity 值是 cookie 識別碼，應用程式閘道會為用戶端設定，以傳送至特定的後端伺服器。

   ![螢幕擷取畫面顯示已醒目提示 [設定 Cookie] 值之記錄專案的詳細資料範例。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **範例 B：** 接下來的會話記錄檔接著是前一個會話記錄，用戶端會回應已設定 ARRAAFFINITY 的應用程式閘道。 如果 ARRAffinity cookie 識別碼相符，封包應該傳送至先前使用的同一部後端伺服器。 請檢查接下來幾行的 HTTP 通訊，以查看用戶端的 ARRAffinity cookie 是否正在變更。

   ![螢幕擷取畫面顯示已反白顯示 cookie 之記錄專案的詳細資料範例。](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 針對相同的通訊會話，cookie 不應變更。 選取右側的上方方塊，選取 [Cookie] 索引標籤，以查看用戶端是否正在使用 cookie，並將其傳回給應用程式閘道。 如果沒有，用戶端瀏覽器不會保留並使用 cookie 進行交談。 有時用戶端可能會位於。

 

## <a name="next-steps"></a>後續步驟

如果上述步驟無法解決問題，請開啟 [支援票證](https://azure.microsoft.com/support/options/)。
