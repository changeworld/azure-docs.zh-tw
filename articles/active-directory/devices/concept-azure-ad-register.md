---
title: 什麼是 Azure AD 註冊的裝置？
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462755"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

Azure AD 已註冊裝置的目標，是要為您的使用者提供「攜帶您自己的裝置（BYOD）」或「行動裝置」案例的支援。 在這些案例中，使用者可以使用個人裝置存取您組織的 Azure Active Directory 控制資源。

|   | 已註冊 Azure AD |
| --- | --- |
| **定義** | 已向 Azure AD 註冊，而不需要組織帳戶來登入裝置 |
| **主要物件** | 適用于具有下列準則的所有使用者： |
|   | 攜帶您自己的裝置 (BYOD) |
|   | 行動裝置 |
| **裝置擁有權** | 使用者或組織 |
| **作業系統** | Windows 10、iOS、Android 和 MacOS |
| **佈建** | Windows 10 –設定 |
|   | iOS/Android –公司入口網站或 Microsoft Authenticator 應用程式 |
|   | MacOS –公司入口網站 |
| **裝置登入選項** | 終端使用者本機認證 |
|   | 密碼 |
|   | Windows Hello |
|   | PIN 碼 |
|   | 其他裝置的生物識別或模式 |
| **裝置管理** | 行動裝置管理（範例： Microsoft Intune） |
|   | 行動應用程式管理 |
| **主要功能** | SSO 到雲端資源 |
|   | 註冊至 Intune 時的條件式存取 |
|   | 透過應用程式保護原則的條件式存取 |
|   | 啟用 Microsoft Authenticator 應用程式的手機登入 |

![Azure AD 註冊裝置](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 已註冊的裝置會使用本機帳戶登入，例如 Windows 10 裝置上的 Microsoft 帳戶，但此外還會附加 Azure AD 帳戶以存取組織資源。 根據套用至裝置身分識別的 Azure AD 帳戶和條件式存取原則，可以進一步限制組織中資源的存取權。

系統管理員可以使用 Microsoft Intune 等行動裝置管理（MDM）工具來保護及進一步控制這些 Azure AD 註冊的裝置。 MDM 提供一種方式來強制執行組織所需的設定，例如要求存放裝置加密、密碼複雜性和安全性軟體保持更新。 

當第一次存取工作應用程式或手動使用 Windows 10 [設定] 功能表時，可以完成 Azure AD 註冊。 

## <a name="scenarios"></a>案例

您組織中的使用者想要存取電子郵件的工具、報告時間，以及從其家用電腦註冊的權益。 您的組織在條件式存取原則背後有這些工具，需要從符合 Intune 規範的裝置進行存取。 使用者會新增其組織帳戶，並向 Azure AD 註冊其家用電腦，並強制執行必要的 Intune 原則，讓使用者能夠存取其資源。

另一位使用者想要在其個人 Android 手機上存取已根目錄的組織電子郵件。 您的公司需要符合規範的裝置，並已建立 Intune 合規性原則來封鎖任何根裝置。 員工已停止，無法存取此裝置上的組織資源。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站管理裝置身分識別](device-management-azure-portal.md)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
