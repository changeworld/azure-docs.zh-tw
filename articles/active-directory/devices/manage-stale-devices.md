---
title: 如何在 Azure AD 中管理過時的裝置 | Microsoft Docs
description: 瞭解如何在 Azure Active Directory 中移除已註冊裝置的資料庫中的過時裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea5d24fd36e668fc52a8b5c9a20472c42ef3c420
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825960"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>如何：管理 Azure AD 中的過時裝置

在理想情況下，若要完成生命週期，已註冊的裝置應在不需要時取消註冊。 不過，因為一些原因，例如裝置遺失、遭竊、損毀或作業系統重新安裝，造成您環境中常有過時的裝置。 身為 IT 系統管理員，您可能需要一個移除過時裝置的方法，讓您能專心管理裝置上實際需要管理的資源。

在本文中，您將了解如何有效率地管理環境中的過時裝置。
  

## <a name="what-is-a-stale-device"></a>什麼是過時裝置？

過時裝置是已向 Azure AD 註冊，但在特定時間範圍內未存取任何雲端應用程式的裝置。 過時裝置會影響您管理和支援租用戶中裝置和使用者的能力，因為： 

- 重複的裝置會讓技術服務人員難以識別目前正在使用的裝置。
- 增加的裝置數目會產生不必要的裝置回寫，Azure AD 連接同步處理的時間增加。
- 為了具備一般防護並遵循合規性，您的裝置應處於乾淨的狀態。 

Azure AD 中若有過時裝置，可能會干擾您組織中裝置的一般生命週期原則。

## <a name="detect-stale-devices"></a>偵測過時裝置

由於過時裝置的定義是：已註冊但在特定時間範圍內未存取任何雲端應用程式的裝置，因此偵測過時裝置需要與時間戳記相關的屬性。 在 Azure AD 中，此屬性稱為 **ApproximateLastLogonTimestamp** 或**活動時間戳記**。 如果目前時間和**活動時間戳記**值之間的差異超過您為作用中裝置定義的時間範圍，則裝置會被視為過時。 此**活動時間戳記**現在處於公開預覽狀態。

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>如何管理活動時間戳記的值？  

活動時間戳記的評估會在裝置有驗證嘗試時觸發。 Azure AD 會在以下時機評估活動時間戳記：

- 已觸發要求 [受管理裝置](../conditional-access/require-managed-devices.md) 或 [已核准用戶端應用程式](../conditional-access/app-based-conditional-access.md) 的條件式存取原則。
- 已加入 Azure AD 或已加入混合式 Azure AD 的 Windows 10 裝置正在網路上運作。 
- Intune 受控裝置已簽入至服務。

如果啟用時間戳的現有值與目前值之間的差異超過14天 (+/-5 天的變異數) ，則會將現有的值取代為新值。

## <a name="how-do-i-get-the-activity-timestamp"></a>如何取得活動時間戳記？

您有兩個選項可擷取活動時間戳記值：

- Azure 入口網站[裝置頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)上的**活動**資料行

    ![活動時間戳記](./media/manage-stale-devices/01.png)

- [Get-azureaddevice](/powershell/module/azuread/Get-AzureADDevice)指令 Cmdlet

    ![活動時間戳記](./media/manage-stale-devices/02.png)

## <a name="plan-the-cleanup-of-your-stale-devices"></a>規劃過時裝置的清除作業

若要有效率地清除環境中的過時裝置，您應定義相關原則。 此原則可協助您確實擷取所有與過時裝置相關的考量。 下列各節提供一般原則考量的範例。 

### <a name="cleanup-account"></a>清除帳戶

若要在 Azure AD 中更新裝置，您需要已指派下列其中一個角色的帳戶：

- 全域管理員
- 雲端裝置管理員
- Intune 服務管理員

在清除原則中，選取已指派必要角色的帳戶。 

### <a name="timeframe"></a>時間範圍

定義時間範圍，這會是過時裝置的指標。 定義您的時間範圍時，請考慮將啟用時間戳更新至您的值時所記下的視窗。 例如，您不應該考慮超過21天的時間戳記， (包含變異數) 作為過時裝置的指標。 有些狀況會使得沒有過時的裝置看起來已過時。 例如，受影響裝置的擁有者可能在度假或請病假。  而這超過您過時裝置的時間範圍。

### <a name="disable-devices"></a>停用裝置

我們不建議立即刪除似乎已過時的裝置，因為您無法復原因誤判而刪除的裝置。 最佳做法是在寬限期內先停用裝置，然後再刪除裝置。 在您的原則中，可以定義在刪除裝置前先停用的時間範圍。

### <a name="mdm-controlled-devices"></a>由 MDM 控制的裝置

如果裝置在 Intune 或任何其他 MDM 解決方案的控制之下，請在管理系統中淘汰該裝置，然後再將其停用或刪除。

### <a name="system-managed-devices"></a>由系統管理的裝置

請勿刪除由系統管理的裝置。 這些通常是裝置，例如 Autopilot。 一旦刪除後，就無法重新布建這些裝置。 新的 `Get-AzureADDevice` Cmdlet 會根據預設排除由系統管理的裝置。 

### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

已加入混合式 Azure AD 的裝置應遵循原則來管理內部部署的過時裝置。 

若要清除 Azure AD：

- **Windows 10 裝置** - 在內部部署 AD 中停用或刪除 Windows 10 裝置，並讓 Azure AD Connect 將變更的裝置狀態同步至 Azure AD。
- **Windows 7/8** -先停用或刪除內部部署 AD 中的 windows 7/8 裝置。 您無法使用 Azure AD Connect 來停用或刪除 Azure AD 中的 Windows 7/8 裝置。 相反地，當您在內部部署中進行變更時，您必須在 Azure AD 中停用/刪除。

