---
title: 什麼是 Azure AD 註冊設備？
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462755"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

Azure AD 註冊設備的目標是為使用者提供對自帶設備 （BYOD） 或行動裝置方案的支援。 在這些情況下，使用者可以使用個人設備訪問組織的 Azure 活動目錄控制資源。

|   | Azure AD 已註冊 |
| --- | --- |
| **定義** | 註冊到 Azure AD，無需組織帳戶登錄設備 |
| **主要受眾** | 適用于所有具有以下條件的使用者： |
|   | 攜帶您自己的裝置 (BYOD) |
|   | 行動裝置 |
| **裝置擁有權** | 使用者或組織 |
| **作業系統** | Windows 10、iOS、安卓和 MacOS |
| **佈建** | Windows 10 = 設置 |
|   | iOS/安卓 – 公司門戶或微軟身份驗證器應用程式 |
|   | MacOS » 公司門戶 |
| **設備登錄選項** | 最終使用者本地憑據 |
|   | 密碼 |
|   | Windows Hello |
|   | PIN 碼 |
|   | 其他設備的生物識別或模式 |
| **裝置管理** | 行動裝置管理（例如：微軟Intune） |
|   | 行動應用程式管理 |
| **主要功能** | SSO 到雲資源 |
|   | 註冊到 Intune 時的條件訪問 |
|   | 通過應用保護原則進行條件訪問 |
|   | 使用 Microsoft 身份驗證器應用啟用電話登錄 |

![Azure AD 註冊裝置](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 註冊設備已登錄到使用本地帳戶（如 Windows 10 設備上的 Microsoft 帳戶），但另外還附加了 Azure AD 帳戶以訪問組織資源。 根據應用於設備標識的 Azure AD 帳戶和條件訪問策略，可以進一步限制對組織中資源的訪問。

管理員可以使用 Microsoft Intune 等行動裝置管理 （MDM） 工具保護和進一步控制這些 Azure AD 註冊設備。 MDM 提供了一種強制實施組織所需的配置的方法，例如要求對存儲進行加密、密碼複雜性以及安全軟體不斷更新。 

首次訪問工作應用程式或使用 Windows 10 設置功能表手動時，可以完成 Azure AD 註冊。 

## <a name="scenarios"></a>案例

組織中的使用者希望從其主電腦訪問用於電子郵件、報告休假和福利註冊的工具。 您的組織在條件訪問策略後面擁有這些工具，該策略需要從符合 Intune 的設備進行訪問。 使用者添加其組織帳戶，並將其主 PC 註冊到 Azure AD，並強制執行所需的 Intune 策略，以允許使用者訪問其資源。

另一個使用者想要訪問他們的組織電子郵件在他們的個人Android手機已經紮根。 您的公司需要符合要求的設備，並創建了 Intune 合規性策略來阻止任何根設備。 員工將停止訪問此設備上的組織資源。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 門戶管理設備標識](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
