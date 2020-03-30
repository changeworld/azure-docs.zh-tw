---
title: 針對 Azure FarmBeats 進行疑難排解
description: 本文介紹如何對 Azure 伺服器場節拍進行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a429a1e454e73a1a9d544e308e5b2d60052d91a9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349765"
---
# <a name="troubleshoot"></a>疑難排解

本文提供了常見 Azure 農場節拍問題的解決方案。

如需其他説明，請通過farmbeatssupport@microsoft.com聯繫我們。 確保在電子郵件中包含**部署器.log**檔。

要下載**Deployer.log**檔，請執行以下操作：

1. 登錄到 Azure**門戶**並選擇訂閱和 Azure AD 租戶。
2. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。
3. 選擇**Bash**作為首選雲外殼體驗。
4. 選擇突出顯示的圖示，然後在下拉清單中選擇 **"下載**"。

    ![專案農場節拍](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 在下一個窗格中，輸入**Deployer.log**檔的路徑。 例如，輸入**伺服器場節拍部署器.log**。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="cant-view-telemetry-data"></a>無法查看遙測資料

**症狀**：設備或感應器已部署，並且您已與設備合作夥伴連結了 FarmBeats，但無法獲取或查看 FarmBeats 上的遙測資料。

**糾正措施**：

1. 轉到伺服器場節拍 Datahub 資源組。   
2. 選擇**事件中心**（資料摘要事件Hub命名空間），然後檢查傳入消息的數量。
3. 執行下列任一步驟：   
   - 如果沒有*傳入的消息*，請與您的設備合作夥伴聯繫。  
   - 如果有*傳入消息*，請與farmbeatssupport@microsoft.com. 附加 Datahub 和加速器日誌和捕獲的遙測資料。

要瞭解如何下載日誌，請轉到["手動收集日誌"](#collect-logs-manually)部分。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器引入歷史/流資料後無法查看遙測資料

**症狀**：設備或感應器已部署，您已創建 FarmBeats 上的設備/感應器，並將遙測資料引入到 EventHub，但無法獲取或查看 FarmBeats 上的遙測資料。

**糾正措施**：

1. 確保合作夥伴註冊工作正確 - 您可以通過訪問資料庫搖曳器、導航到 /合作夥伴 API、執行獲取並檢查合作夥伴是否已註冊來檢查此情況。 如果沒有，請按照[此處的步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作夥伴。
2. 確保使用了正確的遙測消息格式：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>沒有 Azure 事件中心連接字串

**糾正措施**：

1. 在 Datahub Swagger 中，轉到合作夥伴 API。
2. 選擇 **"試用** > **它** > **執行**"。
3. 請注意您感興趣的感應器合作夥伴的合作夥伴 ID。
4. 返回合作夥伴 API，然後選擇**獲取/ID\<>**。
5. 從步驟 3 中指定合作夥伴 ID，然後選擇 **"執行**"。

   API 回應應具有事件中心連接字串。

### <a name="device-appears-offline"></a>設備顯示為離線

**症狀**：設備已安裝，您已與設備合作夥伴連結了 FarmBeats。 設備處於線上狀態併發送遙測資料，但它們顯示為離線。

**糾正措施**：未為此設備配置報告間隔。 要設置報告間隔，請與設備製造商聯繫。 

### <a name="error-deleting-a-device"></a>刪除設備錯誤

刪除設備時，可能會遇到以下常見錯誤方案之一：  

**消息**："感應器中引用了設備：有一個或多個感應器與設備相關聯。 刪除感應器，然後刪除設備。  

**含義**：設備與部署在伺服器場中的多個感應器相關聯。   

**糾正措施**：  

1. 通過加速器刪除與設備關聯的感應器。  
2. 如果要將感應器與其他設備關聯，請讓設備合作夥伴也這樣做。  
3. 使用`DELETE API`調用刪除設備，並將強制參數設置為*true*。  

**消息**："設備在設備中被引用為父設備Id：有一個或多個設備作為子設備與此設備關聯。 刪除它們，然後刪除此設備。  

**含義**：您的設備具有與其關聯的其他設備。  

**更正措施**

1. 刪除與此特定設備關聯的設備。  
2. 刪除特定設備。  

    > [!NOTE]
    > 如果感應器與其關聯，則無法刪除設備。 有關如何刪除相關感應器的詳細資訊，請參閱[從感應器合作夥伴獲取感應器資料的](get-sensor-data-from-sensor-partner.md)**"刪除感應器"** 部分。

    > 合作夥伴無法刪除設備或感應器。 只有管理員才能執行此操作。


## <a name="issues-with-jobs"></a>工作問題

### <a name="farmbeats-internal-error"></a>伺服器場內部錯誤

**消息**："FarmBeats 內部錯誤，請參閱故障排除指南，瞭解更多詳細資訊"。

**糾正措施**：此問題可能是由於資料管道中的臨時故障造成的。 再次創建作業。 如果錯誤仍然存在，請在 FarmBeats 論壇的帖子中添加錯誤訊息，或聯繫FarmBeatsSupport@microsoft.com。

## <a name="accelerator-troubleshooting"></a>加速器故障排除

### <a name="access-control"></a>存取控制

**問題**：添加角色指派時收到錯誤。

**消息**："未找到匹配的使用者。

**糾正措施**：檢查您嘗試為其添加角色指派的電子郵件 ID。 電子郵件 ID 必須與該 ID 完全符合，該 ID 是在活動目錄中為該使用者註冊的。 如果錯誤仍然存在，請在 FarmBeats 論壇的帖子中添加錯誤訊息，或聯繫FarmBeatsSupport@microsoft.com。

### <a name="unable-to-log-in-to-accelerator"></a>無法登錄到加速器

**消息**："錯誤：您無權調用服務。 請與管理員聯繫以尋求授權。

**糾正措施**：要求管理員授權您訪問伺服器場節拍部署。 這可以通過執行角色指派 API 的 POST 或通過加速器中的 **"設置"** 窗格中的"存取控制"來實現。  

如果您已被授予存取權限並面臨此錯誤，請嘗試刷新頁面。 如果錯誤仍然存在，請在 FarmBeats 論壇的帖子中添加錯誤訊息，或聯繫FarmBeatsSupport@microsoft.com。

![專案農場節拍](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器問題  

**問題**：您收到未確定原因的加速器錯誤。

**消息**："錯誤：發生未知錯誤。

**更正操作**：如果頁面閒置時間過長，則會發生此錯誤。 重新整理頁面。  

如果錯誤仍然存在，請在 FarmBeats 論壇的帖子中添加錯誤訊息，或聯繫FarmBeatsSupport@microsoft.com。

**問題**： FarmBeats 加速器不會顯示最新版本，即使在升級了伺服器場Beats部署之後也是如此。

**更正操作**：由於瀏覽器中的服務輔助角色持久性，發生此錯誤。 執行下列動作：

1. 關閉打開加速器的所有瀏覽器選項卡，然後關閉瀏覽器視窗。
2. 啟動瀏覽器的新實例，然後重新載入加速器 URI。 此操作載入新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>哨兵：與影像有關的問題

### <a name="wrong-username-or-password"></a>錯誤的使用者名或密碼

**作業失敗消息**："訪問此資源需要完全身份驗證"。

**糾正措施**：

執行下列其中一個動作：

- 重新運行安裝程式，以便使用正確的使用者名和密碼升級 Datahub。
- 重新運行失敗的作業，或運行 5 到 7 天的日期範圍的衛星索引作業，然後檢查作業是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>哨兵中心：無法訪問錯誤的 URL 或網站 

**工作失敗消息**："哎呀，出事了。 您嘗試訪問的頁面（暫時）不可用。 

**糾正措施**：
1. 在瀏覽器中打開[哨兵](https://scihub.copernicus.eu/dhus/)以查看網站是否可以訪問。 
2. 如果無法訪問網站，請檢查是否有任何防火牆、公司網路或其他阻止軟體阻止訪問網站，然後採取必要步驟允許 Sentinel URL。 
3. 重新運行失敗的作業，或運行 5 到 7 天的日期範圍的衛星索引作業，然後檢查作業是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>哨兵伺服器：停機進行維護

**工作失敗消息**："哥白尼開放訪問中心將很快回來！ 很抱歉給您帶來不便，我們目前正在進行一些維護。 我們很快就會回到網上！ 

**糾正措施**：

如果在 Sentinel 伺服器上執行任何維護活動，則可能會出現此問題。

1. 如果由於執行維護而導致任何作業或管道失敗，則在一段時間後重新提交作業。 

   有關任何計畫外哨兵維護活動的資訊，請訪問[哥白尼開放訪問中心新聞網站](https://scihub.copernicus.eu/news/)。  

2. 重新運行失敗的作業，或運行 5 到 7 天的日期範圍的衛星索引作業，然後檢查作業是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>哨兵：達到的最大連接數

**作業失敗消息**："使用者"使用者名>"\<實現的最大兩個併發流數。

**含義**：如果作業由於已達到最大連接數而失敗，則同一 Sentinel 帳戶正在其他軟體部署中使用。

**糾正措施**：請嘗試以下任一操作：

* 創建新的 Sentinel 帳戶，然後重新運行安裝程式，使用新的 Sentinel 使用者名和密碼升級 Datahub。  
* 重新運行失敗的作業或運行 5 到 7 天的日期範圍的衛星索引作業，然後檢查作業是否成功。

### <a name="sentinel-server-refused-connection"></a>哨兵伺服器：拒絕連接 

**作業失敗消息**："伺服器拒絕連接："。 http://172.30.175.69:8983/solr/dhus 

**糾正措施**：如果在 Sentinel 伺服器上執行任何維護活動，則可能會出現此問題。 
1. 如果由於執行維護而導致任何作業或管道失敗，則在一段時間後重新提交作業。 

   有關任何計畫外哨兵維護活動的資訊，請訪問[哥白尼開放訪問中心新聞網站](https://scihub.copernicus.eu/news/)。  

2. 重新運行失敗的作業，或運行 5 到 7 天的日期範圍的衛星索引作業，然後檢查作業是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土壤水分圖有白色區域 

**問題**： 土壤水分地圖生成，但地圖大多是白色區域。

**糾正措施**：如果請求地圖時生成的衛星索引的 NDVI 值小於 0.3，則可能會出現此問題。 欲瞭解更多資訊，請訪問[《哨兵》的技術指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。
1. 重新運行不同日期範圍的作業，並檢查衛星索引中的 NDVI 值是否大於 0.3

## <a name="collect-logs-manually"></a>手動收集日誌

[安裝和部署 Azure 存儲資源管理器]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>在 Datahub 中收集 Azure 資料工廠作業日誌

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜尋伺服器場節拍 Datahub 資源組。

    > [!NOTE]
    > 選擇您在伺服器場節拍安裝期間指定的 Datahub 資源組。

3. 在 **"資源組**"儀表板上，搜索*\*datahublogs*存儲帳戶。 例如，搜索**資料博客mvxmq**。  
4. 在"**名稱**"列中，選擇存儲帳戶以查看**存儲帳戶**儀表板。
5. 在**資料庫\*博客**窗格中，選擇 **"在資源管理器中打開**"以查看**打開的 Azure 存儲資源管理器**應用程式。
6. 在左側窗格中，選擇**Blob 容器**，然後選擇**作業日誌**。
7. 在**作業日誌**窗格中，選擇 **"下載**"。
8. 將日誌下載到電腦上的本地資料夾。
9. 將下載的 .ZIP 檔案farmbeatssupport@microsoft.com通過電子郵件發送到 。

    ![專案農場節拍](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>在加速器中收集 Azure 資料工廠作業日誌

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜尋伺服器場節拍加速器資源組。

    > [!NOTE]
    > 選擇在伺服器場節拍安裝期間指定的加速器資源組。

3. 在 **"資源組**"儀表板上，搜索*存儲\** 存儲帳戶。 例如，搜索**存儲多普4k\***。
4. 在 **"名稱"** 列中選擇存儲帳戶以查看**存儲帳戶**儀表板。
5. 在**存儲\*** 窗格中，選擇 **"在資源管理器中打開**"以打開 Azure 存儲資源管理器應用程式。
6. 在左側窗格中，選擇**Blob 容器**，然後選擇**作業日誌**。
7. 在**作業日誌**窗格中，選擇 **"下載**"。
8. 將日誌下載到電腦上的本地資料夾。
9. 將下載的 .ZIP 檔案farmbeatssupport@microsoft.com通過電子郵件發送到 。


### <a name="collect-datahub-app-service-logs"></a>收集 Datahub 應用服務日誌

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜尋伺服器場節拍 Datahub 資源組。

    > [!NOTE]
    > 選擇您在伺服器場節拍安裝期間指定的 Datahub 資源組。

3. 在資源組中，搜索*datahublogs\** 存儲帳戶。 例如，搜索**datahublogsmvxmq\***。
4. 在 **"名稱"** 列中選擇存儲帳戶以查看**存儲帳戶**儀表板。
5. 在**資料庫博客\*** 窗格中，選擇 **"在資源管理器中打開**"以打開 Azure 存儲資源管理器應用程式。
6. 在左側窗格中，選擇**Blob 容器**，然後選擇**應用見解日誌**。
7. 在**應用見解日誌**窗格中，選擇 **"下載**"。
8. 將日誌下載到電腦上的本地資料夾。
9. 將下載的 .ZIP 檔案farmbeatssupport@microsoft.com通過電子郵件發送到 。

### <a name="collect-accelerator-app-service-logs"></a>收集加速器應用服務日誌

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在 **"搜索"** 框中，搜尋伺服器場節拍加速器資源組。

    > [!NOTE]
    > 選擇伺服器場節拍安裝期間提供的伺服器場節拍加速器資源組。

3. 在資源組中，搜索*存儲\** 存儲帳戶。 例如，搜索**存儲多普4k\***。
4. 在 **"名稱"** 列中選擇存儲帳戶以查看**存儲帳戶**儀表板。
5. 在**存儲\*** 窗格中，選擇 **"在資源管理器中打開**"以打開 Azure 存儲資源管理器應用程式。
6. 在左側窗格中，選擇**Blob 容器**，然後選擇**應用見解日誌**。
7. 在**應用見解日誌**窗格中，選擇 **"下載**"。
8. 將日誌下載到電腦上的本地資料夾。
9. 將下載的資料夾通過電子郵件發送到farmbeatssupport@microsoft.com。

## <a name="known-issues"></a>已知問題

## <a name="batch-related-issues"></a>批次相關問題

**錯誤訊息**："已達到指定訂閱的批次處理帳戶的區域帳戶配額。

**糾正措施**：增加配額，或刪除未使用的批次處理帳戶並重新運行部署。

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure 活動目錄 （Azure AD） 相關問題

**錯誤訊息**："無法將所需的設置更新到 Azure AD 應用 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：無法完成操作的許可權不足。 確保為 Azure AD 應用正確配置了上述設置。

**含義**：Azure AD 應用註冊配置未正確完成。  

**糾正措施**：要求 IT 管理員（具有租戶讀取存取許可權的人員）使用我們的[腳本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)創建 Azure AD 應用註冊。 此腳本還會自動處理配置步驟。

**錯誤訊息**："無法在此租戶中創建新的活動目錄應用程式 '\<應用程式名稱\>'：屬性識別碼 URI 的值相同的另一個物件已存在。

**含義**：已存在同名的 Azure AD 應用註冊。

**糾正措施**：刪除現有的 Azure AD 應用註冊，或將其重複使用以進行安裝。 如果要重用現有的 Azure AD 應用註冊，請將應用程式 ID 和用戶端金鑰傳遞給安裝程式並重新部署。

## <a name="issues-with-the-inputjson-file"></a>input.json 檔的問題

**錯誤**：從*input.json*檔中讀取輸入時出錯。

**糾正措施**：此問題通常是因為向安裝程式指定正確的*input.json*檔路徑或名稱時出錯。 進行適當的更正，並重試部署。

**錯誤**：*在 input.json*檔中存在錯誤分析值。

**糾正措施**：此問題主要由於*input.json*檔中的值不正確而出現。 進行適當的更正，並重試部署。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**： 您收到一個電子郵件警報，其中提到**高 CPU 使用率警報**。 

**糾正措施**： 
1. 轉到伺服器場節拍 Datahub 資源組。
2. 選擇**應用服務**。  
3. 轉到"向上擴展[應用服務定價"頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然後選擇適當的定價層。

## <a name="next-steps"></a>後續步驟

如果您仍然面臨 FarmBeats 問題，請聯繫我們[的支援論壇](https://aka.ms/farmbeatssupport)。
