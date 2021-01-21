---
title: 將 Barracuda CloudGen 防火牆連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Barracuda CloudGen 防火牆連線到 Azure Sentinel。
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
ms.openlocfilehash: dd0115fa34b10805570b6e9d8d09ae96fc169dfc
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621126"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>連線 Barracuda CloudGen Firewall

Barracuda CloudGen Firewall (CGFW) connector 可讓您輕鬆地將您的 Barracuda CGFW 記錄與 Azure Sentinel 連線、查看儀表板、建立自訂警示，以及改進調查。 這可讓您深入瞭解組織的網路，並改善安全性作業功能。

## <a name="prerequisites"></a>必要條件

- Azure Sentinel 工作區的讀取和寫入權限。

- Barracuda CloudGen 防火牆必須設定為透過 Syslog 匯出記錄。

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>將 Azure Sentinel 連線至 Barracuda CloudGen 防火牆

1. 在 Azure 入口網站中，流覽至 **Azure Sentinel**  >  **資料連線器**，然後選取 **Barracuda CloudGen 防火牆** 連接器。

2. 選取 [ **開啟連接器] 頁面**。

3. 依照 **Barracuda CloudGen 防火牆** 頁面上的指示進行。


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Barracuda CloudGen 防火牆連線到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。


