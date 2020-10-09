---
title: 瞭解 Azure AD Connect 1.4. x 和裝置消失 |Microsoft Docs
description: 本檔說明 1.4. x 版的 Azure AD Connect 所產生的問題
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73176035"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>瞭解 Azure AD Connect 1.4. x 和裝置消失
在 Azure AD Connect 版的中，某些客戶可能會看到部分或所有的 Windows 裝置從 Azure AD 消失。 這不會造成問題，因為 Azure AD 不會在條件式存取授權期間使用這些裝置身分識別。 此變更不會刪除已正確註冊 Azure AD 的任何 Windows 裝置以進行混合式 Azure AD Join。

如果您在 Azure AD 中看到裝置物件的刪除超過匯出刪除閾值，建議客戶允許刪除進行。 [如何：允許刪除超過刪除閾值時的流程](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>背景
註冊為混合式 Azure AD 聯結的 Windows 裝置會在 Azure AD 中表示為裝置物件。 這些裝置物件可以用於條件式存取。 Windows 10 裝置透過 Azure AD Connect 同步處理至雲端，下層 Windows 裝置會使用 AD FS 或無縫單一登入直接註冊。

## <a name="windows-10-devices"></a>Windows 10 裝置
只有 Windows 10 具有混合式 Azure AD Join 設定之特定 userCertificate 屬性值的裝置，才能 Azure AD Connect 同步至雲端。 在舊版 Azure AD Connect 不會嚴格強制執行此需求，因此 Azure AD 中會產生不必要的裝置物件。 Azure AD 中的這類裝置一律維持「擱置」狀態，因為這些裝置並不是要向 Azure AD 註冊。 

此版本的 Azure AD Connect 只會同步處理已正確設定混合式 Azure AD 聯結 Windows 10 裝置。 Windows 10 不含 Azure AD 聯結特定 userCertificate 的裝置物件將會從 Azure AD 中移除。

## <a name="down-level-windows-devices"></a>Down-Level Windows 裝置
Azure AD Connect 永遠不應該同步 [下層 Windows 裝置](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 Azure AD 先前未正確同步處理的任何裝置，現在將會從 Azure AD 刪除。 如果 Azure AD Connect 嘗試刪除 [下層 Windows 裝置](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，則裝置不是由 [Microsoft Workplace Join 針對非 WINDOWS 10 的電腦 MSI](https://www.microsoft.com/download/details.aspx?id=53554) 所建立的裝置，也不能由任何其他 Azure AD 功能使用。

某些客戶可能需要重新流覽 how [to：規劃您的混合式 Azure Active Directory 聯結實行](../devices/hybrid-azuread-join-plan.md) ，以正確註冊其 Windows 裝置，並確保這類裝置可充分參與以裝置為基礎的條件式存取。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何使用此更新來確認刪除哪些裝置？

若要確認哪些裝置已刪除，您可以使用此 PowerShell 腳本： https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此腳本會產生有關儲存在 Active Directory 電腦物件中之憑證的報表，特別是混合式 Azure AD join 功能所發行的憑證。
它會檢查 AD 中電腦物件之 UserCertificate 屬性中的憑證，並且針對每個未過期的憑證，驗證是否已針對混合式 Azure AD 聯結功能發出憑證 (亦即，主體名稱符合 CN = {ObjectGUID} ) 。
在之前，Azure AD Connect 會 Azure AD 任何至少包含一個有效憑證的電腦進行同步處理，但從 Azure AD Connect 版本1.4 開始，同步處理引擎可以識別混合式 Azure AD 加入憑證，並將電腦物件從同步處理到 Azure AD，除非有有效的混合式 Azure AD 加入憑證。
Azure AD 已同步至 AD 但沒有有效混合式 Azure AD 聯結憑證的裝置，將會 (CloudFiltered = TRUE) 由同步處理引擎刪除。

## <a name="next-steps"></a>後續步驟
- [Azure AD Connect 版本歷程記錄](reference-connect-version-history.md)
