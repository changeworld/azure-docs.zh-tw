---
title: 將 Symantec ICDx 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Symantec ICDx 連接器，輕鬆地將所有 Symantec 安全性解決方案記錄與 Azure Sentinel 連線。
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
ms.openlocfilehash: 67fc80b5f34cf3a98fd39ddc352cb2dd9a5e7151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564917"
---
# <a name="connect-your-symantec-icdx-appliance"></a>連接您的 Symantec ICDx 設備 



Symantec ICDx 連接器可讓您輕鬆地將所有 Symantec 安全性解決方案記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 這可讓您深入瞭解組織的網路，並改善安全性作業功能。 Symantec ICDx 與 Azure Sentinel 之間的整合會使用 REST API。


> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-symantec-icdx"></a>設定並連接 Symantec ICDx 

Symantec ICDx 可以將記錄直接整合和匯出至 Azure Sentinel。

1. 開啟 ICDx 管理主控台，以新增 Microsoft Azure Sentinel (Log Analytics) 轉寄站。
2. 在 ICDx 巡覽列上 **，按一下 [** 設定]。 
3. **在 [設定] 畫面的**頂端 **，按一下 [** 轉寄站]。
4. 在 [轉寄站] 下的 [Microsoft Azure Sentinel (Log Analytics) ] **旁，按一下**[ **新增**]。 
4. 在 [ **Microsoft Azure Sentinel (Log Analytics) ** ] 視窗中，按一下 [ **顯示 Advanced**]。 
5. 在展開至 Microsoft Azure Sentinel (Log Analytics) 視窗的頂端，執行下列步驟：
    -   **名稱**：輸入不超過30個字元之轉寄站的名稱。 選擇唯一且有意義的名稱。 此名稱會出現 **在 [設定] 畫面上** 的轉寄站清單中，以及 **儀表板** 畫面上的儀表板中。 例如： Microsoft Azure Log Analytics 東部。 此為必要欄位。
    -   **描述**：輸入轉寄站的描述。 此描述也會出現在 [設定 **] 畫面上的轉寄** 站清單中。 包含詳細資料，例如轉送的事件種類，以及需要檢查資料的群組。
    -   **啟動類型**：為轉寄站設定選取啟動的方法。 您的選項為 [手動] 和 [自動]。<br>預設值為 [自動]。 
6. 在 [ **事件**] 底下，執行下列動作： 
    - **來源**：選取一或多個要轉寄事件的封存。 您可以選取使用中的收集器封存 (包括一般封存) 、孤立的收集器封存 (也就是您已刪除之收集器的封存) 、ICDx 接收者封存或系統封存。 <br>預設值為一般封存。
      > [!NOTE]
      > ICDx 接收者封存會依名稱分別列出。 
 
    - **篩選**：加入篩選以指定要轉寄的事件子集。 請執行下列其中一項：
        - 若要選取篩選準則，請按一下 [類型]、[屬性]、[運算子] 和 [值]。 
        - 在 [篩選] 欄位中，檢查您的篩選準則。 您可以直接在欄位中編輯，或視需要加以刪除。
        - 按一下 [和] 或 [或] 以新增至篩選準則。
        - 您也可以按一下 [已儲存的查詢] 來套用已儲存的查詢。
    - **包含的屬性**：輸入要包含在轉送資料中的屬性清單（以逗號分隔）。 包含的屬性會優先于排除的屬性。
    - **排除的屬性**：輸入要從轉送的資料中排除的屬性清單（以逗號分隔）。
    - **批次大小**：選取每個批次傳送的事件數目。 您的選項為10、50、100、500和1000。<br>預設值為 100。 
    - **速率限制**：選取轉送事件的速率，以每秒的事件表示。 您的選項為無限制、500、1000、5000、10000。 <br> 預設值為5000。 
7. 在 [ **Azure 目的地**] 底下，執行下列動作： 
    - **工作區識別碼**：貼上以下的工作區識別碼。 此為必要欄位。
    - **主鍵**：貼上下列主要金鑰。 此為必要欄位。
    - **自訂記錄檔名稱**：在您即將轉寄事件的 Microsoft Azure 入口網站 log Analytics 工作區中輸入自訂記錄檔名稱。 預設值為 SymantecICDx。 此為必要欄位。
8. 按一下 [ *儲存* ] 以完成轉寄站設定。 
9. 若要啟動轉寄站，請在 [ **選項**] 底下，按一下 [ **其他** ]，然後 **啟動**。
10. 若要在 Log Analytics 中針對 Symantec ICDx 事件使用相關的架構，請搜尋 **SymantecICDx_CL**。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。 



## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何將 Symantec ICDx 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


