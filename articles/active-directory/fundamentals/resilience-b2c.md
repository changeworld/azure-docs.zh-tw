---
title: 使用 Azure AD B2C 在客戶身分識別和存取管理中建立恢復功能 |Microsoft Docs
description: 使用 Azure AD B2C 在客戶身分識別和存取管理中建立恢復能力的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda9378a41e0d98276957a6fad75db2c20e6e4e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919462"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在您的客戶身分識別和存取管理中打造復原能力

[Azure Active Directory (AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) 是客戶身分識別和存取管理 (CIAM) 平臺，其設計目的是協助您成功地啟動您的重要客戶應用程式。 我們有許多內建的 [恢復](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) 功能，其設計目的是為了協助我們的服務調整您的需求，並在可能發生中斷的情況下提高復原能力。 此外，當您啟動任務關鍵性應用程式時，請務必考慮您應用程式中的各種設計和設定專案，以及如何在 Azure AD B2C 內設定應用程式，以確保您可以在回應中斷或失敗案例時取得復原行為。 在本文中，我們將討論一些可協助您提高復原能力的最佳作法。

復原服務是一種持續運作（儘管中斷）的服務。 您可以透過下列方式，協助改善服務中的復原能力：

- 瞭解所有元件

- 消除單一失敗點

- 隔離失敗的元件來限制其影響

- 以快速容錯移轉機制和復原路徑提供冗余

當您開發應用程式時，我們建議您考慮如何在您的應用程式中使用解決方案的身分識別元件來 [提高驗證和授權的復原能力](resilience-app-development-overview.md) 。 本文會嘗試針對 Azure AD B2C 應用程式特有的恢復功能進行處理。 我們的建議是依 CIAM 函式來分組。

![影像顯示 ](media/resilience-b2c/high-level-components.png) 後續章節中的 CIAM 元件，我們會引導您在下欄區域中建立恢復功能：

- [使用者體驗](resilient-end-user-experience.md)：啟用驗證流程的回溯方案，並減輕 Azure AD B2C authentication 服務中斷的潛在影響。

- [具有外部進程的介面](resilient-external-processes.md)：從錯誤中復原，以在您的應用程式和介面中建立恢復功能。  

- [開發人員最佳做法](resilience-b2c-developer-best-practices.md)：避免脆弱數，因為常見的自訂原則問題，並改善與宣告驗證器、協力廠商應用程式和 REST api 互動等區域中的錯誤處理。

- [監視和分析](resilience-with-monitoring-alerting.md)：透過警示來監視關鍵指標並偵測失敗和效能中斷，以評估服務的健全狀況。

- Azure AD B2C 開發人員的復原能力資源
  - [復原的終端使用者體驗](resilient-end-user-experience.md)
  - [具有外部進程的復原介面](resilient-external-processes.md)
  - [透過開發人員最佳作法的復原能力](resilience-b2c-developer-best-practices.md)
  - [透過監視及分析的復原能力](resilience-with-monitoring-alerting.md)
- [在您的驗證基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [提高應用程式中驗證和授權的復原能力](resilience-app-development-overview.md)
