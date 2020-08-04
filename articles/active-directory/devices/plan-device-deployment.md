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
ms.openlocfilehash: 9e3b0b8ab4a432254835e43bff4893aaee81b04e
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541867"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>規劃您的 Azure Active Directory 裝置部署

本文可協助您評估將裝置與 Azure AD 整合的方法、選擇執行計畫，並提供支援的裝置管理工具的重要連結。

使用者登入的裝置的橫向擴充。 組織可能會提供桌上型電腦、膝上型電腦、手機、平板電腦和其他裝置。 您的使用者可能會攜帶自己的裝置陣列，並從不同的位置存取訊號。 在此環境中，身為系統管理員的工作是讓您的組織資源在所有裝置上保持安全。

Azure Active Directory （Azure AD）可讓您的組織透過裝置身分識別管理來達到這些目標。 您現在可以在 Azure AD 中取得您的裝置，並從[Azure 入口網站](https://portal.azure.com/)中的中央位置控制它們。 這可提供您一致的體驗、增強的安全性，並減少設定新裝置所需的時間。

有多種方法可以將您的裝置整合到 Azure AD：

* 您可以使用 Azure AD[註冊裝置](concept-azure-ad-register.md)

* 將[裝置加入](concept-azure-ad-join.md)Azure AD （僅限雲端）或

* 在內部部署 Active Directory 和 Azure AD 中的裝置之間[建立混合式 Azure AD 聯結](concept-azure-ad-join-hybrid.md)。 

## <a name="learn"></a>Learn

開始之前，請確定您已經熟悉[裝置身分識別管理的總覽](overview.md)。

### <a name="benefits"></a>優點

為您的裝置提供 Azure AD 身分識別的主要優點：

* 提高生產力–透過 Azure AD，您的使用者可以對您的內部部署和雲端資源執行順暢的登[入（SSO）](./azuread-join-sso.md) ，讓他們能夠隨時隨地保持生產力。

* 提高安全性– Azure AD 裝置可讓您根據裝置或使用者的身分識別，將[條件式存取（CA）原則](../conditional-access/require-managed-devices.md)套用至資源。 CA 原則可以使用[Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)提供額外的保護。 將裝置加入 Azure AD 是使用[無密碼 Authentication](../authentication/concept-authentication-passwordless.md)策略增加安全性的必要條件。

* 改善使用者體驗–在 Azure AD 中使用裝置身分識別，可讓使用者輕鬆地從個人和公司裝置存取您組織的雲端式資源。 系統管理員可以在所有 Windows 裝置上啟用[企業狀態漫遊](enterprise-state-roaming-overview.md)以取得一致的體驗。

* 簡化部署和管理–裝置身分識別管理可簡化將裝置帶入[Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)、[大量](https://docs.microsoft.com/mem/intune/enrollment/windows-bulk-enroll)布建和[自助：全新體驗（OOBE）](../user-help/user-help-join-device-on-network.md)Azure AD 的程式。 您可以使用行動裝置管理（MDM）工具（例如[Microsoft Intune](https://docs.microsoft.com/mem/intune/fundamentals/what-is-intune)）和其在[Azure 入口網站](https://portal.azure.com/)中的身分識別來管理這些裝置。

### <a name="training-resources"></a>訓練資源

影片：[使用裝置控制項的條件式存取](https://youtu.be/NcONUf-jeS4)

常見問題： [Azure AD 裝置管理常見問題](faq.md)和[設定和資料漫遊常見問題](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>規劃部署專案

在決定環境中此部署的策略時，請考慮您的組織需求。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，其通常是因為人員對影響、結果與責任抱持不相符的預期而造成。 若要避免這些錯誤，請[確定您參與的是正確的專案關係人](https://aka.ms/deploymentplans)，而且已充分瞭解專案中的專案關係人角色。 

針對此方案，請將下列專案關係人新增至您的清單：

| 角色| 描述 |
| - | - |
| 裝置系統管理員| 裝置團隊提供的代表，可驗證方案是否符合您組織的裝置需求。 |
| 網路管理員| 來自網路小組的代表，可確保符合網路需求。 |
| 裝置管理小組| 管理裝置清查的小組。 |
| OS 特定的系統管理小組| 支援和管理特定作業系統版本的小組。 例如，可能有以 Mac 或 iOS 為焦點的小組。 |

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="plan-a-pilot"></a>規劃試驗

我們建議您整合方法的初始設定是在測試環境中，或包含一小組測試裝置。 請參閱[試驗的最佳做法](../fundamentals/active-directory-deployment-plans.md) \(部分機器翻譯\)。

混合式 Azure AD 聯結部署非常簡單，而且在不需要使用者動作的情況下，它是100% 的系統管理員工作。 您可能想要對[混合式 Azure AD 聯結進行受控制的驗證](hybrid-azuread-join-control.md)，然後一次在整個組織中啟用它。

## <a name="choose-your-integration-methods"></a>選擇您的整合方法

您的組織可以在單一 Azure AD 租使用者中使用多個裝置整合方法。 目標是選擇適當的方法，讓您的裝置在 Azure AD 中安全地管理。 有許多參數會驅動此決策，包括擁有權、裝置類型、主要物件，以及您組織的基礎結構。

下列資訊可協助您決定要使用的整合方法。

### <a name="decision-tree-for-devices-integration"></a>裝置整合的決策樹

使用此樹狀結構來判斷組織擁有之裝置的選項。 

> [!NOTE]
> 此圖表中不會顯示個人或自備裝置（BYOD）案例。 它們一律會導致 Azure AD 註冊。

 ![決策樹](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>比較矩陣

iOS 和 Android 裝置僅可 Azure AD 註冊。 下表顯示 Windows 用戶端裝置的高階考慮。 使用它做為總覽，然後詳細探索不同的整合方法。

| 考量 | 已註冊 Azure AD| Azure AD Join| 混合式 Azure AD Join |
| - | - | - | - |
| **用戶端作業系統**| | |  |
| Windows 10 裝置| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| 舊版 windows 裝置（Windows 8.1 或 Windows 7）| | | ![核取](./media/plan-device-deployment/check.png) |
|**登入選項**| | |  |
| 終端使用者本機認證| ![核取](./media/plan-device-deployment/check.png)| |  |
| 密碼| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| 裝置 PIN| ![核取](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![核取](./media/plan-device-deployment/check.png)| |  |
| Windows Hello 企業版| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| FIDO 2.0 安全性金鑰| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 應用程式（無密碼）| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
|**主要功能**| | |  |
| SSO 到雲端資源| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| SSO 至內部部署資源| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| 條件式存取 <br> （需要將裝置標示為符合規範） <br> （必須由 MDM 管理）| ![核取](./media/plan-device-deployment/check.png) | ![核取](./media/plan-device-deployment/check.png)|![核取](./media/plan-device-deployment/check.png) |
條件式存取 <br>（需要混合式 Azure AD 聯結裝置）| | | ![核取](./media/plan-device-deployment/check.png)
| Windows 登入畫面的自助式密碼重設| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| Windows hello PIN 碼重設| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |
| 跨裝置的企業狀態漫遊| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 註冊 

已註冊的裝置通常會使用[Microsoft Intune](https://docs.microsoft.com/mem/intune/enrollment/device-enrollment)來管理。 裝置會以數種方式在 Intune 中註冊，視作業系統而定。 

Azure AD 已註冊的裝置可支援攜帶您自己的裝置（BYOD）和公司擁有的裝置，以 SSO 到雲端資源。 資源的存取是以套用至裝置和使用者的 Azure AD [CA 原則](../conditional-access/require-managed-devices.md)為基礎。

### <a name="registering-devices"></a>註冊裝置

已註冊的裝置通常會使用[Microsoft Intune](https://docs.microsoft.com/mem/intune/enrollment/device-enrollment)來管理。 裝置會以數種方式在 Intune 中註冊，視作業系統而定。 

BYOD 和公司擁有的行動裝置是由安裝公司入口網站應用程式的使用者所註冊。

* [iOS](https://docs.microsoft.com/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](https://docs.microsoft.com/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](https://docs.microsoft.com/mem/intune/user-help/enroll-windows-10-device)

如果註冊您的裝置是您組織的最佳選項，請參閱下列資源：

* 此[Azure AD 已註冊裝置](concept-azure-ad-register.md)的總覽。

* 此使用者檔說明如何在[組織的網路上註冊您的個人裝置](../user-help/user-help-register-device-on-network.md)。

## <a name="azure-ad-join"></a>Azure AD Join

Azure AD 聯結可讓您使用 Windows 轉換到雲端優先的模型。 如果您打算將裝置管理現代化，並降低與裝置相關的 IT 成本，它提供了絕佳的基礎。 Azure AD join 僅適用于 Windows 10 裝置。 請將它視為新裝置的第一個選項。

不過，Azure AD 聯結的裝置若位於組織的網路上，[就可以 SSO 到內部部署資源](azuread-join-sso.md)，可以向內部部署伺服器（例如檔案、列印和其他應用程式）進行驗證。

如果這是您組織的最佳選項，請參閱下列資源：

* 這是[Azure AD 聯結裝置](concept-azure-ad-join.md)的總覽。

* 熟悉[Azure AD Join 的執行計畫](azureadjoin-plan.md)。

### <a name="provisioning-azure-ad-join-to-your-devices"></a>布建 Azure AD 加入至您的裝置

若要布建 Azure AD 聯結，您有下列方法：

* 自助： [Windows 10 首次執行體驗](azuread-joined-devices-frx.md)

如果您在裝置上安裝了 Windows 10 專業版或 Windows 10 企業版，體驗則預設為公司所擁有裝置的安裝程序。

* [Windows 全新體驗（OOBE）或 Windows 設定](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)

* [大量註冊](https://docs.microsoft.com/mem/intune/enrollment/windows-bulk-enroll)

仔細[比較這些方法](azureadjoin-plan.md)之後，請選擇您的部署程式。

您可以判斷 Azure AD Join 是裝置的最佳解決方案，而該裝置可能已經處於不同的狀態。 以下是升級考慮。

| 目前的裝置狀態| 所需的裝置狀態| 操作方式 |
| - | - | - |
| 已加入內部部署網域| Azure AD Join| 在加入 Azure AD 之前，從內部部署網域退出裝置 |
| 混合式 Azure AD 加入| Azure AD Join| 在加入之前，從內部部署網域和 Azure AD 退出裝置 Azure AD |
| 已註冊 Azure AD| Azure AD Join| 請先取消註冊裝置，然後再加入 Azure AD |


## <a name="hybrid-azure-ad-join"></a>混合式 Azure AD Join

如果您有內部部署 Active Directory 環境，而且想要將已加入 Active Directory 網域的電腦加入 Azure AD，您可以使用混合式 Azure AD 聯結來完成此操作。 它支援[廣泛的 windows 裝置](hybrid-azuread-join-plan.md)，包括 windows 目前和舊版 windows 裝置。

大部分的組織都已加入網域的裝置，並透過群組原則或 System Center Configuration Manager （SCCM）加以管理。 在這種情況下，我們建議您設定混合式 Azure AD 聯結，以在運用現有投資的同時開始獲得效益。

如果混合式 Azure AD 聯結是您組織的最佳選項，請參閱下列資源：

* 這是[混合式 Azure AD 聯結裝置](concept-azure-ad-join-hybrid.md)的總覽。

* 熟悉[混合式 Azure AD join 的執行](hybrid-azuread-join-plan.md)計畫。

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>布建混合式 Azure AD 聯結至您的裝置

[檢查您的身分識別基礎結構](hybrid-azuread-join-plan.md)。 Azure AD Connect 會為您提供設定混合式 Azure AD 聯結的嚮導：

* [同盟網域](hybrid-azuread-join-federated-domains.md)

* [受控網域](hybrid-azuread-join-managed-domains.md)

如果安裝所需的 Azure AD Connect 版本不是您的選項，請參閱[如何手動設定混合式 Azure AD 聯結](hybrid-azuread-join-manual.md)。 

> [!NOTE] 
> 已加入網域的內部部署 Windows 10 裝置會嘗試自動加入 Azure AD，以預設為已加入混合式 Azure AD。 只有在您必備技術設定正確的環境時，才會成功。 

您可以判斷混合式 Azure AD Join 是裝置的最佳解決方案，而且該裝置可能已經處於不同的狀態。 以下是升級考慮。

| 目前的裝置狀態| 所需的裝置狀態| 操作方式 |
| - | - | - |
| 內部部署網域加入| 混合式 Azure AD 加入| 使用 Azure AD connect 或 AD FS 加入 Azure |
| 已加入內部部署工作組或新增| 混合式 Azure AD 加入| 支援[Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-autopilot)。 否則，裝置必須已加入內部部署網域，才能混合式 Azure AD Join |
| 已聯結的 Azure AD| 混合式 Azure AD 加入| 退出 Azure AD，將其置於內部部署工作組或新狀態。 |
| Azure AD 註冊| 混合式 Azure AD 加入| 視 Windows 版本而定。 [請參閱這些考慮](hybrid-azuread-join-plan.md)。 |

## <a name="manage-your-devices"></a>管理您的裝置

在您註冊或將裝置加入 Azure AD 之後，請使用[Azure 入口網站](https://portal.azure.com/)作為集中位置來管理您的裝置身分識別。 [Azure Active Directory 裝置] 頁面可讓您：

* [設定您的裝置設定](device-management-azure-portal.md#configure-device-settings)
* 您必須是本機系統管理員，才能管理 Windows 裝置。 [Azure AD 會為已加入 Azure AD 的裝置更新此成員資格](assign-local-admin.md)，並自動以 [裝置管理員] 角色將其新增為所有已加入裝置的系統管理員。

藉由[管理過時裝置](manage-stale-devices.md)來確保環境保持乾淨，並將您的資源集中在管理目前的裝置上。

* [查看裝置相關的審核記錄](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>支援的裝置管理工具

系統管理員可以使用其他裝置管理工具來保護及進一步控制這些已註冊和已加入的裝置。 這些工具可讓您強制執行組織所需的設定，例如要求儲存加密、密碼複雜性、軟體安裝和軟體更新。 

檢查整合裝置支援和不支援的平臺：

| 裝置管理工具| 已註冊 Azure AD| Azure AD Join| 混合式 Azure AD Join|
| - | - | - | - |
| [行動裝置管理（MDM）](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>範例： Microsoft Intune| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)|  |
| [Microsoft Intune 與 Microsoft 端點的共同管理 Configuration Manager](https://docs.microsoft.com/mem/configmgr/comanage/overview) <br>（Windows 10 和更新版本）| | ![核取](./media/plan-device-deployment/check.png)| ![核取](./media/plan-device-deployment/check.png)|  |
| [群組原則](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>（僅限 Windows）| | | ![核取](./media/plan-device-deployment/check.png)|  |



 我們建議您針對已註冊的 iOS 或 Android 裝置，考慮[Microsoft Intune 行動應用程式管理（MAM）（](https://docs.microsoft.com/mem/intune/apps/app-management)不論是否有裝置管理）。

 系統管理員也可以部署在其組織中裝載 Windows 作業系統的[虛擬桌面基礎結構（VDI）平臺](howto-device-identity-virtual-desktop-infrastructure.md)，藉由匯總和集中化資源來簡化管理並降低成本。 

### <a name="troubleshoot-device-identities"></a>裝置身分識別問題疑難排解

* [使用 dsregcmd.exe 命令針對裝置進行疑難排解](troubleshoot-device-dsregcmd.md)

* [針對 Azure Active Directory 中的企業狀態漫遊設定進行疑難排解](enterprise-state-roaming-troubleshooting.md)

如果您為已加入網域的 Windows 裝置完成混合式 Azure AD Join 時遇到問題，請參閱：

* [針對現行 Windows 裝置的混合式 Azure AD Join 進行疑難排解](troubleshoot-hybrid-join-windows-current.md)

* [針對 Windows 下層裝置的混合式 Azure AD 聯結進行疑難排解](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>後續步驟

* [規劃您的 Azure AD 聯結執行](azureadjoin-plan.md)
* [規劃您的混合式 Azure AD 聯結執行](hybrid-azuread-join-plan.md)
* [管理裝置身分識別](device-management-azure-portal.md)
