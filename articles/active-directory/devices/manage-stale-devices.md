---
title: 如何在 Azure AD 中管理過時的裝置 | Microsoft Docs
description: 瞭解如何從 Azure 活動目錄中的已註冊設備的資料庫中刪除陳舊設備。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46be728216ed4b9c9e84c1c7f68c5ddf2051f42b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672311"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>如何：在 Azure AD 中管理陳舊設備

在理想情況下，若要完成生命週期，已註冊的裝置應在不需要時取消註冊。 不過，因為一些原因，例如裝置遺失、遭竊、損毀或作業系統重新安裝，造成您環境中常有過時的裝置。 身為 IT 系統管理員，您可能需要一個移除過時裝置的方法，讓您能專心管理裝置上實際需要管理的資源。

在本文中，您將了解如何有效率地管理環境中的過時裝置。
  

## <a name="what-is-a-stale-device"></a>什麼是過時裝置？

過時裝置是已向 Azure AD 註冊，但在特定時間範圍內未存取任何雲端應用程式的裝置。 過時裝置會影響您管理和支援租用戶中裝置和使用者的能力，因為： 

- 重複的裝置會讓技術服務人員難以識別目前正在使用的裝置。
- 設備數量的增加會產生不必要的設備回寫，從而增加 Azure AD 連接同步的時間。
- 為了具備一般防護並遵循合規性，您的裝置應處於乾淨的狀態。 

Azure AD 中若有過時裝置，可能會干擾您組織中裝置的一般生命週期原則。

## <a name="detect-stale-devices"></a>偵測過時裝置

由於過時裝置的定義是：已註冊但在特定時間範圍內未存取任何雲端應用程式的裝置，因此偵測過時裝置需要與時間戳記相關的屬性。 在 Azure AD 中，此屬性稱為 **ApproximateLastLogonTimestamp** 或**活動時間戳記**。 如果目前時間和**活動時間戳記**值之間的差異超過您為作用中裝置定義的時間範圍，則裝置會被視為過時。 此**活動時間戳記**現在處於公開預覽狀態。

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>如何管理活動時間戳記的值？  

活動時間戳記的評估會在裝置有驗證嘗試時觸發。 Azure AD 會在以下時機評估活動時間戳記：

- 已觸發需要[託管設備](../conditional-access/require-managed-devices.md)或[已批准用戶端應用](../conditional-access/app-based-conditional-access.md)的條件訪問策略。
- 已加入 Azure AD 或已加入混合式 Azure AD 的 Windows 10 裝置正在網路上運作。 
- Intune 受控裝置已簽入至服務。

如果啟用時間戳的現有值和當前值之間的增量超過 14 天（+/-5 天差異），則現有值將替換為新值。

## <a name="how-do-i-get-the-activity-timestamp"></a>如何取得活動時間戳記？

您有兩個選項可擷取活動時間戳記值：

- Azure 入口網站[裝置頁面](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)上的**活動**資料行

    ![活動時間戳記](./media/manage-stale-devices/01.png)

- [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) Cmdlet

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

定義時間範圍，這會是過時裝置的指標。 定義時間幀時，將用於將啟用時間戳更新到值中的視窗進行分解。 例如，不應將小於 21 天的時間戳記（包括方差）視為陳舊設備的指示器。 有些狀況會使得沒有過時的裝置看起來已過時。 例如，受影響裝置的擁有者可能在度假或請病假。  而這超過您過時裝置的時間範圍。

### <a name="disable-devices"></a>停用裝置

我們不建議立即刪除似乎已過時的裝置，因為您無法復原因誤判而刪除的裝置。 最佳做法是在寬限期內先停用裝置，然後再刪除裝置。 在您的原則中，可以定義在刪除裝置前先停用的時間範圍。

### <a name="mdm-controlled-devices"></a>由 MDM 控制的裝置

如果裝置在 Intune 或任何其他 MDM 解決方案的控制之下，請在管理系統中淘汰該裝置，然後再將其停用或刪除。

### <a name="system-managed-devices"></a>由系統管理的裝置

請勿刪除由系統管理的裝置。 這些通常是設備，如自動駕駛儀。 刪除後，無法重新預配這些設備。 新的 `get-msoldevice` Cmdlet 會根據預設排除由系統管理的裝置。 

### <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

已加入混合式 Azure AD 的裝置應遵循原則來管理內部部署的過時裝置。 

若要清除 Azure AD：

- **Windows 10 裝置** - 在內部部署 AD 中停用或刪除 Windows 10 裝置，並讓 Azure AD Connect 將變更的裝置狀態同步至 Azure AD。
- **Windows 7/8** - 首先禁用或刪除本地 AD 中的 Windows 7/8 設備。 您無法使用 Azure AD Connect 來停用或刪除 Azure AD 中的 Windows 7/8 裝置。 相反，在本地進行更改時，必須在 Azure AD 中禁用/刪除。

