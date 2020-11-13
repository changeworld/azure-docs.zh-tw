---
title: 使用 Azure Active Directory 的 Kerberos 限制委派
description: 使用 Azure Active Directory 達到 Kerberos 限制委派的架構指引。
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
ms.openlocfilehash: 62c8f230ca2b2d0db1170cde9b24f9e4819889bb
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577119"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows 驗證-具有 Azure Active Directory 的 Kerberos 限制委派

Kerberos 限制委派 (KCD) 提供資源之間的限制委派，而且是根據服務主體名稱。 它需要網域系統管理員來建立委派，而且僅限於單一網域。 以資源為基礎的 KCD 通常是用來為 web 應用程式提供 Kerberos 驗證，此應用程式的使用者位於 Active Directory 樹系內的多個網域中。

Azure Active Directory 應用程式 Proxy 可以提供單一登入 (SSO) 和遠端存取 KCD 型應用程式，這些應用程式需要 Kerberos 票證來進行存取，以及 Kerberos 限制委派 (KCD) 。

您可以提供應用程式 Proxy 連接器許可權，以在 Active Directory 中模擬使用者，以啟用使用整合式 Windows 驗證 (IWA) 的內部部署 KCD 應用程式的 SSO。 應用程式 Proxy 連接器會使用此許可權來代表使用者傳送和接收權杖。

## <a name="use-when"></a>使用時機

需要提供遠端存取、使用預先驗證進行保護，並提供 SSO 給內部部署 IWA 應用程式。

![架構圖表](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者** ：存取應用程式 Proxy 所提供的繼承應用程式。

* **Web 瀏覽器** ：使用者與之互動的元件，以存取應用程式的外部 URL。

* **Azure AD** ：驗證使用者。 

* **應用程式 Proxy 服務** ：作為反向 Proxy，將要求從使用者傳送至內部部署應用程式。 它位於 Azure AD 中。 應用程式 Proxy 也可以強制執行任何條件式存取原則。

* **應用程式 Proxy 連接器** ：安裝在 Windows 伺服器上的內部部署環境，以提供應用程式的連線能力。 傳回 Azure AD 的回應。 使用 Active Directory 執行 KCD 協商，模擬使用者以取得應用程式的 Kerberos 權杖。

* **Active Directory** ：將應用程式的 Kerberos 權杖傳送至應用程式 Proxy 連接器。

* **繼承應用程式** ：從應用程式 Proxy 接收使用者要求的應用程式。 繼承應用程式會將回應傳回給應用程式 Proxy 連接器。

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>使用 Azure AD 執行 Windows 驗證 (KCD) 

* [可供使用應用程式 Proxy 單一登入應用程式的 Kerberos 限制委派](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [新增內部部署應用程式，以透過 Azure Active Directory 中的應用程式 Proxy 進行遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
