---
title: 裝置身分識別與桌面虛擬化-Azure Active Directory
description: 瞭解如何搭配使用 VDI 和 Azure AD 裝置身分識別
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a43538ec7fa17764e8d9d53aad305480bd3ab4f5
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419796"
---
# <a name="device-identity-and-desktop-virtualization"></a>裝置身分識別與桌面虛擬化

系統管理員通常會部署在其組織中裝載 Windows 作業系統的虛擬桌面基礎結構（VDI）平臺。 系統管理員將 VDI 部署到：

- 簡化管理。
- 透過匯總和集中化資源來降低成本。
- 隨時隨地都能從任何裝置，提供使用者的行動性和自由存取虛擬桌面。

虛擬桌面電腦有兩種主要類型：

- 持續性
- 非永續性

持續性版本會針對每個使用者或使用者集區使用唯一的桌面映射。 這些獨特的桌面可以自訂及儲存以供日後使用。 

非持續版本會使用使用者可以視需要存取的桌面集合。 在使用者登出之後，這些非持續性的桌面會還原為其原始狀態。

本文將涵蓋 Microsoft 針對裝置身分識別和 VDI 的支援所提供的指引。 如需裝置身分識別的詳細資訊，請參閱[什麼是裝置身分識別一](overview.md)文。

## <a name="supported-scenarios"></a>支援的案例

在您的 VDI 環境 Azure AD 中設定裝置身分識別之前，請先熟悉支援的案例。 下表說明支援的布建案例。 在此內容中進行布建，表示系統管理員可以大規模設定裝置身分識別，而不需要任何使用者互動。

| 裝置身分識別類型 | 身分識別基礎結構 | Windows 裝置 | VDI 平臺版本 | 支援 |
| --- | --- | --- | --- | --- |
| 已聯結的混合式 Azure AD | 建立 | Windows 目前 * * * 和舊版 Windows * * * * | 持續性 | 是 |
|   |   | Windows 目前 | 非持續性 | 否 |
|   |   | 舊版 Windows | 非持續性 | 是 |
|   | 受控 * * | Windows 目前和舊版 Windows | 持續性 | 是 |
|   |   | Windows 目前 | 非持續性 | 否 |
|   |   | 舊版 Windows | 非持續性 | 是 |
| 已聯結的 Azure AD | 同盟 | Windows 目前 | 持續性 | 否 |
|   |   |   | 非持續性 | 否 |
|   | 受管理 | Windows 目前 | 持續性 | 否 |
|   |   |   | 非持續性 | 否 |
| 已註冊 Azure AD | 同盟 | Windows 目前 | 持續性 | 不適用 |
|   |   |   | 非持續性 | 不適用 |
|   | 受管理 | Windows 目前 | 持續性 | 不適用 |
|   |   |   | 非持續性 | 不適用 |

\*同盟**身分識別基礎**結構環境代表具有身分識別提供者（例如 AD FS 或其他協力廠商 IDP）的環境。

\*\***受控**識別基礎結構環境代表的環境具有 Azure AD，做為使用[密碼雜湊同步處理（PHS）](../hybrid/whatis-phs.md)或具有[無縫單一登入](../hybrid/how-to-connect-sso.md)的[傳遞驗證（PTA）](../hybrid/how-to-connect-pta.md)部署的身分識別提供者。

\*\*\***Windows 目前**的裝置代表 windows 10、windows server 2016 和 windows server 2019。

\*\*\*\*舊版**windows**裝置代表 windows 7、Windows 8.1、windows Server 2008 R2、windows server 2012 和 Windows Server 2012 R2。 如需 Windows 7 的支援資訊，請參閱[windows 7 的支援即將結束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 如需 Windows Server 2008 R2 的支援資訊，請參閱[為 Windows server 2008 終止支援做好準備](https://www.microsoft.com/cloud-platform/windows-server-2008)。

## <a name="microsofts-guidance"></a>Microsoft 的指引

系統管理員應該根據其身分識別基礎結構來參考下列文章，以瞭解如何設定混合式 Azure AD 聯結。

- [設定聯合環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
- [設定受管理環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-managed-domains.md)

如果您依賴系統準備工具（sysprep.exe），而且您使用 Windows 前 10 1809 映射來進行安裝，請確定映射不是來自已向 Azure AD 註冊的裝置，因為已加入混合式 Azure AD。

如果您依賴虛擬機器（VM）快照集來建立其他 Vm，請確定快照集不是來自已向 Azure AD 註冊的 VM，因為混合式 Azure AD 聯結。

部署非持續性的 VDI 時，IT 系統管理員應該特別注意管理 Azure AD 中的過時裝置。 Microsoft 建議 IT 系統管理員執行下列指導方針。 如果無法這麼做，將會導致您的目錄中有許多已從您的非持續性 VDI 平臺註冊的過時混合式 Azure AD 聯結裝置。

- 建立並使用電腦顯示名稱的前置詞，以 VDI 為基礎來表示桌面。
- 在登出腳本中執行下列命令。 此命令將會觸發 Azure AD 的最佳呼叫，以刪除裝置。
   - 針對舊版 Windows 裝置– autoworkplace.exe/leave
- 定義和執行[管理過時裝置](manage-stale-devices.md)的處理常式。
   - 一旦您有識別非持續性混合式 Azure AD 聯結裝置的策略之後，您就可以更積極地清理這些裝置，以確保您的目錄不會與許多過時裝置一起使用。
 
## <a name="next-steps"></a>後續步驟

[設定聯合環境的混合式 Azure Active Directory 聯結](hybrid-azuread-join-federated-domains.md)
