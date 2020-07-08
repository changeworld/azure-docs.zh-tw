---
title: 將 Cloud App Security 資料連線到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Microsoft Cloud App Security （MCAS）連接器將警示和 Cloud Discovery 記錄從 MCAS 串流至 Azure Sentinel。 
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 8439c8f7aa4e75abd727d2ce2e80d98e6fce5411
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563955"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>從 Microsoft Cloud App Security 連接資料 

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) （MCAS）連接器可讓您將警示和[CLOUD DISCOVERY 記錄](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)從 MCAS 串流至 Azure Sentinel。 這可讓您深入瞭解您的雲端應用程式、取得精密的分析來識別和對抗網路威脅，以及控制資料的傳輸方式。

## <a name="prerequisites"></a>必要條件

- 您的使用者必須具有工作區的 [讀取] 和 [寫入] 許可權。
- 您的使用者必須具有工作區租使用者的全域管理員或安全性系統管理員許可權。
- 若要將 Cloud Discovery 記錄串流至 Azure Sentinel，請[在 Microsoft Cloud App Security 中啟用 Azure Sentinel 作為您的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> 內嵌 Cloud Discovery 記錄目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>連線至 Cloud App Security

如果您已經有 Cloud App Security，請確定已[在您的網路上啟用該功能](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)。
如果 Cloud App Security 已部署並內嵌您的資料，則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 從 Azure Sentinel 導覽功能表中，選取 [**資料連線器**]。 從連接器清單中，按一下 [ **Microsoft Cloud App Security** ] 磚，然後在右下方的 [**開啟連接器頁面**] 按鈕。

1. 選取您想要串流至 Azure Sentinel 的記錄檔;您可以選擇 [**警示**] 和 [ **Cloud Discovery 記錄**（預覽）]。 

1. 按一下 [套用變更]****。

1. 您可以選取是否要讓警示 Azure 資訊安全中心在 Azure Sentinel 中自動產生事件。 在 [**建立事件**] 底下，選取 [**已啟用**] 以開啟自動從警示建立事件的預設分析規則。 接著，您可以在 [作用中**規則**] 索引標籤中的 [**分析**] 底下編輯此規則。

1. 若要在 Log Analytics 中使用適用于 Cloud App Security 警示的相關架構，請 `SecurityAlert` 在查詢視窗中輸入。 針對 [Cloud Discovery 記錄] 架構，輸入 `McasShadowItReporting` 。

> [!NOTE]
> Cloud Discovery 藉由匯總雲端應用程式的基礎使用者連線資料，協助偵測及識別趨勢。
>
> 由於 Cloud Discovery 的資料會以每日為基礎進行匯總，請注意，最多24小時的最近資料將不會反映在 Azure Sentinel 中。 如果低層級的調查需要更即時的資料，應該直接在原始資料所在的來源應用裝置或服務中完成。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Cloud App Security 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用[內建](tutorial-detect-threats.md)或[自訂](tutorial-detect-threats-custom.md)規則，開始偵測 Azure Sentinel 的威脅。
