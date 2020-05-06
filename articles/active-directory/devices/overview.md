---
title: 什麼是 Azure Active Directory 中的裝置身分識別？
description: 了解裝置身分識別管理如何協助您管理會存取環境中各項資源的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 03/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba0630474224c34eb96429cd7592028362e03381
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82024433"
---
# <a name="what-is-a-device-identity"></a>什麼是裝置身分識別？

隨著各種外型和大小的裝置和自備裝置 (BYOD) 觀念的快速發展，IT 專業人員面臨到兩個有點互相衝突的目標︰

- 讓使用者隨時隨地都具有生產力
- 保護組織的資產

為了保護這些資產，IT 人員必須先管理裝置身分識別。 IT 人員可以使用 Microsoft Intune 等工具來建置裝置身分識別，以確保能符合安全性與合規性的標準。 Azure Active Directory (Azure AD) 可讓使用者透過這些裝置從任何位置對裝置、應用程式和服務進行單一登入。

- 使用者可對您的組織存取他們所需的資產。 
- IT 人員保有必要的掌控權可保護您的組織。

裝置身分識別管理是[裝置型條件式存取](../conditional-access/require-managed-devices.md)的基礎。 透過裝置型條件式存取原則，您可以確保只有受控裝置可存取您環境中的資源。

## <a name="getting-devices-in-azure-ad"></a>在 Azure AD 中取得裝置

若要將裝置移入 Azure AD 中，您有數個選項：

- **已在 Azure AD 註冊**
   - 已在 Azure AD 註冊的裝置通常是個人擁有的裝置或行動裝置，且會以個人 Microsoft 帳戶或其他本機帳戶登入。
      - Windows 10
      - iOS
      - Android
      - MacOS
- **已加入 Azure AD**
   - 已加入 Azure AD 的裝置由組織所擁有，且會使用屬於該組織的 Azure AD 帳戶進行登入。 這些裝置僅存在於雲端中。
      - Windows 10 
      - Windows Server 2019 (不支援伺服器核心)
- **已加入混合式 Azure AD**
   - 已加入混合式 Azure AD 的裝置由組織所擁有，且會使用屬於該組織的 Azure AD 帳戶進行登入。 它們存在於雲端和內部部署中。
      - Windows 7、8.1 或 10
      - Windows Server 2008 或更新版本

![[Azure AD 裝置] 刀鋒視窗中顯示的裝置](./media/overview/azure-active-directory-devices-all-devices.png)

> [!NOTE]
> 混合式狀態所指的不只是裝置的狀態。 若要讓混合式狀態有效，也必須具備有效的 Azure AD 使用者。

## <a name="device-management"></a>裝置管理

Azure AD 中的裝置可使用 Microsoft Intune、Microsoft Endpoint Configuration Manager、群組原則 (混合式 Azure AD Join)、行動應用程式管理 (MAM) 工具等行動裝置管理 (MDM) 工具來管理，或以其他第三方工具管理。

## <a name="resource-access"></a>資源存取

註冊裝置並將其加入 Azure AD，可讓您的使用者無縫登入 (SSO) 雲端資源。 此程序也可讓系統管理員能夠根據其用於存取的裝置，將條件式存取原則套用到資源。 

> [!NOTE]
> 裝置型條件式存取原則需要已加入混合式 Azure AD 的裝置或符合規範且已加入 Azure AD 或已註冊 Azure AD 的裝置。

主要的重新整理權杖 (PRT) 包含裝置的相關資訊，並且是 SSO 的必要條件。 如果您已在應用程式上設定裝置型條件式存取原則，但沒有 PRT，則存取會遭到拒絕。 混合條件式存取原則需要混合式狀態裝置和已登入的有效使用者。

已加入 Azure AD 或混合式 Azure AD 的裝置，可以對您組織的內部部署資源和雲端資源進行 SSO。 如需詳細資訊，請參閱[內部部署資源的 SSO 如何在加入 Azure AD 的裝置上運作](azuread-join-sso.md)一文。

## <a name="device-security"></a>裝置安全性

- **Azure AD 註冊裝置**會使用由使用者管理的帳戶，而此帳戶不是 Microsoft 帳戶，就是其他由下列一或多項機制保護的本機管理認證。
   - 密碼
   - PIN 碼
   - 模式
   - Windows Hello
- **已加入 Azure AD 或混合式 Azure AD 的裝置**會使用 Azure AD 中受到下列一或多項機制保護的組織帳戶。
   - 密碼
   - Windows Hello 企業版

## <a name="provisioning"></a>佈建

將裝置放入 Azure AD 中的作業可用自助方式完成，或以系統管理員所控制的佈建程序完成。

## <a name="summary"></a>摘要

使用 Azure AD 中的裝置身分識別管理，您可以：

- 簡化將裝置導入 Azure AD 中進行管理的流程
- 為使用者提供對貴組織雲端式資源易於使用的存取方式

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure AD 裝置註冊](concept-azure-ad-register.md)
- 深入了解[已加入 Azure AD 的裝置](concept-azure-ad-join.md)
- 深入了解[已加入混合式 Azure AD 的裝置](concept-azure-ad-join-hybrid.md)
- 若要取得在 Azure 入口網站中管理裝置身分識別的概觀，請參閱[使用 Azure 入口網站管理裝置身分識別](device-management-azure-portal.md)。
- 若要深入了解裝置型條件式存取，請參閱[設定 Azure Active Directory 裝置型條件式存取原則](../conditional-access/require-managed-devices.md)。
