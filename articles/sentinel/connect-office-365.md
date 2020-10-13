---
title: 將 Office 365 記錄連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Office 365 記錄連接器，以帶入 Exchange、小組和 SharePoint （包括 OneDrive）中進行中的使用者和系統管理活動的相關資訊。
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
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: d6b59de048cdf00d352c4f488ecb51bfdf83640f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89178921"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>將 Office 365 記錄連線至 Azure Sentinel

[Office 365](https://docs.microsoft.com/office/)記錄連接器將 Azure Sentinel 資訊帶入**Exchange**和**SharePoint**中進行中的使用者和系統管理活動 (包括**OneDrive**) ，現在也是在**小組**中。 這項資訊包含動作的詳細資料，例如檔案下載、傳送的存取要求、群組事件的變更、信箱作業、小組事件 (例如聊天、小組、成員和頻道事件) ，以及執行動作之使用者的詳細資料。 將 Office 365 記錄檔連接到 Azure Sentinel 可讓您在活頁簿中查看和分析此資料、查詢它以建立自訂警示，並將其納入以改善您的調查流程，讓您更深入瞭解 Office 365 安全性。

> [!IMPORTANT]
> Office 365 記錄連接器的 **Microsoft 團隊記錄延伸** 模組目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

- 您必須是租用戶的全域管理員或安全性系統管理員。

- 您的 Office 365 部署必須與您的 Azure Sentinel 工作區位於相同的租使用者上。

> [!IMPORTANT]
> - 為了讓連接器能夠透過 Office 365 管理活動 API 存取資料，您必須在 Office 365 部署上啟用 **整合的審核記錄** 。 視您擁有的 Office 365/Microsoft 365 授權類型而定，預設可能不會啟用。 請參閱「 [Office 365 安全性與合規性中心](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center) 」，以根據您的授權類型來檢查統一審核記錄的狀態。
> - 您也可以手動啟用、停用和檢查 Office 365 統一審核記錄的目前狀態。 如需相關指示，請參閱[開啟或關閉 Office 365 稽核記錄搜尋](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off)。
> - 如需詳細資訊，請參閱 [Office 365 管理活動 API 參考](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)。


   > [!NOTE]
   > 如先前所述，您會在 [連接器] 頁面的 [ **資料類型**] 下看到，Azure Sentinel Office 365 連接器目前僅支援從 Microsoft Exchange 和 SharePoint (內嵌審核記錄，包括 OneDrive) ，以及 **現在也從小組**。 但是，如果您有興趣將 [其他 Office 資料](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888) 帶入 Azure Sentinel，則有一些外部解決方案。 

## <a name="enable-the-office-365-log-connector"></a>啟用 Office 365 記錄連接器

### <a name="instructions-tab"></a>指示索引標籤

1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **Office 365**]，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面** 。

1. 在 **標示為**[設定] 區段下，將您想要連線的 Office 365 活動記錄的核取方塊標示 Azure Sentinel，然後按一下 [套用 **變更**]。 

   > [!NOTE]
   > 如果您先前已將多個租使用者連接到 Azure Sentinel，使用支援此版本的舊版 Office 365 連接器，您將能夠查看和修改您從每個租使用者收集的記錄。 您無法新增其他租用戶，但可以移除先前新增的租用戶。

### <a name="next-steps-tab"></a>後續步驟索引標籤

- 請參閱與 **Office 365** 記錄連接器配套的建議活頁簿、查詢範例和分析規則範本，以深入瞭解您的 SharePoint、OneDrive、Exchange 和團隊記錄資料。

- 若要在 **記錄**檔中手動查詢 Office 365 記錄檔資料，請 `OfficeActivity` 在查詢視窗的第一行輸入。
   - 若要篩選特定記錄類型的查詢，請 `| where OfficeWorkload == "<logtype>"` 在查詢的第二行中輸入，其中 *\<logtype\>* 是 `SharePoint` 、 `OneDrive` 、 `Exchange` 或 `MicrosoftTeams` 。

## <a name="next-steps"></a>後續步驟
在本文件中，您已了解如何將 Office 365 連線至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何深入瞭解 [您的資料和潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats-built-in.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。

