---
title: 針對 Azure FarmBeats 進行疑難排解
description: 本文說明如何針對 Azure FarmBeats 進行疑難排解。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: fb4b06eca0d6df6848e2e215d8890569701f7596
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705610"
---
# <a name="troubleshoot"></a>疑難排解

本文提供常見 Azure FarmBeats 問題的解決方案。

如需其他說明，請聯絡我們 farmbeatssupport@microsoft.com。 請務必在您的電子郵件中包含部署程式 **.log**檔案。

若要下載部署器 **.log**檔案，請執行下列動作：

1. 登入**Azure 入口網站**並選取您的訂用帳戶，然後 Azure AD 租使用者。
2. 從 Azure 入口網站的頂端導覽啟動 Cloud Shell。
3. 選取**Bash**做為慣用的 Cloud Shell 體驗。
4. 選取反白顯示的圖示，然後在下拉式清單中選取 [**下載**]。

    ![專案 FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. 在下一個窗格中，輸入您的部署程式 **.log**檔案的路徑。 例如，輸入**farmbeats-deployer**。

## <a name="sensor-telemetry"></a>感應器遙測

### <a name="cant-view-telemetry-data"></a>無法查看遙測資料

**徵兆**：已部署裝置或感應器，而且您已將 FarmBeats 與裝置合作夥伴連結，但無法取得或查看 FarmBeats 上的遙測資料。

矯正**措施：**

1. 移至您的 FarmBeats Datahub 資源群組。   
2. 選取**事件中樞**（DatafeedEventHubNamespace），然後檢查傳入訊息的數目。
3. 執行下列任一步驟：   
   - 如果沒有內送*訊息*，請聯絡您的裝置合作夥伴。  
   - 如果有*傳入訊息*，請聯絡 farmbeatssupport@microsoft.com。 附加您的 Datahub 和加速器記錄檔，以及已捕獲的遙測。

若要瞭解如何下載記錄，請移至「[手動收集記錄](#collect-logs-manually)」一節。  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>從感應器內嵌歷程/串流資料之後，無法查看遙測資料

**徵兆**：已部署裝置或感應器，而且您已在 FarmBeats 上建立裝置/感應器，並將遙測內嵌至 EventHub，但無法取得或查看 FarmBeats 上的遙測資料。

矯正**措施：**

1. 請確認您已正確完成合作夥伴註冊-您可以前往 datahub swagger，流覽至/Partner API，進行取得，並檢查合作夥伴是否已註冊。 如果沒有，請遵循[這裡的步驟](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)來新增合作夥伴。
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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
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

矯正**措施：**

1. 在 Datahub Swagger 中，移至合作夥伴 API。
2. 選取 [**取得 > 立即** **試用** > **執行**]。
3. 請注意您感興趣之感應器合作夥伴的合作夥伴識別碼。
4. 返回合作夥伴 API，然後選取 [**取得/\<識別碼] >** 。
5. 指定步驟3中的 [合作夥伴識別碼]，然後選取 [**執行**]。

   API 回應應具有事件中樞連接字串。

### <a name="device-appears-offline"></a>裝置顯示為離線

**徵兆**：已安裝裝置，且您已將 FarmBeats 與裝置合作夥伴連結。 裝置已上線並傳送遙測資料，但其顯示為離線。

**更正動作**：未針對此裝置設定報告間隔。 若要設定報告間隔，請洽詢您的裝置製造商。 

### <a name="error-deleting-a-device"></a>刪除裝置時發生錯誤

當您刪除裝置時，可能會遇到下列其中一個常見的錯誤案例：  

**訊息**：「感應器中參照的裝置：有一或多個感應器與裝置相關聯。 刪除感應器，然後刪除裝置。」  

**意義**：裝置會與部署在伺服器陣列中的多個感應器相關聯。   

矯正**措施：**  

1. 透過 [加速器] 刪除與裝置相關聯的感應器。  
2. 如果您想要將感應器與不同的裝置建立關聯，請要求裝置夥伴執行相同動作。  
3. 使用 `DELETE API` 呼叫來刪除裝置，並將 force 參數設定為*true*。  

**訊息**： "裝置在裝置中被參照為 ParentDeviceId：有一或多個裝置與此裝置相關聯，做為子裝置。 將其刪除，然後再刪除此裝置。」  

**意義**：您的裝置有其他相關聯的裝置。  

**矯正措施**

1. 刪除與此特定裝置相關聯的裝置。  
2. 刪除特定裝置。  

    > [!NOTE]
    > 如果感應器與裝置相關聯，您就無法將它刪除。 如需有關如何刪除相關聯感應器的詳細資訊，請參閱[從感應器合作夥伴取得感應器資料](get-sensor-data-from-sensor-partner.md)中的「刪除感應器」一節。


## <a name="issues-with-jobs"></a>作業的問題

### <a name="farmbeats-internal-error"></a>FarmBeats 內部錯誤

**訊息**：「FarmBeats 內部錯誤，如需詳細資訊，請參閱疑難排解指南」。

**更正動作**：此問題可能是因為資料管線中的暫時性失敗所造成。 再次建立作業。 若錯誤持續發生，請將錯誤訊息新增至 FarmBeats 論壇上的文章，或連絡人 FarmBeatsSupport@microsoft.com。

## <a name="accelerator-troubleshooting"></a>加速器疑難排解

### <a name="access-control"></a>存取控制

**問題**：您在新增角色指派時收到錯誤。

**訊息**：「找不到相符的使用者」。

**更正動作**：檢查您嘗試新增角色指派的電子郵件識別碼。 電子郵件識別碼必須完全符合在 Active Directory 中為該使用者註冊的識別碼。 若錯誤持續發生，請將錯誤訊息新增至 FarmBeats 論壇上的文章，或連絡人 FarmBeatsSupport@microsoft.com。

### <a name="unable-to-log-in-to-accelerator"></a>無法登入加速器

**訊息**：「錯誤：您沒有呼叫服務的授權。 請洽詢系統管理員以取得授權。」

**更正動作**：要求系統管理員授權您存取 FarmBeats 部署。 這可以藉由在快速鍵的 [**設定**] 窗格中，執行 RoleAssignment API 的 POST 或透過存取控制來完成。  

如果您已授與存取權，並遇到此錯誤，請重新整理頁面，然後再試一次。 若錯誤持續發生，請將錯誤訊息新增至 FarmBeats 論壇上的文章，或連絡人 FarmBeatsSupport@microsoft.com。

![專案 FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器問題  

**問題**：您收到無法確定原因的加速器錯誤。

**訊息**：「錯誤：發生未知的錯誤。」

**更正動作**：如果您讓頁面閒置太久，就會發生此錯誤。 重新整理頁面。  

若錯誤持續發生，請將錯誤訊息新增至 FarmBeats 論壇上的文章，或連絡人 FarmBeatsSupport@microsoft.com。

**問題**： FarmBeats 加速器未顯示最新版本，即使您已升級 FarmBeatsDeployment 也是一樣。

**更正動作**：此錯誤是因為瀏覽器中的服務工作者持續性所造成。 執行下列動作：
1. 關閉所有已開啟快速鍵的瀏覽器索引標籤，然後關閉瀏覽器視窗。
2. 啟動瀏覽器的新實例，然後重載快速鍵 URI。 此動作會載入新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：影像相關問題

### <a name="wrong-username-or-password"></a>錯誤的使用者名稱或密碼

**作業失敗訊息**：「需要完整驗證才能存取此資源」。

矯正**措施：**

執行下列其中一個動作：
- 重新執行安裝程式，以正確的使用者名稱和密碼來升級 Datahub。
- 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel 中樞：錯誤的 URL 或網站無法存取 

**作業失敗訊息**：「糟糕，發生錯誤。 您嘗試存取的網頁是（暫時）無法使用。」 

矯正**措施：**
1. 在瀏覽器中開啟[Sentinel](https://scihub.copernicus.eu/dhus/) ，查看網站是否可存取。 
2. 如果無法存取網站，請查看是否有任何防火牆、公司網路或其他封鎖軟體無法存取網站，然後採取必要的步驟來允許 Sentinel URL。 
3. 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 伺服器：關閉以進行維護

**作業失敗訊息**：「Copernicus Open Access Hub 即將恢復！ 很抱歉造成您的不便，我們目前正在執行一些維護。 我們很快就會恢復上線！」 

矯正**措施：**

如果在 Sentinel 伺服器上進行了任何維護活動，就會發生此問題。

1. 如果任何作業或管線因為正在執行維護而失敗，請在一段時間後重新提交作業。 

   如需任何已規劃或非計畫的 Sentinel 維護活動的相關資訊，請移至[Copernicus Open Access Hub 新聞](https://scihub.copernicus.eu/news/)網站。  
2. 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：已達到最大連接數目

**作業失敗訊息**：「使用者 '\<username > ' 的兩個並行流程數目上限。」

**意義**：如果作業因為已達到最大連接數目而失敗，則會在另一個軟體部署中使用相同的 Sentinel 帳戶。

**更正動作**：請嘗試下列其中一項：
* 建立新的 Sentinel 帳戶，然後重新執行安裝程式，以使用新的 Sentinel 使用者名稱和密碼來升級 Datahub。  
* 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。

### <a name="sentinel-server-refused-connection"></a>Sentinel 伺服器：拒絕的連接 

**作業失敗訊息**：「伺服器拒絕連接于： http://172.30.175.69:8983/solr/dhus 」。 

**更正動作**：如果在 Sentinel 伺服器上進行任何維護活動，就會發生此問題。 
1. 如果任何作業或管線因為正在執行維護而失敗，請在一段時間後重新提交作業。 

   如需任何已規劃或非計畫的 Sentinel 維護活動的相關資訊，請移至[Copernicus Open Access Hub 新聞](https://scihub.copernicus.eu/news/)網站。  
2. 重新執行失敗的作業，或在5到7天的日期範圍內執行附屬索引作業，然後查看作業是否成功。

## <a name="collect-logs-manually"></a>手動收集記錄檔

[安裝和部署 Azure 儲存體總管]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>在 Datahub 中收集 Azure Data Factory 作業記錄
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats Datahub 資源群組。

    > [!NOTE]
    > 選取您在 FarmBeats 安裝期間指定的 Datahub 資源群組。

3. 在**資源群組**儀表板上，搜尋*datahublogs\** 儲存體帳戶。 例如，搜尋**datahublogsmvxmq**。  
4. 在 [**名稱**] 欄中，選取儲存體帳戶以查看**儲存體帳戶**儀表板。
5. 在 [ **datahubblogs\*** ] 窗格中，選取 [**在 Explorer 中開啟**] 以查看**開啟的 Azure 儲存體總管**應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 [**作業記錄**]。
7. 在 [**作業-記錄**檔] 窗格中，選取 [**下載**]。
8. 將記錄下載到您電腦上的本機資料夾。
9. 以電子郵件將下載的 .zip 檔案傳送至 farmbeatssupport@microsoft.com。

    ![專案 FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>在加速器中收集 Azure Data Factory 作業記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats 加速器資源群組。

    > [!NOTE]
    > 選取您在 FarmBeats 安裝期間指定的加速器資源群組。

3. 在**資源群組**儀表板上，搜尋*儲存體\** 儲存體帳戶。 例如，搜尋**storagedop4k\*** 。
4. 在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5. 在 [**儲存體\*** ] 窗格中，選取 [**在 Explorer 中開啟**] 以開啟 Azure 儲存體總管應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 [**作業記錄**]。
7. 在 [**作業-記錄**檔] 窗格中，選取 [**下載**]。
8. 將記錄下載到您電腦上的本機資料夾。
9. 以電子郵件將下載的 .zip 檔案傳送至 farmbeatssupport@microsoft.com。


### <a name="collect-datahub-app-service-logs"></a>收集 Datahub app service 記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats Datahub 資源群組。

    > [!NOTE]
    > 選取您在 FarmBeats 安裝期間指定的 Datahub 資源群組。

3. 在資源群組中，搜尋*datahublogs\** 儲存體帳戶。 例如，搜尋**fordatahublogsmvxmq\*** 。
4. 在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5. 在 [ **datahubblogs\*** ] 窗格中，選取 [**在 Explorer 中開啟**] 以開啟 Azure 儲存體總管應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 [ **appinsights-logs**]。
7. 在 [ **appinsights-記錄**] 窗格中，選取 [**下載**]。
8. 將記錄下載到您電腦上的本機資料夾。
9. 以電子郵件將下載的 .zip 檔案傳送至 farmbeatssupport@microsoft.com。

### <a name="collect-accelerator-app-service-logs"></a>收集加速器應用程式服務記錄

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在**搜尋**方塊中，搜尋 FarmBeats 加速器資源群組。

    > [!NOTE]
    > 選取在 FarmBeats 安裝期間所提供的 FarmBeats 加速器資源群組。

3. 在資源群組中，搜尋*儲存體\** 儲存體帳戶。 例如，搜尋**storagedop4k\*** 。
4. 在 [**名稱**] 資料行中選取儲存體帳戶，以查看**儲存體帳戶**儀表板。
5. 在 [**儲存體\*** ] 窗格中，選取 [**在 Explorer 中開啟**] 以開啟 Azure 儲存體總管應用程式。
6. 在左窗格中，選取 [ **Blob 容器**]，然後選取 [ **appinsights-logs**]。
7. 在 [ **appinsights-記錄**] 窗格中，選取 [**下載**]。
8. 將記錄下載到您電腦上的本機資料夾。
9. 將下載的資料夾以電子郵件傳送至 farmbeatssupport@microsoft.com。

## <a name="known-issues"></a>已知問題

## <a name="batch-related-issues"></a>批次相關問題

**錯誤訊息**：「已達到指定訂用帳戶的 Batch 帳戶的區域帳戶配額」。

**更正動作**：增加配額，或刪除未使用的 batch 帳戶，然後重新執行部署。

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory （Azure AD）相關問題

**錯誤訊息**：「無法將必要的設定更新為 Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：許可權不足，無法完成作業。 請確定已針對 Azure AD App 正確設定上述設定。」

**意義**： Azure AD 應用程式註冊設定未正確完成。  

**更正動作**：向 IT 系統管理員（具有租使用者讀取權限的人員）要求使用我們的[腳本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)來建立 Azure AD 應用程式註冊。 此腳本也會自動負責設定步驟。

**錯誤訊息**：「無法在此租使用者中建立新的 Active Directory 應用程式 '\<應用程式名稱\>'：已有另一個物件具有相同的屬性識別碼 uri 值。」

**表示**：已有相同名稱的 Azure AD 應用程式註冊。

**更正動作**：刪除現有的 Azure AD 應用程式註冊，或重複使用它進行安裝。 如果您要重複使用現有的 Azure AD 應用程式註冊，請將應用程式識別碼和用戶端秘密傳遞至安裝程式，並重新部署。

## <a name="issues-with-the-inputjson-file"></a>輸入 json 檔案的問題

**錯誤**：從*輸入 json*檔案讀取輸入時發生錯誤。

**更正動作**：此問題通常是因為指定正確的*輸入 json*檔案路徑或名稱給安裝程式時發生錯誤。 進行適當的更正，然後重試部署。

**錯誤**：剖析*輸入 json*檔案中的值時發生錯誤。

**更正動作**：因為*輸入 json*檔案中的值不正確，所以大部分都會發生此問題。 進行適當的更正，然後重試部署。

## <a name="high-cpu-usage"></a>高 CPU 用量

**錯誤**：您收到的電子郵件警示參考**高 CPU 使用量警示**。 

矯正**措施：** 
1. 移至您的 FarmBeats Datahub 資源群組。
2. 選取 [ **App service**]。  
3. 移至 [相應增加[App Service 定價] 頁面](https://azure.microsoft.com/pricing/details/app-service/windows/)，然後選取適當的定價層。

## <a name="next-steps"></a>後續步驟

如果您仍然遇到 FarmBeats 問題，請洽詢我們的[支援論壇](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)。
