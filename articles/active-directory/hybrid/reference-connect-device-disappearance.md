---
title: 瞭解 Azure AD Connect 1.4. x 和裝置消失 |Microsoft Docs
description: 本檔說明 1.4. x 版的 Azure AD Connect 所發生的問題
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "73176035"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>瞭解 Azure AD Connect 1.4. x 和裝置消失
在 1.4. x 版的 Azure AD Connect 中，有些客戶可能會看到部分或所有的 Windows 裝置從 Azure AD 消失。 這不是問題的原因，因為在條件式存取授權期間，Azure AD 不會使用這些裝置身分識別。 這種變更不會刪除所有已正確向 Azure AD 註冊混合式 Azure AD Join 的 Windows 裝置。

如果您在中看到刪除裝置物件的 Azure AD 超過匯出的刪除閾值，建議客戶允許刪除作業繼續進行。 [如何：允許刪除超過刪除閾值的流程](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>背景
註冊為混合式 Azure AD 聯結的 Windows 裝置會在 Azure AD 中表示為裝置物件。 這些裝置物件可以用來進行條件式存取。 Windows 10 裝置會透過 Azure AD Connect 同步處理至雲端，而下層的 Windows 裝置則直接使用 AD FS 或無縫單一登入來註冊。

## <a name="windows-10-devices"></a>Windows 10 裝置
只有具有混合式 Azure AD Join 所設定之特定 userCertificate 屬性值的 Windows 10 裝置，才應該透過 Azure AD Connect 同步處理到雲端。 在舊版的 Azure AD Connect 不會嚴格強制執行這項需求，而是在 Azure AD 中產生不必要的裝置物件。 Azure AD 中的這類裝置一律會保持「擱置」狀態，因為這些裝置不適合用 Azure AD 進行註冊。 

此版本的 Azure AD Connect 只會同步已正確設定為混合式 Azure AD 聯結的 Windows 10 裝置。 沒有 Azure AD 聯結特定 userCertificate 的 Windows 10 裝置物件將會從 Azure AD 移除。

## <a name="down-level-windows-devices"></a>舊版 Windows 裝置
Azure AD Connect 應該永遠不會同步[下層的 Windows 裝置](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 Azure AD 中的任何裝置先前未正確同步處理，現在會從 Azure AD 中刪除。 如果 Azure AD Connect 嘗試刪除舊版[windows 裝置](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，則裝置不是由[Microsoft Workplace Join 為非 Windows 10 電腦的 MSI](https://www.microsoft.com/download/details.aspx?id=53554)所建立，且無法由任何其他 Azure AD 功能使用。

某些客戶可能需要重新流覽[如何：規劃您的混合式 Azure Active Directory 聯結執行](../devices/hybrid-azuread-join-plan.md)，讓其 Windows 裝置正確註冊，並確保這類裝置可以完全參與以裝置為基礎的條件式存取。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何使用此更新來驗證刪除的裝置？

若要確認哪些裝置已被刪除，您可以使用此 PowerShell 腳本：https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此腳本會產生有關儲存在 Active Directory 電腦物件中之憑證的報告，特別是混合式 Azure AD join 功能所發行的憑證。
它會檢查 AD 中電腦物件的 UserCertificate 屬性中存在的憑證，並針對每個未過期的憑證，驗證是否已針對混合式 Azure AD 聯結功能發行憑證（也就是主體名稱符合 CN = {ObjectGUID}）。
在之前，Azure AD Connect 會 Azure AD 同步處理至少包含一個有效憑證的電腦，但從 Azure AD Connect 版本1.4 開始，同步處理引擎可以識別混合式 Azure AD 加入憑證，而且除非有有效的 Azure AD join 憑證，否則電腦物件不會同步至混合式 Azure AD。
Azure AD 已同步處理至 AD 但沒有有效混合式 Azure AD 聯結憑證的裝置，將會被同步處理引擎刪除（CloudFiltered = TRUE）。

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 版本歷程記錄](reference-connect-version-history.md)
