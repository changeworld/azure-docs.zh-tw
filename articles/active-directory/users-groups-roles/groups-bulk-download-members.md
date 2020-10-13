---
title: 大量下載群組成員資格清單-Azure Active Directory 入口網站 |Microsoft Docs
description: 在 Azure 系統管理中心內大量新增使用者。
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9abfe79989dbefe79a001e4638c7318bd99d54a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421700"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>大量下載 Azure Active Directory 中群組的成員

使用 Azure Active Directory (Azure AD) 入口網站時，您可以將組織中群組的成員大量下載至 (CSV) 檔的逗號分隔值。

## <a name="to-bulk-download-group-membership"></a>大量下載群組成員資格

1. 使用組織中的使用者系統管理員帳戶來登入 [Azure 入口網站](https://portal.azure.com)。 群組擁有者也可以大量下載他們所擁有之群組的成員。
1. 在 Azure AD 中，選取 [群組] > [所有群組]。
1. 開啟您想要下載其成員資格的群組，然後選取 [ **成員**]。
1. 在 [ **成員** ] 頁面上，選取 [ **下載成員** ] 以下載列出群組成員的 CSV 檔案。

   ![[下載成員] 命令位於群組的 [設定檔] 頁面上](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>檢查下載狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

[![檢查大量作業結果頁面中的狀態。](media/groups-bulk-download-members/bulk-center.png)](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>大量下載服務限制

每個要下載群組成員清單的大量活動最多可執行一小時。 這可讓您下載至少500000個成員的清單。

## <a name="next-steps"></a>後續步驟

- [大量匯入群組成員](groups-bulk-import-members.md)
- [大量移除群組成員](groups-bulk-download-members.md)
