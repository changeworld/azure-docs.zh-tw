---
title: 將安全性 beSECURE 以外的資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Security beSECURE data connector 以外的資料連線器，將 beSECURE 記錄提取至 Azure Sentinel。 查看活頁簿中的 beSECURE 資料、建立警示及改進調查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 313f201aeabd470850b27d979dc5253f80e82a55
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541152"
---
# <a name="connect-your-beyond-security-besecure-to-azure-sentinel"></a>將您的超越安全性 beSECURE 連接到 Azure Sentinel

> [!IMPORTANT]
> 超越安全性 beSECURE 連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

超越安全性 beSECURE 連接器可讓您輕鬆地將所有 beSECURE 安全性解決方案記錄檔與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 BeSECURE 與 Azure Sentinel 之間的整合會使用 REST API。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-besecure"></a>設定並連接 beSECURE

beSECURE 可以直接與 Azure Sentinel 整合和匯出記錄。

1. 在 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **超越安全性 beSECURE (預覽])** 然後 **開啟 [連接器] 頁面**。

1. 遵循下列步驟來設定您的 beSECURE 解決方案，以將掃描結果、掃描狀態和審核記錄記錄傳送至 Azure Sentinel。

    **存取 [整合] 功能表：**
    1. 按一下 [更多] 功能表選項

    1. Select Server

    1. 選取整合

    1. 啟用 Azure Sentinel

    **提供 beSECURE Azure Sentinel 設定：**

      從 Azure Sentinel 連接器] 頁面複製 *工作區識別碼* 和 *主要金鑰* 值，並將其貼到 beSECURE 設定中，然後按一下 [ **修改**]。
      
      :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{工作區識別碼和主要金鑰}":::

## <a name="find-your-data"></a>尋找您的資料

建立成功的連接之後，資料會出現在 [ **記錄**] 的 [ **CustomLogs** ] 區段下的一或多個下列資料表中：
  - `beSECURE_ScanResults_CL`
  - `beSECURE_ScanEvents_CL`
  - `beSECURE_Audit_CL`

若要查詢分析規則中的 beSECURE 記錄、搜尋查詢、調查或 Azure Sentinel 中的其他位置，請在查詢視窗的頂端輸入上述其中一個資料表名稱。

## <a name="validate-connectivity"></a>驗證連線能力
最多可能需要20分鐘的時間，記錄才會出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 beSECURE 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何深入瞭解 [您的資料和潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
