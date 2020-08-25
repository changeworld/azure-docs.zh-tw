---
title: 針對 Azure FarmBeats 進行疑難排解
description: 本文說明如何針對 Azure FarmBeats 進行疑難排解。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c66d3d6fd3ee0bcba01db61183f40cd3ccf3f39d
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797933"
---
# <a name="troubleshoot-azure-farmbeats"></a>針對 Azure FarmBeats 進行疑難排解

本文提供 Azure FarmBeats 常見問題的解決方案。 如需其他協助，請與我們的 [Q&支援論壇](https://aka.ms/farmbeatssupport) ，或傳送電子郵件給我們 farmbeatssupport@microsoft.com 。

> [!NOTE]
  > 如果您在四月期間安裝了 FarmBeats，而您的作業失敗並出現空白的錯誤訊息，表示您的安裝可能未配置任何批次配額，無法優先處理重要的健康與安全組織支援。 如需詳細資訊，請參閱 [此處](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) 。 您必須要求將 VM 配置給 Batch 帳戶，才能成功執行作業。

## <a name="install-issues"></a>安裝問題

  > [!NOTE]
  > 如果您因為發生錯誤而重新啟動安裝，請務必先刪除**資源群組**，或刪除資源群組中的所有資源，然後再重新觸發安裝。

### <a name="invalid-sentinel-credentials"></a>不正確的 Sentinel 認證

安裝期間提供的 Sentinel 認證不正確。 請使用正確的認證重新啟動安裝。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>已達到指定訂用帳戶上 Batch 帳戶的區域帳戶配額

增加配額，或刪除未使用的 Batch 帳戶，然後重新啟動安裝。

### <a name="invalid-resource-group-location"></a>不正確的資源群組位置

請確定**資源群組**位於安裝期間指定的**區域**中。

### <a name="other-install-issues"></a>其他安裝問題

請與我們連絡並提供下列詳細資料：

- 您的訂用帳戶識別碼
- 資源群組名稱
- 請遵循下列步驟來附加部署失敗的記錄檔：

    1. 在 Azure 入口網站中，瀏覽至**資源群組**。

    2. 在左側的 [設定] 區段底下，選取 [部署]。

    3. 針對顯示**失敗**的每個部署，選取其詳細資料並下載部署詳細資料。 將此檔案附加至郵件。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="cant-view-telemetry-data"></a>無法檢視遙測資料

**徵兆**：裝置或感應器已部署，而且您已將 FarmBeats 與裝置合作夥伴連結，但無法取得或檢視 FarmBeats 上的遙測資料。

**矯正措施**

1. 移至您的 FarmBeats 資源群組。
2. 選取 **事件中樞** 命名空間 ( 「感應器-夥伴-eh-命名空間-xxxx」 ) ，按一下 [事件中樞]，然後檢查指派給夥伴的事件中樞內的傳入訊息數目
3. 執行下列任一步驟：

   - 如果「沒有內送郵件」，請連絡您的裝置合作夥伴。  
   - 如果有「內送郵件」，請與我們連絡，並提供您的 Datahub 和加速器記錄，以及已擷取的遙測資料。

若要了解如何下載記錄，請移至[＜手動收集記錄＞](#collect-logs-manually)區段。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器內嵌歷程/串流資料之後，無法檢視遙測資料

**徵兆**：裝置或感應器已部署，而且您已在 FarmBeats 上建立裝置/感應器，並將遙測內嵌至 EventHub，但無法取得或檢視 FarmBeats 上的遙測資料。

**矯正措施**

1. 請確認您已正確完成合作夥伴註冊 - 若要進行確認，您可以前往 Datahub Swagger 並瀏覽至 /Partner API，然後執行 Get，並檢查合作夥伴是否已註冊。 如果未註冊，請遵循下列[步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)來新增合作夥伴。

2. 請確定您已使用正確的遙測訊息格式：

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
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>沒有 Azure 事件中樞連接字串

**矯正措施**

1. 在 Datahub Swagger 中，移至合作夥伴 API。
2. 選取 [取得] > [試用] > [執行]。

> [!NOTE]
> 所關注感應器合作夥伴的合作夥伴識別碼。

3. 返回至夥伴 API，然後選取 [**取得/ \<ID> **]。
4. 指定步驟 3 中的合作夥伴識別碼，然後選取 [執行]。

   API 回應應具有事件中樞連接字串。

### <a name="device-appears-offline"></a>我的裝置顯示為離線

**徵兆**：已安裝裝置，而且您已將 FarmBeats 與裝置合作夥伴連結。 裝置已上線並正在傳送遙測資料，但其顯示為離線。

**矯正措施** 未針對此裝置設定報告間隔。 若要設定報告間隔，請連絡您的裝置製造商。 

### <a name="error-deleting-a-device"></a>刪除裝置時發生錯誤

當您刪除裝置時，可能會遇到下列其中一個常見的錯誤情況：  

**訊息**：「多個感應器參照該裝置：有一或多個與裝置相關聯的感應器。 請刪除感應器，然後再刪除裝置。」  

**意義**：裝置與伺服器陣列中部署的多個感應器相關聯。

**矯正措施**  

1. 透過加速器刪除與裝置相關聯的感應器。  
2. 如果您想要將感應器與不同的裝置建立關聯，請要求裝置夥伴執行相同動作。  
3. 使用 `DELETE API` 呼叫來刪除裝置，並將 force 參數設定為 true。  

**訊息**：「多個裝置將該裝置視為 ParentDeviceId 來進行參照：有一個或多個裝置以子裝置的形式與此裝置相關聯。 請將這些子裝置刪除，然後再刪除此裝置。」  

**意義**：您的裝置有其他相關聯的裝置。  

**矯正措施**

1. 刪除與此特定裝置相關聯的裝置。  
2. 刪除特定裝置。  

    > [!NOTE]
    > 如果感應器與裝置相關聯，您將無法刪除裝置。 如需有關如何刪除相關聯感應器的詳細資訊，請參閱[從感應器合作夥伴取得感應器資料](get-sensor-data-from-sensor-partner.md)中的＜**刪除感應器**＞一節。
    > 合作夥伴沒有刪除裝置或感應器的權限。 只有系統管理員具有刪除的權限。

## <a name="issues-with-jobs"></a>作業問題

### <a name="farmbeats-internal-error"></a>FarmBeats 內部錯誤

**訊息**：「FarmBeats 內部錯誤，請參閱疑難排解指南以取得詳細資料。」

**矯正措施** 此問題可能是因為資料管線中的暫時性失敗所造成。 請再次建立作業。 若錯誤持續發生，請與我們連絡，並提供錯誤訊息/記錄。

## <a name="accelerator-troubleshooting"></a>加速器疑難排解

### <a name="access-control"></a>存取控制

**問題**：您在新增角色指派時收到錯誤。

**訊息**：「找不到相符的使用者。」

**矯正措施** 檢查您嘗試對其新增角色指派的電子郵件識別碼。 電子郵件識別碼必須完全符合 Active Directory 中為該使用者註冊的識別碼。 若錯誤持續發生，請與我們連絡，並提供錯誤訊息/記錄。

### <a name="unable-to-log-in-to-accelerator"></a>無法登入加速器

**訊息**：「錯誤：您未獲授權，無法呼叫服務。 請連絡系統管理員以取得授權。」

**矯正措施** 要求系統管理員授權您存取 FarmBeats 部署。 若要執行此動作，您可以執行 RoleAssignment API 的 POST，或透過加速器 [設定] 窗格中的存取控制。  

如果您是在已獲得存取權的情況下遇到此錯誤，請重新整理頁面，然後再試一次。 若錯誤持續發生，請與我們連絡，並提供錯誤訊息/記錄。

![FarmBeats 專案](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器問題  

**問題**：您收到原因不明的加速器錯誤。

**訊息**：「錯誤：發生未知的錯誤。」

**矯正措施**如果您讓頁面閒置太久，就會發生此錯誤。 重新整理頁面。 若錯誤持續發生，請與我們連絡，並提供錯誤訊息/記錄。

**問題**：FarmBeats 加速器不會顯示最新版本，即使在您升級 FarmBeatsDeployment 之後也一樣。

**矯正措施** 此錯誤是因為瀏覽器中的服務背景工作角色持續性所造成。 執行下列動作：

1. 關閉所有已開啟加速器的瀏覽器索引標籤，然後關閉瀏覽器視窗。
2. 啟動新的瀏覽器執行個體，然後重載加速器 URI。 此動作會載入新版的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：映像相關問題

### <a name="wrong-username-or-password"></a>錯誤的使用者名稱或密碼

**作業失敗訊息**：「需要完整驗證才能存取此資源」。

**矯正措施**：執行下列其中一個動作：

- 請使用下列步驟，以正確的使用者名稱/密碼更新 FarmBeats，然後重試此作業。

  **更新 Sentinel 使用者名稱**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在 [搜尋] 方塊中，搜尋 FarmBeats Datahub 資源群組。
    3. 選取儲存體帳戶儲存體***** > [容器] > [batch-prep-files] > [to_vm] > [config.ini]
    4. 選取 [編輯]
    5. 更新 [sentinel_account] 區段中的使用者名稱

  **更新 Sentinel 密碼**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在 [搜尋] 方塊中，搜尋 FarmBeats Datahub 資源群組。
    3. 選取 [keyvault-*****]
    4. 選取 [設定] 底下的 [存取原則]
    5. 選取 [新增存取原則]
    6. 在 [從範本設定] 中使用 [祕密管理]，並將您自己新增為 [主體]
    7. 選取 [新增]，然後在 [存取原則] 頁面上選取 [儲存]
    8. 在 [設定] 底下選取 [祕密]
    9. 選取 [Sentinel-password]
    10. 建立新版的值，並加以啟用。

- 重新執行失敗的作業，或在 5 到 7 天的日期範圍內執行衛星指數作業，然後查看作業是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 中樞：錯誤的 URL 或網站無法存取

**作業失敗訊息**：「哎呀，發生錯誤。 您嘗試存取的網頁 (暫時) 無法使用。」

**矯正措施**：

1. 在瀏覽器中開啟 [Sentinel](https://scihub.copernicus.eu/dhus/)，查看網站是否可存取。
2. 如果無法存取網站，請檢查是否有任何防火牆、公司網路或其他封鎖軟體阻擋網站的存取，然後採取必要的步驟來允許 Sentinel URL。 
3. 重新執行失敗的作業，或在 5 到 7 天的日期範圍內執行衛星指數作業，然後查看作業是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 伺服器：因維護造成的停機

**作業失敗訊息**：「Copernicus Open Access Hub 很快就會恢復！ 很抱歉造成您的不便，我們目前正在進行一些維護作業。 我們很快就會恢復運作！」 

**矯正措施**：

如果在 Sentinel 伺服器上正在進行任何維護活動，就會發生此問題。

1. 如果任何作業或管線因為正在進行的維護而失敗，請在一段時間後重新提交作業。 

   如需有關任何已計畫或非計畫的 Sentinel 維護活動相關資訊，請移至 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 網站。  

2. 重新執行失敗的作業，或在 5 到 7 天的日期範圍內執行衛星指數作業，然後查看作業是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：已達連線數目上限

**作業失敗訊息**：「使用者 ' ' 所達到的兩個並行流程的最大數目 \<username> 。」

**意義**：如果作業因為已達到連線數目上限而失敗，則會在多個作業中使用相同的 Sentinel 帳戶。

**矯正措施**：請試著執行下列作業：

* 等候其他作業完成，再重新執行失敗的作業。
* 建立新的 Sentinel 帳戶，然後更新 FarmBeats 中的 Sentinel 使用者名稱和密碼。

### <a name="sentinel-server-refused-connection"></a>Sentinel 伺服器：拒絕的連線

**作業失敗訊息**：「伺服器已拒絕以下位置的連線： http://172.30.175.69:8983/solr/dhus 」。

**矯正措施**：如果在 Sentinel 伺服器上正在進行任何維護活動，就會發生此問題。

1. 如果任何作業或管線因為正在進行的維護而失敗，請在一段時間後重新提交作業。

   如需有關任何已計畫或非計畫的 Sentinel 維護活動相關資訊，請移至 [Copernicus Open Access Hub News](https://scihub.copernicus.eu/news/) 網站。  

2. 重新執行失敗的作業，或在 5 到 7 天的日期範圍內執行衛星指數作業，然後查看作業是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土壤濕度地圖有白色區域

**問題**：已產生**土壤濕度地圖**，但地圖大部分都是白色區域。

**矯正措施**：如果在要求地圖時產生的衛星指數具有小於 0.3 的 NDVI 值，就會發生此問題。 如需詳細資訊，請瀏覽 [Sentinel 的技術指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。

1. 以不同日期範圍重新執行作業，並檢查衛星指數中的 NDVI 值是否大於 0.3。

## <a name="collect-logs-manually"></a>手動收集記錄

[安裝和部署 Azure 儲存體總管]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>收集 Datahub 中的 Azure Data Factory 作業記錄或 App Service 記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [搜尋] 方塊中，搜尋 FarmBeats Datahub 資源群組。
3. 在**資源群組**儀表板上，搜尋 datahublogs\* 儲存體帳戶。 例如 datahublogsmvxmq。  
4. 在 [名稱] 欄中選取 [儲存體帳戶]，以檢視**儲存體帳戶**儀表板。
5. 在 **datahubblogs\*** 窗格中，選取 [在總管中開啟]，以開啟 **Azure 儲存體總管**應用程式。
6. 在左側窗格中，選取 [Blob 容器]，然後針對 Azure Data Factory 記錄選取 [job-logs]，或針對 App Service 選取 [appinsights-logs]。
7. 選取 [下載]，並將記錄下載到您機器上的本機資料夾。

    ![FarmBeats 專案](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>收集加速器的 Azure Data Factory 作業記錄或 App Service 記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [搜尋] 方塊中，搜尋 FarmBeats 加速器資源群組。
3. 在**資源群組**儀表板上，搜尋 storage\* 儲存體帳戶。 例如 storagedop4k\*。
4. 在 [名稱] 資料行中選取儲存體帳戶，以檢視**儲存體帳戶**儀表板。
5. 在 **storage\*** 窗格中，選取 [在總管中開啟]，以開啟 Azure 儲存體總管應用程式。
6. 在左側窗格中，選取 [Blob 容器]，然後針對 Azure Data Factory 記錄選取 [job-logs]，或針對 App Service 選取 [appinsights-logs]。
7. 選取 [下載]，並將記錄下載到您機器上的本機資料夾。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**：您會收到一封指出**高 CPU 使用量警示**的電子郵件警示。

**矯正措施**：

1. 移至您的 FarmBeats Datahub 資源群組。
2. 選取 [App Service]。  
3. 移至 [App Service 定價頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然後選取適當的定價層。

## <a name="weather-data-job-failures"></a>天氣資料工作失敗

**錯誤**：您執行工作以取得天氣資料，但作業失敗

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>收集記錄以針對氣象資料作業失敗進行疑難排解

1. 移至 Azure 入口網站中的 FarmBeats 資源群組。
2. 按一下屬於資源群組的 Data Factory 服務。 服務將會有一個標記 "sku： Datahub"

> [!NOTE]
> 若要在資源群組內查看服務的標記，請按一下 [編輯資料行]，並將 [標籤] 新增至資源群組視圖

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="FarmBeats 專案":::

3. 在 Data factory 的 [總覽] 頁面上，按一下 [ **撰寫和監視**]。 您的瀏覽器會開啟新的索引標籤。 按一下 [**監視**]

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="FarmBeats 專案":::

4. 您會看到屬於氣象工作執行一部分的管線執行清單。 按一下您要收集記錄的作業
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="FarmBeats 專案":::

5. 在 [管線總覽] 頁面上，您會看到活動執行清單。 記下您要收集記錄的活動執行識別碼
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="FarmBeats 專案":::

6. 返回至 Azure 入口網站中的 FarmBeats 資源群組，然後按一下名為**datahublogs**的儲存體帳戶
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="FarmBeats 專案":::

7. 按一下 [**容器**]  ->  **adfjobs**。 在 [搜尋] 方塊中，輸入您在上述步驟5中記下的工作執行識別碼。
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="FarmBeats 專案":::

8. 搜尋結果將包含包含與工作相關之記錄檔的資料夾。 下載記錄並將其傳送至，以 farmbeatssupport@microsoft.com 協助您進行問題的偵錯工具。
