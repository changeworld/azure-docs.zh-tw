---
title: Azure CDN 的監視、計量和原始記錄
description: 本文說明如何設定和使用 Azure CDN 監視、計量和原始記錄。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 43f53d1098e08a0f913e3baec2c6aaf3d65054d0
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501414"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>適用于 Azure CDN 的即時監視、計量和存取記錄
透過 Microsoft 提供的 Azure CDN，您可以透過下列方式監視資源，以協助您疑難排解、追蹤和偵測問題。 

* 未經處理的記錄提供有關 CDN 收到的每個要求的豐富資訊。 原始記錄與活動記錄不同。 活動記錄可讓您看見在 Azure 資源上完成的作業。
* 計量會顯示 CDN 上的四個關鍵計量，包括位元組點擊率、要求計數、回應大小和延遲總計。 它也會提供不同的維度來細分度量。
* 警示，可讓客戶設定關鍵計量的警示
* 額外的計量，可讓客戶使用 Azure Log Analytics 來啟用價值的其他計量。 我們也會在 Azure Log Analytics 下提供其他一些計量的查詢範例。

> [!IMPORTANT]
> HTTP 原始記錄功能適用於 Microsoft 的 Azure CDN。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="configuration---azure-portal"></a>設定-Azure 入口網站

若要為 Microsoft 設定檔中的 Azure CDN 設定原始記錄： 

1. 從 Azure 入口網站] 功能表中，選取 [**所有資源**]  >>  **\<your-CDN-profile>** 。

2. 在 [監視] 下方選取 [診斷設定]。

3. 選取 [+新增診斷設定]。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="新增 CDN 設定檔的診斷設定。" border="true":::
    
    > [!IMPORTANT]
    > 原始記錄僅適用於設定檔層級，而彙總的 HTTP 狀態碼記錄則適用於端點層級。

4. 在 [診斷設定] 下方，於 [診斷設定名稱] 底下輸入診斷設定的名稱。

5. 選取 [ **AzureCdnAccessLog** ]，並設定保留天數（以天為單位）。

6. 選取 [目的地詳細資料]。 目的地選項有：
    * **傳送至 Log Analytics**
        * 選取 [訂用帳戶] 和 [Log Analytics 工作區]。
    * **封存至儲存體帳戶**
        * 選取 [訂用帳戶] 和 [儲存體帳戶]。
    * **串流至事件中樞**
        * 選取 [訂用帳戶]、[事件中樞命名空間]、\[事件中樞名稱 \(選擇性\)\] 及 [事件中樞原則名稱]。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="設定記錄檔設定的目的地。" border="true":::

7. 選取 [儲存]。

## <a name="configuration---azure-powershell"></a>設定-Azure PowerShell

使用 [>set-azdiagnosticsetting](/powershell/module/az.monitor/set-azdiagnosticsetting) 來設定原始記錄的診斷設定。

保留資料是由命令中的 **->-retentionindays** 選項所定義。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>在儲存體帳戶中啟用診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 若要在儲存體帳戶中啟用診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>啟用 Log Analytics 工作區的診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要啟用 Log Analytics 工作區的診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>啟用事件中樞命名空間的診斷記錄

1. 登入 Azure PowerShell：

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. 若要啟用事件中樞命名空間的診斷記錄，請輸入下列命令。 將變數取代為您的值：

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>原始記錄屬性

來自 Microsoft 服務的 Azure CDN 目前提供原始記錄。 原始記錄會提供個別的 API 要求，其中每個項目都具有下列結構描述： 

