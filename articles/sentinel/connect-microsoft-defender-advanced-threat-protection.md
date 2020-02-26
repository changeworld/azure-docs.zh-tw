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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588207"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>從 Microsoft Defender Advanced 威脅防護連接警示 


> [!IMPORTANT]
> Microsoft Defender Advanced 威脅防護記錄的內嵌功能目前為公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

您只要按一下，就可以將來自[Microsoft Defender Advanced 威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)的警示串流到 Azure Sentinel。 此連線可讓您將來自 Microsoft Defender Advanced 威脅防護的警示串流至 Azure Sentinel。 

## <a name="prerequisites"></a>Prerequisites

- 如[驗證授權布建和完整設定 Microsoft Defender Advanced 威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述，已啟用 Microsoft Defender Advanced 威脅防護的有效授權。 
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
