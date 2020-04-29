---
title: 針對 Azure FarmBeats 進行疑難排解
description: 本文說明如何針對 Azure FarmBeats 進行疑難排解。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113497"
---
# <a name="troubleshoot"></a>疑難排解

本文提供常見 Azure FarmBeats 問題的解決方案。 如需其他說明，請聯絡我們的[支援論壇](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)， farmbeatssupport@microsoft.com或傳送電子郵件給我們。

> [!NOTE]
  > 如果您在四月期間安裝了 FarmBeats，而您的工作失敗並出現空白的錯誤訊息，表示您的安裝可能未配置任何批次配額，以優先處理重要健康和安全性群組織的支援。 如需詳細資訊，請參閱[這裡](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/)。 您必須要求將 Vm 配置給 Batch 帳戶，才能成功執行作業。

## <a name="install-issues"></a>安裝問題

  > [!NOTE]
  > 如果您因為發生錯誤而重新開機安裝，請務必先刪除**資源群組**，或刪除資源群組中的所有資源，再重新觸發安裝。

### <a name="invalid-sentinel-credentials"></a>不正確 Sentinel 認證

安裝期間提供的 Sentinel 認證不正確。 請使用正確的認證重新開機安裝。

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>已達到指定訂用帳戶之 Batch 帳戶的區域帳戶配額

增加配額，或刪除未使用的 batch 帳戶，然後重新開機安裝。

### <a name="invalid-resource-group-location"></a>資源群組位置無效

請確定**資源群組**所在的位置與安裝期間所指定的**區域**相同。

### <a name="other-install-issues"></a>其他安裝問題

請與我們聯絡下列詳細資料：

- 您的訂用帳戶識別碼
- 資源群組名稱
- 請遵循下列步驟來附加部署失敗的記錄檔：

    1. 流覽至 Azure 入口網站中的**資源群組**。

    2. 在左側的 [**設定**] 區段下選取 [**部署**]。

    3. 針對顯示 [**失敗**] 的每個部署，選取 [到詳細資料] 並下載部署詳細資料。 將此檔案附加至郵件。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="cant-view-telemetry-data"></a>無法查看遙測資料

**徵兆**：已部署裝置或感應器，而且您已將 FarmBeats 與裝置合作夥伴連結，但無法取得或查看 FarmBeats 上的遙測資料。

**更正措施**

1. 移至您的 FarmBeats Datahub 資源群組。
2. 選取**事件中樞**（DatafeedEventHubNamespace），然後檢查傳入訊息的數目。
3. 執行下列任一步驟：

   - 如果沒有內送*訊息*，請聯絡您的裝置合作夥伴。  
   - 如果有*傳入訊息*，請與我們聯絡您的 Datahub 和加速器記錄檔，以及已捕獲的遙測。

