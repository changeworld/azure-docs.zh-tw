---
title: 批量下載組成員身份清單 - Azure 活動目錄門戶 |微軟文檔
description: 在 Azure 管理中心大量新增使用者。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517169"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>批量下載 Azure 活動目錄中的組（預覽）成員

使用 Azure 活動目錄 （Azure AD） 門戶，可以將組織中組的成員批量下載到逗號分隔的值 （CSV） 檔。

## <a name="to-bulk-download-group-membership"></a>批量下載組成員身份

1. 使用組織中的使用者管理員帳戶登錄到[Azure 門戶](https://portal.azure.com)。 組擁有者還可以批量下載其擁有的組的成員。
1. 在 Azure AD 中，選擇 **"對所有** > **組進行**分組"。
1. 打開要下載其成員資格的組，然後選擇 **"成員**"。
1. 在 **"成員"** 頁上，選擇 **"下載成員"** 以下載列出組成員的 CSV 檔。

   !["下載成員"命令位於組的設定檔頁面上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>檢查下載狀態

您可以在**批量操作結果（預覽）** 頁中查看所有掛起的批量請求的狀態。

   ![批量操作結果頁顯示批量請求狀態](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>批量下載服務限制

下載組成員清單的每個批量活動最多可以運行一小時。 這使您能夠下載至少 500，000 個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入組成員](groups-bulk-import-members.md)
- [大量刪除組成員](groups-bulk-download-members.md)
