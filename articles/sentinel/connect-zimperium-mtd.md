---
title: 將 Zimperium 移動威脅防禦連接到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Zimperium 移動威脅防禦連接到 Azure 哨兵。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587935"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>將 Zimperium 移動威脅防禦連接到 Azure 哨兵


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium 移動威脅防禦資料連線器當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Zimperium 移動威脅防禦連接器使您能夠將 Zimperium 威脅日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 這使您可以更深入地瞭解組織的移動威脅環境，並增強您的安全操作功能。

> [!NOTE]
> 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>配置和連接 Zimperium 移動威脅防禦

Zimperium 移動威脅防禦可以直接集成日誌並將其匯出到**Azure Sentinel。**

1. 在 Azure Sentinel 門戶中，按一下"資料連線器"並選擇 **"Zimperium 移動威脅防禦**"。
2. 選擇 **"打開連接器"頁**。
3. 按照**Zimperium 移動威脅防禦**連接器頁面上的說明進行操作，摘要如下。
 1. 在 zConsole 中，按一下巡覽列上的 **"管理**"。
 2. 按一下 [整合] **** 索引標籤。
 3. 按一下**威脅報告**按鈕，然後按一下 **"添加集成"** 按鈕。
 4. 通過從可用集成中選擇**Microsoft Azure 哨兵**來創建集成，並輸入工作區 ID 和主鍵以連接到 Azure Sentinel。
 5. 也可以選擇用於將威脅資料推送到 Azure Sentinel 的篩選器級別的選項。 

4. 有關詳細資訊，請參閱[Zimperium 客戶支援門戶](https://support.zimperium.com)。


## <a name="find-your-data"></a>查找您的資料

成功建立連接後，資料將顯示在自訂日誌ZimperiumThreatLog_CL和ZimperiumMitigationLog_CL下的日誌分析中。

要在日誌分析中使用相關架構進行 Zimperium 移動威脅防禦，請搜索ZimperiumThreatLog_CL並ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何將 Zimperium 移動威脅防禦連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。

- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

要瞭解有關 Zimperium 的更多，請參閱以下內容：

- [Zimperium](https://zimperium.com)

- [Zimperium 移動安全博客](https://blog.zimperium.com)

- [Zimperium 客戶支援門戶](https://support.zimperium.com)

