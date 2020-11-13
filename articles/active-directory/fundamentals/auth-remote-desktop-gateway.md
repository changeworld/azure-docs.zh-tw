---
title: 使用 Azure Active Directory 遠端桌面閘道服務
description: 使用 Azure Active Directory 實現遠端桌面閘道服務的架構指引。
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
ms.openlocfilehash: 57466cccb71444d5711a9221c324a107757c5e82
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576790"
---
# <a name="remote-desktop-gateway-services"></a>遠端桌面閘道服務

標準遠端桌面服務 (RDS) 部署包括在 Windows Server 上執行的各種 [遠端桌面角色服務](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) 。 具有 Azure Active Directory (Azure AD) 應用程式 Proxy 的 RDS 部署，具有來自執行連接器服務之伺服器的永久輸出連線。 其他部署會透過負載平衡器保留開啟的輸入連線。 這種驗證模式可讓您透過遠端桌面服務發佈內部部署應用程式，以提供更多類型的應用程式。 它也會使用 Azure AD 應用程式 Proxy，減少其部署的受攻擊面。

## <a name="use-when"></a>使用時機

您必須提供遠端存取，並使用預先驗證來保護您的遠端桌面服務部署。

![架構圖](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者** ：存取應用程式 Proxy 所提供的 RDS。

* **Web 瀏覽器** ：使用者與之互動的元件，以存取應用程式的外部 URL。

* **Azure AD** ：驗證使用者。 

* **應用程式 Proxy 服務** ：作為反向 Proxy，將要求從使用者轉寄到 RDS。 應用程式 Proxy 也可以強制執行任何條件式存取原則。 

* **遠端桌面服務** ：作為個別虛擬化應用程式的平臺，提供安全的行動和遠端桌面存取，並讓使用者能夠從雲端執行其應用程式和桌上型電腦。 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>使用 Azure AD 來執行遠端桌面閘道服務

* [使用 Azure AD 應用程式 Proxy 發佈遠端桌面](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [新增內部部署應用程式，以透過 Azure AD 中的應用程式 Proxy 進行遠端存取](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