> [!NOTE]
>* 刪除內部部署 AD 或 Azure AD 中的裝置並不會移除用戶端上的註冊。 它只會防止使用裝置作為身分識別來存取資源 (例如，條件式存取) 。 閱讀有關如何在 [用戶端上移除註冊](faq.md#hybrid-azure-ad-join-faq)的其他資訊。
>* 只有在 Azure AD 中刪除 Windows 10 裝置，將會使用 Azure AD connect，而非處於「擱置」狀態的新物件，從內部部署重新同步處理裝置。 需要重新註冊裝置。
>* 從 Windows 10/伺服器2016裝置的同步範圍移除裝置，將會刪除 Azure AD 裝置。 將其新增回同步範圍將會將新物件置於「擱置」狀態。 需要重新註冊裝置。
>* 如果您未使用 Windows 10 裝置的 Azure AD Connect 來同步處理 (例如，只使用 AD FS 進行註冊) ，您必須管理與 Windows 7/8 裝置類似的生命週期。


### <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

在 Azure AD 中停用或刪除加入 Azure AD 的裝置。

> [!NOTE]
>* 刪除 Azure AD 裝置並不會移除用戶端上的註冊。 它只會防止使用裝置作為身分識別來存取資源， (例如條件式存取) 。 
>* 深入瞭解 [如何退出 Azure AD](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

在 Azure AD 中停用或刪除 Azure AD 註冊裝置。

> [!NOTE]
>* 在 Azure AD 中刪除 Azure AD 註冊的裝置並不會移除用戶端上的註冊。 它只會防止使用裝置作為身分識別來存取資源 (例如，條件式存取) 。
>* 深入瞭解 [如何移除用戶端上的註冊](faq.md#azure-ad-register-faq)

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>在 Azure 入口網站中清除過時裝置  

雖然您可以在 Azure 入口網站中清除過時裝置，但使用 PowerShell 指令碼來處理此程序會更有效率。 使用最新的 PowerShell V1 模組來使用時間戳篩選器，並篩選出系統管理的裝置（例如 Autopilot）。 目前尚不建議使用 PowerShell V2。

典型的執行階段包含下列步驟：

1. 使用 [AzureAD](/powershell/module/azuread/connect-azuread) Cmdlet 連接到 Azure Active Directory
1. 取得裝置清單
1. 使用 [get-azureaddevice](/powershell/module/azuread/Set-AzureADDevice) 指令程式停用裝置 (停用-AccountEnabled 選項) 。 
1. 須等到您選擇的寬限期 (無論多久) 結束，才能刪除裝置。
1. 使用 [get-azureaddevice](/powershell/module/azuread/Remove-AzureADDevice) Cmdlet 移除裝置。

### <a name="get-the-list-of-devices"></a>取得裝置清單

若要取得所有裝置，並將傳回的資料儲存在 CSV 檔案：

```PowerShell
Get-AzureADDevice -All:$true | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-summary.csv
```

如果您的目錄中有大量的裝置，請使用時間戳篩選器來縮小傳回的裝置數目。 若要取得時間戳記晚於特定日期的所有裝置，並將傳回的資料儲存在 CSV 檔案： 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-AzureADDevice | Where {$_.ApproximateLastLogonTimeStamp -le $dt} | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>您應該知道的事項

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>時間戳記為什麼不更頻繁地更新？

更新時間戳記是為了支援裝置生命週期情節。 這不是稽核。 使用登入稽核記錄可了解裝置上較頻繁的更新。

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>為什麼我應該擔心 BitLocker 金鑰？

若在 Windows 10 裝置上設定 BitLocker 金鑰，這些金鑰會儲存在 Azure AD 中的裝置物件上。 如果您刪除過時裝置，裝置上儲存的 BitLocker 金鑰也會一併刪除。 您應該先判斷清除原則是否與您裝置的實際生命週期一致，再刪除過時裝置。 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>為什麼我應該擔心 Windows Autopilot 裝置？

當您刪除與 Windows Autopilot 物件相關聯的 Azure AD 裝置時，如果未來將會重新規劃裝置，則可能會發生下列三種情況：
- 使用 Windows Autopilot 的使用者導向部署，而不使用預先布建，則會建立新的 Azure AD 裝置，但不會將它標記為 ZTDID。
- 使用 Windows Autopilot 自我部署模式部署時，將會失敗，因為找不到關聯 Azure AD 裝置。   (這種安全性機制，可確保沒有任何「冒名頂替」的裝置嘗試加入沒有認證的 Azure AD。 ) 失敗將表示 ZTDID 不相符。
- 在 Windows Autopilot 預先布建的部署中，因為找不到相關聯的 Azure AD 裝置，所以會失敗。 在幕後 (，預先布建的部署會使用相同的自我部署模式進程，因此會強制執行相同的安全性機制。 ) 

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>如何得知已加入的所有裝置類型？

若要深入了解不同類型，請參閱[裝置管理概觀](overview.md)。

### <a name="what-happens-when-i-disable-a-device"></a>當我停用裝置時，會發生什麼事？

任何使用裝置對 Azure AD 進行驗證的驗證都會遭到拒絕。 常見範例包括：

- **混合式 Azure AD 加入的裝置** -使用者可能可以使用裝置來登入其內部部署網域。 不過，他們無法存取 Azure AD 資源，例如 Microsoft 365。
- **加入 Azure AD 的裝置** - 使用者不能使用裝置來登入。 
- 行動**裝置**-使用者無法存取 Azure AD 資源，例如 Microsoft 365。 

## <a name="next-steps"></a>後續步驟

若要取得在 Azure 入口網站中管理裝置的概觀，請參閱[使用 Azure 入口網站來管理裝置](device-management-azure-portal.md)
