---
title: 將 Zimperium 行動裝置威脅防護連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Zimperium 行動裝置威脅防護連線至 Azure Sentinel。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77587935"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>將您的 Zimperium 行動裝置威脅防護連線到 Azure Sentinel


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium 行動裝置威脅防護資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Zimperium 行動裝置威脅防護連接器可讓您將 Zimperium 威脅記錄與 Azure Sentinel 連線以查看儀表板、建立自訂警示，以及改進調查。 這可讓您更深入瞭解組織的行動裝置威脅，並增強您的安全性作業功能。

> [!NOTE]
> 資料會儲存在您執行 Azure Sentinel 之工作區的地理位置。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>設定和連接 Zimperium 行動裝置威脅防護

Zimperium 行動裝置威脅防禦可將記錄直接整合並匯出至 **Azure Sentinel**。

1. 在 Azure Sentinel 入口網站中，按一下 [資料連線器]，然後選取 [Zimperium 行動裝置 **威脅防護**]。
2. 選取 [ **開啟連接器] 頁面**。
3. 遵循 Zimperium 行動裝置 **威脅防護** 連接器頁面上的指示，摘要如下。
 1. 在 [zConsole] 中，按一下巡覽列上的 [ **管理** ]。
 2. 按一下 [整合] **** 索引標籤。
 3. 按一下 [ **威脅報告** ] 按鈕，然後按一下 [ **新增** 整合] 按鈕。
 4. 從可用的整合中選取 **Microsoft Azure Sentinel** ，然後輸入工作區識別碼和主要金鑰以連接到 Azure Sentinel，以建立整合。
 5. 也提供選項來選取要推送至 Azure Sentinel 之威脅資料的篩選準則層級。 

4. 如需詳細資訊，請參閱 [Zimperium 客戶支援入口網站](https://support.zimperium.com)。


## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會出現在 Log Analytics 中的 CustomLogs ZimperiumThreatLog_CL，並 ZimperiumMitigationLog_CL。

若要在 Log Analytics 中使用適用于 Zimperium 行動裝置威脅防護的相關架構，請搜尋 ZimperiumThreatLog_CL 和 ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Zimperium Mobile 威脅防護連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。

- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

若要深入瞭解 Zimperium，請參閱下列各項：

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security Blog](https://blog.zimperium.com)

- [Zimperium 客戶支援入口網站](https://support.zimperium.com)

