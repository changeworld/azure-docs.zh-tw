---
title: 如何使用 Azure 入口網站來管理裝置 | Microsoft Docs
description: 了解如何使用 Azure 入口網站來管理裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09de5911ca0946bfcbcb77d1ad4131c8feac9f0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79262239"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 入口網站管理裝置身分識別

透過 Azure Active Directory （Azure AD）中的裝置身分識別管理，您可以確保使用者會從符合安全性與合規性標準的裝置存取您的資源。

本文：

- 假設您已熟悉[中的裝置身分識別管理簡介 Azure Active Directory](overview.md)
- 提供您使用 Azure AD 入口網站來管理裝置身分識別的相關資訊

## <a name="manage-device-identities"></a>管理裝置身分識別

Azure AD 入口網站可讓您集中管理您的裝置身分識別。 您可以使用[直接連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或下列方法來取得這個位置：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 流覽至**Azure Active Directory** > **裝置**]。

[裝置]**** 頁面可讓您：

- 設定您的裝置設定
- 尋找裝置
- 執行裝置身分識別管理工作
- 查看裝置相關的審核記錄  
  
## <a name="configure-device-settings"></a>設定裝置設定

若要使用 Azure AD 入口網站來管理您的裝置身分識別，您的裝置必須[註冊或加入](overview.md)Azure AD。 身為系統管理員，您可以透過設定裝置設定，來微調註冊和加入裝置的程序。

[裝置設定] 頁面可讓您設定與裝置身分識別相關的設定：

![管理 Intune 裝置](./media/device-management-azure-portal/21.png)

- **使用者可以將裝置加入至 Azure AD** -此設定可讓您選取可以將其裝置註冊為 Azure AD 已加入裝置的使用者。 預設為 **All**。

> [!NOTE]
> **使用者可以將裝置加入 Azure AD**設定僅適用于 Windows 10 上的 Azure AD 加入。

