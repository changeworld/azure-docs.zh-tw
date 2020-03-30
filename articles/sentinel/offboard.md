---
title: 刪除 Azure 哨兵*微軟文檔
description: 如何刪除 Azure Sentinel 實例。
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
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581679"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>從工作區中刪除 Azure 哨兵

如果不再想要使用 Azure Sentinel，本文將介紹如何將其從工作區中刪除。

## <a name="how-to-remove-azure-sentinel"></a>如何刪除 Azure 哨兵

按照此過程從工作區中刪除 Azure 哨兵：

1. 轉到**Azure 哨兵**，後跟 **"設置"，** 然後選擇"**刪除 Azure 哨兵"選項卡**。

1. 在刪除 Azure 哨兵之前，請使用核取方塊告知我們刪除它的原因。

1. 選擇**從工作區中刪除 Azure 哨兵**。
    
    ![刪除安全見解解決方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>幕後會發生什麼？

刪除解決方案時，Azure Sentinel 最多需要 48 小時才能完成刪除過程的第一階段。

確定斷開連接後，開始卸載過程。

**這些連接器的配置將被刪除：**
-   Office 365

-   AWS

-   微軟服務安全警報（Azure ATP、微軟雲應用安全（包括雲發現影子 IT 報告、Azure AD 標識保護、微軟防禦者 ATP、Azure 安全中心）

-   威脅情報

-   常見安全性記錄檔（包括基於 CEF 的日誌、梭子魚和 Syslog）（如果您有 Azure 安全中心，將繼續收集這些日誌。

-   Windows 安全事件（如果您有 Azure 安全中心，將繼續收集這些日誌。

在最初的 48 小時內，資料和分析規則（包括即時自動化配置）將不再在 Azure Sentinel 中訪問或查詢。

**30 天后，這些資源將被刪除：**

-   事件（包括調查中繼資料）

-   分析規則

-   書籤

不會刪除您的行動手冊、保存的活頁簿、保存的狩獵查詢和筆記本。 **有些可能會由於刪除的資料而中斷。您可以手動刪除這些。**

刪除服務後，有 30 天的寬限期，在此期間您可以重新啟用解決方案，您的資料和分析規則將被還原，但必須重新連接斷開連接的已配置連接器。

> [!NOTE]
> 如果刪除解決方案，您的訂閱將繼續註冊到 Azure Sentinel 資來源提供者。 **您可以手動將其刪除。**




## <a name="next-steps"></a>後續步驟
在本文檔中，您學習了如何刪除 Azure Sentinel 服務。 如果您改變主意，想再次安裝它：
- 開始[載入 Azure 哨兵](quickstart-onboard.md)。
