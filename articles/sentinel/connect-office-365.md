---
title: 將 Office 365 記錄連接到 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Office 365 記錄連接器來帶入 Exchange 和 SharePoint （包括 OneDrive）中進行中的使用者和系統管理活動的相關資訊。
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
ms.date: 05/21/2020
ms.author: yelevin
ms.openlocfilehash: 180b25f80bd27caea20b1c17cd84fda38c172e0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559342"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>將 Office 365 記錄連接到 Azure Sentinel

[Office 365](https://docs.microsoft.com/office/)記錄連接器會帶入**Exchange**和**SharePoint** （包括**OneDrive**）中進行中的使用者和系統管理活動的 Azure Sentinel 資訊。 這項資訊包含動作的詳細資料，例如檔案下載、傳送的存取要求、群組事件的變更，以及信箱作業，以及執行動作之使用者的詳細資料。 將 Office 365 記錄連接到 Azure Sentinel 可讓您在活頁簿中查看和分析此資料、查詢它以建立自訂警示，並將其納入以改善您的調查流程，讓您更深入瞭解 Office 365 的安全性。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的 [讀取] 和 [寫入] 許可權。

- 您必須是租用戶的全域管理員或安全性系統管理員。

- 您的 Office 365 部署必須位於與 Azure Sentinel 工作區相同的租使用者上。

> [!IMPORTANT]
> - 為了讓連接器能夠透過 Office 365 管理活動 API 存取資料，您必須在 Office 365 部署上啟用**統一的審核記錄**。 視您擁有的 Office 365/Microsoft 365 授權類型而定，預設可能不會啟用。 請參閱[Office 365 安全性與合規性中心](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)，以根據您的授權類型來檢查整合的審核記錄狀態。
> - 您也可以手動啟用、停用及檢查 Office 365 整合審核記錄的目前狀態。 如需相關指示，請參閱[開啟或關閉 Office 365 稽核記錄搜尋](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。
> - 如需詳細資訊，請參閱[Office 365 管理活動 API 參考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。


   > [!NOTE]
   > 如先前所述，您會在 [**資料類型**] 下的 [連接器] 頁面上看到 Azure Sentinel Office 365 連接器目前僅支援從 Microsoft Exchange 和 SharePoint （包括 OneDrive）內嵌 audit 記錄檔。 不過，如果您想要將來自小組或[其他 Office 資料](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)[的資料](https://techcommunity.microsoft.com/t5/azure-sentinel/protecting-your-teams-with-azure-sentinel/ba-p/1265761)帶入 Azure Sentinel，則有一些外部解決方案。 

## <a name="enable-the-office-365-log-connector"></a>啟用 Office 365 記錄連接器

1. 從 Azure Sentinel 導覽功能表中，選取 [**資料連線器**]。

1. 從 [**資料連線器**] 清單中，依序按一下 [ **Office 365**] 和右下方的 [**開啟連接器頁面**] 按鈕。

1. 在標示為 [設定] 的區段下，將您要連線的 Office 365 活動記錄的複選**框標示 Azure Sentinel**，然後按一下 [套用**變更**]。 

   > [!NOTE]
   > 如果您先前已將多個租使用者連線到 Azure Sentinel，使用支援此版本的舊版 Office 365 連接器，您將能夠查看和修改您從每個租使用者收集的記錄。 您無法新增其他租用戶，但可以移除先前新增的租用戶。

1. 若要在 Log Analytics 中查詢 Office 365 記錄資料，請 `OfficeActivity` 在查詢視窗的第一行中輸入。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 Office 365 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何[查看您的資料和潛在威脅](quickstart-get-visibility.md)。
- 使用[內建](tutorial-detect-threats-built-in.md)或[自訂](tutorial-detect-threats-custom.md)規則，開始偵測 Azure Sentinel 的威脅。