| 屬性  | 描述 |
| ------------- | ------------- |
| BackendHostname | 如果將要求轉送至後端，此欄位代表後端的主機名稱。 如果將要求重新導向或轉送至區域快取，則此欄位將會是空白， (當已啟用路由規則) 的快取時。 |
| CacheStatus | 針對快取案例，此欄位會在 POP 上定義快取點擊/遺漏 |
| ClientIp | 發出要求之用戶端的 IP 位址。 如果要求中有 X 轉送的標頭，則會從相同的挑選用戶端 IP。 |
| ClientPort | 提出要求之用戶端的 IP 埠。 |
| HttpMethod | 要求使用的 HTTP 方法。 |
| HttpStatusCode | 從 Proxy 傳回的 HTTP 狀態碼。 |
| HttpStatusDetails | 對要求產生的狀態。 此字串值的意義可以在狀態參考資料表中找到。 |
| HttpVersion | 要求或連線的類型。 |
| POP | 要求進入之邊緣的簡短名稱。 |
| RequestBytes | HTTP 要求訊息的大小 (以位元組為單位)，包括要求標頭和要求本文。 |
| RequestUri | 接收之要求的 URI。 |
| ResponseBytes | 後端伺服器以回應形式傳回的位元組。  |
| RoutingRuleName | 要求相符的路由規則名稱。 |
| RulesEngineMatchNames | 要求相符的規則名稱。 |
| SecurityProtocol | 要求所使用的 TLS/SSL 通訊協定版本，如果沒有加密則為 null。 |
| SentToOriginShield </br>  (已淘汰) * **請參閱下一節中的淘汰注意事項。**| 若為 true，則表示是從來源保護盾快取回答要求，而非邊緣 Pop。 原始保護盾是用來改善快取點擊率的父代快取。 |
| isReceivedFromClient | 若為 true，表示要求來自用戶端。 如果為 false，表示要求在 edge (子 POP) 中遺失，並從來源防護 (父 POP) 回應。 |
| TimeTaken | 從要求的第一個位元組到回應輸出之最後一個位元組的 Front Door 時間長度（以秒為單位）。 |
| TrackingReference | 這項唯一的參考字串可識別 Front Door 所提供的要求，也會以 X-Azure Ref 標頭的形式傳送給用戶端， 這是在特定要求的存取記錄中搜尋詳細資料時的必要項目。 |
| UserAgent | 用戶端使用的瀏覽器類型。 |
| ErrorInfo | 此欄位包含特定類型的錯誤，以縮小疑難排解區域範圍。 </br> 可能的值包括： </br> **>aad-userreadusingalternativesecurityid-noerror**：指出找不到任何錯誤。 </br> **CertificateError**：一般 SSL 憑證錯誤。</br> **CertificateNameCheckFailed**： SSL 憑證中的主機名稱無效或不相符。 </br> **ClientDisconnected**：由於用戶端網路連線而導致要求失敗。 </br> **UnspecifiedClientError**：一般用戶端錯誤。 </br> **InvalidRequest**：不正確要求。 原因可能是標頭、主體和 URL 格式不正確所造成。 </br> **DNSFailure**： DNS 失敗。 </br> **DNSNameNotResolved**：無法解析伺服器名稱或位址。 </br> **OriginConnectionAborted**：來源的連接突然停止。 </br> **OriginConnectionError**：一般來源連接錯誤。 </br> **OriginConnectionRefused**：無法建立與來源的連接。 </br> **OriginError**：一般來源錯誤。 </br> **OriginInvalidResponse**：來源傳回無效或無法辨識的回應。 </br> **OriginTimeout**：原始要求過期的超時時間。 </br> **ResponseHeaderTooBig**：來源傳回太大的回應標頭。 </br> **RestrictedIP**：因為限制 IP 而封鎖要求。 </br> **SSLHandshakeError**：無法建立與來源的連線，因為發生 SSL 手動搖動失敗。 </br> **UnspecifiedError**：發生錯誤，不符合資料表中的任何錯誤。 |
| TimeToFirstByte | 從 Microsoft CDN 收到第一個位元組傳送給用戶端的要求時，以毫秒為單位的時間長度（以毫秒為單位）。 只會從 Microsoft 端測量時間。 不會測量用戶端資料。 |
> [!NOTE]
> 您可以藉由執行查詢，以在 Log Analytics 設定檔下方檢視記錄。 範例查詢如下所示：
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>傳送至原始防護盾
原始記錄屬性 **isSentToOriginShield** 已被取代，並以新的欄位 **isReceivedFromClient** 取代。 如果您已經在使用已被取代的欄位，請使用新欄位。 

原始記錄包含從 CDN edge (子 POP) 和來源防護產生的記錄。 來源防護是指策略性地位於全球各地的父節點。 這些節點會與源伺服器通訊，並減少來源的流量負載。 

針對移至來源防護的每個要求，有2個記錄檔專案：

* 一個用於邊緣節點
* 一個用於來源盾牌。 

若要區別邊緣節點與來源防護之間的輸出或回應，您可以使用欄位 **isReceivedFromClient** 來取得正確的資料。 

如果值為 false，則表示要求會從來源防護回應至邊緣節點。 這種方法適用于比較原始記錄與計費資料。 從原始防護站輸出到邊緣節點時，不會產生費用。 從邊緣節點至用戶端的輸出會產生費用。 

**Kusto 查詢範例，以排除記錄分析中原始防護板上產生的記錄。**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> 系統會自動為 **2020 年 2 月 25 日** 之後建立或更新的任何設定檔提供 HTTP 原始記錄功能。 對於之前建立的 CDN 設定檔，應於設定記錄之後更新 CDN 端點。 例如，您可以瀏覽至 CDN 端點底下的地區篩選，並封鎖任何與工作負載無關的國家/地區，然後點擊 [儲存]。


