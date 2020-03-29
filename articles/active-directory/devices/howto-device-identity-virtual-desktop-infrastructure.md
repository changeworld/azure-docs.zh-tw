---
title: 設備標識和桌面虛擬化 - Azure 活動目錄
description: 瞭解如何將 VDI 和 Azure AD 設備標識一起使用
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
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900363"
---
# <a name="device-identity-and-desktop-virtualization"></a>設備標識和桌面虛擬化

管理員通常在其組織中部署託管 Windows 作業系統的虛擬桌面基礎結構 （VDI） 平臺。 管理員將 VDI 部署到：

- 簡化管理。
- 通過整合和集中資源降低成本。
- 提供最終使用者的移動性和隨時隨地在任何設備上訪問虛擬桌面的自由。

虛擬桌面有兩種主要類型：

- 持續性
- 非永續性

持久版本為每個使用者或使用者池使用唯一的桌面映射。 這些獨特的桌面可以自訂和保存以供將來使用。 

非持久性版本使用使用者可以根據需要訪問的桌面集合。 使用者退出後，這些非持久性桌面將恢復到其原始狀態。

本文將介紹 Microsoft 向管理員提供有關支援設備標識和 VDI 的指導。 有關設備標識的詳細資訊，請參閱文章["什麼是設備標識](overview.md)"。

## <a name="supported-scenarios"></a>支援的案例

在為 VDI 環境在 Azure AD 中配置設備標識之前，請熟悉支援的方案。 下表說明了支援哪些預配方案。 在此上下文中進行預配意味著管理員可以大規模配置設備標識，而無需任何最終使用者交互。

| 設備標識類型 | 身份基礎結構 | Windows 裝置 | VDI 平臺版本 | 支援 |
| --- | --- | --- | --- | --- |
| 已聯結的混合式 Azure AD | 聯合* | 視窗電流* 和 Windows 下級* | 持續性 | 是 |
|   |   | 視窗當前 | 非持久性 | 否 |
|   |   | 舊版 Windows | 非持久性 | 是 |
|   | 託管* | 視窗當前和視窗下級 | 持續性 | 是 |
|   |   | 視窗當前 | 非持久性 | 否 |
|   |   | 舊版 Windows | 非持久性 | 是 |
| 已聯結的 Azure AD | 同盟 | 視窗當前 | 持續性 | 否 |
|   |   |   | 非持久性 | 否 |
|   | 受控 | 視窗當前 | 持續性 | 否 |
|   |   |   | 非持久性 | 否 |
| 已註冊的 Azure AD | 同盟 | 視窗當前 | 持續性 | 否 |
|   |   |   | 非持久性 | 否 |
|   | 受控 | 視窗當前 | 持續性 | 否 |
|   |   |   | 非持久性 | 否 |

\***聯合**標識基礎結構環境表示具有標識提供程式（如 AD FS 或其他協力廠商 IDP）的環境。

\*\***託管**標識基礎結構環境表示具有 Azure AD 的環境，該環境是使用[密碼雜湊同步 （PHS）](../hybrid/whatis-phs.md)或具有[無縫單一登入的](../hybrid/how-to-connect-sso.md)[直通身份驗證 （PTA）](../hybrid/how-to-connect-pta.md)部署的標識提供程式。

\*\*\***Windows 當前**設備表示 Windows 10、Windows 伺服器 2016 和 Windows 伺服器 2019。

\*\*\*\***Windows 低級**設備表示 Windows 7、Windows 8.1、Windows 伺服器 2008 R2、Windows 伺服器 2012 和 Windows 伺服器 2012 R2。 有關 Windows 7 的支援資訊，請參閱[對 Windows 7 的支援即將結束](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 有關 Windows 伺服器 2008 R2 的支援資訊，請參閱[為 Windows 伺服器 2008 結束支援做好準備](https://www.microsoft.com/cloud-platform/windows-server-2008)。

## <a name="microsofts-guidance"></a>微軟指南

管理員應參考以下文章（基於其標識基礎結構）來瞭解如何配置混合 Azure AD 聯接。

- [為聯合環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-federated-domains.md)
- [為託管環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-managed-domains.md)

如果依賴于系統準備工具 （sysprep.exe），並且使用 Windows 10 1809 前映射進行安裝，請確保映射不是來自已註冊為混合 Azure AD 加入的設備。

如果依賴于虛擬機器 （VM） 快照來創建其他 VM，請確保快照不是來自已在 Azure AD 註冊為混合 Azure AD 聯接的 VM。

部署非持久性 VDI 時，IT 管理員應密切關注 Azure AD 中陳舊設備的管理。 Microsoft 建議 IT 管理員實施以下指南。 否則將導致目錄具有大量從非持久性 VDI 平臺註冊的陳舊混合 Azure AD 加入設備。

- 創建並使用指示桌面為基於 VDI 的電腦顯示名稱的首碼。
- 作為登出腳本的一部分實現以下命令。 此命令將觸發對 Azure AD 的盡最大努力調用以刪除設備。
   - 對於 Windows 低級設備 – 自動工作區.exe /離開
- 定義和實現[管理陳舊設備的](manage-stale-devices.md)流程。
   - 制定識別非持久性混合 Azure AD 聯接設備的策略後，可以更積極地清理這些設備，以確保目錄不會被大量陳舊的設備所使用。
 
## <a name="next-steps"></a>後續步驟

[為聯合環境配置混合 Azure 活動目錄聯接](hybrid-azuread-join-federated-domains.md)
