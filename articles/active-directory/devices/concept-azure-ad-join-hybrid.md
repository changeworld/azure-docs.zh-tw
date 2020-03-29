---
title: 什麼是混合 Azure AD 聯接設備？
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512244"
---
# <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

十多年來，許多組織已對他們的內部部署 Active Directory 使用網域加入以讓：

- IT 部門可從中央位置管理工作用的裝置。
- 使用者可使用使用其 Active Directory 公司或學校帳戶登入他們的裝置。

通常，具有本地佔用空間的組織依賴映射方法來預配設備，並且他們經常使用**組態管理員**或**群組原則 （GP）** 來管理設備。

如果您的環境具有內部部署 AD 使用量，而且您也想要從 Azure Active Directory 所提供的功能受益，您可以實作混合式 Azure AD 已加入裝置。 這些設備是已加入本地活動目錄並在 Azure 活動目錄中註冊的設備。

|   | 混合式 Azure AD 加入 |
| --- | --- |
| **定義** | 已加入本地 AD 和 Azure AD，要求組織帳戶登錄到設備 |
| **主要受眾** | 適用于具有現有本地 AD 基礎設施的混合組織 |
|   | 適用于組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | 視窗 10、8.1 和 7 |
|   | Windows 伺服器 2008/R2， 2012/R2， 2016 和 2019 |
| **佈建** | 視窗 10， 視窗伺服器 2016/2019 |
|   | IT 加入域並通過 Azure AD 連接或 ADFS 配置自動聯接 |
|   | 通過 Windows 自動駕駛儀加入域並通過 Azure AD 連接或 ADFS 配置自動聯接 |
|   | Windows 8.1、Windows 7、Windows 伺服器 2012 R2、Windows 伺服器 2012 和 Windows 伺服器 2008 R2 - 需要 MSI |
| **設備登錄選項** | 使用： |
|   | 密碼 |
|   | 適用于 Win10 的業務視窗你好 |
| **裝置管理** | 群組原則 |
|   | 組態管理員獨立或與微軟 Intune 共同管理 |
| **主要功能** | SSO 到雲和本地資源 |
|   | 通過域聯接或通過 Intune 進行條件訪問（如果共同管理） |
|   | 在鎖定螢幕上的自助服務密碼重設和 Windows Hello PIN 重置 |
|   | 跨設備遍漫遊的企業狀態 |

![混合式 Azure AD 已加入裝置](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>案例

在：

- 您已將 Win32 應用程式部署至這些倚賴 Active Directory 電腦驗證的裝置。
- 您希望繼續使用群組原則來管理設備配置。
- 您希望繼續使用現有映射解決方案來部署和配置設備。
- 除了 Windows 10 之外，還必須支援低級 Windows 7 和 8.1 設備

## <a name="next-steps"></a>後續步驟

- [規劃混合式 Azure AD 聯結實作](hybrid-azuread-join-plan.md)
- [使用 Azure 門戶管理設備標識](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
