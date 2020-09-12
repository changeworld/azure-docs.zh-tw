---
title: 將適用于端點資料的 Microsoft Defender 連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 microsoft Defender for Endpoint (先前的 Microsoft Defender ATP) 資料連線至 Azure Sentinel。
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
ms.openlocfilehash: 69ce6cb42fd18364a7b93faa9cc01d1f793d5cd6
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657538"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>將 microsoft Defender for Endpoint (的警示連線到先前的 Microsoft Defender ATP)  


> [!IMPORTANT]
> 適用于端點警示的 Microsoft Defender 內嵌目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

[Microsoft defender For endpoint](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) connector 可讓您將來自 Microsoft Defender for endpoint 的警示串流至 Azure Sentinel。 這可讓您更全面地分析組織內的安全性事件，並建立腳本，以提供有效且立即的回應。

## <a name="prerequisites"></a>必要條件

- 您必須擁有適用于 Microsoft Defender for Endpoint 的有效授權，如 [安裝 Microsoft defender 以進行端點部署](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 
- 您必須是 Azure Sentinel 租使用者的系統管理員或安全性系統管理員。


## <a name="connect-to-microsoft-defender-for-endpoint"></a>連接到 Microsoft Defender for Endpoint

如果已部署 Microsoft Defender for Endpoint 並擷取您的資料，則可以輕鬆地將警示串流至 Azure Sentinel。


1. 在 Azure Sentinel 中，選取 [ **資料連線器**]，從資源庫中選取 [ **Endpoint (的 Microsoft Defender** ] Microsoft Defender 進階威脅防護) ，然後選取 [ **開啟連接器] 頁面**。
1. 按一下 [ **連接**]。 
1. 若要在 Log Analytics 中使用適用于 Defender ATP 警示的相關架構，請搜尋 **SecurityAlert** ， **提供者名稱** 為 **MDATP**。

## <a name="next-steps"></a>接下來的步驟
在本檔中，您已瞭解如何將 Microsoft Defender for Endpoint 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
