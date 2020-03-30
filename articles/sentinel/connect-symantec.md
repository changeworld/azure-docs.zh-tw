---
title: 將賽門鐵克 ICDx 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將賽門鐵克 ICDx 資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: cac63aee5f9ebf3859b138e6444e40b1e2dd30f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588088"
---
# <a name="connect-your-symantec-icdx-appliance"></a>連接賽門鐵克 ICDx 設備 



賽門鐵克 ICDx 連接器允許您輕鬆地將所有賽門鐵克安全解決方案日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 這使您可以更深入地瞭解組織的網路，並改進您的安全操作功能。 賽門鐵克 ICDx 和 Azure 哨兵之間的集成利用了 REST API。


> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-symantec-icdx"></a>配置和連接賽門鐵克 ICDx 

賽門鐵克 ICDx 可以直接集成日誌並將其匯出到 Azure Sentinel。

1. 打開 ICDx 管理主控台以添加 Microsoft Azure Sentinel（日誌分析）轉寄站。
2. 在 ICDx 巡覽列上，按一下"**配置**"。 
3. 在 **"配置"** 螢幕頂部，按一下 **"轉寄站**"。
4. 在**轉寄站**下，在 Microsoft Azure 哨兵（日誌分析）旁邊，按一下"**添加**"。 
4. 在**Microsoft Azure 哨兵（日誌分析）** 視窗中，按一下"**顯示高級**"。 
5. 在擴展到 Microsoft Azure 哨兵（日誌分析）視窗的頂部，執行以下操作：
    -   **名稱**： 為不超過 30 個字元的轉寄站鍵入名稱。 選擇唯一、有意義的名稱。 此名稱將顯示在 **"配置"** 螢幕上的轉寄站清單中和**儀表板**螢幕上的儀表板中。 例如：微軟 Azure 日誌分析東部。 這是必填欄位。
    -   **說明**： 鍵入轉寄站的說明。 此說明也顯示在 **"配置**"螢幕上的轉寄站清單中。 包括要轉發的事件種類和需要檢查資料的組等詳細資訊。
    -   **啟動類型**：為轉寄站配置選擇啟動方法。 您的選項是手動和自動的。<br>預設值為"自動"。 
6. 在 **"事件"** 下，執行以下操作： 
    - **源**： 選擇要轉發事件的一個或多個存檔。 您可以選擇活動收集器存檔（包括公共存檔）、孤立收集器存檔（即已刪除的收集器的存檔）、ICDx 接收器存檔或系統存檔。 <br>預設值為"通用存檔"。
      > [!NOTE]
      > ICDx 接收器存檔按名稱單獨列出。 
 
    - **篩選器**：添加一個篩選器，指定要轉發的事件子集。 執行下列其中一個動作：
        - 要選擇篩選器條件，請按一下"類型、屬性、運算子"和"值"。 
        - 在"篩選器"欄位中，查看篩選器條件。 您可以直接在欄位中編輯它，也可以根據需要將其刪除。
        - 按一下 AND 或 OR 以添加到篩選器條件。
        - 您還可以按一下"保存的查詢"以應用已保存的查詢。
    - **包含屬性**：鍵入要包含在轉發資料中的屬性的逗號分隔清單。 包含的屬性優先于排除的屬性。
    - **排除屬性**：鍵入要從轉發的資料中排除的屬性的逗號分隔清單。
    - **批次處理大小**：選擇每個批次處理要發送的事件數。 您的選項是 10、50、100、500 和 1000。<br>預設值為 100。 
    - **速率限制**：選擇轉發事件的速率，以每秒事件表示。 您的選項是無限，500，1000，5000，10000。 <br> 預設值為 5000。 
7. 在**Azure 目標**下，執行以下操作： 
    - **工作區 ID**：從下面粘貼工作區 ID。 這是必填欄位。
    - **主鍵**：從下面粘貼主金鑰。 這是必填欄位。
    - **自訂日誌名稱**：在要轉發事件的 Microsoft Azure 門戶日誌分析工作區中鍵入自訂日誌名稱。 預設值為賽門鐵克ICDx。 這是必填欄位。
8. 按一下 *"保存*"以完成轉寄站配置。 
9. 要啟動轉寄站，請在 **"選項**"下按一下 **"更多**"，然後**開始**。
10. 要在日誌分析中為賽門鐵克 ICDx 事件使用相關架構，請搜索**SymantecICDx_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 



## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將賽門鐵克 ICDx 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


