---
title: 將 Zimperium 行動裝置威脅防護連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Zimperium 行動裝置威脅防護連線到 Azure Sentinel。
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: a11d4602882973a24e09c62c12a9dc1dcdc8246d
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501247"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>將您的 Zimperium 行動裝置威脅防護連線到 Azure Sentinel


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium 行動裝置威脅防護資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Zimperium 行動裝置威脅防護連接器可讓您將 Zimperium 威脅記錄與 Azure Sentinel 連線，以查看儀表板、建立自訂警示，以及改善調查。 這可讓您深入瞭解組織的行動威脅環境，並增強您的安全性作業功能。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>設定及連接 Zimperium 行動裝置威脅防護

Zimperium 行動裝置威脅防護可以將記錄直接整合到**Azure Sentinel**，並將其匯出。

1. 在 Azure Sentinel 入口網站中，按一下 [資料連線器]，然後選取 [ **Zimperium Mobile 威脅防護**]。
2. 選取 [**開啟連接器] 頁面**。
3. 依照 [ **Zimperium 行動威脅防護**連接器] 頁面上的指示進行，摘要如下。
 1. 在 zConsole 中，按一下巡覽列上的 [**管理**]。
 2. 按一下 [整合] 索引標籤。
 3. 按一下 [**威脅報告**] 按鈕，然後按 [**新增**整合] 按鈕。
 4. 從可用的整合選取 [ **Microsoft Azure Sentinel** ]，然後輸入 [工作區識別碼] 和 [主要金鑰] 來連線到 Azure Sentinel，以建立整合。
 5. 也提供選取要推送至 Azure Sentinel 之威脅資料的篩選準則層級選項。 

4. 如需其他資訊，請參閱[Zimperium 客戶支援入口網站](https://support.zimperium.com)。


## <a name="find-your-data"></a>尋找您的資料

建立成功的連線之後，資料會顯示在 Log Analytics 的 [CustomLogs ZimperiumThreatLog_CL] 和 [ZimperiumMitigationLog_CL] 之下。

若要在 Log Analytics 中使用相關的 Zimperium 行動裝置威脅防護架構，請搜尋 ZimperiumThreatLog_CL 和 ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要20分鐘的時間，您的記錄才會開始出現在 Log Analytics 中。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Zimperium 行動裝置威脅防護連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。

- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

- [使用活頁簿](tutorial-monitor-your-data.md)來監視您的資料。

若要深入瞭解 Zimperium，請參閱下列各項：

- [Zimperium](https://zimperium.com)

- [Zimperium 行動安全性 Blog](https://blog.zimperium.com)

- [Zimperium 客戶支援入口網站](https://support.zimperium.com)

