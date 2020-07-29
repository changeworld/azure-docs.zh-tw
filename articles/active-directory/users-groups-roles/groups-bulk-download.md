---
title: 在 Azure Active Directory 入口網站中下載群組清單 |Microsoft Docs
description: 在 Azure Active Directory 的 Azure 系統管理中心下載大量的群組屬性。
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
ms.openlocfilehash: 00fdd94e8a8cd4b8769260cd595dfee5ff898039
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84732613"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>大量下載 Azure Active Directory 中的群組清單

使用 Azure Active Directory （Azure AD）入口網站，您可以將組織中所有群組的清單大量下載到逗號分隔值（CSV）檔案。

## <a name="to-download-a-list-of-groups"></a>下載群組清單

1. 使用組織中的系統管理員帳戶登入[Azure 入口網站](https://portal.azure.com)。
1. 在 Azure AD 中，選取 [**群組**] [  >  **下載群組**]。
1. 在 [**群組下載**] 頁面上，選取 [**啟動**] 以接收列出群組的 CSV 檔案。

   ![[下載群組] 命令位於 [所有群組] 頁面](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>檢查下載狀態

您可以在 [大量作業結果] 頁面中，查看所有待決之大量要求的狀態。

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>大量下載服務限制

每個要下載群組清單的大量活動最多可以執行一小時。 這可讓您下載至少300000個群組的清單。

## <a name="next-steps"></a>後續步驟

- [大量移除群組成員](groups-bulk-remove-members.md)
- [下載群組的成員](groups-bulk-download-members.md)
