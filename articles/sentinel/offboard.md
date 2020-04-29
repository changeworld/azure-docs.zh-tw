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
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77581679"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>從您的工作區移除 Azure Sentinel

如果您不想再使用 Azure Sentinel，本文會說明如何從您的工作區中移除它。

## <a name="how-to-remove-azure-sentinel"></a>如何移除 Azure Sentinel

遵循此程式，從您的工作區中移除 Azure Sentinel：

1. 移至**Azure Sentinel**，然後依序選取 [**設定**] 和 [**移除 Azure Sentinel**] 索引標籤。

1. 移除 Azure Sentinel 之前，請使用核取方塊讓我們知道您要移除的原因。

1. **從您的工作區選取 [移除 Azure Sentinel**]。
    
    ![刪除 SecurityInsights 解決方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>幕後會發生什麼事？

當您移除解決方案時，Azure Sentinel 最多需要48小時的時間，才能完成刪除程式的第一個階段。

在識別中斷連線之後，就會開始進行脫離進程。

**這些連接器的設定已移除：**
-   Office 365

-   AWS

-   Microsoft 服務安全性警示（Azure ATP，Microsoft Cloud App Security 包括 Cloud Discovery 影子 IT 報告、Azure AD Identity Protection、Microsoft Defender ATP、Azure 資訊安全中心）

-   威脅情報

-   常見的安全性記錄（包括 CEF 記錄、Barracuda 和 Syslog）（如果您有 Azure 資訊安全中心，將會繼續收集這些記錄）。

-   Windows 安全性事件（如果您有 Azure 資訊安全中心，將會繼續收集這些記錄）。

在前48小時內，資料和分析規則（包括即時自動化設定）在 Azure Sentinel 中將無法再存取或查詢。

**30天后，就會移除這些資源：**

-   事件（包括調查中繼資料）

-   分析規則

-   書籤

您的腳本、儲存的活頁簿、儲存的搜尋查詢和筆記本都不會移除。 **有些可能會因為移除的資料而中斷。您可以手動移除這些。**

移除服務之後，有30天的寬限期，您可以重新啟用解決方案，而您的資料和分析規則將會還原，但已中斷連線的已設定連接器必須重新連接。

> [!NOTE]
> 如果您移除此解決方案，您的訂用帳戶將會繼續向 Azure Sentinel 資源提供者註冊。 **您可以手動將它移除。**




## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何移除 Azure Sentinel 服務。 如果您改變主意，而且想要再次安裝它：
- 快速入門[Azure Sentinel](quickstart-onboard.md)。
