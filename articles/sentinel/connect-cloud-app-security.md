---
title: 將雲端應用安全資料連接到 Azure 哨兵*微軟文件
description: 瞭解如何將雲端應用安全數據連接到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422150"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>連線來自微軟雲應用安全的資料 



[Microsoft 雲應用安全](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)(MCAS) 連接器允許您將警報和[雲發現日誌](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)從 MCAS 流式傳輸到 Azure Sentinel 中。 這將使您能夠深入瞭解雲應用,獲得複雜的分析,以識別和打擊網路威脅,並控制數據傳輸方式。

## <a name="prerequisites"></a>Prerequisites

- 您的用戶必須具有工作區的讀取和寫入許可權。
- 您的用戶必須對工作區的租戶具有全域管理員或安全管理員許可權。
- 要將雲發現日誌流式傳輸到 Azure Sentinel 中,[請啟用 Azure Sentinel 作為 Microsoft 雲端應用安全中的 SIEM。](https://aka.ms/AzureSentinelMCAS)

> [!IMPORTANT]
> 雲發現日誌的引入當前處於公共預覽版中。
> 此功能在沒有服務級別協議的情況下提供,不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>連線至 Cloud App Security

如果您已經擁有雲端應用程式的安全性,請確保您的[網路上已啟用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果部署雲應用安全性並引入數據,則警報數據可以輕鬆地流式傳輸到 Azure Sentinel 中。


1. 在 Azure Sentinel 導航功能表中,選擇 **「資料連接器**」。 從連接器清單中,按一下 Microsoft**雲端應用安全**磁貼,然後單擊右下角的 **「打開連接器」頁面**按鈕。

1. 選擇要流式傳輸到 Azure Sentinel 的日誌;您可以選擇 **「警報**」和「**雲發現日誌**」(預覽)。 

1. 按一下 [套用變更]****。

1. 要在日誌分析中使用雲應用安全警報的日誌分析中的相關架構`SecurityAlert`,請鍵入查詢視窗。 對於雲發現日誌架構,鍵入`McasShadowItReporting`。

> [!NOTE]
> 雲發現通過聚合使用者與雲端應用連接的基礎數據,幫助檢測和識別趨勢。
>
> 由於雲發現數據是每天聚合的,因此請注意,Azure Sentinel 中不會反映最多 24 小時的最新數據。 如果低級調查需要更直接的數據,則應直接在原始數據所在的源設備或服務中進行。

## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何將 Microsoft 雲端應用安全性連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用[內置](tutorial-detect-threats.md)或[自訂](tutorial-detect-threats-custom.md)規則開始使用 Azure Sentinel 檢測威脅。
