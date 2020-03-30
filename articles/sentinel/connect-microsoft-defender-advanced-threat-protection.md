---
title: 將微軟防禦者 ATP 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Microsoft 防禦者高級威脅保護資料連線到 Azure 哨兵。
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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588207"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>連接來自 Microsoft 防禦者高級威脅防護的警報 


> [!IMPORTANT]
> 微軟防禦者高級威脅保護日誌的引入目前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

只需按一下一下，即可將來自[Microsoft 防禦者高級威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)的警報資料流到 Azure 哨兵中。 此連接使您能夠將來自 Microsoft 防禦器高級威脅保護的警報資料流到 Azure Sentinel 中。 

## <a name="prerequisites"></a>Prerequisites

- 微軟防禦者高級威脅保護的有效許可證，如[驗證許可預配中所述，並完成為 Microsoft 防禦者高級威脅保護設置](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)。 
- 您必須是 Azure Sentinel 租戶上的管理員或安全管理員。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>連接到微軟防禦者高級威脅保護

如果部署 Microsoft 防禦器高級威脅保護並引入您的資料，則警報可以輕鬆地資料流到 Azure Sentinel 中。


1. 在 Azure 哨兵中，選擇 **"資料連線器**"，按一下**Microsoft 防禦者高級威脅保護**磁貼並選擇 **"打開連接器"頁**。
1. 按一下 [連線]****。 
1. 要在日誌分析中為防禦者 ATP 警報使用相關架構，請搜索**安全警報**，**供應商名稱**為**MDATP**。




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Microsoft 防禦者 ATP 連接到 Azure 哨兵。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
