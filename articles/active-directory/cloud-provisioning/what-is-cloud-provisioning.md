---
title: 什麼是 Azure AD Connect 雲端佈建。 | Microsoft Docs
description: 說明 Azure AD Connect 雲端佈建。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0acef468aa53e456cd6fb416fe45558aee064699
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355812"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>什麼是 Azure AD Connect 雲端佈建？
Azure AD Connect 雲端佈建是新的 Microsoft 代理程式，其設計目的是為了符合並實現您的混合式身分識別目標，讓使用者、群組和連絡人可同步至 Azure AD。  其可搭配 Azure AD Connect 同步使用，並提供下列優點：
    
- 支援從多樹系且已中斷連線的 Active Directory 樹系環境同步至 Azure AD 租用戶：常見的案例包括合併與收購，被收購公司的 AD 樹系會與母公司的 AD 樹系及過去有多個 AD 樹系的公司各自分開。
- 使用輕量佈建代理程式簡化安裝：代理程式會作為 AD 到 Azure AD 之間的橋梁，並在雲端中管理所有同步設定。 
- 您可以使用多個佈建代理程式來簡化高可用性部署，這對依賴使用密碼雜湊從 AD 同步到 Azure AD 的組織特別重要。


![何謂 Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Azure AD Connect 雲端佈建與 Azure AD Connect 同步有何不同？
若使用 Azure AD Connect 雲端佈建，從 AD 佈建至 Azure AD 的作業會在 Microsoft Online Services 中進行協調。 組織只需要在其內部部署和 IaaS 主控的環境中部署輕量代理程式，以作為 Azure AD 和 AD 之間的橋梁。 佈建設定會儲存在 Azure AD 中，並作為服務的一部分進行管理。

## <a name="azure-ad-connect-cloud-provisioning-video"></a>Azure AD Connect 雲端佈建影片
下列簡短影片提供 Azure AD Connect 雲端佈建的絕佳概觀：

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-provisioning"></a>Azure AD Connect 與雲端佈建之間的比較

下表提供 Azure AD Connect 和 Azure AD Connect 雲端佈建之間的比較：

| 特徵 | Azure Active Directory Connect 同步| Azure Active Directory Connect 雲端佈建 |
|:--- |:---:|:---:|
|連接到單一內部部署 AD 樹系|● |● |
| 連接到多個內部部署 AD 樹系 |● |● |
| 連線到多個已中斷連線的內部部署 AD 樹系 | |● |
| 輕量代理程式安裝模型 | |● |
| 以多個使用中代理程式來達到高可用性 | |● |
| 連線到 LDAP 目錄|●| | 
| 支援使用者物件 |● |● |
| 支援群組物件 |● |● |
| 支援連絡人物件 |● |● |
| 支援裝置物件 |● | |
| 允許屬性流程的基本自訂 |● |● |
| 同步 Exchange Online 屬性 |● |● |
| 同步擴充屬性 1-15 |● |● |
| 同步客戶定義的 AD 屬性 (目錄擴充功能) |● | |
| 支援密碼雜湊同步 |●|●|
| 支援傳遞驗證 |●||
| 支援同盟 |●|●|
| 無縫單一登入|● |●|
| 支援在網域控制站上安裝 |● |● |
| Windows Server 2012 和 Windows Server 2012 R2 的支援 |● |● |
| 篩選網域/OU/群組 |● |● |
| 針對物件的屬性值篩選 |● | |
| 允許同步處理一組最基本的屬性 (MinSync) |● |● |
| 允許移除從 AD 流向 Azure AD 的屬性 |● |● |
| 允許屬性流程的進階自訂 |● | |
| 支援回寫 (密碼、裝置、群組) |● | |
| Azure AD Domain Services 支援|● | |
| [Exchange 混合回寫](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| 每個 AD 網域可支援超過 50,000 個物件 |● | |
| 跨網域參考|● | |

## <a name="next-steps"></a>後續步驟 

- [什麼是佈建？](what-is-provisioning.md)
- [安裝雲端佈建](how-to-install.md)