## <a name="metrics"></a>計量
來自 Microsoft 的 Azure CDN 已與 Azure 監視器整合，併發布四個 CDN 度量，以協助追蹤、疑難排解及偵測問題。 

計量會顯示在圖表中，並可透過 PowerShell、CLI 和 API 存取。 CDN 計量是免費的。

來自 Microsoft 的 Azure CDN 會測量並以60秒的間隔傳送其度量。 計量最多可能需要3分鐘的時間才會出現在入口網站中。 

如需詳細資訊，請參閱 [Azure 監視器計量](../azure-monitor/platform/data-platform-metrics.md)。

**來自 Microsoft 的 Azure CDN 所支援的計量**

| 計量  | 描述 | 維度 |
| ------------- | ------------- | ------------- |
| 位元組點擊率 * | CDN 快取的輸出百分比，針對輸出總計進行計算。 | 端點 |
| RequestCount | CDN 所服務的用戶端要求數目。 | 端點 </br> 用戶端國家/地區。 </br> 用戶端區域。 </br> HTTP 狀態。 </br> HTTP 狀態群組。 |
| ResponseSize | 從 CDN edge 傳送至用戶端之回應的位元組數目。 |端點 </br> 用戶端國家/地區。 </br> 用戶端區域。 </br> HTTP 狀態。 </br> HTTP 狀態群組。 |
| TotalLatency | 從 CDN 收到的用戶端要求到 **最後一個回應位元組傳送至用戶端之前** 的總時間。 |端點 </br> 用戶端國家/地區。 </br> 用戶端區域。 </br> HTTP 狀態。 </br> HTTP 狀態群組。 |

**_命中的位元組數比例 = 從邊緣輸出的邊緣輸出) 從邊緣傳出的 (輸出_*

以位元組點擊率計算排除的案例：

* 您可以透過規則引擎或查詢字串快取行為明確設定沒有快取。
* 您可以明確地設定沒有存放區或私用快取的快取控制指示詞。

### <a name="metrics-configuration"></a>計量設定

1. 從 Azure 入口網站] 功能表中，選取 [**所有資源**]  >>  **\<your-CDN-profile>** 。

2. 在 [ **監視**] 底下，選取 [ **計量**：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="CDN 設定檔的計量。" border="true":::

3. 選取 [ **新增度量**]，選取要新增的度量：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="新增並選取 CDN 設定檔的度量。" border="true":::

4. 選取 [ **新增篩選** ] 以新增篩選準則：
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="將篩選套用至度量。" border="true":::

5. 選取 **[** 套用分割] 以查看依不同維度的趨勢：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="將分割套用至度量。" border="true":::

6. 選取 [ **新增圖表** ] 以加入新的圖表：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="將新的圖表新增至度量視圖。" border="true":::

### <a name="alerts"></a>警示

您可以選取 [**監視** 警示]，在 Microsoft CDN 上設定警示  >>  ****。

針對 [計量] 區段中所列的計量，選取 **新的警示規則** ：

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="設定 CDN 端點的警示。" border="true":::

警示將依據 Azure 監視器收費。 如需警示的詳細資訊，請參閱 [Azure 監視器警示](../azure-monitor/platform/alerts-overview.md)。

### <a name="additional-metrics"></a>其他計量
您可以使用 Azure Log Analytics 和原始記錄來啟用額外的計量，以產生額外的費用。

1. 依照中的步驟進行，以啟用診斷功能，以將原始記錄傳送至 log analytics。

2. 選取您所建立的 Log Analytics 工作區：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="選取 log analytics 工作區" border="true":::   

3. 在 log analytics 工作區中，選取 **[一般**] 底下的 [**記錄**]。  然後選取 **開始**：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log analytics 資源工作區。" border="true":::   
 
4. 選取 [ **CDN 設定檔**]。  選取要執行的範例查詢，或關閉範例畫面來輸入自訂查詢：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="範例查詢畫面。" border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="查詢執行。" border="true":::   

4. 若要依圖表來查看資料，請選取 [ **圖表**]。  選取 [ **釘選到儀表板** ]，將圖表釘選到 Azure 儀表板：

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="將圖表釘選到儀表板。" border="true"::: 

## <a name="next-steps"></a>後續步驟
在本文中，您已啟用 Microsoft CDN 服務的 HTTP 原始記錄。

如需有關 Azure CDN 和本文所述其他 Azure 服務的詳細資訊，請參閱：

* [分析](cdn-log-analysis.md) Azure CDN 使用模式。

* 深入了解 [Azure 監視器](../azure-monitor/overview.md)。

* 設定 [Azure 監視器中的 Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)。