---
title: 使用 Azure Active Directory 的 LDAP 驗證
description: 達到此驗證模式的架構指引
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a70cb4754d98f4573670860c510692a7a2d134c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114135"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 的 LDAP 驗證

輕量型目錄存取協定 (LDAP) 是使用各種目錄服務的應用程式協定。 目錄服務（例如 Active Directory、 [儲存使用者和帳戶資訊](https://www.dnsstuff.com/active-directory-service-accounts)），以及安全性資訊（例如密碼）。 接著，服務會允許資訊與網路上的其他裝置共用。 企業應用程式（例如電子郵件、客戶關係管理員 (Crm) 和人力資源） (HR) 軟體可以使用 LDAP 來驗證、存取和尋找資訊。 

Azure Active Directory (Azure AD) 透過 Azure AD Domain Services (AD DS) 支援此模式。 它可讓採用雲端優先策略的組織，將其內部部署 LDAP 資源移至雲端，以將其環境現代化。 立即的優點將是： 

* 與 Azure AD 整合。 新增使用者和群組，或其物件的屬性變更，會自動從您的 Azure AD 租使用者同步處理到 AD DS。 對內部部署 Active Directory 中的物件所做的變更會同步處理至 Azure AD，然後再 AD DS。

* 簡化作業。 減少手動保留和修補內部部署基礎結構的需求。 

* 可靠的。 您會取得受控、高可用性的服務 

## <a name="use-when"></a>使用時機

應用程式或服務需要使用 LDAP 驗證。

![架構圖表](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者**：透過瀏覽器存取 LDAP 相依的應用程式。

* **Web 瀏覽器**：使用者互動的介面，以存取應用程式的外部 URL。

* **虛擬網路**： Azure 中的私人網路，讓繼承應用程式可以使用 LDAP 服務。 

* **繼承應用程式**：需要在 Azure 中的虛擬網路中部署 LDAP 的應用程式或伺服器工作負載，或可透過網路路由查看 AD DS 實例 ip 的應用程式或伺服器工作負載。 

* **Azure AD**：透過 Azure AD Connect 從組織的內部部署目錄同步身分識別資訊。

* **Azure AD Domain Services (AD DS) **：從 Azure AD 執行單向同步處理，以提供一組集中使用者、群組和認證的存取權。 AD DS 實例會指派給虛擬網路。 Azure 中的應用程式、服務和 Vm 連接到指派給 AD DS 的虛擬網路，可以使用一般的 AD DS 功能，例如 LDAP、網域加入、群組原則、Kerberos 和 NTLM 驗證。
   > [!NOTE]
   >  在組織無法同步處理密碼雜湊的環境中，或使用者使用智慧卡登入時，我們建議您在 AD DS 中使用資源樹系。 

* **Azure AD Connect**：將內部部署身分識別資訊同步處理至 Microsoft Azure AD 的工具。 部署嚮導和引導式體驗可協助您設定連線所需的必要條件和元件，包括同步處理和從 Active Directory 登入 Azure AD。 

* **Active Directory**：儲存 [內部部署身分識別資訊（例如使用者和帳戶資訊](https://www.dnsstuff.com/active-directory-service-accounts)）和安全性資訊（如密碼）的目錄服務。

## <a name="implement-ldap-authentication-with-azure-ad"></a>使用 Azure AD 來執行 LDAP 驗證

* [建立並設定 Azure AD DS 實例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance) 

* [設定 Azure AD DS 實例的虛擬網路](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-networking) 

* [設定 Azure AD DS 受控網域的安全 LDAP](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps) 

* [在 Azure AD DS 中建立內部部署網域的輸出樹系信任](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-forest-trust)

 
