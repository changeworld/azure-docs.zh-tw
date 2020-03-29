---
title: 將 Azure 活動資料連接到 Azure 哨兵 |微軟文檔
description: 瞭解如何將 Azure 活動資料連接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 82dfcaf3394703aae531c828a1b96ad290bab798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124994"
---
# <a name="connect-data-from-azure-activity-log"></a>從 Azure 活動日誌連接資料

只需按一下一下，即可將[Azure 活動日誌](../azure-monitor/platform/platform-logs-overview.md)資料流到 Azure Sentinel 中。 活動日誌是一個訂閱日誌，用於記錄和顯示 Azure 中的訂閱級事件，從 Azure 資源管理器運算元據到服務運行狀況事件更新。 使用"活動"日誌，您可以確定對訂閱中的資源執行的任何寫入操作（PUT、POST、DELETE）的"什麼、誰和時間"。 您還可以瞭解操作和其他相關屬性的狀態。 活動日誌不包括使用經典/"RDFE"模型的資源的讀取 （GET） 操作或操作。 

## <a name="prerequisites"></a>Prerequisites

- 您的使用者必須具有日誌分析工作區的"參與者"許可權。
- 使用者必須具有要資料流到 Azure Sentinel 的任何訂閱的讀取器許可權。

## <a name="set-up-the-azure-activity-connector"></a>設置 Azure 活動連接器

1. 在 Azure Sentinel 導航功能表中，選擇 **"資料連線器**"。 從連接器清單中，按一下 Azure**活動**，然後在右下角的 **"打開連接器"頁面**按鈕上按一下。

2. 在 **"說明"** 選項卡下，按一下"**配置 Azure 活動日誌>** 連結。

3. 在**Azure 活動日誌**窗格中，選擇要資料流到 Azure Sentinel 的日誌的訂閱。 

4. 在向右打開的訂閱窗格中，按一下"**連接**"。

5. 要在日誌分析中使用 Azure 活動警報的相關架構，`AzureActivity`請鍵入查詢視窗。

## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Azure 活動日誌連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用[內置](tutorial-detect-threats-built-in.md)或[自訂](tutorial-detect-threats-custom.md)規則開始使用 Azure Sentinel 檢測威脅。
