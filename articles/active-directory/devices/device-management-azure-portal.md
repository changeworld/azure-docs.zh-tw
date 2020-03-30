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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262239"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 門戶管理設備標識

借助 Azure 活動目錄 （Azure AD） 中的設備標識管理，可以確保使用者從符合安全性和合規性標準的設備訪問資源。

本文：

- 假設您熟悉[Azure 活動目錄中的設備標識管理簡介](overview.md)
- 提供有關使用 Azure AD 門戶管理設備標識的資訊

## <a name="manage-device-identities"></a>管理裝置身分識別

Azure AD 門戶為您提供了管理設備標識的中心位置。 您可以使用[直接連結](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices)或：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 流覽到**Azure 活動目錄** > **設備**。

[裝置]**** 頁面可讓您：

- 設定您的裝置設定
- 尋找裝置
- 執行設備標識管理工作
- 查看與設備相關的稽核記錄  
  
## <a name="configure-device-settings"></a>設定裝置設定

要使用 Azure AD 門戶管理設備標識，您的設備需要[註冊或加入](overview.md)到 Azure AD。 身為系統管理員，您可以透過設定裝置設定，來微調註冊和加入裝置的程序。

通過設備設置頁面，您可以配置與設備標識相關的設置：

![管理 Intune 裝置](./media/device-management-azure-portal/21.png)

- **使用者可以將設備加入 Azure AD** - 此設置允許您選擇可以將設備註冊為 Azure AD 聯接設備的使用者。 預設為 **All**。

> [!NOTE]
> **使用者可以將設備加入 Azure AD**設置僅適用于 Windows 10 上的 Azure AD 聯接。

- **加入 Azure AD 的裝置上其他本機系統管理員** - 您可以選取哪些使用者會授與裝置的本機系統管理員權限。 新增至此處的使用者將會新增至 Azure AD 中的「裝置系統管理員」** 角色。 Azure AD 中的全域管理員和裝置擁有者預設會授與本機系統管理員權限。 此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階編輯功能。
- **使用者可以在 Azure AD 中註冊其設備**- 您需要配置此設置，以允許 Windows 10 個人、iOS、Android 和 macO 設備在 Azure AD 中註冊。 如果選擇 **"無"，** 則不允許設備向 Azure AD 註冊。 需要先註冊 (registration)，才可註冊 (enrollment) Microsoft Intune 或適用於 Office 365 的行動裝置管理 (MDM)。 如果您已設定任一服務，則會選取 [全部]**** 且無法使用 [無]****。
- **需要多因素身份驗證才能加入設備**- 您可以選擇是否需要使用者提供其他身份驗證因數才能將其設備加入 Azure AD。 預設值為 **"否**"。 建議在註冊裝置時要求 Multi-Factor Authentication。 啟用此服務的 Multi-Factor Authentication 之前，您必須確定已為註冊其裝置的使用者設定 Multi-Factor Authentication。 如需不同 Azure Multi-Factor Authentication 服務的詳細資訊，請參閱[開始使用 Azure Multi-Factor Authentication](../authentication/concept-mfa-whichversion.md)。 

> [!NOTE]
> **需要多因素身份驗證才能加入設備**設置應用於 Azure AD 加入或 Azure AD 註冊的設備。 此設置不適用於混合 Azure AD 聯接設備。

- **設備的最大數量**- 此設置允許您選擇使用者可以在 Azure AD 中擁有的最大 Azure AD 加入或 Azure AD 註冊設備數。 如果使用者達到此配額，則在移除一或多個現有的裝置之前，將無法新增其他裝置。 預設值為 **20**。

> [!NOTE]
> **設備設置的最大數量**適用于 Azure AD 已加入或 Azure AD 註冊的設備。 此設置不適用於混合 Azure AD 聯接設備。

- **使用者可以在裝置間同步設定及應用程式資料** - 根據預設，這項設定會設定為 [無]****。 選取特定使用者或群組，或是選取 [全部]，以便在使用者的 Windows 10 裝置間同步其設定及應用程式資料。 深入了解同步在 Windows 10 中的運作方式。
此選項是可透過 Azure AD Premium 或 Enterprise Mobility Suite (EMS) 等產品使用的進階功能。

## <a name="locate-devices"></a>尋找裝置

您有兩個選項可用來尋找已註冊及已加入的裝置：

