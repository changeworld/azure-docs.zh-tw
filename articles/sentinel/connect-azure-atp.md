---
title: 將 Microsoft Defender for Identity (先前 Azure ATP) 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何從 Microsoft Defender 針對身分識別 (Azure 進階威脅防護)  (ATP) 至 Azure Sentinel，只要按一下即可。
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
ms.openlocfilehash: 319e0d29ef297f2a675128d498820a2d5b8ac42e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579516"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>從 Microsoft Defender for Identity (的資料連線先前 Azure 進階威脅防護) 

> [!IMPORTANT]
> Azure Sentinel 中的 Microsoft Defender for Identity data connector 目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只要按一下，您就可以將 [Microsoft Defender For Identity 的](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) 記錄串流至 Azure Sentinel。

## <a name="prerequisites"></a>Prerequisites

- 具有全域管理員或安全性系統管理員許可權的使用者
- 您必須是 Microsoft Defender 身分識別的預覽客戶，並啟用 Microsoft Defender 的身分識別與 Microsoft Cloud App Security 之間的整合。 如需詳細資訊，請參閱 [Microsoft Defender 的身分識別整合](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection)。

## <a name="connect-to-microsoft-defender-for-identity"></a>連接到 Microsoft Defender 以進行身分識別

請確定 [您的網路上已啟用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Microsoft Defender for Identity preview 版本。
如果已部署 Microsoft Defender 的身分識別，並擷取您的資料，則可以輕鬆地將可疑的警示串流至 Azure Sentinel。 最多可能需要24小時的時間，警示才會開始串流至 Azure Sentinel。


1. 若要將 Microsoft Defender for Identity 連接到 Azure Sentinel，您必須先啟用 Microsoft Defender for Identity 和 Microsoft Cloud App Security 之間的整合。 如需如何進行此作業的詳細資訊，請參閱 [Microsoft Defender 的身分識別整合](https://www.microsoft.com/microsoft-365/identity/advance-threat-protection)。

1. 在 Azure Sentinel 中，選取 [ **資料連線器** ]，然後按一下 [ **適用于身分識別的 Microsoft Defender (預覽])** 圖格。

1. 您可以選取是否要讓 Microsoft Defender for Identity 的警示自動在 Azure Sentinel 自動產生事件。 在 [建立事件] 底下，選取 [啟用] 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析] 下編輯此規則，然後編輯 [有效規則]。

1. 按一下 [ **連接** ]。

1. 若要在 Log Analytics 中使用適用于 Microsoft Defender 身分識別警示的相關架構，請搜尋 **SecurityAlert** 。

> [!NOTE]
> 如果警示超過 30 KB，Azure Sentinel 會停止在警示中顯示 [實體] 欄位。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Defender for Identity 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

