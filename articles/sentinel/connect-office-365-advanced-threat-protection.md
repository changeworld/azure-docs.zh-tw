---
title: 將 Microsoft Defender for Office 365 (先前的 Office 365 ATP) 資料連線至 Azure Sentinel |Microsoft Docs
description: 將適用于 Office 365 的 Microsoft Defender 資料內嵌至 Azure Sentinel，以取得可見度並建立自動化的回應案例。
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655489"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>從適用于 Office 365 的 Microsoft Defender 連接警示 

> [!IMPORTANT]
>
> - **適用于 office 365 的 Microsoft Defender** 先前稱為 **Office 365 Advanced 威脅防護 (ATP)**。
>
>     您可能會在產品 (中看到舊名稱仍在使用中，包括 Azure Sentinel) 的資料連線器一段時間。
>
> - Microsoft Defender for Office 365 警示目前處於公開預覽狀態。 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
[適用于 Office 365 的 Microsoft Defender](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) 可保護您的組織，避免在電子郵件訊息、惡意 URL 連結和共同作業工具中有未知惡意程式碼所造成的零天和其他先進的威脅。 藉由擷取 Microsoft Defender for Office 365 警示進入 Azure Sentinel，您將能夠在安全性作業中使用電子郵件、檔案共用和 URL 型威脅的相關資訊。 然後，您可以更全面地分析組織內的安全性事件，並建立工作手冊以進行有效且立即的回應。

連接器會匯入下列警示：

- 偵測到潛在的惡意 URL click 

- 電子郵件訊息包含在傳遞後移除的惡意程式碼

- 傳送電子郵件訊息，其中包含在傳遞後移除的網路釣魚 Url 

- 使用者回報為惡意程式碼或網路釣魚的電子郵件 

- 偵測到可疑的電子郵件傳送模式 

- 使用者限制無法傳送電子郵件 

Office **安全性與合規性中心** 的 office 客戶可以看到這些警示。

## <a name="prerequisites"></a>先決條件

- 當您啟用連接器時，您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須是全域管理員或 Azure Sentinel 工作區租使用者的安全性系統管理員。

- 您必須擁有 office [365 ATP 方案 2](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) 的有效授權 (隨附于 Office 365 E5、Office 365 A5 和 Microsoft 365 E5 授權，並且可個別購買) 。 

## <a name="connect-to-microsoft-defender-for-office-365"></a>連接到適用于 Office 365 的 Microsoft Defender

如果已部署適用于 Office 365 的 Microsoft Defender，且已設定原則，則可以輕鬆地將警示內嵌至 Azure Sentinel。

1. 在 Azure Sentinel 中，從導覽功能表中選取 [ **資料連線器** ]。

1. 選取 [連接器資源庫] 中的 [ **Microsoft Defender For office 365** ] (可能仍稱為 [ *Office 365 Advanced 威脅防護* ]) ，然後選取 [ **開啟連接器] 頁面**。

1. **在 [設定**] 區段中，按一下 [連線 **]**。 

1. 在 [ **建立事件** ] 區段中，按一下 [ **啟用**]。

1. 若要使用相關的架構來查詢 Office 365 ATP 警示，請搜尋 **SecurityAlert** 並指定 **提供者名稱** 為 **OATP**。

1. 選取 [ **下一** 步] 索引標籤，以查看並使用與 Microsoft Defender for Office 365 連接器配套的查詢範例和分析規則範本。

## <a name="next-steps"></a>後續步驟

在本檔中，您已瞭解如何將適用于 Office 365 的 Microsoft Defender 連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](./tutorial-detect-threats-built-in.md)。