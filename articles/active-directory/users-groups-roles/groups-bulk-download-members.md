---
title: 大量下載群組成員身份清單 - Azure 活動目錄門戶 |微軟文件
description: 在 Azure 管理中心批量添加使用者。
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
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533665"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>大量下載 Azure 活動目錄中的群組成員

使用 Azure 活動目錄 (Azure AD) 門戶,可以將組織中組的成員批量下載到逗號分隔的值 (CSV) 檔。

## <a name="to-bulk-download-group-membership"></a>批次下載群組成員身份

1. 使用組織中的使用者管理員帳戶登入[Azure 門戶](https://portal.azure.com)。 組擁有者還可以批量下載其擁有的組的成員。
1. 在 Azure AD 中,選擇 **「對所有** > **組進行**群組」。
1. 打開要下載其成員資格的組,然後選擇 **「成員**」。。
1. 在 **「成員」** 頁上,選擇 **「下載成員」** 以下載列出組成員的 CSV 檔。

   ![「下載成員」指令位於群組的設定檔頁上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>檢查下載狀態

您可以在 **「批量操作結果」** 頁中查看所有掛起的批量請求的狀態。

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>批次下載服務限制

下載組成員清單的每個批量活動最多可以運行一小時。 這使您能夠下載至少 500,000 個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入群組成員](groups-bulk-import-members.md)
- [大量群組成員](groups-bulk-download-members.md)
