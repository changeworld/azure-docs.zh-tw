---
title: 什麼是 Azure AD 已註冊的裝置？
description: 瞭解 Azure AD 已註冊的裝置如何為您的使用者提供攜帶您自己的裝置 (BYOD) 或行動裝置案例的支援。
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
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256433"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 註冊裝置

Azure AD 已註冊裝置的目標是要為您的使用者提供自備裝置 (BYOD) 或行動裝置案例的支援。 在這些案例中，使用者可以使用個人裝置來存取您組織 Azure Active Directory 控制的資源。

| Azure AD 已註冊 | 說明 |
| --- | --- |
| **[定義]** | 註冊至 Azure AD，而不需要組織帳戶來登入裝置 |
| **主要對象** | 適用于具有下列準則的所有使用者： |
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
|   | 其他裝置的生物特徵辨識或模式 |
| **裝置管理** | 行動裝置管理 (例如：Microsoft Intune) |
|   | 行動應用程式管理 |
| **主要功能** | SSO 至雲端資源 |
|   | 註冊至 Intune 時的條件式存取 |
|   | 透過應用程式保護原則的條件式存取 |
|   | 使用 Microsoft Authenticator 應用程式啟用手機登入 |

![Azure AD 註冊裝置](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 已註冊的裝置會使用本機帳戶（例如 Windows 10 裝置上的 Microsoft 帳戶）登入，但還會附加 Azure AD 帳戶以存取組織資源。 根據 Azure AD 帳戶和套用至裝置身分識別的條件式存取原則，您可以進一步限制組織中資源的存取權。

系統管理員可以使用行動裝置管理 (MDM) 工具（例如 Microsoft Intune）來保護及進一步控制這些 Azure AD 註冊的裝置。 MDM 提供一種方法來強制執行組織所需的設定，例如要求儲存加密、密碼複雜性和安全性軟體保持更新。 

當您第一次存取工作應用程式，或使用 [Windows 10 設定] 功能表手動存取 Azure AD 註冊時，可以完成這項作業。 

## <a name="scenarios"></a>案例

您組織中的使用者想要存取電子郵件的工具、報告時間，以及從家用電腦註冊的權益。 您的組織在條件式存取原則背後有這些工具，需要從符合 Intune 規範的裝置進行存取。 使用者新增其組織帳戶並向 Azure AD 註冊家用電腦，並強制執行必要的 Intune 原則，讓使用者能夠存取其資源。

另一位使用者想要在他們的個人 Android 手機上存取其組織電子郵件，其為根目錄。 您的公司需要符合規範的裝置，並已建立 Intune 合規性原則來封鎖任何 root 破解的裝置。 員工已停止存取此裝置上的組織資源。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 入口網站管理裝置身分識別](device-management-azure-portal.md) (機器翻譯)
- [管理 Azure AD 中的過時裝置](manage-stale-devices.md)
