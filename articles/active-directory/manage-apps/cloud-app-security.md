---
title: Microsoft Cloud App Security 的應用程式可見性和控制
description: 了解如何識別應用程式風險層級、即時阻止缺口和流失，以及使用應用程式連接器來利用提供者 API，以達到可見性和治理。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e3e0c10a7ec6b42420db30955ae4911bca27cc0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651221"
---
# <a name="cloud-app-visibility-and-control"></a>雲端應用程式的可見性和控制

若要取得雲端應用程式和服務的完整優勢，IT 小組必須在支援存取與同時保有控制之間取得適當平衡，以保護重要資料。 Microsoft Cloud App Security 提供豐富的資料移動可見性和控制權，以及精細的分析來識別及對抗所有 Microsoft 和第三方雲端服務的網路威脅。

## <a name="discover-and-manage-shadow-it-in-your-network"></a>探索和管理網路中的影子 IT

當 IT 系統管理員被問到他們認為其員工使用多少雲端應用程式時，他們表示平均 30 或 40 個，但事實上，貴組織的員工目前使用平均超過 1,000 個不同的應用程式。 影子 IT 可協助您得知和識別正在使用的應用程式，以及您的風險層級為何。 80% 的員工會使用待批准的應用程式，這類應用程式未經任何人審核，而且可能不符合您的安全性與合規性原則。 此外，由於員工能夠從公司網路外部存取您的資源和應用程式，因此防火牆的規則和原則將不再足夠。

使用 Microsoft Cloud App Discovery (Azure Active Directory Premium P1 功能) 來探索正在使用哪些應用程式、探索這些應用程式的風險、設定原則來識別新近有風險的應用程式，而且不要批准這些應用程式，以便使用 Proxy 或防火牆設備以原生方式進行封鎖。

- 探索和識別影子 IT
- 評估和分析
- 管理應用程式
- 進階影子 IT 探索報告
- 控制批准的應用程式
 
### <a name="learn-more"></a>深入了解

- [探索和管理網路中的影子 IT](/cloud-app-security/tutorial-shadow-it)
- [使用 Cloud App Security 探索到的應用程式](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>使用者工作階段可見性和控制 

在現今的工作場所中，事後得知雲端環境發生什麼情況通常有所不足。 您想要在員工刻意或不小心讓您的資料和組織面臨風險之前，阻止缺口和流失。 搭配 Azure Active Directory (Azure AD) 使用，Microsoft Cloud App Security 會透過「條件式存取應用程式控制」，以整體性整合體驗來提供這些功能。 

工作階段控制會使用反向 Proxy 架構，並與 Azure AD 條件式存取進行獨一為二的整合。 Azure AD 條件式存取可讓您根據特定條件，對貴組織的應用程式強制執行存取控制。 條件會定義要套用條件式存取原則的對象 (使用者或使用者群組)、項目 (哪些雲端應用程式) 和位置 (哪些位置和網路)。 判斷條件之後，即可將使用者路由傳送至 Cloud App Security，以便即時保護資料。  

透過這項控制，您可以：  
- 控制檔案下載
- 監視 B2B 案例  
- 控制檔案的存取  
- 在下載時保護文件  
 
### <a name="learn-more"></a>深入了解

- [使用 Cloud App Security 中的工作階段控制保護應用程式](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>進階應用程式可見性和控制 

應用程式連接器使用應用程式提供者的 API，讓 Microsoft Cloud App Security 對您所連線的應用程式有更好的可見性和控制。 Cloud App Security 可利用雲端提供者所提供的 API。 每個服務都有自己的架構和 API 限制，例如節流、API 限制、動態時間偏移 API 視窗等等。 Cloud App Security 產品小組與這些服務合作，以將 API 的使用最佳化並提供最佳效能。 Cloud App Security 引擎考量到服務加諸於其 API 的不同限制，因此會使用其最大允許的容量。 某些作業 (例如掃描租用戶中的所有檔案) 需要大量 API 呼叫，以便延續較長的時間。 預計有些原則會執行數小時或數天。 
 
### <a name="learn-more"></a>深入了解  

- [在 Cloud App Security 中連線應用程式](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>後續步驟

- [探索和管理網路中的影子 IT](/cloud-app-security/tutorial-shadow-it)
- [使用 Cloud App Security 探索到的應用程式](/cloud-app-security/discovered-apps)
- [使用 Cloud App Security 中的工作階段控制保護應用程式](/cloud-app-security/proxy-intro-aad)
- [在 Cloud App Security 中連線應用程式](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)