---
title: 如何使用 Azure 入口網站來管理裝置 | Microsoft Docs
description: 了解如何使用 Azure 入口網站來管理裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce09bd2a3f5f474ad5c6e6eb73865e2b2dc9fe3a
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541939"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 入口網站管理裝置身分識別 (機器翻譯)

Azure AD 提供您一個集中位置來管理裝置身分識別。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **裝置**]。

[![Azure 入口網站中的所有裝置視圖](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

[**所有裝置**] 頁面可讓您：

- 識別裝置，包括：
   - 已聯結或已在 Azure AD 中註冊的裝置。
   - 使用[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)部署的裝置。
   - 使用[通用列印](https://docs.microsoft.com/universal-print/fundamentals/universal-print-getting-started)的印表機
- 執行裝置身分識別管理工作，例如啟用、停用、刪除或管理。
   - [印表機](/universal-print/fundamentals/)和[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)裝置在 Azure AD 中具有有限的管理選項。 它們必須從其個別的系統管理介面加以管理。
- 設定您的裝置身分識別設定。
- 啟用或停用企業狀態漫遊。
- 查看裝置相關的審核記錄

## <a name="manage-devices"></a>管理裝置

Azure AD 中有兩個位置可以管理裝置：

- **Azure 入口網站**  > **Azure Active Directory**  > **裝置**
- **Azure 入口網站**  > **Azure Active Directory**  > **使用者**> 選取 >**裝置**的使用者

這兩個選項都可讓系統管理員能夠：

- 搜尋裝置。
- 請參閱裝置詳細資料，包括：
    - 裝置名稱
    - 裝置識別碼
    - 作業系統和版本
    - 聯結類型
    - 擁有者
    - 行動裝置管理與合規性
    - BitLocker 修復金鑰
- 執行裝置身分識別管理工作，例如、啟用、停用、刪除或管理。
   - [印表機](/universal-print/fundamentals/)和[Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)裝置在 Azure AD 中具有有限的管理選項。 它們必須從其個別的系統管理介面加以管理。

> [!TIP]
> - 已聯結混合式 Azure AD 的 Windows 10 裝置沒有擁有者。 如果您要依擁有者尋找裝置，但找不到，請依裝置識別碼進行搜尋。
>
> - 如果您在已註冊的資料行下看到「混合式 Azure AD 聯結」的裝置，且狀態為「擱置」，表示裝置已從 Azure AD connect 同步處理，且正在等候從用戶端完成註冊。 深入瞭解如何[規劃您的混合式 Azure AD 聯結執行](hybrid-azuread-join-plan.md)。 如需其他資訊，請參閱[裝置](faq.md)的常見問題。
>
> - 就某些 iOS 裝置而言，包含單引號的裝置名稱可能會使用看起來像單引號的不同字元。 因此，搜尋這類裝置有點棘手，如果您不會看到正確的搜尋結果，請確定搜尋字串包含相符的單引號字元。

### <a name="manage-an-intune-device"></a>管理 Intune 裝置

如果您是 Intune 管理員，您可以管理已將 MDM 標示為**Microsoft Intune**的裝置。 如果裝置未向 Microsoft Intune 註冊，[管理] 選項將會呈現灰色。

### <a name="enable-or-disable-an-azure-ad-device"></a>啟用或停用 Azure AD 裝置

若要啟用或停用裝置，您有兩個選項：

- 選取一或多個裝置之後，[**所有裝置**] 頁面上的工具列。
- 向下切入至特定裝置之後的工具列。

> [!IMPORTANT]
> - 您必須是 Azure AD 中的全域管理員或雲端設備系統管理員，才能啟用或停用裝置。 
> - 停用裝置會導致裝置無法成功地向 Azure AD 進行驗證，藉此防止裝置存取受裝置型條件式存取或使用 Windows Hello 企業版認證保護的 Azure AD 資源。
> - 停用裝置將會同時撤銷裝置上的主要重新整理權杖（PRT）和任何重新整理權杖（RT）。
> - 無法在 Azure AD 中啟用或停用印表機。

### <a name="delete-an-azure-ad-device"></a>刪除 Azure AD 裝置

若要刪除裝置，您有兩個選項：

- 選取一或多個裝置之後，[**所有裝置**] 頁面上的工具列。
- 向下切入至特定裝置之後的工具列。

> [!IMPORTANT]
> - 您必須在 Azure AD 中指派雲端裝置管理員、Intune 管理員或全域系統管理員角色，才能刪除裝置。
> - 無法在 Azure AD 中刪除印表機和 Windows Autopilot 裝置
> - 刪除裝置：
>    - 防止裝置存取您的 Azure AD 資源。
>    - 移除所有附加至裝置的詳細資料，例如適用於 Windows 裝置的 BitLocker 金鑰。  
>    - 代表無法復原的活動，除非必要，否則不建議使用。

如果裝置是由另一個管理授權單位（例如 Microsoft Intune）所管理，請確定已抹除/淘汰裝置，然後才在 Azure AD 中刪除裝置。 在刪除任何裝置之前，請先參閱如何[管理過時的裝置](manage-stale-devices.md)。

### <a name="view-or-copy-device-id"></a>檢視或複製裝置識別碼

您可以使用裝置識別碼來確認裝置上的裝置識別碼詳細資料，或在疑難排解期間使用 PowerShell。 若要存取複製選項，請按一下裝置。

![檢視裝置識別碼](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>檢視或複製 BitLocker 金鑰

您可以查看並複製 BitLocker 金鑰，讓使用者能夠復原加密的磁片磁碟機。 這些金鑰只適用於已加密並將其金鑰儲存在 Azure AD 中的 Windows 裝置。 您可以在存取裝置的詳細資料時找到這些金鑰，方法是選取 [**顯示修復金鑰**]。 選取 [**顯示修復金鑰**] 將會產生 [審核記錄]，您可以在類別目錄中找到它 `KeyManagement` 。

![檢視 BitLocker 金鑰](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

若要檢視或複製 BitLocker 金鑰，您必須是裝置的擁有者，或是至少已獲指派下列其中一個角色的使用者：

- 雲端裝置管理員
- 全域管理員
- 服務台系統管理員
- Intune 服務管理員
- 安全性系統管理員
- 安全性讀取者

### <a name="device-list-filtering-preview"></a>裝置清單篩選（預覽）

先前，您只能依照活動和啟用狀態來篩選裝置清單。 此預覽現在可讓您根據裝置上的下列屬性來篩選裝置清單：

- 啟用狀態
- 符合規範狀態
- 聯結類型（Azure AD 聯結，混合式 Azure AD 聯結，Azure AD 註冊）
- 活動時間戳記
- OS
- 裝置類型（印表機、安全的 Vm、共用的裝置、已註冊的裝置）

若要在 [**所有裝置**] 視圖中啟用預覽篩選功能：

![啟用篩選預覽功能](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory**  >  **裝置**]。
1. 選取顯示的橫幅，**試用新的裝置篩選改良功能。按一下以啟用預覽。**

您現在可以將**篩選器新增**至 [**所有裝置**] 視圖。

## <a name="configure-device-settings"></a>設定裝置設定

若要使用 Azure AD 入口網站來管理裝置身分識別，這些裝置必須[註冊或加入](overview.md)Azure AD。 身為系統管理員，您可以藉由設定下列裝置設定來控制註冊和加入裝置的程式。

![與 Azure AD 相關的裝置設定](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **使用者可以將裝置加入至 Azure AD** -此設定可讓您選取可以將其裝置註冊為 Azure AD 已加入裝置的使用者。 預設為 **All**。

> [!NOTE]
> **使用者可以將裝置加入 Azure AD**設定僅適用于 Windows 10 上的 Azure AD 加入。

- **加入 Azure AD 的裝置上其他本機系統管理員** - 您可以選取哪些使用者會授與裝置的本機系統管理員權限。 這些使用者會新增至 Azure AD 中的 [*裝置系統管理員*] 角色。 Azure AD 中的全域管理員和裝置擁有者預設會授與本機系統管理員權限。 此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階編輯功能。
- **使用者可以向 Azure AD 註冊其裝置**-您需要設定此設定，以允許 Azure AD 註冊 Windows 10 個人版、IOS、Android 和 macOS 裝置。 如果您選取 [**無**]，則不允許裝置向 Azure AD 註冊。 需要先註冊 (registration)，才可註冊 (enrollment) Microsoft Intune 或適用於 Office 365 的行動裝置管理 (MDM)。 如果您已設定任一服務，則會選取 [全部]**** 且無法使用 [無]****。
- **需要多重要素驗證才能加入裝置**-您可以選擇使用者是否需要提供額外的驗證因素，才能將其裝置加入 Azure AD。 預設值為 [**否**]。 建議在註冊裝置時要求 Multi-Factor Authentication。 啟用此服務的 Multi-Factor Authentication 之前，您必須確定已為註冊其裝置的使用者設定 Multi-Factor Authentication。 如需不同 Azure Multi-Factor Authentication 服務的詳細資訊，請參閱[開始使用 Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> [**需要多因素驗證才能加入裝置**] 設定會套用至已加入 Azure AD 或 Azure AD 註冊的裝置。 此設定不適用於已加入混合式 Azure AD 的裝置。

- **裝置數目上限**-這項設定可讓您選取使用者已加入的 Azure AD 數目上限，或在 Azure AD 中 Azure AD 已註冊裝置。 如果使用者達到此配額，則在移除一或多個現有的裝置之前，將無法新增其他裝置。 預設值為 **20**。

> [!NOTE]
> [**裝置數目上限**] 設定適用于已加入 Azure AD 或 Azure AD 註冊的裝置。 此設定不適用於已加入混合式 Azure AD 的裝置。

- [企業狀態漫遊](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>稽核記錄

您可以透過活動記錄來取得裝置活動。 這些記錄包含由裝置註冊服務和使用者所觸發的活動：

- 建立裝置，以及在裝置上新增擁有者/使用者
- 變更裝置設定
- 刪除或更新裝置等裝置作業

稽核資料的進入點是 [裝置]**** 頁面之 [活動]**** 區段中的 [稽核記錄]****。

Audit 記錄檔具有預設清單視圖，顯示：

- 發生時間與日期
- 目標
- 活動的啟動者/執行者 (對象)
- 活動 (項目)

![稽核記錄](./media/device-management-azure-portal/63.png)

您可以按一下工具列中的 [資料**行**] 來自訂清單視圖。

![稽核記錄](./media/device-management-azure-portal/64.png)

若要將報告的資料縮小至您適用的層級，您可以使用下列欄位篩選稽核資料︰

- 類別
- 活動資源類型
- 活動
- 日期範圍
- 目標
- 啟動者 (執行者)

除了篩選，您還可以搜尋特定項目。

![稽核記錄](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>後續步驟

[如何管理 Azure AD 中的過時裝置](manage-stale-devices.md)

[企業狀態漫遊](enterprise-state-roaming-overview.md)