- [裝置]**** 頁面之 [管理]**** 區段中的 [所有裝置]****  
- [使用者]**** 頁面之 [管理]**** 區段中的 [裝置]****

您可以透過這兩個選項移至檢視，該檢視：

- 使您能夠使用顯示名稱或裝置識別碼 作為篩選器搜索設備。
- 為您提供已註冊和已加入裝置的詳細概觀
- 可讓您執行一般裝置管理工作

![所有裝置](./media/device-management-azure-portal/51.png)

>[!TIP]
>
>* 如果在"註冊"列下看到"混合 Azure AD 已聯接"狀態的設備，則表示設備已從 Azure AD 連接同步，正在等待從用戶端完成註冊。 詳細瞭解如何[規劃混合 Azure AD 聯接實現](hybrid-azuread-join-plan.md)。 其他資訊可以在文章中找到，[設備常見問題](faq.md)。
>
>   ![掛起的設備](./media/device-management-azure-portal/75.png)
>
>* 就某些 iOS 裝置而言，包含單引號的裝置名稱可能會使用看起來像單引號的不同字元。 因此，搜索此類設備有點棘手 - 如果您未正確看到搜尋結果，請確保搜索字串包含匹配的撇號字元。

## <a name="device-identity-management-tasks"></a>設備標識管理工作

作為全域管理員或雲裝置管理員，您可以管理已註冊或加入的設備。 Intune 服務管理員可以：

- 更新裝置 – 範例為啟用/停用裝置等每日作業
- 刪除裝置 – 當裝置已淘汰且應在 Azure AD 中刪除時

本節提供有關常見設備標識管理工作的資訊。

### <a name="manage-an-intune-device"></a>管理 Intune 裝置

如果您是 Intune 管理員，您可以管理標示為 **Microsoft Intune** 的裝置。 如果設備未在 Microsoft Intune 註冊，"管理"選項將灰顯。

![管理 Intune 裝置](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>啟用/停用 Azure AD 裝置

若要啟用/停用裝置，您有兩個選項：

- [所有裝置]**** 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/71.png)

- [裝置]**** 頁面上的工具列

   ![管理 Intune 裝置](./media/device-management-azure-portal/32.png)

**言論：**

- 您需要是 Azure AD 中的全域管理員或雲裝置管理員才能啟用/禁用設備。 
- 禁用設備可防止設備成功使用 Azure AD 進行身份驗證，從而阻止設備訪問由設備 CA 保護的 Azure AD 資源或使用 WH4B 憑據。
- 禁用設備將撤銷設備上的主刷新權杖 （PRT） 和任何刷新權杖 （RT）。

### <a name="delete-an-azure-ad-device"></a>刪除 Azure AD 裝置

若要刪除裝置，您有兩個選項：

- [所有裝置]**** 頁面上的 [工作] 功能表 ("...")

   ![管理 Intune 裝置](./media/device-management-azure-portal/72.png)

- [裝置]**** 頁面上的工具列

   ![刪除裝置](./media/device-management-azure-portal/34.png)

**言論：**

- 您必須是 Azure AD 中的全域管理員或 Intune 系統管理員，才能刪除裝置。
- 刪除裝置：
   - 防止裝置存取您的 Azure AD 資源。
   - 移除所有附加至裝置的詳細資料，例如適用於 Windows 裝置的 BitLocker 金鑰。  
   - 代表無法復原的活動，除非必要，否則不建議使用。

如果設備由其他管理機構（例如 Microsoft Intune）管理，請確保在 Azure AD 中刪除設備之前已擦除/停用設備。 在刪除任何設備之前，請查看如何[管理陳舊設備](device-management-azure-portal.md)。

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

您可以透過活動記錄來取得裝置活動。 這些日誌包括設備註冊服務和使用者觸發的活動：

- 建立裝置，以及在裝置上新增擁有者/使用者
- 變更裝置設定
- 刪除或更新裝置等裝置作業

稽核資料的進入點是 [裝置]**** 頁面之 [活動]**** 區段中的 [稽核記錄]****。

稽核記錄具有預設清單視圖，該視圖顯示：

- 發生時間與日期
- 目標
- 活動的啟動者/執行者 (對象)
- 活動 (項目)

![稽核記錄](./media/device-management-azure-portal/63.png)

您可以通過按一下工具列中的**列**來自訂清單視圖。

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

[如何在 Azure AD 中管理陳舊設備](manage-stale-devices.md)
