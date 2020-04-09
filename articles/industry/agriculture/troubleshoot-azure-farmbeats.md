---
title: 針對 Azure FarmBeats 進行疑難排解
description: 本文介紹如何對 Azure 伺服器場節拍進行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 74522502f5405b56a3a604f20a695de83ef3c3a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886478"
---
# <a name="troubleshoot"></a>疑難排解

本文提供了常見 Azure 農場節拍問題的解決方案。 若需其他說明,請聯絡我們[支援論壇](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)或傳送farmbeatssupport@microsoft.com電子郵件至 。

> [!NOTE]
  > 如果您在 4 月安裝了 FarmBeats,並且作業因空錯誤消息而失敗,則由於所有資源都用於緊急、醫療和其他關鍵需求,您的安裝可能尚未分配任何批處理配額。 您需要請求將 VM 分配給批處理帳戶。

## <a name="install-issues"></a>安裝問題

  > [!NOTE]
  > 如果由於錯誤而重新啟動安裝,請確保在重新觸發安裝之前刪除**資源組**或從資源組中刪除所有資源。

### <a name="invalid-sentinel-credentials"></a>無效的哨兵認證

安裝期間提供的 Sentinel 認證不正確。 使用正確的憑據重新啟動安裝。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>已達到指定訂閱的批次帳戶的區域帳戶配額

增加配額,或刪除未使用的批處理帳戶並重新啟動安裝。

### <a name="invalid-resource-group-location"></a>不合法的資源位置

確保**資源組**與安裝期間指定的**區域**位於同一位置。

### <a name="other-install-issues"></a>其他安裝問題

聯絡我們瞭解以下詳細資訊:

- 您的訂用帳戶識別碼
- 資源群組名稱
- 依以下步驟附加部署失敗的紀錄檔:

    1. 瀏覽到 Azure 門戶中的**資源群組**。
    2. 在左側的 **「設置」** 部分下選擇 **「部署**」。
    3. 對於顯示 **「失敗」** 的每個部署,請選擇「通過詳細資訊」並下載部署詳細資訊。 將此檔附加到郵件。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="cant-view-telemetry-data"></a>無法檢視遙測資料

**症狀**:設備或感測器已部署,並且您已與設備合作夥伴連結了 FarmBeats,但無法獲取或查看 FarmBeats 上的遙測數據。

**更正措施**

1. 轉到伺服器場節拍 Datahub 資源組。
2. 選擇**事件中心**(數據饋送事件Hub命名空間),然後檢查傳入消息的數量。
3. 執行下列任一步驟：

   - 如果沒有*傳入的消息*,請與您的設備合作夥伴聯繫。  
   - 如果有*傳入的消息*,請通過 Datahub 和加速器日誌和捕獲的遙測數據與我們聯繫。