若要瞭解如何下載記錄，請移至「[手動收集記錄](#collect-logs-manually)」一節。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器內嵌歷程/串流資料之後，無法查看遙測資料

**徵兆**：已部署裝置或感應器，而且您已在 FarmBeats 上建立裝置/感應器，並將遙測內嵌至 EventHub，但無法取得或查看 FarmBeats 上的遙測資料。

**更正措施**

1. 請確認您已正確完成合作夥伴註冊-您可以前往 datahub swagger，流覽至/Partner API，進行取得，並檢查合作夥伴是否已註冊。 如果沒有，請遵循下列[步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)來新增合作夥伴。

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

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>沒有 Azure 事件中樞連接字串

**更正措施**

1. 在 Datahub Swagger 中，移至合作夥伴 API。
2. 選取 [**取得** > ] [**試試** > 看] [**執行**]。

> [!NOTE]
> 您感興趣之感應器合作夥伴的合作夥伴識別碼。

3. 返回合作夥伴 API，然後選取 [**取得/\<識別碼>**]。
4. 指定步驟3中的 [合作夥伴識別碼]，然後選取 [**執行**]。

   API 回應應具有事件中樞連接字串。

### <a name="device-appears-offline"></a>裝置顯示為離線

**徵兆**：已安裝裝置，且您已將 FarmBeats 與裝置合作夥伴連結。 裝置已上線並傳送遙測資料，但其顯示為離線。

**Corrective action**矯正措施未針對此裝置設定報告間隔。 若要設定報告間隔，請洽詢您的裝置製造商。 

### <a name="error-deleting-a-device"></a>刪除裝置時發生錯誤

當您刪除裝置時，可能會遇到下列其中一個常見的錯誤案例：  

**訊息**：「感應器中參照的裝置：有一或多個感應器與裝置相關聯。 刪除感應器，然後刪除裝置。」  

**意義**：裝置會與部署在伺服器陣列中的多個感應器相關聯。

**更正措施**  

1. 透過 [加速器] 刪除與裝置相關聯的感應器。  
2. 如果您想要將感應器與不同的裝置建立關聯，請要求裝置夥伴執行相同動作。  
3. 使用`DELETE API`呼叫來刪除裝置，並將 force 參數設定為*true*。  

**訊息**： "裝置在裝置中被參照為 ParentDeviceId：有一或多個裝置與此裝置相關聯，做為子裝置。 將其刪除，然後再刪除此裝置。」  

**意義**：您的裝置有其他相關聯的裝置。  

**更正措施**

1. 刪除與此特定裝置相關聯的裝置。  
2. 刪除特定裝置。  

    > [!NOTE]
    > 如果感應器與裝置相關聯，您就無法將它刪除。 如需有關如何刪除相關聯感應器的詳細資訊，請參閱[從感應器合作夥伴取得感應器資料](get-sensor-data-from-sensor-partner.md)中的**刪除感應器**一節。
    > 合作夥伴沒有刪除裝置或感應器的許可權。 只有系統管理員具有刪除的許可權。

## <a name="issues-with-jobs"></a>作業的問題

### <a name="farmbeats-internal-error"></a>FarmBeats 內部錯誤

**訊息**：「FarmBeats 內部錯誤，如需詳細資訊，請參閱疑難排解指南」。

**Corrective action**矯正措施此問題可能是因為資料管線中的暫時性失敗所造成。 再次建立作業。 若錯誤持續發生，請與我們聯絡錯誤訊息/記錄。

## <a name="accelerator-troubleshooting"></a>加速器疑難排解

### <a name="access-control"></a>存取控制

**問題**：您在新增角色指派時收到錯誤。

**訊息**：「找不到相符的使用者」。

**Corrective action**矯正措施檢查您正嘗試新增角色指派的電子郵件識別碼。 電子郵件識別碼必須完全符合識別碼，這會在 Active Directory 中註冊給該使用者。 若錯誤持續發生，請與我們聯絡錯誤訊息/記錄。

### <a name="unable-to-log-in-to-accelerator"></a>無法登入加速器

**訊息**：「錯誤：您沒有呼叫服務的授權。 請洽詢系統管理員以取得授權。」

**Corrective action**矯正措施要求系統管理員授權您存取 FarmBeats 部署。 這可以藉由在快速鍵的 [**設定**] 窗格中，執行 RoleAssignment API 的 POST 或透過存取控制來完成。  

如果您已授與存取權並遇到此錯誤，請重新整理頁面，然後再試一次。 若錯誤持續發生，請與我們聯絡錯誤訊息/記錄。

![專案 FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器問題  

**問題**：您收到無法確定原因的加速器錯誤。

**訊息**：「錯誤：發生未知的錯誤。」

**Corrective action**矯正措施如果您讓頁面閒置太久，就會發生此錯誤。 重新整理頁面。 若錯誤持續發生，請與我們聯絡錯誤訊息/記錄。

**問題**： FarmBeats 加速器未顯示最新版本，即使您已升級 FarmBeatsDeployment 也是一樣。

**Corrective action**矯正措施此錯誤是因為瀏覽器中的服務工作者持續性所造成。 執行下列動作：

1. 關閉所有已開啟快速鍵的瀏覽器索引標籤，然後關閉瀏覽器視窗。
2. 啟動瀏覽器的新實例，然後重載快速鍵 URI。 此動作會載入新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：影像相關問題

### <a name="wrong-username-or-password"></a>錯誤的使用者名稱或密碼

**作業失敗訊息**：「需要完整驗證才能存取此資源」。

**更正動作**：執行下列其中一項：

- 請使用下列步驟，以正確的使用者名稱/密碼更新 FarmBeats，然後重試此作業。

  **更新 Sentinel 使用者名稱**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在**搜尋**方塊中，搜尋 FarmBeats Datahub 資源群組。
    3. 選取儲存體帳戶儲存體 * * * * >**容器** > **batch-準備-** > 檔案**to_vm** > **config.xml**
    4. 選取 [**編輯**]
    5. 更新 [sentinel_account] 區段中的 [使用者名稱]

  **更新 Sentinel 密碼**

    1. 登入 [Azure 入口網站](https://portal.azure.com)。
    2. 在**搜尋**方塊中，搜尋 FarmBeats Datahub 資源群組。
    3. 選取 keyvault-* * * * *
    4. 選取 [設定] 底下的 [存取原則]
    5. 選取 [**新增存取原則**]
    6. 針對 [從範本設定] 使用 [**秘密管理**]，並將您自己新增至主體
    7. 選取 [**新增**]，然後選取 [**存取原則**] 頁面上的 [**儲存**]
    8. 在 [**設定**] 底下選取 [**秘密**]
    9. 選取 [ **Sentinel-密碼**]
    10. 建立值的新版本，並加以啟用。

- 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 中樞：錯誤的 URL 或網站無法存取

**作業失敗訊息**：「糟糕，發生錯誤。 您嘗試存取的網頁是（暫時）無法使用。」

矯正**措施：**

1. 在瀏覽器中開啟[Sentinel](https://scihub.copernicus.eu/dhus/) ，查看網站是否可存取。
2. 如果無法存取網站，請檢查是否有任何防火牆、公司網路或其他封鎖軟體無法存取網站，然後採取必要的步驟來允許 Sentinel URL。 
3. 重新執行失敗的作業，或針對5到7天的日期範圍執行附屬索引作業，然後檢查作業是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 伺服器：關閉以進行維護

**作業失敗訊息**：「Copernicus Open Access Hub 即將恢復！ 很抱歉造成您的不便，我們目前正在執行一些維護。 我們很快就會恢復上線！」 

矯正**措施：**

如果在 Sentinel 伺服器上進行了任何維護活動，就會發生此問題。

1. 如果任何作業或管線因為正在執行維護而失敗，請在一段時間後重新提交作業。 

   如需任何已規劃或非計畫的 Sentinel 維護活動的相關資訊，請移至[Copernicus Open Access Hub 新聞](https://scihub.copernicus.eu/news/)網站。  

2. 重新執行失敗的作業，或針對5到7天的日期範圍執行附屬索引作業，然後檢查作業是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：已達到最大連接數目

**作業失敗訊息**：「使用者的使用者\<名稱> '，達到兩個並行流程的最大數目。」

**意義**：如果作業因為已達到最大連接數目而失敗，則會在多個作業中使用相同的 Sentinel 帳戶。

**更正動作**：請嘗試下列其中一項：

* 等候其他工作完成，再重新執行失敗的工作。
* 建立新的 Sentinel 帳戶，然後更新 FarmBeats 中的 Sentinel 使用者名稱和密碼。

### <a name="sentinel-server-refused-connection"></a>Sentinel 伺服器：拒絕的連接

**作業失敗訊息**：「伺服器拒絕連接于： http://172.30.175.69:8983/solr/dhus。」

**更正動作**：如果在 Sentinel 伺服器上進行任何維護活動，就會發生此問題。

1. 如果任何作業或管線因為正在執行維護而失敗，請在一段時間後重新提交作業。

   如需任何已規劃或非計畫的 Sentinel 維護活動的相關資訊，請移至[Copernicus Open Access Hub 新聞](https://scihub.copernicus.eu/news/)網站。  

2. 重新執行失敗的作業，或針對5到7天的日期範圍執行附屬索引作業，然後檢查作業是否成功。

### <a name="soil-moisture-map-has-white-areas"></a>土裡濕度對應有白色區域

**問題**：已產生「**土裡濕度」對應**，但地圖大多具有白色區域。

**更正動作**：如果針對要求對應的時間產生的附屬索引具有小於0.3 的 NDVI 值，就會發生此問題。 如需詳細資訊，請流覽[Sentinel 的技術指南](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)。

1. 重新執行不同日期範圍的作業，並檢查衛星索引中的 NDVI 值是否大於0.3。

## <a name="collect-logs-manually"></a>手動收集記錄檔

[安裝和部署 Azure 儲存體總管]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>收集 Azure Data Factory 作業記錄或在 Datahub 中 App Service 記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats Datahub 資源群組。
3. 在**資源群組**儀表板上，搜尋*datahublogs\* *儲存體帳戶。 例如， *datahublogsmvxmq*。  
4. 在 [**名稱**] 欄中，選取儲存體帳戶以查看**儲存體帳戶**儀表板。
5. 在 [ **datahubblogs\* ** ] 窗格中，選取 [**在 Explorer 中開啟**] 以查看**open Azure 儲存體總管**應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 Azure Data Factory 記錄的**作業記錄**檔，或 App Service 記錄檔的**appinsights 記錄**檔。
7. 選取 [**下載**]，並將記錄下載到您電腦上的本機資料夾。

    ![專案 FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>收集 Azure Data Factory 作業記錄檔或加速器 App Service 記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats 加速器資源群組。
3. 在**資源群組**儀表板上，搜尋*儲存體\* *儲存體帳戶。 例如， *storagedop4k\**。
4. 在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5. 在 [**儲存體\* ** ] 窗格中，選取 [**在 Explorer 中開啟**] 以開啟 Azure 儲存體總管應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 Azure Data Factory 記錄的**作業記錄**檔，或 App Service 記錄檔的**appinsights 記錄**檔。
7. 選取 [**下載**]，並將記錄下載到您電腦上的本機資料夾。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**：您收到的電子郵件警示參考**高 CPU 使用量警示**。

矯正**措施：**

1. 移至您的 FarmBeats Datahub 資源群組。
2. 選取 [ **App service**]。  
3. 移至 [相應增加[App Service 定價] 頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然後選取適當的定價層。
