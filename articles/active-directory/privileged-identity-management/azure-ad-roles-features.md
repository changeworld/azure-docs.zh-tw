---
title: 在 Privileged Identity Management （PIM）中管理 Azure AD 角色 |Microsoft Docs
description: 如何管理指派 Privileged Identity Management （PIM） Azure AD 角色
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375421"
---
# <a name="management-capabilities-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management 中 Azure AD 角色的管理功能

Privileged Identity Management 中 Azure AD 角色的管理體驗已更新，以整合 Azure AD 角色和 Azure 資源角色的管理方式。 先前，Azure 資源角色的 Privileged Identity Management 具有 Azure AD 角色無法使用的幾個重要功能。

在目前推出更新的情況下，我們會將這兩者合併成單一管理體驗，而在其中，您會取得與 Azure 資源角色相同的 Azure AD 角色功能。 本文會通知您已更新的功能和任何需求。


## <a name="time-bound-assignments"></a>時間界限指派

先前在 Azure AD 角色的 Privileged Identity Management 中，您已熟悉具有兩種可能狀態的角色指派–*合格*和*永久*。 現在您可以設定每個指派類型的開始和結束時間。 這項新增功能提供您四個可能的狀態，您可以在其中放置指派：

- 已永久符合資格
- 永久有效
- 合格，具有指定的開始/結束日期以進行指派
- 作用中，具有指定的開始/結束日期以進行指派

在許多情況下，即使您不想讓使用者每次都有合格的指派和啟動角色，仍然可以藉由設定指派的到期時間來保護您的 Azure AD 組織。 例如，如果您有一些有資格的暫時使用者，請考慮設定到期日，以便在工作完成時，自動從角色指派中移除它們。

## <a name="new-role-settings"></a>新的角色設定

我們也會為 Azure AD 角色新增設定。 之前，您只能以每個角色為基礎來設定啟用設定。 也就是說，啟用設定（例如多重要素驗證需求和事件/要求票證需求）會套用至所有符合指定角色的使用者。 現在，您可以設定個別使用者是否需要執行多重要素驗證，然後才能啟動角色。 此外，您還可以對與特定角色相關的 Privileged Identity Management 電子郵件進行先進的控制。

## <a name="extend-and-renew-assignments"></a>延長和更新指派

一旦您找出時間限制指派，您可能會問的第一個問題是，如果角色已過期，會發生什麼事？ 在這個新版本中，我們提供兩個適用于此案例的選項：

- 擴充–角色指派接近其到期時間時，使用者可以使用 Privileged Identity Management 來要求該角色指派的延伸模組
- 續訂–角色指派過期時，使用者可以使用 Privileged Identity Management 來要求更新該角色指派

使用者起始的動作都需要全域管理員或特殊權限角色管理員的核准。 系統管理員不再需要管理這些過期的業務。 他們只需要等待擴充或更新要求，並在要求有效時予以核准。

## <a name="api-changes"></a>API 變更

當客戶將更新版本推出至其 Azure AD 組織時，現有的圖形 API 將會停止運作。 您必須轉換為使用[Azure 資源角色的圖形 API](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-resources?view=graph-rest-beta)。 若要使用該 API 來管理 Azure AD 角色，請在簽章中使用 `/aadroles` 交換 `/azureResources`，並使用 `resourceId`的目錄識別碼。

我們已嘗試與使用先前 API 的所有客戶，讓他們事先瞭解這項變更。 如果您的 Azure AD 組織已移至新版本，而您仍然依賴舊的 API，請在 pim_preview@microsoft.com與小組聯繫。

## <a name="powershell-change"></a>PowerShell 變更

針對使用適用于 Azure AD 角色之 Privileged Identity Management PowerShell 模組的客戶，PowerShell 將會停止使用更新。 若要取代先前的 Cmdlet，您必須使用 Azure AD Preview PowerShell 模組內的 Privileged Identity Management Cmdlet。 從[PowerShell 資源庫](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)安裝 Azure AD PowerShell 模組。 您現在可以[在此 PowerShell 模組中閱讀 PIM 作業的檔和範例](powershell-for-azure-ad-roles.md)。

## <a name="next-steps"></a>後續步驟

- [指派 Azure AD 自訂角色](azure-ad-custom-roles-assign.md)
- [移除或更新 Azure AD 自訂角色指派](azure-ad-custom-roles-update-remove.md)
- [設定 Azure AD 自訂角色指派](azure-ad-custom-roles-configure.md)
- [Azure AD 中的角色定義](../users-groups-roles/directory-assign-admin-roles.md)
