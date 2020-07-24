---
title: 將 Illusive 攻擊管理系統資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何將 Illusive 攻擊管理系統資料連線至 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038111"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>將您的 Illusive 攻擊管理系統連線至 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 Illusive 攻擊管理系統資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文說明如何將您的[Illusive 攻擊管理系統](https://www.illusivenetworks.com/technology/platform/attack-detection-system)連線到 Azure Sentinel。 Illusive 攻擊管理系統資料連線器可讓您與 Azure Sentinel 共用 Illusive 的攻擊面分析資料和事件記錄，並在專用的儀表板中查看這項資訊，讓您深入瞭解組織的受攻擊面風險（ASM 儀表板），並追蹤組織網路中未經授權的橫向移動（ADS 儀表板）。

> [!NOTE]
> 資料將會儲存在您執行 Azure Sentinel 之工作區的地理位置中。

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>將 Illusive 攻擊管理系統記錄轉送到 Syslog 代理程式  

設定受攻擊管理系統，以透過 Syslog 代理程式將 Syslog 訊息以 CEF 格式轉送至您的 Azure 工作區。

1. 登入 Illusive 主控台，然後流覽至 [設定]-[>報告]。

1. 尋找 Syslog 伺服器。

1. 請提供下列資訊：
   - 主機名稱： Linux Syslog 代理程式 IP 位址或 FQDN 主機名稱
   - 埠：514
   - 通訊協定：TCP
   - Audit messages：將 audit 訊息傳送至伺服器

1. 若要新增 syslog 伺服器，請按一下 [新增]。

1. 若要在 Illusive 攻擊管理系統的**記錄**中使用相關的架構，請搜尋**CommonSecurityLog**。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將 Illusive 攻擊管理系統連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：

- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。
