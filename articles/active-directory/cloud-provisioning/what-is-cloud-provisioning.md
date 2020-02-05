---
title: 什麼是 Azure AD Connect 雲端布建。 | Microsoft Docs
description: 說明 Azure AD Connect 雲端布建。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69b8cbdba018b9d03dca67573b1f4fe6efa546b6
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024036"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>什麼是 Azure AD Connect 雲端佈建？
Azure AD Connect 雲端布建是新的 Microsoft 代理程式，其設計目的是為了符合並完成您的混合式身分識別目標，讓使用者、群組和連絡人同步處理 Azure AD。  它可以與 Azure AD Connect 同步處理一起使用，並提供下列優點：
    
- 支援從多樹系已中斷連線的 Active Directory 樹系環境同步處理至 Azure AD 的租使用者：常見的案例包括合併 & 取得，其中所購買的公司 AD 樹系與父公司的 AD 隔離過去有多個 AD 樹系的樹系和公司。
- 使用輕量布建代理程式簡化安裝：代理程式可作為從 AD 到 Azure AD 的橋接器，並在雲端中管理所有同步設定。 
- 多個布建代理程式可以用來簡化高可用性部署，特別是依賴從 AD 到 Azure AD 的密碼雜湊同步處理的組織。


![何謂 Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Azure AD Connect 雲端布建與 Azure AD Connect 同步處理有何不同？
透過 Azure AD Connect 雲端布建，將從 AD 布建至 Azure AD 已在 Microsoft Online Services 中進行協調。 組織只需要在其內部部署和 IaaS 裝載的環境中部署，這是一個可做為 Azure AD 和 AD 間橋樑的輕量代理程式。 布建設定會儲存在 Azure AD 中，並作為服務的一部分進行管理。

下表提供 Azure AD Connect 和 Azure AD Connect 雲端布建之間的比較：

| 功能 | Azure Active Directory Connect 同步| Azure Active Directory Connect 雲端布建 |
|:--- |:---:|:---:|
|連接到單一內部部署 AD 樹系|● |● |
| 連接到多個內部部署 AD 樹系 |● |● |
| 連接到多個已中斷連線的內部部署 AD 樹系 | |● |
| 輕量代理程式安裝模型 | |● |
| 高可用性的多個使用中代理程式 | |● |
| 連接到 LDAP 目錄|●| | 
| 使用者物件的支援 |● |● |
| 群組物件的支援 |● |● |
| Contact 物件的支援 |● |● |
| 裝置物件的支援 |● | |
| 允許屬性流程的基本自訂 |● |● |
| 同步處理 Exchange online 屬性 |● |● |
| 同步處理延伸模組屬性1-15 |● |● |
| 同步處理客戶定義的 AD 屬性（目錄延伸模組） |● | |
| 支援密碼雜湊同步處理 |●|●|
| 支援傳遞驗證 |●||
| 同盟的支援 |●|●|
| 無縫單一登入|● |●|
| 支援在網域控制站上安裝 |● |● |
| Windows Server 2012 和 Windows Server 2012 R2 的支援 |● |● |
| 篩選網域/Ou/群組 |● |● |
| 針對物件的屬性值篩選 |● | |
| 允許同步處理一組最基本的屬性 (MinSync) |● |● |
| 允許移除從 AD 流向 Azure AD 的屬性 |● |● |
| 允許屬性流程的進階自訂 |● | |
| 支援回寫（密碼、裝置、群組） |● | |
| Azure AD Domain Services 支援|● | |
| [Exchange 混合式回寫](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| 支援每個 AD 網域超過50000個物件 |● | |

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [安裝雲端佈建](how-to-install.md)
