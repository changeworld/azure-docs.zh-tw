---
title: 將梭子魚雲根防火牆連接到 Azure 哨兵*微軟文檔
description: 瞭解如何將梭子魚雲根防火牆連接到 Azure 哨兵。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: aaedbfdd3b1bbbc653756d74ee86fc277b21caec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588496"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>連線 Barracuda CloudGen Firewall

梭子魚雲根防火牆 （CGFW） 連接器可讓您輕鬆地將梭子魚 CGFW 日誌與 Azure Sentinel 連接，以查看儀表板、創建自訂警報並改進調查。 這使您可以更深入地瞭解組織的網路，並改進您的安全操作功能。




## <a name="prerequisites"></a>Prerequisites

- 讀取和寫入 Azure Sentinel 工作區的許可權。

- 梭子魚雲根防火牆必須配置為通過 Syslog 匯出日誌。

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>將 Azure 哨兵連接到梭子魚雲根防火牆

1. 在 Azure 門戶中，導航到**Azure 哨兵** > **資料連線器**，然後選擇**梭子魚雲根防火牆**連接器。

2. 選擇 **"打開連接器"頁**。

3. 按照**梭子魚雲根防火牆**頁面上的說明進行操作。


## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將梭子魚雲根防火牆連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