- **加入 Azure AD 的裝置上其他本機系統管理員** - 您可以選取哪些使用者會授與裝置的本機系統管理員權限。 新增至此處的使用者將會新增至 Azure AD 中的「裝置系統管理員」** 角色。 Azure AD 中的全域管理員和裝置擁有者預設會授與本機系統管理員權限。 此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階編輯功能。
- **使用者可以向 Azure AD 註冊其裝置**-您需要設定此設定，以允許 Azure AD 註冊 Windows 10 個人版、IOS、Android 和 macOs 裝置。 如果您選取 [**無**]，則不允許裝置向 Azure AD 註冊。 需要先註冊 (registration)，才可註冊 (enrollment) Microsoft Intune 或適用於 Office 365 的行動裝置管理 (MDM)。 如果您已設定任一服務，則會選取 [全部]**** 且無法使用 [無]****。
- **需要多重要素驗證才能加入裝置**-您可以選擇使用者是否需要提供額外的驗證因素，才能將其裝置加入 Azure AD。 預設值為 [**否**]。 建議在註冊裝置時要求 Multi-Factor Authentication。 啟用此服務的 Multi-Factor Authentication 之前，您必須確定已為註冊其裝置的使用者設定 Multi-Factor Authentication。 如需不同 Azure Multi-Factor Authentication 服務的詳細資訊，請參閱[開始使用 Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> [**需要多因素驗證才能加入裝置**] 設定會套用至已加入 Azure AD 或 Azure AD 註冊的裝置。 此設定不適用於已加入混合式 Azure AD 的裝置。

- **裝置數目上限**-這項設定可讓您選取使用者已加入的 Azure AD 數目上限，或在 Azure AD 中 Azure AD 已註冊裝置。 如果使用者達到此配額，則在移除一或多個現有的裝置之前，將無法新增其他裝置。 預設值為 **20**。

> [!NOTE]
> [**裝置數目上限**] 設定適用于已加入 Azure AD 或 Azure AD 註冊的裝置。 此設定不適用於已加入混合式 Azure AD 的裝置。

- **使用者可以在裝置間同步設定及應用程式資料** - 根據預設，這項設定會設定為 [無]****。 選取特定使用者或群組，或是選取 [全部]，以便在使用者的 Windows 10 裝置間同步其設定及應用程式資料。 深入了解同步在 Windows 10 中的運作方式。
此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階功能。

## <a name="locate-devices"></a>尋找裝置

您有兩個選項可用來尋找已註冊及已加入的裝置：

- [裝置]**** 頁面之 [管理]**** 區段中的 [所有裝置]****  
- [使用者]**** 頁面之 [管理]**** 區段中的 [裝置]****

您可以透過這兩個選項移至檢視，該檢視：

- 可讓您使用 [顯示名稱] 或 [裝置識別碼] 做為篩選準則來搜尋裝置。
- 為您提供已註冊和已加入裝置的詳細概觀
- 可讓您執行一般裝置管理工作

![所有裝置](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* 如果您在已註冊的資料行下看到「混合式 Azure AD 聯結」的裝置，且狀態為「擱置」，表示裝置已從 Azure AD connect 同步處理，且正在等候從用戶端完成註冊。 深入瞭解如何[規劃您的混合式 Azure AD 聯結執行](hybrid-azuread-join-plan.md)。 如需其他資訊，請參閱[裝置](faq.md)的常見問題。
>
>   ![擱置裝置](./media/device-management-azure-portal/75.png)
>
>* 就某些 iOS 裝置而言，包含單引號的裝置名稱可能會使用看起來像單引號的不同字元。 因此，搜尋這類裝置有點棘手，如果您不會看到正確的搜尋結果，請確定搜尋字串包含相符的單引號字元。

## <a name="device-identity-management-tasks"></a>裝置身分識別管理工作

身為全域管理員或雲端裝置管理員，您可以管理已註冊或已加入的裝置。 Intune 服務管理員可以：

- 更新裝置 – 範例為啟用/停用裝置等每日作業
- 刪除裝置 – 當裝置已淘汰且應在 Azure AD 中刪除時

本節提供一般裝置身分識別管理工作的相關資訊。

### <a name="manage-an-intune-device"></a>管理 Intune 裝置

如果您是 Intune 管理員，您可以管理標示為 **Microsoft Intune** 的裝置。 如果裝置未向註冊 Microsoft Intune 則 [管理] 選項會呈現灰色。

![管理 Intune 裝置](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>啟用/停用 Azure AD 裝置

若要啟用/停用裝置，您有兩個選項：

- [所有裝置]**** 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/71.png)

- [裝置]**** 頁面上的工具列

   ![管理 Intune 裝置](./media/device-management-azure-portal/32.png)

**標記**

- 您必須是 Azure AD 中的全域管理員或雲端裝置系統管理員，才能啟用/停用裝置。 
- 停用裝置會導致裝置無法成功地向 Azure AD 進行驗證，藉此防止裝置存取受裝置 CA 保護的 Azure AD 資源，或使用您的 WH4B 認證。
- 停用裝置將會同時撤銷裝置上的主要重新整理權杖（PRT）和任何重新整理權杖（RT）。

### <a name="delete-an-azure-ad-device"></a>刪除 Azure AD 裝置

若要刪除裝置，您有兩個選項：

- [所有裝置]**** 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/72.png)

- [裝置]**** 頁面上的工具列

   ![刪除裝置](./media/device-management-azure-portal/34.png)

**標記**

- 您必須是 Azure AD 中的全域管理員或 Intune 系統管理員，才能刪除裝置。
- 刪除裝置：
   - 防止裝置存取您的 Azure AD 資源。
   - 移除所有附加至裝置的詳細資料，例如適用於 Windows 裝置的 BitLocker 金鑰。  
   - 代表無法復原的活動，除非必要，否則不建議使用。

如果裝置是由另一個管理授權單位（例如 Microsoft Intune）所管理，請確定已抹除/淘汰裝置，然後才在 Azure AD 中刪除裝置。 在刪除任何裝置之前，請先參閱如何[管理過時的裝置](device-management-azure-portal.md)。

### <a name="view-or-copy-device-id"></a>檢視或複製裝置識別碼

您可以使用裝置識別碼來確認裝置上的裝置識別碼詳細資料，或在疑難排解期間使用 PowerShell。 若要存取複製選項，請按一下裝置。

![檢視裝置識別碼](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>檢視或複製 BitLocker 金鑰

您可以檢視和複製 BitLocker 金鑰，以協助使用者復原他們所加密的磁碟機。 這些金鑰只適用於已加密並將其金鑰儲存在 Azure AD 中的 Windows 裝置。 您可以在存取裝置的詳細資料時複製這些金鑰。

![檢視 BitLocker 金鑰](./media/device-management-azure-portal/36.png)

若要檢視或複製 BitLocker 金鑰，您必須是裝置的擁有者，或是至少已獲指派下列其中一個角色的使用者：

- 雲端裝置管理員
- 全域管理員
- 服務台系統管理員
- Intune 服務管理員
- 安全性系統管理員
- 安全性讀取者

> [!NOTE]
> 已聯結混合式 Azure AD 的 Windows 10 裝置沒有擁有者。 因此，如果您依擁有者尋找裝置卻未找到，請依裝置識別碼來搜尋。

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
