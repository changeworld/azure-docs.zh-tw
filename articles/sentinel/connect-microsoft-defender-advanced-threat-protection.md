---
title: 將微軟防禦者 ATP 資料連接到 Azure 哨兵*微軟文件
description: 瞭解如何將 Microsoft 防禦者高級威脅保護數據連接到 Azure 哨兵。
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756350"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>連接來自 Microsoft 防禦者高級威脅防護的警報 


> [!IMPORTANT]
> 微軟防禦者高級威脅保護警報的引入目前處於公共預覽版中。
> 此功能在沒有服務級別協議的情況下提供,不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

[Microsoft 防禦者高級威脅保護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)連接器允許您將來自 Microsoft 防禦者高級威脅保護的警報流式傳輸到 Azure Sentinel 中。 這將使您能夠更全面地分析整個組織的安全事件,並構建行動手冊,以便有效和立即回應。

## <a name="prerequisites"></a>Prerequisites

- 您必須擁有 Microsoft 防禦者高級威脅保護的有效許可證,如設置[Microsoft Defender ATP 部署](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 
- 您必須是 Azure Sentinel 租戶上的管理員或安全管理員。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>連接到微軟防禦者進階威脅保護

如果部署 Microsoft 防禦器進階威脅保護並引入您的資料,則警報可以輕鬆地流式傳輸到 Azure Sentinel 中。


1. 在 Azure 哨兵中,選擇 **「資料連線器**」,按下**Microsoft 防禦者進階威脅保護**磁貼並選擇 **「打開連接器」 頁**。
1. 按一下 [ **連接**]。 
1. 要在紀錄分析中為防禦者 ATP 警報使用相關架構,請搜尋**安全警報**,**提供者名稱**為**MDATP**。




## <a name="next-steps"></a>後續步驟
在本文件中,您學習了如何將 Microsoft 防禦者 ATP 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
