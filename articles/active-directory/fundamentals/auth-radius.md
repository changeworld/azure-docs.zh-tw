---
title: 使用 Azure Active Directory 的 RADIUS 驗證
description: 使用 Azure Active Directory 達到 RADIUS 驗證的架構指引。
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
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168622"
---
# <a name="radius-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 的 RADIUS 驗證

遠端驗證撥入消費者服務 (RADIUS) 是一種網路通訊協定，可透過啟用使用者的集中式驗證與授權來保護網路。 許多應用程式仍依賴 RADIUS 通訊協定來驗證使用者。

Microsoft Windows Server 的角色稱為「網路原則伺服器」 (NPS) ，其可作為 RADIUS 伺服器並支援 RADIUS 驗證。

Azure Active Directory (Azure AD) 會啟用以 RADIUS 為基礎系統的多重要素驗證。 如果客戶想要將 Azure AD Multi-Factor Authentication 套用至任何先前提及的 RADIUS 工作負載，他們可以在其 Windows NPS 伺服器上 Azure AD 安裝 Multi-Factor Authentication NPS 擴充功能。 

Windows NPS 伺服器會根據 Active Directory 來驗證使用者的認證，然後將 Multi-Factor Authentication 要求傳送至 Azure。 使用者接著會在其行動驗證器上收到挑戰。 成功之後，用戶端應用程式就可以連接到服務。 

## <a name="use-when"></a>使用時機： 

您必須將 Multi-Factor Authentication 新增至應用程式，例如
* 虛擬私人網路 (VPN) 
* WiFi 存取
* 遠端桌面閘道 (RDG) 
* 虛擬桌面基礎結構 (VDI)
* 相依于 RADIUS 通訊協定的任何其他專案，可向服務驗證使用者。 

> [!NOTE]
> 與其依賴 RADIUS 和 Azure AD Multi-Factor Authentication NPS 擴充功能將 Azure AD Multi-Factor Authentication 套用至 VPN 工作負載，我們建議您將 VPN 升級為 SAML，並直接將 VPN 與 Azure AD 同盟。 這可讓您的 VPN 充分利用 Azure AD 保護，包括條件式存取、Multi-Factor Authentication、裝置合規性和身分識別保護。

![架構圖](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>系統的元件 

* **用戶端應用程式 (VPN 用戶端)**：將驗證要求傳送至 RADIUS 用戶端。

* **RADIUS 用戶端**：從用戶端應用程式轉換要求，並將其傳送至已安裝 NPS 擴充功能的 RADIUS 伺服器。

* **Radius 伺服器**：與 Active Directory 連接，以執行 RADIUS 要求的主要驗證。 成功時，會將要求傳遞至 Azure AD Multi-Factor Authentication NPS 擴充功能。

* **NPS 擴充** 功能：觸發對次要驗證 Azure AD Multi-Factor Authentication 的要求。 如果成功，NPS 延伸模組會提供 RADIUS 伺服器安全性權杖（包含由 Azure 安全性權杖服務所發出的 Multi-Factor Authentication 宣告）來完成驗證要求。

* **Azure AD Multi-Factor Authentication**：與 Azure AD 通訊以抓取使用者的詳細資料，並使用使用者所設定的驗證方法執行次要驗證。

## <a name="implement-radius-with-azure-ad"></a>使用 Azure AD 來執行 RADIUS 

* [使用 NPS 提供 Azure AD Multi-Factor Authentication 功能](../authentication/howto-mfa-nps-extension.md) 

* [設定 Azure AD Multi-Factor Authentication NPS 擴充功能](../authentication/howto-mfa-nps-extension-advanced.md) 

* [具有 Azure AD Multi-Factor Authentication 使用 NPS 擴充功能的 VPN](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

