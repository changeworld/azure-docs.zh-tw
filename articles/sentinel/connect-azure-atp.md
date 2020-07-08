---
title: 將 Azure ATP 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何只需按一下，就能將記錄從 Azure 進階威脅防護（ATP）串流至 Azure Sentinel。
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
ms.openlocfilehash: f58c38ccfa234752a80c05c300d245c6c9e97cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559173"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>從 Azure 進階威脅防護連接資料（ATP）

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 進階威脅防護資料連線器目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

您只要按一下，就可以將[Azure 進階威脅防護](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)的記錄串流至 Azure Sentinel。

## <a name="prerequisites"></a>必要條件

- 具有全域管理員或安全性系統管理員許可權的使用者
- 您必須是 Azure ATP 的預覽客戶，並且能夠在 Azure ATP 和 Microsoft Cloud App Security 之間進行整合。 如需詳細資訊，請參閱[Azure Advanced Protection 整合](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

## <a name="connect-to-azure-atp"></a>連接到 Azure ATP

請確定已[在您的網路上啟用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP preview 版本。
如果 Azure ATP 已部署並內嵌您的資料，則可以輕鬆地將可疑的警示串流至 Azure Sentinel。 最多可能需要24小時的時間，警示才會開始串流至 Azure Sentinel。


1. 若要將 Azure ATP 連接到 Azure Sentinel，您必須先啟用 Azure ATP 與 Microsoft Cloud App Security 之間的整合。 如需如何執行此動作的詳細資訊，請參閱[Azure 進階威脅防護整合](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中，選取 [**資料連線器**]，然後按一下 [ **Azure 進階威脅防護（預覽）** ] 磚。

1. 您可以選取是否要讓警示 Azure ATP 自動在 Azure Sentinel 中自動產生事件。 在 [建立事件] 底下，選取 [啟用] 來啟用預設分析規則，以自動從已連線安全性服務中產生的警示建立事件。 接著，您可以在 [分析] 下編輯此規則，然後編輯 [有效規則]。

1. 按一下 [ **連接**]。

1. 若要在 Log Analytics 中針對 Azure ATP 警示使用相關的架構，請搜尋**SecurityAlert**。

> [!NOTE]
> 如果警示大於 30 KB，Azure Sentinel 會停止在警示中顯示 [實體] 欄位。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Azure 進階威脅防護連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

