---
title: 如何手動修正 ServiceNow 同步問題
description: 重設 ServiceNow 的連線，讓 Microsoft Azure 中的警示可以再次呼叫 ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: cea4503c4e3b9dd58cc475aaec355a2bb2e0bd29
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065170"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>針對 ITSM 連接器中的問題進行疑難排解

本文討論 ITSM 連接器中的常見問題，以及如何疑難排解這些問題。

當您在監視資料中找到重要的條件時，Azure 監視器警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 如需警示的詳細資訊，請參閱 Microsoft Azure 中的警示總覽。
客戶可以選取想要在警示上收到警示通知的方式，無論是由郵件、SMS、Webhook 或甚至是將解決方案自動化。 另一個要通知的選項是使用 ITSM。
ITSM 可讓您選擇將警示傳送至外部票證系統，例如 ServiceNow。

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>將事件和變更要求資料視覺化並加以分析

視您設定連線時的設定而定，ITSMC 最多可以同步處理120天的事件和變更要求資料。 本文的 [ [其他資訊] 區段](./itsmc-synced-data.md) 中會提供此資料的記錄檔記錄架構。

您可以使用 ITSMC 儀表板，將事件視覺化並變更要求資料：

![顯示 ITSMC 儀表板的螢幕擷取畫面。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

儀表板也會提供連接器狀態的相關資訊，您可以使用此資訊作為起點來分析連接問題。

### <a name="error-investigation-using-the-dashboard"></a>使用儀表板進行錯誤調查

若要在儀表板中查看錯誤，您應該遵循後續步驟：

1. 在 [ **所有資源**] 中，尋找 **>servicedesk (*您的工作區名稱*)**：

   ![顯示 Azure 入口網站中最近資源的螢幕擷取畫面。](media/itsmc-definition/create-new-connection-from-resource.png)

2. 在左窗格的 [ **工作區資料來源** ] 底下，選取 [ **ITSM 連接**：

   ![顯示 [ITSM 連接] 功能表項目的螢幕擷取畫面。](media/itsmc-overview/add-new-itsm-connection.png)

3. 在 [ **IT 服務管理連接器**] 左側方塊的 [**摘要**] 底下，選取 [ **View Summary**：

    ![顯示視圖摘要的螢幕擷取畫面。](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. 在左側 box **IT 服務管理連接器** 的 [**摘要**] 底下，按一下圖形：

    ![顯示圖形點擊的螢幕擷取畫面。](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. 使用此儀表板，您將能夠檢查連接器中的狀態和錯誤。
    ![顯示連接器狀態的螢幕擷取畫面。](media/itsmc-resync-servicenow/connector-dashboard.png)

### <a name="service-map"></a>服務對應

您也可以在服務對應中將與受影響電腦同步的事件視覺化。

服務對應可自動探索 Windows 和 Linux 系統上的應用程式元件，並對應服務之間的通訊。 它可讓您依自己的想法來查看您的伺服器：做為提供重要服務的互連系統。 服務對應顯示任何 TCP 連線架構的伺服器、進程和埠之間的連接。 除了安裝代理程式以外，不需要進行任何設定。 如需詳細資訊，請參閱 [使用服務對應](../insights/service-map.md)。

如果您使用服務對應，可以查看 ITSM 方案中建立的服務台專案，如下所示：

![顯示 Log Analytics 畫面的螢幕擷取畫面。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>針對 ITSM 連線進行疑難排解

- 如果連接無法連線到 ITSM 系統，而且您 **在儲存連接訊息時發生錯誤** ，請執行下列步驟：
   - 針對 ServiceNow、Cherwell 和 Provance 連線：  
     - 確定您已正確輸入每個連線的使用者名稱、密碼、用戶端識別碼和用戶端密碼。  
     - 請確定您在對應的 ITSM 產品中有足夠的許可權，才能建立連接。  
   - 針對 Service Manager 連接：  
     - 確定已成功部署 web 應用程式，且已建立混合式連接。 若要確認是否已成功建立與內部部署 Service Manager 電腦的連線，請移至 web 應用程式 URL，如建立 [混合](./itsmc-connections-scsm.md#configure-the-hybrid-connection)式連線的檔中所述。  

- 如果 ServiceNow 的資料未同步處理至 Log Analytics，請確定 ServiceNow 實例未進入睡眠狀態。 當 ServiceNow 開發人員實例閒置很長一段時間時，有時會進入睡眠狀態。 如果發生這種情況，請回報問題。
- 如果記錄分析警示引發但未在 ITSM 產品中建立工作專案，如果設定專案未建立/連結至工作專案，或其他資訊，請參閱下列資源：
   -  ITSMC：解決方案會顯示連線、工作專案、電腦等的摘要。 選取具有 **連接器狀態** 標籤的磚。 這樣做會讓您使用相關查詢來 **記錄搜尋** 。 查看記錄檔記錄，以 `LogType_S` `ERROR` 取得詳細資訊。
   - **記錄搜尋** 頁面：使用查詢直接查看錯誤和相關資訊 `*ServiceDeskLog_CL*` 。

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager web 應用程式部署進行疑難排解

-   如果您有 web 應用程式部署的問題，請確定您有權在訂用帳戶中建立/部署資源。
-   如果您在執行 [腳本](itsmc-service-manager-script.md)時取得 **未設定為物件錯誤實例的物件參考**，請確定您已在 [**使用者** 設定] 區段中輸入有效的值。
-   如果您無法建立服務匯流排轉送命名空間，請確定已在訂用帳戶中註冊必要的資源提供者。 如果未註冊，請從 Azure 入口網站手動建立服務匯流排轉送命名空間。 您也可以在 Azure 入口網站中 [建立混合](./itsmc-connections-scsm.md#configure-the-hybrid-connection) 式連線時建立它。

### <a name="how-to-manually-fix-sync-problems"></a>如何手動修正同步問題

Azure 監視器可以連接至協力廠商 IT 服務管理 (ITSM) 提供者。 ServiceNow 是其中一個提供者。

基於安全性理由，您可能需要重新整理與 ServiceNow 連線所用的驗證權杖。
使用下列同步處理常式來重新啟用連線，並重新整理權杖：


1. 在頂端搜尋橫幅中搜尋解決方案，然後選取相關的解決方案

    ![顯示頂端搜尋橫幅，以及要在哪裡選取相關解決方案的螢幕擷取畫面。](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. 在 [方案畫面] 中，選擇 [訂用帳戶] 篩選中的 [全選]，然後依 [>servicedesk] 篩選

    ![顯示要在哪裡選擇 [全選] 以及要依 >servicedesk 篩選之位置的螢幕擷取畫面。](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. 選取 ITSM 連接的解決方案。
1. 在左邊的橫幅中選取 [ITSM 連接]。

    ![顯示選取 ITSM 連接之位置的螢幕擷取畫面。](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. 從清單中選取每個連接器。 
    1. 按一下連接器名稱以進行設定
    1. 刪除不再使用的任何連接器

    1. 根據您的合作夥伴類型，根據 [這些定義](./itsmc-connections.md) 更新欄位

    1. 按一下 [同步]

       ![反白顯示 [同步處理] 按鈕的螢幕擷取畫面。](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. 按一下 [儲存]

        ![新增連線](media/itsmc-resync-servicenow/save-8bit.png)

f.    檢查通知，查看進程是否已啟動。
