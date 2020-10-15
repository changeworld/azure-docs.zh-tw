---
title: 規劃您的 Azure Active Directory 裝置部署
description: 選擇符合您組織需求的 Azure AD 裝置整合策略。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf30570f10f176c47aed0f99127e03a027eff775
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093084"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>規劃您的 Azure Active Directory 裝置部署

本文可協助您評估將裝置與 Azure AD 整合的方法、選擇實行方案，並提供支援的裝置管理工具的主要連結。

使用者登入的裝置橫向擴充。 組織可以提供桌上型電腦、膝上型電腦、手機、平板電腦和其他裝置。 您的使用者可能會攜帶自己的裝置陣列，並存取來自不同位置的資訊。 在此環境中，您的工作是系統管理員，可讓您的組織資源在所有裝置上保持安全。

Azure Active Directory (Azure AD) 讓您的組織能以裝置身分識別管理來滿足這些目標。 您現在可以在 Azure AD 中取得裝置，並從 [Azure 入口網站](https://portal.azure.com/)集中的位置加以控制。 這可提供您一致的體驗、增強的安全性，並減少設定新裝置所需的時間。

有多種方法可將您的裝置整合至 Azure AD：

* 您可以 [向](concept-azure-ad-register.md) Azure AD 登錄裝置

* 將[裝置加入](concept-azure-ad-join.md)Azure AD (僅限雲端) 或

* 在內部部署 Active Directory 和 Azure AD 的裝置之間[建立混合式 Azure AD 聯結](concept-azure-ad-join-hybrid.md)。 

## <a name="learn"></a>學習

開始之前，請確定您已熟悉 [裝置身分識別管理的總覽](overview.md)。

### <a name="benefits"></a>優點

為您的裝置提供 Azure AD 身分識別的主要優點：

* 提高生產力–透過 Azure AD，您的使用者可以對內部部署和雲端資源進行 [無縫登入 (SSO) ](./azuread-join-sso.md) ，讓他們在任何地方都能提高生產力。

* 提高安全性– Azure AD 裝置可讓您根據裝置或使用者的身分識別，將 [條件式存取 (CA) 原則](../conditional-access/require-managed-devices.md) 套用至資源。 CA 原則可以使用 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)來提供額外的保護。 將裝置加入 Azure AD 是使用 [無密碼 Authentication](../authentication/concept-authentication-passwordless.md) 策略提高安全性的先決條件。

* 使用 Azure AD 中的裝置身分識別改善使用者體驗，您可以讓使用者從個人和公司裝置輕鬆存取組織的雲端式資源。 系統管理員可以在所有 Windows 裝置上啟用統一體驗的 [企業狀態漫遊](enterprise-state-roaming-overview.md) 。

