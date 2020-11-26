---
title: Azure Active Directory 的標頭型驗證
description: 使用 Azure Active Directory 達到標頭型驗證的架構指引。
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
ms.openlocfilehash: 69e87fc919893a544f5d0b1b615a110f25486e57
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168741"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Azure Active Directory 的標頭型驗證

繼承應用程式通常會使用標頭型驗證。 在此案例中，使用者 (或訊息建立者) 驗證中繼身分識別解決方案。 中繼解決方案會驗證使用者，並將所需的超文字傳輸通訊協定 (HTTP) 標頭傳播至目的地 web 服務。 Azure Active Directory (AD) 透過其應用程式 Proxy 服務支援此模式，並與其他網路控制卡解決方案整合。 

在我們的解決方案中，應用程式 Proxy 可提供應用程式的遠端存取許可權、驗證使用者，以及傳遞應用程式所需的標頭。 

## <a name="use-when"></a>使用時機

遠端使用者需要安全地單一登入 (SSO) 進入需要以標頭為基礎之驗證的內部部署應用程式。

![以架構映射標頭為基礎的驗證](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者**：存取應用程式 Proxy 所提供的繼承應用程式。

* **Web 瀏覽器**：使用者與之互動的元件，以存取應用程式的外部 URL。

* **Azure AD**：驗證使用者。 

* **應用程式 Proxy 服務**：作為反向 Proxy，將要求從使用者傳送至內部部署應用程式。 它位於 Azure AD，也可以強制執行任何條件式存取原則。

* **應用程式 Proxy 連接器**：安裝在 Windows 伺服器上的內部部署環境，以提供應用程式的連線能力。 它只會使用輸出連接。 傳回 Azure AD 的回應。

* **繼承應用程式**：從應用程式 Proxy 接收使用者要求的應用程式。 繼承應用程式會接收所需的 HTTP 標頭來設定會話，並傳迴響應。 

## <a name="implement-header-based-authentication-with-azure-ad"></a>使用 Azure AD 來執行以標頭為基礎的驗證

* [新增內部部署應用程式，以透過 Azure AD 中的應用程式 Proxy 進行遠端存取](../manage-apps/application-proxy-add-on-premises-application.md)  

* [使用應用程式 Proxy 與 PingAccess 的單一登入之標頭式驗證](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) 

* [使用應用程式傳遞控制器和網路保護舊版應用程式](../manage-apps/secure-hybrid-access.md)