> [!NOTE]
>* 刪除本地 AD 或 Azure AD 中的設備不會刪除用戶端上的註冊。 它只會阻止使用設備作為標識（例如條件訪問）訪問資源。 閱讀有關如何[刪除用戶端註冊](faq.md#hybrid-azure-ad-join-faq)的其他資訊。
>* 僅在 Azure AD 中刪除 Windows 10 設備將使用 Azure AD 連接從本地重新同步設備，但作為處於"掛起"狀態的新物件。 設備上需要重新註冊。
>* 從 Windows 10/Server 2016 設備的同步作用域中刪除設備將刪除 Azure AD 設備。 將其重新添加到同步作用域將使新物件處於"掛起"狀態。 需要重新註冊設備。
>* 如果不對 Windows 10 設備使用 Azure AD 連接進行同步（例如，僅使用 AD FS 進行註冊），則必須管理類似于 Windows 7/8 設備的生命週期。


### <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

在 Azure AD 中停用或刪除加入 Azure AD 的裝置。

> [!NOTE]
>* 刪除 Azure AD 設備不會刪除用戶端上的註冊。 它只會阻止使用設備作為標識（例如條件訪問）訪問資源。 
>* 閱讀有關如何[在 Azure AD 上取消聯接的更多內容](faq.md#azure-ad-join-faq) 

### <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

在 Azure AD 中停用或刪除 Azure AD 註冊裝置。

> [!NOTE]
>* 刪除 Azure AD 註冊的設備不會刪除用戶端上的註冊。 它只會阻止使用設備作為標識（例如條件訪問）訪問資源。
>* 閱讀有關如何[刪除用戶端註冊](faq.md#azure-ad-register-faq)的詳細資訊

## <a name="clean-up-stale-devices-in-the-azure-portal"></a>在 Azure 入口網站中清除過時裝置  

雖然您可以在 Azure 入口網站中清除過時裝置，但使用 PowerShell 指令碼來處理此程序會更有效率。 使用最新的 PowerShell V1 模組使用時間戳篩選器並篩選出系統管理的設備（如自動駕駛儀）。 目前尚不建議使用 PowerShell V2。

典型的執行階段包含下列步驟：

1. 使用 [Connect-MsolService](/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) Cmdlet來連線到 Azure Active Directory
1. 取得裝置清單
1. 使用 [Disable-MsolDevice](/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) Cmdlet 停用裝置。 
1. 須等到您選擇的寬限期 (無論多久) 結束，才能刪除裝置。
1. 使用 [Remove-MsolDevice](/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0) Cmdlet 移除裝置。

### <a name="get-the-list-of-devices"></a>取得裝置清單

若要取得所有裝置，並將傳回的資料儲存在 CSV 檔案：

```PowerShell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

如果目錄中有大量設備，請使用時間戳篩選器縮小返回的設備數量。 若要取得時間戳記晚於特定日期的所有裝置，並將傳回的資料儲存在 CSV 檔案： 

```PowerShell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>時間戳記為什麼不更頻繁地更新？

更新時間戳記是為了支援裝置生命週期情節。 這不是稽核。 使用登入稽核記錄可了解裝置上較頻繁的更新。

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>為什麼我應該擔心 BitLocker 金鑰？

若在 Windows 10 裝置上設定 BitLocker 金鑰，這些金鑰會儲存在 Azure AD 中的裝置物件上。 如果您刪除過時裝置，裝置上儲存的 BitLocker 金鑰也會一併刪除。 您應該先判斷清除原則是否與您裝置的實際生命週期一致，再刪除過時裝置。 

### <a name="why-should-i-worry-about-windows-autopilot-devices"></a>為什麼我應該擔心 Windows 自動駕駛儀設備？

當 Azure AD 設備與 Windows 自動駕駛儀物件關聯時，如果將來將重新使用該設備，則可能會出現以下三種情況：
- 使用 Windows 自動駕駛儀使用者驅動的部署時不使用白手套，將創建新的 Azure AD 設備，但不會使用 ZTDID 對其進行標記。
- 使用 Windows 自動駕駛儀自部署模式部署時，它們將失敗，因為找不到關聯的 Azure AD 設備。  （這是一種安全機制，可確保沒有"冒名頂替"設備嘗試加入沒有憑據的 Azure AD。故障將指示 ZTDID 不匹配。
- 使用 Windows 自動駕駛儀白手套部署時，它們將失敗，因為找不到關聯的 Azure AD 設備。 （在幕後，白手套部署使用相同的自部署模式過程，因此它們強制實施相同的安全機制。

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>如何得知已加入的所有裝置類型？

若要深入了解不同類型，請參閱[裝置管理概觀](overview.md)。

### <a name="what-happens-when-i-disable-a-device"></a>當我停用裝置時，會發生什麼事？

任何使用裝置對 Azure AD 進行驗證的驗證都會遭到拒絕。 常見範例包括：

- **加入混合式 Azure AD 的裝置** - 使用者可能會使用裝置登入其內部部署網域。 不過，他們無法存取 Azure AD 資源，例如 Office 365。
- **加入 Azure AD 的裝置** - 使用者不能使用裝置來登入。 
- **行動裝置** - 使用者無法存取 Azure AD 資源，例如 Office 365。 

## <a name="next-steps"></a>後續步驟

若要取得在 Azure 入口網站中管理裝置的概觀，請參閱[使用 Azure 入口網站來管理裝置](device-management-azure-portal.md)
