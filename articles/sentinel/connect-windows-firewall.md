---
title: 將 Windows 防火牆資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Windows 防火牆資料連線到 Azure 哨兵。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588054"
---
# <a name="connect-windows-firewall"></a>連線 Windows 防火牆



Windows 防火牆連接器允許您輕鬆連接 Windows 防火牆日誌（如果它們已連接到 Azure Sentinel 工作區）。 此連接使您能夠查看儀表板、創建自訂警報和改進調查。 這使您可以更深入地瞭解組織的網路，並改進您的安全操作功能。 該解決方案從安裝日誌分析代理的 Windows 電腦收集 Windows 防火牆事件。 


> [!NOTE]
> - 資料將存儲在運行 Azure Sentinel 的工作區的地理位置中。
> - 如果將 Azure Sentinel 和 Azure 安全中心收集到同一工作區，則無需通過此連接器啟用 Windows 防火牆解決方案。 如果啟用了它，它將不會導致重複的資料。 

## <a name="enable-the-connector"></a>啟用連接器 

1. 在 Azure Sentinel 門戶中，選擇 **"資料"連接器**，然後按一下**Windows 防火牆**磁貼。 
1.  如果 Windows 電腦位於 Azure 中：
    1. 按一下**Azure Windows 虛擬機器上的安裝代理**。
    1. 在 **"虛擬機器**"清單中，選擇要資料流到 Azure Sentinel 的 Windows 電腦。 請確保這是 Windows VM。
    1. 在為該 VM 打開的視窗中，按一下"**連接**"。  
    1. 按一下 Windows**防火牆連接器**視窗中**的啟用**。 

2. 如果 Windows 電腦不是 Azure VM：
    1. 按一下**非 Azure 電腦上安裝代理**。
    1. 在 **"直接代理"** 視窗中，選擇 **"下載 Windows 代理（64 位）"** 或 **"下載 Windows 代理"（32 位）。**
    1. 在 Windows 電腦上安裝代理。 複製**工作區 ID、****主鍵**和**輔助鍵**，並在安裝過程中提示時使用它們。

4. 選擇要資料流的資料類型。
5. 按一下 **"安裝解決方案**"。
6. 要在 Windows 防火牆的日誌分析中使用相關架構，請搜索**安全事件**。

## <a name="validate-connectivity"></a>驗證連線能力

可能需要 20 分鐘以上，直到日誌開始出現在日誌分析中。 



## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Windows 防火牆連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

