---
title: 在特權標識管理 （PIM） 中管理 Azure AD 角色 |微軟文檔
description: 如何管理 Azure AD 角色以分配特權標識管理 （PIM）
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5d63577cbdbdf18cb17618bdb5c9e3b5de0e44a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245976"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>特權標識管理中 Azure AD 角色的管理功能

已更新特權標識管理中的 Azure AD 角色的管理體驗，以統一 Azure AD 角色和 Azure 資源角色的管理方式。 以前，Azure 資源角色的特權標識管理具有一些關鍵功能，這些功能不適用於 Azure AD 角色。

當前推出更新後，我們將兩者合併為單個管理體驗，並在其中獲得 Azure AD 角色與 Azure 資源角色相同的功能。 本文將更新的功能和任何要求通知您。


## <a name="time-bound-assignments"></a>有時限的分配

以前在 Azure AD 角色的特權標識管理中，您熟悉具有兩種可能狀態的角色指派 -*合格*和*永久*。 現在，您可以為每個類型的分配設置開始和結束時間。 此添加提供了四種可能狀態，您可以在其中放置賦值：

- 永久合格
- 永久活動
- 符合條件，具有指定的作業開始/結束日期
- 活動，具有指定的作業開始/結束日期

在許多情況下，即使您不希望使用者每次都具有符合條件的分配和啟動角色，您仍可以通過為分配設置過期時間來保護 Azure AD 組織。 例如，如果您有一些符合資格的臨時使用者，請考慮設置過期，以便在工作完成後自動將其從角色指派中刪除。

## <a name="new-role-settings"></a>新角色設置

我們還為 Azure AD 角色添加新設置。 以前，您只能根據每個角色配置啟動設置。 也就是說，啟動設置（如多重要素驗證要求和事件/請求票證要求）已應用於符合指定角色的所有使用者。 現在，您可以配置單個使用者是否需要執行多重要素驗證，然後才能啟動角色。 此外，您還可以對與特定角色相關的特權身份管理電子郵件進行高級控制。

## <a name="extend-and-renew-assignments"></a>擴展和續訂分配

一旦您找出了有時限的分配，您可能會問的第一個問題是，如果角色已過期，會發生什麼情況？ 在此新版本中，我們為此方案提供了兩個選項：

- 擴展 — 當角色指派接近其到期時，使用者可以使用特權標識管理請求該角色指派的擴展
- 續訂 - 角色指派過期後，使用者可以使用特權標識管理請求該角色指派的續訂

使用者啟動的兩個操作都需要獲得全域管理員或特權角色管理員的批准。 管理員不再需要從事管理這些過期的業務。 他們只需要等待擴展或續訂請求，如果請求有效，則批准它們。

## <a name="api-changes"></a>API 變更

當客戶將更新的版本推出到其 Azure AD 組織時，現有的圖形 API 將停止工作。 必須轉換以使用圖形[API 進行 Azure 資源角色](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)。 要使用該 API 管理 Azure `/azureResources` AD`/aadroles`角色，請在簽名中交換，並使用`resourceId`中的目錄 ID。

我們已經盡力聯繫使用前一個 API 的所有客戶，讓他們提前瞭解此更改。 如果 Azure AD 組織已移動到新版本，但仍依賴于舊 API，請通過 與pim_preview@microsoft.com團隊聯繫。

## <a name="powershell-change"></a>電源殼更改

對於使用特權標識管理 PowerShell 模組執行 Azure AD 角色的客戶，PowerShell 將停止使用更新。 代替前面的 Cmdlet，您必須在 Azure AD 預覽 PowerShell 模組中使用特權標識管理 Cmdlet。 從[PowerShell 庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 現在，您可以在[此 PowerShell 模組中閱讀 PIM 操作的文檔和示例](powershell-for-azure-ad-roles.md)。

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../users-groups-roles/directory-assign-admin-roles.md)
