---
title: 什麼是 Azure AD 聯接設備？
description: 了解裝置身分識別管理如何協助您管理存取您環境中資源的裝置。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672673"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 加入裝置

Azure AD 聯接適用于希望成為雲優先或僅雲的組織。 任何組織都可以部署 Azure AD 聯接的設備，無論大小或行業如何。 Azure AD 聯接即使在混合環境中也能正常工作，從而允許訪問雲和本地應用和資源。

|   | Azure AD Join |
| --- | --- |
| **定義** | 僅加入 Azure AD，要求組織帳戶登錄到設備 |
| **主要受眾** | 適用于僅雲和混合組織。 |
|   | 適用于組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | 所有 Windows 10 裝置 |
| **佈建** | 自助服務：Windows OOBE 或設置 |
|   | 大量註冊 |
|   | Windows Autopilot |
| **設備登錄選項** | 使用： |
|   | 密碼 |
|   | Windows Hello 企業版 |
|   | FIDO2.0 安全金鑰（預覽版） |
| **裝置管理** | 行動裝置管理（例如：微軟Intune） |
|   | 與微軟 Intune 和微軟端點組態管理員共同管理 |
| **主要功能** | SSO 到雲和本地資源 |
|   | 通過 MDM 註冊和 MDM 合規性評估進行條件訪問 |
|   | 在鎖定螢幕上的自助服務密碼重設和 Windows Hello PIN 重置 |
|   | 跨設備遍漫遊的企業狀態 |

Azure AD 聯接的設備已登錄到使用組織 Azure AD 帳戶。 根據應用於設備標識的 Azure AD 帳戶和[條件訪問策略](../conditional-access/overview.md)，可以進一步限制對組織中資源的訪問。

管理員可以使用行動裝置管理 （MDM） 工具（如 Microsoft Intune）或使用 Microsoft 端點組態管理員在共同管理方案中保護和進一步控制 Azure AD 聯接的設備。 這些工具提供了一種實施組織所需的配置的方法，例如要求對存儲進行加密、密碼複雜性、軟體安裝和軟體更新。 管理員可以使用組態管理員將組織應用程式提供給 Azure AD 聯接的設備，以[管理來自 Microsoft 應用商店的商業和教育應用](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)。

Azure AD 聯接可以使用自助服務選項（如開箱即用體驗 （OOBE）、批量註冊或[Windows 自動駕駛儀等完成](/intune/enrollment-autopilot)。

Azure AD 加入的設備在組織網路上時仍可以維護對本地資源的單一登入存取權限。 Azure AD 聯接的設備仍然可以對本機伺服器（如檔、列印和其他應用程式）進行身份驗證。

## <a name="scenarios"></a>案例

雖然 Azure AD Join 主要適用於沒有內部部署 Windows Server Active Directory 基礎結構的組織，但您也可以在下列情況下使用它：

- 您想要使用 Azure AD 和 MDM (例如 Intune) 來轉換成雲端式基礎結構。
- 例如，如果您需要取得行動裝置 (例如控制下的平板電腦和電話)，您無法使用內部部署網域加入。
- 您的使用者主要需要存取 Office 365 或與 Azure AD 整合的其他 SaaS 應用程序。
- 您想要管理 Azure AD 中的使用者群組，而不是 Active Directory 中的使用者群組。 例如，此方案可以適用于季節性工人、承包商或學生。
- 您要為內部部署基礎結構受到限制的遠端分公司工作者提供加入功能。

您可以設定適用於 Windows 10 裝置的 Azure AD 已加入裝置。

Azure AD 加入裝置的目標是簡化：

- Windows 部署工作用的裝置
- 從任何 Windows 裝置存取組織應用程式與資源
- 工作用裝置的雲端式管理
- 使用者使用 Azure AD 或同步的活動目錄工作或學校帳戶登錄到其設備。

![Azure AD 加入裝置](./media/concept-azure-ad-join/azure-ad-joined-device.png)

您可以使用下列任何一種方法來部署 Azure AD Join：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [大量部署](/intune/windows-bulk-enroll)
- [自助體驗](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>後續步驟

- [規劃 Azure AD 聯結實作](azureadjoin-plan.md)
- [如何管理已加入 Azure AD 的裝置上的本機系統管理員群組](assign-local-admin.md)
- [使用 Azure 門戶管理設備標識](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
