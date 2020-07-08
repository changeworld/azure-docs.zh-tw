---
title: 什麼是混合式 Azure AD 加入的裝置？
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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85554918"
---
# <a name="hybrid-azure-ad-joined-devices"></a>混合式 Azure AD 已加入裝置

十多年來，許多組織已對他們的內部部署 Active Directory 使用網域加入以讓：

- IT 部門可從中央位置管理工作用的裝置。
- 使用者可使用使用其 Active Directory 公司或學校帳戶登入他們的裝置。

一般而言，具有內部部署使用量的組織會依賴映射處理方法來布建裝置，而且通常會使用**Configuration Manager**或**群組原則（GP）** 來管理它們。

如果您的環境具有內部部署 AD 使用量，而且您也想要從 Azure Active Directory 所提供的功能受益，您可以實作混合式 Azure AD 已加入裝置。 這些裝置是聯結至內部部署 Active Directory 並向您的 Azure Active Directory 註冊的裝置。

| 混合式 Azure AD 加入 | Description |
| --- | --- |
| **定義** | 已加入內部部署 AD 和 Azure AD 需要組織帳戶才能登入裝置 |
| **主要對象** | 適用于具有現有內部部署 AD 基礎結構的混合式組織 |
|   | 適用於組織中的所有使用者 |
| **裝置擁有權** | 組織 |
| **作業系統** | Windows 10、8.1 和7 |
|   | Windows Server 2008/R2、2012/R2、2016和2019 |
| **佈建** | Windows 10、Windows Server 2016/2019 |
|   | 由 IT 加入網域，並透過 Azure AD Connect 或 ADFS config 進行自動連接 |
|   | 透過 Windows Autopilot 加入網域，並透過 Azure AD Connect 或 ADFS config 進行自動連接 |
|   | Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2-需要 MSI |
| **裝置登入選項** | 使用下列項目的組織帳戶： |
|   | 密碼 |
|   | 適用于 Win10 的 Windows Hello 企業版 |
| **裝置管理** | 群組原則 |
|   | 使用 Microsoft Intune Configuration Manager 獨立或共同管理 |
| **主要功能** | 雲端和內部部署資源的 SSO |
|   | 透過網域加入或透過 Intune 的條件式存取（如果共同管理） |
|   | 在鎖定畫面上進行自助式密碼重設和 Windows Hello PIN 重設 |
|   | 跨裝置進行企業狀態漫遊 |

![混合式 Azure AD 已加入裝置](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>案例

如果是下列情況，請使用 Azure AD 混合式聯結裝置：

- 您已將 Win32 應用程式部署至這些倚賴 Active Directory 電腦驗證的裝置。
- 您想要繼續使用群組原則來管理裝置設定。
- 您想要繼續使用現有的映射解決方案來部署和設定裝置。
- 除了 Windows 10 以外，您還必須支援舊版 Windows 7 和8.1 裝置

## <a name="next-steps"></a>後續步驟

- [規劃混合式 Azure AD Join 實作](hybrid-azuread-join-plan.md)
- [使用 Azure 入口網站管理裝置身分識別](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
