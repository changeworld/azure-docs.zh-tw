---
title: Azure Active Directory 的 LDAP 同步處理
description: 使用 Azure Active Directory 達到 LDAP 同步處理的架構指引。
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
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168673"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Azure Active Directory 的 LDAP 同步處理

輕量型目錄存取協定 (LDAP) 是在 TCP/IP 堆疊上執行的目錄服務通訊協定。 它提供一種機制，用來連接、搜尋和修改網際網路目錄。 LDAP 目錄服務是以用戶端-伺服器模型為基礎，而其功能是啟用對現有目錄的存取。 許多公司相依于內部部署 LDAP 伺服器，以儲存其重要商務應用程式的使用者和群組。 

Azure Active Directory (Azure AD) 可以用 Azure AD Connect 取代 LDAP 同步處理。 Azure AD Connect 同步處理服務會執行所有與在內部部署環境和 Azure AD 之間同步處理身分識別資料相關的作業。 

## <a name="use-when"></a>使用時機

您必須同步處理內部部署 LDAP v3 目錄與 Azure AD 之間的身分識別資料。 

![架構圖](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>系統的元件

* **使用者**：存取依賴使用 LDAP v3 目錄來排序使用者和密碼的應用程式。

* **Web 瀏覽器**：使用者與之互動的元件，以存取應用程式的外部 URL

* **Web 應用** 程式：具有 LDAP v3 目錄相依性的應用程式。

* **Azure AD**： Azure AD 同步處理身分識別資訊 (使用者、群組、透過 Azure AD Connect 從組織的內部部署 LDAP 目錄) 的密碼。 

* **Azure AD Connect**：是將內部部署身分識別基礎結構連接到 Microsoft Azure AD 的工具。 Wizard 和引導式體驗可協助您部署及設定連接所需的必要條件和元件。 

* **自訂連接器**：一般 ldap 連接器可讓您將 Azure AD Connect 同步處理服務與 LDAP v3 伺服器整合。 它位於 Azure AD Connect 上。

* **Active Directory**： Active Directory 是大部分 Windows Server 作業系統中包含的目錄服務。 執行 Active Directory 目錄服務的伺服器稱為網域控制站，其會驗證並授權 Windows 網域中的所有使用者和電腦。

* **Ldap v3 伺服器**：儲存用於目錄服務驗證之公司使用者和密碼的 ldap 通訊協定相容目錄。

## <a name="implement-ldap-synchronization-with-azure-ad"></a>使用 Azure AD 來執行 LDAP 同步處理

* [混合式身分識別目錄整合工具](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Azure AD Connect 安裝藍圖](../hybrid/how-to-connect-install-roadmap.md) 

* [總覽和建立 LDAP 連接器](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP 連接器是一種先進的設定，需要先熟悉 Forefront Identity Manager 及/或 Microsoft Identity Manager。 如果在生產環境中使用，建議您將此設定的相關問題移至 [頂級支援](https://support.microsoft.com/premier) 或 Microsoft 合作夥伴網路。