要瞭解如何下載日誌,請轉到[「手動收集日誌」](#collect-logs-manually)部分。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感測器引入歷史/串流資料後無法檢視遙測資料

**症狀**:設備或感測器已部署,您已創建 FarmBeats 上的設備/ 感測器,並將遙測數據引入到 EventHub,但無法獲取或查看 FarmBeats 上的遙測數據。

**更正措施**

1. 確保合作夥伴註冊工作正確 - 您可以通過訪問資料庫搖曳器、導航到 /合作夥伴 API、執行獲取並檢查合作夥伴是否已註冊來檢查此情況。 如果沒有,請按照[以下步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作夥伴。

2. 確保使用了正確的遙測訊息格式:

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

**更正措施**

1. 在 Datahub Swagger 中,轉到合作夥伴 API。
2. 選擇 **「試用** > **它** > **執行**」 。

> [!NOTE]
> 您感興趣的感測器合作夥伴的合作夥伴 ID。

3. 傳回合作夥伴 API,然後選擇**取得\</ID>**。
4. 從步驟 3 中指定合作夥伴 ID,然後選擇 **"執行**"。

   API 回應應具有事件中心連接字串。

### <a name="device-appears-offline"></a>裝置顯示為離線

**症狀**:設備已安裝,您已與設備合作夥伴連結了 FarmBeats。 設備處於連線狀態併發送遙測數據,但它們顯示為離線。

**糾正措施**未為此設備配置報告間隔。 要設置報告間隔,請與設備製造商聯繫。 

### <a name="error-deleting-a-device"></a>移除裝置錯誤

刪除裝置時,可能會遇到以下常見錯誤方案之一:  

**消息**:"感測器中引用了設備:有一個或多個感測器與設備相關聯。 刪除感測器,然後刪除設備。  

**含義**:設備與部署在伺服器場中的多個感測器相關聯。

**更正措施**  

1. 通過加速器刪除與設備關聯的感測器。  
2. 如果要將感測器與其他設備關聯,請讓設備合作夥伴也這樣做。  
3. 使用`DELETE API`呼叫裝置,並將強制參數設定為*true*。  

**消息**:"設備在設備中被引用為父設備Id:有一個或多個設備作為子設備與此設備關聯。 刪除它們,然後刪除此設備。  

**含義**:您的設備具有與其關聯的其他設備。  

**更正措施**

1. 刪除與此特定設備關聯的設備。  
2. 刪除特定設備。  

    > [!NOTE]
    > 如果感測器與其關聯,則無法刪除設備。 有關如何刪除相關感測器的詳細資訊,請參閱[從感測器合作夥伴獲取感測器數據的](get-sensor-data-from-sensor-partner.md)**「刪除感測器」** 部分。
    > 合作夥伴沒有刪除設備或感測器的許可權。 只有管理員才有權刪除。

## <a name="issues-with-jobs"></a>工作問題

### <a name="farmbeats-internal-error"></a>伺服器場內部錯誤

**消息**:"FarmBeats 內部錯誤,請參閱故障排除指南,瞭解更多詳細資訊。

**糾正措施**此問題可能是由於數據管道中的臨時故障造成的。 再次創建作業。 如果錯誤仍然存在,請通過錯誤消息/日誌與我們聯繫。

## <a name="accelerator-troubleshooting"></a>加速器故障排除

### <a name="access-control"></a>存取控制

**問題**:添加角色分配時收到錯誤。

**消息**:"未找到匹配的使用者。

**糾正措施**檢查要為其添加角色分配的電子郵件 ID。 電子郵件ID必須與該ID完全匹配,該ID是在活動目錄中為該用戶註冊的。 如果錯誤仍然存在,請通過錯誤消息/日誌與我們聯繫。

### <a name="unable-to-log-in-to-accelerator"></a>無法登入加速器

**訊息**:"錯誤:您無權調用服務。 請與管理員聯繫以尋求授權。

**糾正措施**請管理員授權您訪問伺服器場節拍部署。 這可以通過執行角色分配 API 的 POST 或透過加速器中的 **「設定」** 窗格中的「存取控制」來實現。  

如果您已被授予訪問許可權並面臨此錯誤,請嘗試刷新頁面。 如果錯誤仍然存在,請通過錯誤消息/日誌與我們聯繫。

![專案農場節拍](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器問題  

**問題**:您收到未確定原因的加速器錯誤。

**消息**:"錯誤:發生未知錯誤。

**糾正措施**如果頁面空閑時間過長,將發生此錯誤。 重新整理頁面。 如果錯誤仍然存在,請通過錯誤消息/日誌與我們聯繫。

**問題**: FarmBeats 加速器不會顯示最新版本,即使在升級了伺服器場Beats部署之後也是如此。

**糾正措施**出現此錯誤的原因是瀏覽器中的服務輔助角色持久性。 執行下列動作：

1. 關閉打開加速器的所有瀏覽器選項卡,然後關閉瀏覽器視窗。
2. 啟動瀏覽器的新實例,然後重新載入加速器 URI。 此操作載入新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>哨兵:與影像有關的問題

### <a name="wrong-username-or-password"></a>錯誤的使用者名稱或密碼

**作業失敗消息**:"訪問此資源需要完全身份驗證」。

**糾正措施**:執行以下操作之一:

- 使用以下步驟使用正確的使用者名/密碼更新 FarmBeats,然後重試作業。

  **更新哨兵使用者名稱**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在 **「搜尋」** 框中,搜尋伺服器場節拍 Datahub 資源組。
    3. 選擇儲存帳號儲存* >**容器** > **批次處理準備檔案** > **to_vm** > **設定.ini**
    4. 選擇**編輯**
    5. 更新sentinel_account部分中的使用者名稱

  **更新哨兵密碼**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在 **「搜尋」** 框中,搜尋伺服器場節拍 Datahub 資源組。
    3. 選擇金鑰庫 *
    4. 在「設定」下選擇存取原則
    5. 選擇 **"添加訪問策略"**
    6. 使用 **「從範本設定」的機密管理**,並將自己添加到主體
    7. 選擇 **'新增**',然後在 **'存取策略**'頁上選擇 **'儲存**'
    8. 在 **「設定」** 選擇**機密**
    9. 選擇**哨兵密碼**
    10. 創建該值的新版本並啟用它。

- 重新執行失敗的作業,或運行 5 到 7 天的日期範圍的衛星索引作業,然後檢查作業是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>哨兵中心:無法存取錯誤的網址或網站

**工作失敗消息**:"哎呀,出事了。 您試著存取的頁面(暫時)不可用。

**修正措施**:

1. 在瀏覽器中打開[哨兵](https://scihub.copernicus.eu/dhus/)以查看網站是否可以訪問。
2. 如果無法訪問網站,請檢查是否有任何防火牆、公司網路或其他阻止軟體阻止訪問網站,然後採取必要步驟允許 Sentinel URL。 
3. 重新執行失敗的作業,或運行 5 到 7 天的日期範圍的衛星索引作業,然後檢查作業是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>哨兵伺服器:停機進行維護

**工作失敗消息**:"哥白尼開放訪問中心將很快回來! 很抱歉給您帶來不便,我們目前正在進行一些維護。 我們很快就會回到網上! 

**修正措施**:

如果在 Sentinel 伺服器上執行任何維護活動,則可能會出現此問題。

1. 如果由於執行維護而導致任何作業或管道失敗,則在一段時間后重新提交作業。 

   有關任何計畫外哨兵維護活動的資訊,請造[訪 哥白尼開放訪問中心新聞網站](https://scihub.copernicus.eu/news/)。  

2. 重新執行失敗的作業,或運行 5 到 7 天的日期範圍的衛星索引作業,然後檢查作業是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>哨兵:達到的最大連線數

**作業失敗消息**:"使用者"使用者名>"\<實現的最大兩個併發流數。

**含義**:如果作業由於已達到最大連接數而失敗,則同一 Sentinel 帳戶正在多個作業中使用。

**糾正措施**:請嘗試以下任一操作:

* 等待其他作業完成,然後再重新運行失敗的作業。
* 創建新的 Sentinel 帳戶,然後在 FarmBeats 中更新 Sentinel 使用者名和密碼。

### <a name="sentinel-server-refused-connection"></a>哨兵伺服器:拒絕連接

**作業失敗消息**:"伺服器拒絕連接:"。 http://172.30.175.69:8983/solr/dhus

**糾正措施**:如果在 Sentinel 伺服器上執行任何維護活動,則可能會出現此問題。

1. 如果由於執行維護而導致任何作業或管道失敗,則在一段時間后重新提交作業。

   有關任何計畫外哨兵維護活動的資訊,請造[訪 哥白尼開放訪問中心新聞網站](https://scihub.copernicus.eu/news/)。  

2. 重新執行失敗的作業,或運行 5 到 7 天的日期範圍的衛星索引作業,然後檢查作業是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土壤水分圖有白色區域

**問題**:**土壤水分地圖**生成,但地圖大多是白色區域。

**糾正措施**:如果請求地圖時生成的衛星索引的 NDVI 值小於 0.3,則可能會出現此問題。 有關詳細資訊,請造[訪哨兵的技術指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。

1. 為不同的日期範圍重新運行作業,並檢查衛星索引中的 NDVI 值是否超過 0.3。

## <a name="collect-logs-manually"></a>手動收集紀錄

[安裝與部署 Azure 資源管理員]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>在 Datahub 中收集 Azure 資料工廠作業紀錄或應用服務日誌

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 **「搜尋」** 框中,搜尋伺服器場節拍 Datahub 資源組。
3. 在 **「資源組**」儀表板上,搜索*\*datahublogs*儲存帳戶。 例如,*資料部落格mvxmq*。  
4. 在"**名稱**「列中,選擇存儲帳戶以查看**存儲帳戶**儀表板。
5. 在**資料庫\*部落格**中,選擇 **「在資源管理員中打開**」以查看**打開的 Azure 儲存資源管理員**應用程式。
6. 在左邊窗格中,選擇**Blob 容器**,然後為 Azure 資料工廠紀錄選擇**工作紀錄**或套用服務**紀錄的應用程式來見解紀錄**。
7. 選擇 **「下載**紀錄並將日誌下載到電腦上的本地資料夾」。

    ![專案農場節拍](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>收集 Azure 資料工廠工作日誌或應用服務日誌以進行加速器

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 **「搜尋」** 框中,搜尋伺服器場節拍加速器資源組。
3. 在 **「資源組**」儀表板上,搜索*\*存儲*存儲帳戶。 例如,*儲存多普4k\**。
4. 在 **「名稱」** 列中選擇儲存帳戶以查看**儲存帳戶**儀表板。
5. 在**儲存\*** 窗格中,選擇 **「在資源管理員中打開**」以打開 Azure 儲存資源管理員應用程式。
6. 在左邊窗格中,選擇**Blob 容器**,然後為 Azure 資料工廠紀錄選擇**工作紀錄**或套用服務**紀錄的應用程式來見解紀錄**。
7. 選擇 **「下載**紀錄並將日誌下載到電腦上的本地資料夾」。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**: 您收到電子郵件警報,其中提到**高 CPU 使用率警報**。

**修正措施**:

1. 轉到伺服器場節拍 Datahub 資源組。
2. 選擇**套用服務**。  
3. 轉到"向上擴展[應用服務定價"頁面](https://azure.microsoft.com/pricing/details/app-service/windows/),然後選擇適當的定價層。
