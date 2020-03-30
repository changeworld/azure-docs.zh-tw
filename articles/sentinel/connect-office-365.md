---
title: 將 Office 365 資料連線到 Azure 哨兵*微軟文檔
description: 瞭解如何將 Office 365 資料連線到 Azure 哨兵。
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
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252505"
---
# <a name="connect-data-from-office-365-logs"></a>連接來自 Office 365 日誌的資料



只需按一下一下，即可將稽核記錄從[Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide)資料流到 Azure Sentinel 中。 可以將 Office 365 中的稽核記錄資料流到同一租戶上的 Azure Sentinel 工作區中。 Office 365 活動日誌連接器提供對正在進行的使用者活動的見解。 您將從 Office 365 獲取有關各種使用者、管理員、系統以及策略操作和事件的資訊。 通過將 Office 365 日誌連接到 Azure Sentinel，可以使用此資料查看儀表板、創建自訂警報並改進調查過程。

> [!IMPORTANT]
> 如果您有 E3 許可證，則必須先為 Office 365 組織啟用統一稽核記錄記錄，然後才能通過 Office 365 管理活動 API 訪問資料。 為此，請打開 Office 365 稽核記錄。 有關說明，請參閱[打開或關閉 Office 365 稽核記錄搜索](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 有關詳細資訊，請參閱[Office 365 管理活動 API 引用](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>Prerequisites

- 您必須是租戶上的全域管理員或安全管理員。
- 租戶必須啟用統一審核。 預設情況下，具有 Office 365 E3 或 E5 許可證的租戶啟用了統一審核。 <br>如果租戶沒有這些許可證之一，則必須使用以下方法之一對租戶啟用統一審核：
    - [使用集-管理稽核記錄配置 Cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps)並啟用參數"啟用統一稽核記錄"。
    - [使用安全&合規性中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。

## <a name="connect-to-office-365"></a>連接至 Office 365

1. 在 Azure 哨兵中，選擇 **"資料"連接器**，然後按一下**Office 365**磁貼。

2. 如果尚未啟用它，則可以通過訪問**資料連線器**邊欄選項卡並選擇**Office 365**連接器來執行此操作。 在這裡，您可以按一下 **"打開連接器頁面**"，並在標記為 **"配置"** 的配置部分下選擇要連接到 Azure Sentinel 的所有 Office 365 活動日誌。 
   > [!NOTE]
   > 如果在 Azure Sentinel 中以前支援的 Office 365 連接器版本中連接了多個租戶，則可以查看和修改從每個租戶收集的日誌。 您將無法添加其他租戶，但可以刪除以前添加的租戶。
3. 要在記錄分析中為 Office 365 日誌使用相關架構，請搜索**OfficeActivity**。


## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何將 Office 365 連接到 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

