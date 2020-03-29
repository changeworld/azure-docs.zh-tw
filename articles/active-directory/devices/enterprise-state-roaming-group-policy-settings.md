---
title: ESR 的群組原則和 MDM 設置 - Azure 活動目錄
description: 企業狀態漫遊的管理設置
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672373"
---
# <a name="group-policy-and-mdm-settings"></a>群組原則和 MDM 設定

只在公司所擁有的裝置上使用這些群組原則和行動裝置管理 (MDM) 設定，因為這些原則會套用到使用者的整個裝置。 套用 MDM 原則來停用個人、使用者所擁有的裝置的設定同步處理，會對使用該裝置造成負面影響。 此外，在裝置上的其他使用者帳戶也會被原則影響。

要管理個人 (非受控) 裝置的漫遊的企業可以使用 Azure 入口網站啟用或停用漫遊，而不是使用群組原則或 MDM。
下表描述可用的原則設定。

> [!NOTE]
> 本文適用于 2015 年 7 月隨 Windows 10 推出的基於 Windows 10 的基於 Microsoft 邊緣舊版 HTML 的瀏覽器。 本文不適用於 2020 年 1 月 15 日發佈的基於 Microsoft 邊緣鉻的新瀏覽器。 有關新 Microsoft 邊緣的同步行為的詳細資訊，請參閱[文章"Microsoft 邊緣同步](/deployedge/microsoft-edge-enterprise-sync)"。

## <a name="mdm-settings"></a>MDM 設定

MDM 原則設定會套用至 Windows 10 及 Windows 10 行動裝置版。  Windows 10 行動裝置版支援僅適用於以 Microsoft 帳戶為基礎且透過使用者的 OneDrive 帳戶進行的漫遊。 有關支援哪些設備進行基於 Azure AD 的同步的詳細資訊，請參閱["設備和終結點](enterprise-state-roaming-windows-settings-reference.md)"。

| 名稱 | 描述 |
| --- | --- |
| 允許 Microsoft 帳戶連接 |允許使用者在裝置上使用 Microsoft 帳戶進行驗證 |
| 允許同步處理我的設定 |讓使用者能夠漫遊 Windows 設定和應用程式資料。停用此原則，將停用行動裝置上的同步處理及備份 |

## <a name="group-policy-settings"></a>群組原則設定

群組原則設定會套用至加入 Active Directory 網域的 Windows 10 裝置上。 資料表也包含看似可管理同步處理設定的舊版設定，但不適用於 Windows 10 的企業狀態漫遊 (已在其說明中註明「不要使用」)。

這些設定位於：`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| 名稱 | 描述 |
| --- | --- |
| 帳戶：封鎖 Microsoft 帳戶 |此原則設定會防止使用者在這部電腦上新增新的 Microsoft 帳戶 |
| 不要同步處理 |防止使用者漫遊 Windows 設定和應用程式資料 |
| 不要同步處理個人化 |停用主題群組的同步處理 |
| 不要同步處理瀏覽器設定 |停用 Internet Explorer 群組的同步處理 |
| 不要同步處理密碼 |停用密碼群組的同步處理 |
| 不要同步處理其他 Windows 設定 |停用其他 Windows 設定群組的同步處理 |
| 不要同步處理桌面個人化 |不要使用；沒有作用 |
| 不要同步處理已計量連接 |停用已計量連接的漫遊，例如行動電話 3G |
| 不要同步處理應用程式 |不要使用；沒有作用 |
| 不要同步處理應用程式設定 |停用應用程式資料的漫遊 |
| 不要同步處理啟動設定 |不要使用；沒有作用 |

## <a name="next-steps"></a>後續步驟

有關概述，請參閱[企業狀態漫遊概述](enterprise-state-roaming-overview.md)。
