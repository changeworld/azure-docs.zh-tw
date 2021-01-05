---
title: IT 服務管理連接器-Azure 監視器中的安全匯出
description: 本文說明如何在 Azure 監視器中將 ITSM 產品/服務與安全匯出連線，以集中監視和管理 ITSM 工作專案。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: ee56d65452cb8535c5197e1b3524bd4e9c9ab9ea
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857404"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>使用安全匯出將 Azure 連接至 ITSM 工具

本文說明如何使用「安全匯出」來設定您的 IT 服務管理 (ITSM) 產品或服務之間的連線。

安全匯出是 [IT 服務管理連接器 (ITSMC) ](./itsmc-overview.md)的更新版本。 這兩個版本都可讓您在 Azure 監視器傳送警示時，在 ITSM 工具中建立工作專案。 這些功能包括計量、記錄和活動記錄警示。

ITSMC 會使用使用者名稱和密碼認證。 安全匯出有更強的驗證，因為它使用 Azure Active Directory (Azure AD) 。 Azure AD 是 Microsoft 的雲端式身分識別和存取管理服務。 它可協助使用者登入並存取內部或外部資源。 搭配使用 Azure AD 與 ITSM 有助於透過傳送至外部系統的 Azure AD 應用程式識別碼) ，來識別 (的 Azure 警示。

> [!NOTE]
> 使用「安全匯出」將 Azure 連線到 ITSM 工具的功能目前為預覽狀態。

## <a name="secure-export-architecture"></a>安全匯出架構

安全匯出架構引進了下列新功能：

* **新的動作群組**：透過安全 Webhook 動作群組將警示傳送至 ITSM 工具，而不是 ITSMC 所使用的 ITSM 動作群組。
* **Azure AD authentication**：驗證是透過 Azure AD 進行，而不是使用者名稱/密碼認證。

## <a name="secure-export-data-flow"></a>安全匯出資料流程

安全匯出資料流程的步驟如下：

1. Azure 監視器傳送已設定為使用安全匯出的警示。
2. 警示內容是由安全的 Webhook 動作傳送至 ITSM 工具。
3. 如果警示已獲授權進入 ITSM 工具，ITSM 應用程式會檢查 Azure AD。
4. 如果警示已獲得授權，則應用程式：
   
   1. 建立工作專案 (例如，ITSM 工具中的事件) 。
   2. 將設定專案的識別碼 (CI) 系結至客戶管理資料庫 (CMDB) 。

![此圖顯示 ITSM 工具如何與 Azure A D、Azure 警示和動作群組進行通訊。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>安全匯出的優點

整合的主要優點如下：

* **更好的驗證**： Azure AD 提供更安全的驗證，而不會發生 ITSMC 中經常發生的超時狀況。
* **ITSM 工具中已解決的警示**：計量警示會執行「已引發」和「已解決」狀態。 符合條件時，警示狀態會是「已引發」。 當條件不再符合時，警示狀態會是「已解決」。 在 ITSMC 中，無法自動解決警示。 使用「安全匯出」時，已解決狀態會流向 ITSM 工具，因此會自動更新。
* **[常見的警示架構](./alerts-common-schema.md)**：在 ITSMC 中，警示承載的架構會根據警示類型而有所不同。 在安全匯出中，所有警示類型都有一個通用的架構。 此通用架構包含所有警示類型的 CI。 所有警示類型都能夠將其 CI 系結至 CMDB。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 警示的 ITSM 工作項目](./itsmc-overview.md)