* 簡化部署和管理–裝置身分識別管理可簡化將裝置帶入 Azure AD 的程式 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)、 [大量](/mem/intune/enrollment/windows-bulk-enroll)布建和 [自助服務：全新體驗 (OOBE) ](../user-help/user-help-join-device-on-network.md)。 您可以使用行動裝置管理 (MDM) 工具（例如 [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)）以及其在 [Azure 入口網站](https://portal.azure.com/)中的身分識別來管理這些裝置。

### <a name="training-resources"></a>訓練資源

影片：  [使用裝置控制項的條件式存取](https://youtu.be/NcONUf-jeS4)

常見問題： [Azure AD 裝置管理常見問題](faq.md)  和 [設定和資料漫遊常見問題](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>規劃部署專案

在決定環境中此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，其通常是因為人員對影響、結果與責任抱持不相符的預期而造成。 若要避免這些問題，請 [確定您已吸引適當的專案關係人](../fundamentals/active-directory-deployment-plans.md) ，並且能瞭解專案中的專案關係人角色。 

針對此方案，請將下列專案關係人加入您的清單：

| 角色| 描述 |
| - | - |
| 裝置系統管理員| 裝置團隊的代表，可驗證方案是否符合您組織的裝置需求。 |
| 網路管理員| 網路小組的代表，可確保符合網路需求。 |
| 裝置管理小組| 管理裝置清查的團隊。 |
| 作業系統特定的系統管理小組| 支援和管理特定 OS 版本的團隊。 例如，可能有以 Mac 或 iOS 為焦點的團隊。 |

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

建議您在測試環境中，或使用一小組測試裝置來初始設定您的整合方法。 請參閱[試驗的最佳做法](../fundamentals/active-directory-deployment-plans.md) \(部分機器翻譯\)。

混合式 Azure AD 聯結部署相當簡單，而且在不需要使用者動作的情況下，它是系統管理員工作的100%。 您可能會想要對 [混合式 Azure AD 聯結進行受控制的驗證](hybrid-azuread-join-control.md) ，然後一次在整個組織內啟用。

## <a name="choose-your-integration-methods"></a>選擇您的整合方法

您的組織可以在單一 Azure AD 租使用者中使用多個裝置整合方法。 目標是選擇) 適用的 (方法，以便在 Azure AD 中安全地管理您的裝置。 有許多參數會推動這類決策，包括擁有權、裝置類型、主要物件和您組織的基礎結構。

下列資訊可協助您決定要使用的整合方法。

### <a name="decision-tree-for-devices-integration"></a>裝置整合的決策樹

使用此樹狀結構來判斷組織所擁有裝置的選項。 

> [!NOTE]
> 本圖中未顯示個人或攜帶您自己的裝置 (BYOD) 案例。 它們一律會導致 Azure AD 註冊。

 ![決策樹](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>比較矩陣

iOS 和 Android 裝置可能只會 Azure AD 註冊。 下表提供 Windows 用戶端裝置的高階考慮。 請使用它作為總覽，然後詳細探索不同的整合方法。

| 考量 | 已註冊的 Azure AD| Azure AD Join| 混合式 Azure AD Join |
| - | - | - | - |
| **用戶端作業系統**| | |  |
| Windows 10 裝置| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| 舊版 windows 裝置 (Windows 8.1 或 Windows 7) | | | ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
|**登入選項**| | |  |
| 終端使用者本機認證| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| |  |
| 密碼| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| 裝置 PIN| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello 企業版| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| FIDO 2.0 安全性金鑰| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 應用程式 (無密碼) | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
|**主要功能**| | |  |
| SSO 至雲端資源| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| SSO 至內部部署資源| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| 條件式存取 <br>  (需要將裝置標示為符合規範)  <br>  (必須由 MDM 管理) | ![這些值的核取記號。](./media/plan-device-deployment/check.png) | ![這些值的核取記號。](./media/plan-device-deployment/check.png)|![這些值的核取記號。](./media/plan-device-deployment/check.png) |
條件式存取 <br> (需要已加入混合式 Azure AD 的裝置) | | | ![這些值的核取記號。](./media/plan-device-deployment/check.png)
| 從 windows 登入畫面進行自助式密碼重設| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| Windows hello PIN 重設| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |
| 跨裝置的企業狀態漫遊| | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 註冊 

註冊的裝置通常會使用 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)來管理。 裝置在 Intune 中的註冊方式有許多種，視作業系統而定。 

Azure AD 已註冊的裝置可支援將您自己的裝置 (BYOD) 和公司擁有的裝置，以 SSO 至雲端資源。 資源的存取取決於套用至裝置和使用者的 Azure AD [CA 原則](../conditional-access/require-managed-devices.md) 。

### <a name="registering-devices"></a>註冊裝置

註冊的裝置通常會使用 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)來管理。 裝置在 Intune 中的註冊方式有許多種，視作業系統而定。 

安裝公司入口網站應用程式的使用者會註冊 BYOD 和公司擁有的行動裝置。

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

如果註冊您的裝置是您組織的最佳選項，請參閱下列資源：

* 此 [Azure AD 已註冊裝置](concept-azure-ad-register.md)的總覽。

* 此使用者檔說明如何在 [組織的網路上註冊您的個人裝置](../user-help/user-help-register-device-on-network.md)。

## <a name="azure-ad-join"></a>Azure AD Join

Azure AD join 可讓您使用 Windows 轉換至雲端優先模型。 如果您打算將裝置管理現代化，並減少裝置相關的 IT 成本，則可提供絕佳的基礎。 Azure AD join 只適用于 Windows 10 裝置。 將它視為新裝置的第一個選擇。

不過， [Azure AD 加入的裝置可](azuread-join-sso.md) 在組織網路上的內部部署資源進行 SSO，可以驗證內部部署伺服器，例如檔案、列印和其他應用程式。

如果這是您組織的最佳選項，請參閱下列資源：

* 此 Azure AD 已 [加入裝置](concept-azure-ad-join.md)的總覽。

* 熟悉 [Azure AD 聯結的執行計畫](azureadjoin-plan.md)。

### <a name="provisioning-azure-ad-join-to-your-devices"></a>布建 Azure AD 加入您的裝置

若要布建 Azure AD 聯結，您有下列方法：

* 自助： [Windows 10 首次執行體驗](azuread-joined-devices-frx.md)

如果您在裝置上安裝了 Windows 10 專業版或 Windows 10 企業版，體驗則預設為公司所擁有裝置的安裝程序。

* [Windows 全新體驗 (OOBE) 或從 Windows 設定](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [大量註冊](/mem/intune/enrollment/windows-bulk-enroll)

請在仔細 [比較這些方法](azureadjoin-plan.md)之後，選擇您的部署程式。

您可以判斷 Azure AD 聯結是裝置的最佳解決方案，且該裝置可能已處於不同的狀態。 以下是升級的考慮。

| 目前的裝置狀態| 所需的裝置狀態| 操作方式 |
| - | - | - |
| 已加入內部部署網域| Azure AD Join| 將裝置從內部部署網域退出，再加入 Azure AD |
| 混合式 Azure AD 加入| Azure AD Join| 將裝置從內部部署網域和 Azure AD 退出，再加入 Azure AD |
| 已註冊的 Azure AD| Azure AD Join| 將裝置取消註冊後再加入 Azure AD |


## <a name="hybrid-azure-ad-join"></a>混合式 Azure AD Join

如果您有內部部署 Active Directory 環境，而且想要將已加入 Active Directory 網域的電腦加入 Azure AD，您可以使用混合式 Azure AD 聯結來完成此操作。 它支援 [廣泛的 windows 裝置](hybrid-azuread-join-plan.md)，包括 windows 目前和舊版 windows 裝置。

大部分的組織已經有加入網域的裝置，並透過群組原則或 System Center Configuration Manager (SCCM) 來管理它們。 在此情況下，我們建議您設定混合式 Azure AD 聯結，以在利用現有投資的同時開始獲得優勢。

如果混合式 Azure AD 聯結是您組織的最佳選項，請參閱下列資源：

* 此混合式 Azure AD 已 [加入裝置](concept-azure-ad-join-hybrid.md)的總覽。

* 熟悉 [混合式 Azure AD 聯結的執行](hybrid-azuread-join-plan.md) 計畫。

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>將混合式 Azure AD 聯結布建到您的裝置

[檢查您的身分識別基礎結構](hybrid-azuread-join-plan.md)。 Azure AD Connect 提供您設定混合式 Azure AD 聯結的 wizard：

* [同盟網域](hybrid-azuread-join-federated-domains.md)

* [受控網域](hybrid-azuread-join-managed-domains.md)

如果您無法選擇安裝所需的 Azure AD Connect 版本，請參閱 [如何手動設定混合式 Azure AD 聯結](hybrid-azuread-join-manual.md)。 

> [!NOTE] 
> 已加入內部部署網域 Windows 10 裝置預設會嘗試自動加入 Azure AD，使其成為預設混合式 Azure AD 聯結。 只有當您必備技術設定正確的環境時，才會成功。 

您可以判斷混合式 Azure AD Join 是裝置的最佳解決方案，且該裝置可能已處於不同的狀態。 以下是升級的考慮。

| 目前的裝置狀態| 所需的裝置狀態| 操作方式 |
| - | - | - |
| 內部部署網域加入| 混合式 Azure AD 加入| 使用 Azure AD connect 或 AD FS 加入 Azure |
| 已加入或新增內部部署工作組| 混合式 Azure AD 加入| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)支援。 否則，裝置必須先加入內部部署網域，才能混合式 Azure AD Join |
| 已聯結的 Azure AD| 混合式 Azure AD 加入| 從 Azure AD 退出，以將其放在內部部署工作組或新的狀態。 |
| Azure AD 者| 混合式 Azure AD 加入| 相依于 Windows 版本。 [請參閱這些考慮](hybrid-azuread-join-plan.md)。 |

## <a name="manage-your-devices"></a>管理您的裝置

一旦您已將裝置註冊或加入 Azure AD，請使用 [Azure 入口網站](https://portal.azure.com/) 作為管理裝置身分識別的集中位置。 [Azure Active Directory 裝置] 頁面可讓您：

* [設定您的裝置設定](device-management-azure-portal.md#configure-device-settings)
* 您必須是本機系統管理員，才能管理 Windows 裝置。 [Azure AD 更新 Azure AD 已加入裝置的成員資格](assign-local-admin.md)，並自動以「裝置管理員」角色將這些成員新增至所有已加入裝置的系統管理員。

藉由 [管理過時的裝置](manage-stale-devices.md)，並將您的資源集中在管理目前的裝置上，確定您可以保持環境乾淨。

* [查看裝置相關的審核記錄](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>支援的裝置管理工具

系統管理員可以使用其他裝置管理工具來保護及進一步控制這些已註冊和已加入的裝置。 這些工具可讓您強制執行組織所需的設定，例如要求儲存加密、密碼複雜性、軟體安裝和軟體更新。 

針對整合式裝置審核支援和不支援的平臺：

| 裝置管理工具| 已註冊的 Azure AD| Azure AD Join| 混合式 Azure AD Join|
| - | - | - | - |
| [ (MDM) 的行動裝置管理 ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>範例： Microsoft Intune| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)|  |
| [使用 Microsoft Intune 和 Microsoft Endpoint Configuration Manager 共同管理](/mem/configmgr/comanage/overview) <br> (Windows 10 和更新版本) | | ![這些值的核取記號。](./media/plan-device-deployment/check.png)| ![這些值的核取記號。](./media/plan-device-deployment/check.png)|  |
| [群組原則](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>僅 (Windows) | | | ![這些值的核取記號。](./media/plan-device-deployment/check.png)|  |



 建議您 Microsoft Intune 將行動 [應用程式管理 (MAM) ](/mem/intune/apps/app-management) （不論是否有裝置管理）用於已註冊的 IOS 或 Android 裝置。

 系統管理員也可以將 [虛擬桌面基礎結構部署 (VDI) ](howto-device-identity-virtual-desktop-infrastructure.md) 在其組織中裝載 Windows 作業系統的平臺，以透過合併和集中資源來簡化管理工作並降低成本。 

### <a name="troubleshoot-device-identities"></a>裝置身分識別問題疑難排解

* [使用 dsregcmd.exe 命令針對裝置進行疑難排解](troubleshoot-device-dsregcmd.md)

* [針對 Azure Active Directory 中的企業狀態漫遊設定進行疑難排解](enterprise-state-roaming-troubleshooting.md)

如果您為已加入網域的 Windows 裝置完成混合式 Azure AD Join 時遇到問題，請參閱：

* [針對現行 Windows 裝置的混合式 Azure AD Join 進行疑難排解](troubleshoot-hybrid-join-windows-current.md)

* [針對 Windows 下層裝置的混合式 Azure AD 聯結進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>後續步驟

* [規劃您的 Azure AD 聯結執行](azureadjoin-plan.md)
* [規劃混合式 Azure AD 聯結的實施](hybrid-azuread-join-plan.md)
* [管理裝置身分識別](device-management-azure-portal.md)