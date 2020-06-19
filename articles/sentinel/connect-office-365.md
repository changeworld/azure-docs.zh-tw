---
title: 將 Office 365 資料連線到 Azure Sentinel | Microsoft Docs
description: 了解如何將 Office 365 資料連線到 Azure Sentinel。
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
ms.openlocfilehash: c3e63063b3ea4e7fba3997ddd645aa59fe857488
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758566"
---
# <a name="connect-data-from-office-365-logs"></a>從 Office 365 記錄連線資料



您只要按一下，就可以將 [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) 的稽核記錄串流至 Azure Sentinel。 您可以將來自 Office 365 的稽核記錄串流至相同租用戶上的 Azure Sentinel 工作區。 Office 365 活動記錄連接器可讓您深入了解進行中的使用者活動。 您會從 Office 365 取得各種使用者、管理員、系統和原則動作與事件的相關資訊。 藉由將 Office 365 記錄連線到 Azure Sentinel，您可以使用此資料來檢視儀表板、建立自訂警示，並改善您的調查流程。

> [!IMPORTANT]
> 如果您有 E3 授權，則在可以透過 Office 365 管理活動 API 存取資料之前，您必須先為 Office 365 組織啟用統一的稽核記錄。 您可以開啟 Office 365 稽核記錄來完成此動作。 如需相關指示，請參閱[開啟或關閉 Office 365 稽核記錄搜尋](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。 如需詳細資訊，請參閱 [Office 365 管理活動 API 參考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。

## <a name="prerequisites"></a>Prerequisites

- 您必須是租用戶的全域管理員或安全性系統管理員。
- 您的租用戶必須已啟用統一稽核。 具有 Office 365 E3 或 E5 授權的租用戶，預設會啟用統一稽核。 <br>如果您的租用戶沒有這些授權的其中一種，您就必須使用下列其中一種方法來啟用租用戶的統一稽核功能：
    - [使用 Set-AdminAuditLogConfig Cmdlet](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) 並啟用參數 “UnifiedAuditLogIngestionEnabled”)。
    - [使用安全性與合規性中心 UI](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin)。
   
   > [!NOTE]
   > 目前，O365 資料連接器只會自動擷取 Exchange 和 Sharepoint 活動，如 [連接器] 頁面中的 [資料類型] 區段所述。 [如果您使用 Sentinel 來要求 Teams 稽核資料及保護 Teams，建議您查看這篇文章](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761)。 

## <a name="connect-to-office-365"></a>連接至 Office 365

1. 在 Azure Sentinel 中選取 [資料連接器]，然後按一下 [Office 365] 圖格。

2. 如果您尚未將其啟用，則可以前往 [資料連接器] 刀鋒視窗，然後選取 [Office 365] 連接器來將其啟用。 在這裡，您可以按一下 [開啟連接器頁面]，並在標示為 [設定] 的設定區段下，選取您想要連線至 Azure Sentinel 的所有 Office 365 活動記錄。 
   > [!NOTE]
   > 如果您已在 Azure Sentinel 中，於先前支援的 Office 365 連接器版本中連線到多個租用戶，則能夠檢視和修改要從每個租用戶收集哪些記錄。 您無法新增其他租用戶，但可以移除先前新增的租用戶。
3. 若要在 Log Analytics 中為 Office 365 記錄使用相關結構描述，請搜尋 **OfficeActivity**。


## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 Office 365 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。

