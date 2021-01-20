---
title: 針對 ITSM 連接器中的問題進行疑難排解
description: 針對 IT 服務管理連接器中的問題進行疑難排解
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a4a7b7a4008d5cc4636e2d533c225a618f35af05
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611180"
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

為了取得有關儀表板調查的詳細資訊，請參閱 [使用儀表板的錯誤調查](./itsmc-dashboard.md)。

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

- 如果記錄分析警示引發但未在 ITSM 產品中建立工作專案，如果設定專案未建立/連結至工作專案，或其他資訊，請參閱下列資源：
   -  ITSMC：解決方案會顯示連線、工作專案、電腦等的 [摘要](itsmc-dashboard.md)。 選取具有 **連接器狀態** 標籤的磚。 這樣做會讓您使用相關查詢來 **記錄搜尋** 。 查看記錄檔記錄，以 `LogType_S` `ERROR` 取得詳細資訊。
   您可以在此查看資料表中有關訊息的詳細[資料。](itsmc-dashboard-errors.md)
   - **記錄搜尋** 頁面：使用查詢直接查看錯誤和相關資訊 `*ServiceDeskLog_CL*` 。

## <a name="common-symptoms---how-should-it-be-resolved"></a>常見的徵兆-如何解決此問題？

下列清單包含常見的徵兆，以及如何解決此問題：

* **徵兆**：已建立重複的工作專案

    **原因**：原因可以是下列兩個選項之一：
    * 針對警示定義了一個以上的 ITSM 動作。
    * 警示已解決。

    **解決** 方式：可以有兩種解決方案：
    * 請確定每個警示都有一個 ITSM 動作群組。
    * 當警示已解決時，ITSM 連接器不支援相符的工作專案狀態更新。 已建立新的已解決工作專案。
* **徵兆**：未建立工作專案

    **原因**：此徵兆可能有幾個原因：
    * ServiceNow 端的程式碼修改
    * 許可權設定錯誤
    * ServiceNow 速率限制太高/低
    * 重新整理權杖已過期
    * ITSM 連接器已刪除

    **解決** 方式：您可以查看 [儀表板](itsmc-dashboard.md) ，並在 [連接器狀態] 區段中檢查錯誤。 請參閱 [常見的錯誤](itsmc-dashboard-errors.md) ，並瞭解如何解決此錯誤。

* **徵兆**：無法建立動作群組的 ITSM 動作

    **原因**：新建立的 ITSM 連接器尚未完成初始同步處理。

    **解決** 方式：您可以檢查 [常見的 UI 錯誤](itsmc-dashboard-errors.md#ui-common-errors) ，並瞭解如何解決此錯誤。