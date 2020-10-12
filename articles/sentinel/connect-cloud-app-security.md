---
title: 將 Cloud App Security 資料連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Microsoft Cloud App Security (MCAS) 連接器，將警示和 Cloud Discovery 記錄從 MCAS 串流至 Azure Sentinel。 
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
ms.openlocfilehash: 5c67f517ffd3704644fbe8041f3ac1e7b0d1f09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904562"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>從 Microsoft Cloud App Security 連接資料 

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) (MCAS) 連接器可讓您將警示和[CLOUD DISCOVERY 記錄](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)從 MCAS 串流至 Azure Sentinel。 這可讓您瞭解雲端應用程式、取得精密的分析，以識別並對抗網路威脅，以及控制資料的傳輸方式。

## <a name="prerequisites"></a>必要條件

- 您的使用者必須具有工作區的讀取和寫入權限。
- 您的使用者必須具有工作區租使用者的全域管理員或安全性系統管理員許可權。
- 若要將 Cloud Discovery 記錄串流至 Azure Sentinel，請 [啟用 Azure Sentinel 做為 Microsoft Cloud App Security 中的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> Cloud Discovery 記錄的內嵌目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>連線至 Cloud App Security

如果您已經有 Cloud App Security，請確定已 [在您的網路上啟用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署並擷取您的資料，則可以輕鬆地將警示資料串流至 Azure Sentinel。


1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。 從連接器清單中，按一下 [ **Microsoft Cloud App Security** ] 圖格，然後按一下右下方的 [ **開啟連接器] 頁面** 按鈕。

1. 選取您要串流至 Azure Sentinel 的記錄檔;您可以 (preview) 中選擇 **警示** 和 **Cloud Discovery 記錄** 。 

1. 按一下 [套用變更]****。

1. 您可以選取是否要讓 Azure Defender 警示 Azure 資訊安全中心在 Azure Sentinel 中自動產生事件。 在 [ **建立事件**] 底下，選取 [ **已啟用** ] 以開啟自動從警示建立事件的預設分析規則。 然後，您可以在 [作用中**規則**] 索引標籤的 [**分析**] 下編輯此規則。

1. 若要在 Log Analytics 中使用相關的架構來 Cloud App Security 警示，請 `SecurityAlert` 在 [查詢] 視窗中輸入。 針對 Cloud Discovery 記錄架構，請輸入 `McasShadowItReporting` 。

> [!NOTE]
> Cloud Discovery 藉由匯總資料的基礎使用者與雲端應用程式的連線，來協助偵測及識別趨勢。
>
> 由於 Cloud Discovery 資料是以每天為基礎進行匯總，因此請注意，最多可達24小時的最新資料，並不會反映在 Azure Sentinel 中。 如果低層級調查需要更多的即時資料，則應該直接在原始資料所在的來源設備或服務中進行。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Microsoft Cloud App Security 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。
