---
title: 在 Azure 活動目錄門戶下載群組清單 :微軟文件
description: 在 Azure 活動目錄中的 Azure 管理中心批量下載組屬性。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533528"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>大量下載 Azure 活動目錄中的群組清單

使用 Azure 活動目錄 (Azure AD) 門戶,可以將組織中所有組的清單批量下載到逗號分隔的值 (CSV) 檔中。

## <a name="to-download-a-list-of-groups"></a>下載群組清單

1. 使用組織中的管理員帳戶登入[Azure 門戶](https://portal.azure.com)。
1. 在 Azure AD 中,選擇 **「組** > **下載群組**」。。
1. 在 **「組下載**」頁上,選擇 **「開始」** 以接收列出組內容的 CSV 檔。

   ![下載群組指令位於「所有組」頁上](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>檢查下載狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>批次下載服務限制

下載組清單的每個批量活動最多可以運行一個小時。 這使您能夠下載至少 300,000 個組的清單。

## <a name="next-steps"></a>後續步驟

- [大量群組成員](groups-bulk-remove-members.md)
- [下載群組成員](groups-bulk-download-members.md)
