---
title: 瞭解 Azure AD 連接 1.4.xx.x 和設備消失 |微軟文檔
description: 本文檔介紹 Azure AD 連接版本 1.4.xx.x 中出現的問題
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176035"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>瞭解 Azure AD 連接 1.4.xx.x 和設備消失
使用 Azure AD Connect 的版本 1.4.xx.x，某些客戶可能會看到其部分或全部 Windows 設備從 Azure AD 中消失。 這不引起關注，因為在條件訪問授權期間，Azure AD 不會使用這些設備標識。 此更改不會刪除在 Azure AD 中正確註冊用於混合 Azure AD 聯接的任何 Windows 設備。

如果看到 Azure AD 中的設備物件的刪除超過匯出刪除閾值，則建議客戶允許刪除。 [操作操作：允許刪除在超過刪除閾值時流動](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>背景
註冊為混合 Azure AD 加入的 Windows 設備在 Azure AD 中表示為設備物件。 這些設備物件可用於條件訪問。 Windows 10 設備通過 Azure AD 連接同步到雲，下級 Windows 設備直接使用 AD FS 或無縫單一登入註冊。

## <a name="windows-10-devices"></a>Windows 10 裝置
只有具有由混合 Azure AD 聯接配置的特定使用者證書屬性值的 Windows 10 設備才應通過 Azure AD 連接同步到雲。 在早期版本的 Azure AD Connect 中，此要求未嚴格執行，從而導致 Azure AD 中不必要的設備物件。 Azure AD 中的此類設備始終處於"掛起"狀態，因為這些設備不打算在 Azure AD 中註冊。 

此版本的 Azure AD 連接將僅同步正確配置為混合 Azure AD 加入的 Windows 10 設備。 沒有 Azure AD 加入特定使用者證書的 Windows 10 設備物件將從 Azure AD 中刪除。

## <a name="down-level-windows-devices"></a>低級 Windows 設備
Azure AD 連接不應同步[下級 Windows 設備](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 以前未正確同步的 Azure AD 中的任何設備現在都將從 Azure AD 中刪除。 如果 Azure AD Connect 嘗試刪除[低級 Windows 設備](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，則該設備不是 Microsoft[工作區為非 Windows 10 電腦 MSI](https://www.microsoft.com/download/details.aspx?id=53554)創建的設備，並且它無法被任何其他 Azure AD 功能使用。

某些客戶可能需要重新訪問["如何操作：計畫混合 Azure 活動目錄加入實現](../devices/hybrid-azuread-join-plan.md)"，以便正確註冊其 Windows 設備，並確保此類設備可以完全參與基於設備的條件訪問。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何驗證使用此更新刪除哪些設備？

要驗證已刪除哪些設備，可以使用此 PowerShell 腳本：https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此腳本生成有關存儲在 Active Directory 電腦物件中的證書的報告，特別是混合 Azure AD 聯接功能頒發的證書。
它檢查 AD 中電腦物件的 User 證書屬性中的證書，並且對於存在的每個未過期證書，驗證憑證是否為混合 Azure AD 聯接功能頒發（即主題名稱與 CN[ObjectGUID]匹配）。
以前，Azure AD Connect 將同步到 Azure AD 任何至少包含一個有效證書但從 Azure AD Connect 版本 1.4 開始的電腦，同步引擎可以標識混合 Azure AD 聯接證書，並將"雲篩選"電腦物件從同步到 Azure AD，除非有有效的混合 Azure AD 聯接證書。
已同步到 AD 但沒有有效混合 Azure AD 聯接證書的 Azure AD 設備將由同步引擎刪除（雲篩選=TRUE）。

## <a name="next-steps"></a>後續步驟
- [Azure AD 連接版本歷程記錄](reference-connect-version-history.md)
