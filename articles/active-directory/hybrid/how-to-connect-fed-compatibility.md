---
title: Azure AD 同盟相容性清單
description: 此頁面包含可用來實作單一登入的非 Microsoft 識別提供者。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661824"
---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD 同盟相容性清單
Azure Active Directory 為 Microsoft 365 和其他 Microsoft 線上服務提供單一登入和增強的應用程式存取安全性，以進行混合式和僅限雲端的部署，而不需要任何協力廠商解決方案。 Microsoft 365，就像大部分的 Microsoft 線上服務一樣，都與目錄服務、驗證和授權的 Azure Active Directory 整合。 Azure Active Directory 也為數千個 SaaS 應用程式和內部部署 Web 應用程式提供單一登入。 請參閱 Azure Active Directory [應用程式庫](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)以了解支援的 SaaS 應用程式有哪些。 

## <a name="idp-validation"></a>IDP 驗證
如果您的組織使用協力廠商同盟解決方案，則可以使用 Microsoft Online services （例如 Microsoft 365）來設定內部部署 Active Directory 使用者的單一登入，但前提是協力廠商同盟解決方案與 Azure Active Directory 相容。  如有關於相容性的問題，請連絡識別提供者。  如果您想要查看先前已測試過的身分識別提供者清單，以瞭解 Microsoft 是否與 Azure AD 的相容性，請參閱 [Azure AD 身分識別提供者相容性](https://www.microsoft.com/download/details.aspx?id=56843)檔。 

>[!NOTE]
>Microsoft 已不再針對獨立身分識別提供者提供驗證測試，以確認與 Azure Active Directory 之間的相容性。 如果您想要測試您的產品是否具有互通性，請參閱這些[指導方針](https://www.microsoft.com/download/details.aspx?id=56843)。 

## <a name="next-steps"></a>後續步驟

- [整合您的內部部署目錄與 Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect 和同盟](how-to-connect-fed-whatis.md)
