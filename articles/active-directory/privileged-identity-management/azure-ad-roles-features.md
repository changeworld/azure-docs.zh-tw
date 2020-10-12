---
title: Azure AD Privileged Identity Management 中的角色功能 |Microsoft Docs
description: '如何管理指派 Privileged Identity Management (PIM 的 Azure AD 角色) '
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 07/10/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c039842a04923bc02aa288576570d51c39156c40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88784006"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure AD 角色的管理功能

Privileged Identity Management 中 Azure AD 角色的管理體驗已更新，以統一管理 Azure AD 角色和 Azure 資源角色的方式。 先前，Azure 資源角色的 Privileged Identity Management 有幾個主要功能無法供 Azure AD 角色使用。

在目前已推出更新的情況下，我們會將這兩者合併成單一管理體驗，而且您可以針對 Azure 資源角色 Azure AD 角色取得相同的功能。 本文會通知您更新的功能和任何需求。

## <a name="time-bound-assignments"></a>時間界限指派

在過去，角色指派有兩種可能的狀態： *符合資格* 和 *永久*。 現在您也可以設定每個指派類型的開始和結束時間。 這項新增提供四種可能的狀態，您可以在其中放置指派：

- 有資格永久
- 永久有效
- 符合資格，具有指定的開始和結束日期可供指派
- 作用中，具有指定的開始和結束日期來進行指派

在許多情況下，即使您不希望使用者每次都有合格的指派和啟用角色，您仍然可以藉由設定指派的到期時間來保護您的 Azure AD 組織。 例如，如果您有一些有資格的暫時使用者，請考慮設定到期時間，以便在工作完成時，自動從角色指派中移除這些使用者。

## <a name="new-role-settings"></a>新增角色設定

我們也會新增 Azure AD 角色的設定。

- **先前**，您只能針對每個角色來設定啟用設定。 也就是說，啟用設定（例如多重要素驗證需求和事件/要求票證需求）已套用至所有符合指定角色資格的使用者。
- **現在**，您可以設定個別使用者是否需要執行多重要素驗證，才能啟用角色。 此外，您可以針對與特定角色相關的 Privileged Identity Management 電子郵件，進行 advanced control。

## <a name="extend-and-renew-assignments"></a>延長和更新指派

一旦您知道有時間限制的指派，您可能會問的第一個問題是角色過期時會發生什麼事？ 在這個新版本中，我們提供兩個適用於此案例的選項：

- **擴充**：當角色指派接近其到期日時，使用者可以使用 Privileged Identity Management 來要求該角色指派的延伸模組
- **續約**：角色指派過期時，使用者可以使用 Privileged Identity Management 要求更新該角色指派

兩者的使用者起始動作都需要全域管理員或具特殊權限角色管理員的核准。 管理員不再需要管理這些與到期相關的作業， 他們只需要等候延伸或更新要求，並在要求有效時核准。

## <a name="api-changes"></a>API 變更

當客戶將更新版本推出至其 Azure AD 組織時，現有的圖形 API 將會停止運作。 您必須轉換為使用 [適用于 Azure 資源角色的圖形 API](/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)。 若要使用該 API 來管理 Azure AD 角色，請在簽章 `/azureResources` 中使用交換， `/aadroles` 並使用的目錄識別碼 `resourceId` 。

我們試著與所有使用先前 API 的客戶聯繫，讓他們事先知道這項變更。 如果您的 Azure AD 組織已移至新版本，而您仍相依于舊的 API，請與團隊聯繫 pim_preview@microsoft.com 。

## <a name="powershell-change"></a>PowerShell 變更

針對使用 Azure AD 角色 Privileged Identity Management PowerShell 模組的客戶，PowerShell 將會停止使用更新。 您必須使用 Azure AD Preview PowerShell 模組內的 Privileged Identity Management Cmdlet 來取代先前的 Cmdlet。 從 [PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 您現在可以 [在此 PowerShell 模組中閱讀 PIM 作業的檔和範例](powershell-for-azure-ad-roles.md)。

## <a name="next-steps"></a>接下來的步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../users-groups-roles/directory-assign-admin-roles.md)