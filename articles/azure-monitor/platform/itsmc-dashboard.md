---
title: 使用儀表板進行錯誤調查
description: 本檔包含使用儀表板進行錯誤調查的相關資訊
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540550"
---
# <a name="error-investigation-using-the-dashboard"></a>使用儀表板進行錯誤調查

此頁面包含 ITSM 連接器儀表板的相關資訊。 此儀表板可協助您調查 ITSM 連接器的狀態。

## <a name="how-to-view-the-dashboard"></a>如何查看儀表板

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

## <a name="dashboard-elements"></a>儀表板元素

儀表板包含使用此連接器傳送至 ITSM 工具之警示的資訊。
儀表板分為四個部分：

1. 建立的工作專案：圖表和下表包含每個類型的工作專案計數。 如果您按一下圖形或資料表上的，您可以查看更多有關工作專案的詳細資料。
    ![顯示已建立之工作專案的螢幕擷取畫面。](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. 受影響的電腦：這些表格包含有關建立設定專案之設定專案的詳細資料。
    藉由按一下資料表中的資料列，您可以取得設定專案的進一步詳細資料。
    如果您想要查看所有清單，且您可以按一下 [查看全部]，資料表會包含有限的資料列數目。
    ![顯示受影響電腦的螢幕擷取畫面。](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. 連接器狀態：圖形和下表包含連接器狀態的相關訊息。 按一下資料表中的資料列上的圖表，即可取得有關連接器狀態之訊息的進一步詳細資料。
    如果您想要查看所有清單，且您可以按一下 [查看全部]，資料表會包含有限的資料列數目。
    ![顯示連接器狀態的螢幕擷取畫面。](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. 警示規則：表格包含偵測到的警示規則數目的資訊。
    藉由按一下資料表中的資料列，您可以取得所偵測到之規則的進一步詳細資料。
    如果您想要查看所有清單，且您可以按一下 [查看全部]，資料表會包含有限的資料列數目。
    ![顯示警示規則的螢幕擷取畫面。](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)