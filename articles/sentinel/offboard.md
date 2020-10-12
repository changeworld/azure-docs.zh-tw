---
title: 移除 Azure Sentinel |Microsoft Docs
description: 如何刪除您的 Azure Sentinel 實例。
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885845"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>從您的工作區移除 Azure Sentinel

如果您不想再使用 Azure Sentinel，本文將說明如何從您的工作區中移除它。

## <a name="how-to-remove-azure-sentinel"></a>如何移除 Azure Sentinel

遵循此程式以從您的工作區中移除 Azure Sentinel：

1. 移至 **Azure Sentinel**，然後選取 [ **設定**]，然後選取 [移除] 索引標籤 **Azure Sentinel**。

1. 移除 Azure Sentinel 之前，請使用核取方塊讓我們知道您要移除的原因。

1. 選取 [ **移除工作區中的 Azure Sentinel**]。
    
    ![刪除 SecurityInsights 方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>幕後會發生什麼事？

當您移除解決方案時，Azure Sentinel 最多需要48小時的時間，才能完成刪除程式的第一個階段。

在識別出中斷連接之後，就會開始進行中斷處理。

**這些連接器的設定已移除：**
-   Office 365

-   AWS

-   Microsoft 服務安全性警示：適用于身分識別 (的 Microsoft Defender *先前 Azure ATP*) ，Microsoft Cloud App Security 包括 CLOUD DISCOVERY 影子 IT 報告、Azure AD Identity Protection、microsoft Defender for Endpoint (*先前為 microsoft defender ATP*) 、Azure Defender 警示（來自 Azure 資訊安全中心）

-   威脅情報

-   常見的安全性記錄 (包括以 CEF 為基礎的記錄、Barracuda 和 Syslog)  (如果您從 Azure 資訊安全中心取得 Azure Defender 警示，將會繼續收集這些記錄。 ) 

-   Windows 安全性事件 (如果您從 Azure 資訊安全中心取得 Azure Defender 警示，將會繼續收集這些記錄。 ) 

在前48小時內，Azure Sentinel 中將無法再存取或查詢資料和分析規則 (包括即時自動化設定) 。

**30天后，就會移除這些資源：**

-   事件 (包括調查中繼資料) 

-   分析規則

-   書籤

您的工作手冊、儲存的活頁簿、儲存的搜尋查詢和筆記本都不會移除。 **有些可能會因移除的資料而中斷。您可以手動移除它們。**

移除服務之後，有30天的寬限期可以重新啟用解決方案，而且您的資料和分析規則將會還原，但已中斷連線的已設定連接器必須重新連接。

> [!NOTE]
> 如果您移除解決方案，您的訂用帳戶將繼續向 Azure Sentinel 資源提供者註冊。 **您可以手動將其移除。**




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何移除 Azure Sentinel 服務。 如果您改變主意，並且想要再次安裝它：
- 開始 [Azure Sentinel](quickstart-onboard.md)。
