---
title: 將 Dynamics 365 記錄連線至 Azure Sentinel |Microsoft Docs
description: 瞭解如何使用 Dynamics 365 Common Data Service (CD) 活動連接器，以帶入進行中的系統管理員、使用者和支援活動的相關資訊。
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104172"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>將 Dynamics 365 活動記錄連線到 Azure Sentinel

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) COMMON DATA SERVICE (cd) 活動連接器可讓您深入瞭解系統管理員、使用者和支援活動，以及 Microsoft 社交參與記錄事件。 藉由將 Dynamics 365 CRM 記錄連線到 Azure Sentinel 中，您可以在活頁簿中查看此資料，使用它來建立自訂警示，並利用它來改善您的調查流程。 這個新的 Azure Sentinel 連接器會從 Office 管理 API 收集 Dynamics CD 資料。 若要深入瞭解 Power Platform 中所審核的 Dynamics CD 活動，請造訪 [啟用和使用活動記錄](/power-platform/admin/enable-use-comprehensive-auditing)。

> [!IMPORTANT]
>
> Dynamics 365 Common Data Service (CD) 活動連接器目前為 **預覽** 狀態。 請參閱 [Microsoft Azure 預覽的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，以取得適用于 Azure 功能（Beta、預覽或尚未發行正式運作）的其他法律條款。

## <a name="prerequisites"></a>必要條件

- 您必須擁有 Azure Sentinel 工作區的讀取和寫入權限。

- 您必須擁有 [Microsoft Dynamics 365 生產授權](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)。 沙箱環境無法使用此連接器。
    - 需要 Microsoft 365 企業版 [E3 或 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 訂用帳戶，才能進行活動記錄。

- 從 Office 管理 API 提取資料：
    - 您必須是租使用者的全域管理員。

    - Office [audit 記錄](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)必須在[Office 安全性與合規性中心](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)啟用。

## <a name="enable-the-dynamics-365-activities-data-connector"></a>啟用 Dynamics 365 活動資料連接器

1. 從 Azure Sentinel 導覽功能表中，選取 [ **資料連線器**]。

1. 從 **資料連線器** 資源庫中，選取 [ **Dynamics 365 (Preview])**，然後選取 [預覽] 窗格上的 [ **開啟連接器] 頁面** 。

1. 在 [ **說明** ] 索引標籤的 [設定] **底下，按一下** **[連線]**。 

    一旦啟動連接器，將需要大約30分鐘的時間，直到您能夠看到到達圖表的資料為止。 

    根據 [合規性中心的 Office audit 記錄](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)檔，在事件發生之後，可能需要最多30分鐘或24小時的時間，才會在結果中傳回對應的 audit 記錄檔記錄。

1. 您可以在資料表中找到 Microsoft Dynamics audit 記錄檔 `Dynamics365Activity` 。 請參閱資料表的 [架構參考](/azure/azure-monitor/reference/tables/dynamics365activity)。

## <a name="querying-the-data"></a>查詢資料

1. 從 Azure Sentinel 導覽功能表中，選取 [ **記錄**]。

1. 執行下列查詢以確認記錄檔已到達：

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何將 Dynamics 365 活動資料連接至 Azure Sentinel。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 瞭解如何深入瞭解 [您的資料和潛在威脅](quickstart-get-visibility.md)。
- 使用 [內建](tutorial-detect-threats-built-in.md) 或 [自訂](tutorial-detect-threats-custom.md) 規則開始偵測 Azure Sentinel 的威脅。
