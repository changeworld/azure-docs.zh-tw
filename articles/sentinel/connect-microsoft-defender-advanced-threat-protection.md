---
title: 將 Microsoft Defender ATP 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Microsoft Defender Advanced 威脅防護資料連線到 Azure Sentinel。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80756350"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>從 Microsoft Defender Advanced 威脅防護連接警示 


> [!IMPORTANT]
> Microsoft Defender Advanced 威脅防護警示的內嵌功能目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

[Microsoft Defender Advanced 威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)連接器可讓您將來自 Microsoft Defender Advanced 威脅防護的警示串流至 Azure Sentinel。 這可讓您更全面地分析整個組織的安全性事件，並建立有效且立即回應的快速手冊。

## <a name="prerequisites"></a>必要條件

- 如[設定 Microsoft DEFENDER ATP 部署](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述，您必須擁有 Microsoft Defender Advanced 威脅防護的有效授權。 
- 您必須是 Azure Sentinel 租使用者的系統管理員或安全性系統管理員。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>連接到 Microsoft Defender Advanced 威脅防護

如果已部署 Microsoft Defender Advanced 威脅防護，並內嵌您的資料，則可以輕鬆地將警示串流至 Azure Sentinel。


1. 在 Azure Sentinel 中，選取 [**資料連線器**]，按一下 [ **Microsoft Defender Advanced 威脅防護**] 磚，然後選取 [**開啟連接器] 頁面**。
1. 按一下 [ **連接**]。 
1. 若要在 Log Analytics 中針對 Defender ATP 警示使用相關的架構，請搜尋**SecurityAlert** ，而**提供者名稱**為**MDATP**。




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Defender ATP 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
