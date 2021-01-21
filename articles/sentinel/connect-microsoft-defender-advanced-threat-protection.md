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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98623361"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>將 microsoft Defender for Endpoint (的警示連線到先前的 Microsoft Defender ATP) 

> [!IMPORTANT]
>
> - **Microsoft Defender For Endpoint** 先前稱為 **Microsoft Defender 進階威脅防護** 或 **MDATP**。
>
>     您可能會在產品 (中看到舊名稱仍在使用中，包括 Azure Sentinel) 的資料連線器一段時間。

[Microsoft defender For endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) connector 可讓您將來自 Microsoft Defender for endpoint 的警示串流至 Azure Sentinel。 這可讓您更全面地分析組織內的安全性事件，並建立腳本，以提供有效且立即的回應。

> [!NOTE]
>
> 若要從 Microsoft Defender 針對端點的 [advanced 搜尋](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)內嵌新的原始資料記錄檔，請使用新的連接器來 Microsoft 365 Defender (先前 Microsoft 威脅防護， [請參閱檔](./connect-microsoft-365-defender.md)) 。

## <a name="prerequisites"></a>必要條件

- 您必須擁有適用于 Microsoft Defender for Endpoint 的有效授權，如 [安裝 Microsoft defender 以進行端點部署](/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 

- 您必須是 Azure Sentinel 租使用者的全域管理員或安全性系統管理員。

## <a name="connect-to-microsoft-defender-for-endpoint"></a>連接到 Microsoft Defender for Endpoint

如果已部署 Microsoft Defender for Endpoint 並擷取您的資料，則可以輕鬆地將警示串流至 Azure Sentinel。

1. 在 Azure Sentinel 中，選取 [ **資料連線器**]，從資源庫中選取 [ **Endpoint (的 Microsoft Defender** ] *Microsoft Defender 進階威脅防護*) ，然後選取 [ **開啟連接器] 頁面**。

1. 按一下 [連線]。 

1. 若要在 **記錄** 中查詢 Microsoft Defender 的端點警示，請在查詢視窗中輸入 **SecurityAlert** ，然後新增篩選器，其中 **提供者名稱** 為 **MDATP**。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Defender for Endpoint 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](./tutorial-detect-threats-built-in.md)。