---
title: 使用 Azure 事件方格的 Auth0 合作夥伴主題
description: 使用 Azure 事件方格結束從 Auth0 到 Azure 服務的事件。
services: event-grid
author: femila
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: femila
ms.openlocfilehash: 80226822e89344705c24094b073868d8ac0e1e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84560739"
---
# <a name="auth0-partner-topics"></a>Auth0 合作夥伴主題
![Auth0 標誌](./media/auth0-overview/auth0-logo.png)

Auth0 是應用程式建立器的身分識別平台，為開發人員及企業提供保護應用程式所需的建置組塊。

Auth0 合作夥伴主題可讓您使用 Auth0's 系統所發出的事件來完成許多工。 在驗證或將安全性和基礎結構工作自動化之後，以有意義的方式與使用者互動。

此整合可讓您將具有高可靠性的 Auth0 記錄事件串流至 Azure。 在此，您可以使用事件搭配您最愛的 Azure 資源。 此整合可讓您對事件做出反應、取得深入解析、監視安全性問題，以及與其他強大的資料管線互動。

對於使用 Auth0 和 Azure 的組織，此整合可讓您在整個堆疊之間順暢地整合資料。 
 
## <a name="available-event-types"></a>可用的事件類型
可在[此網站](https://auth0.com/docs/logs/references/log-event-type-codes)上取得可用 Auth0 事件種類及其描述的完整清單。

## <a name="use-cases"></a>使用案例

### <a name="engage-with-your-users"></a>與您的使用者互動
提供強大的使用者體驗，對於降低流失及保留使用者來說至關重要。 藉由使用 Auth0 事件與 Azure Functions 和 Azure Logic Apps，提供更多自訂的應用程式體驗。 

### <a name="understand-user-behavior"></a>瞭解使用者行為
了解使用者何時存取您的產品、他們登入的位置，以及他們使用的裝置。 追蹤這些訊號，藉此了解最重要的產品區域。 這些信號可協助您判斷：
- 支援的瀏覽器和裝置。 
- 要在其中當地語系化應用程式的語言。 
- 當尖峰流量時間為時。 

### <a name="manage-user-data"></a>管理使用者資料
保存及稽核您的使用者動作對於維護安全性和遵循業界法規而言非常重要。 編輯、移除或匯出使用者資料的能力，對於下列隱私權法則的重要性日益重要，例如歐盟的一般資料保護規定（GDPR）。

### <a name="secure-your-application"></a>保護您的應用程式
當您保護分散式系統時，結合安全性監視和事件回應程式是很重要的。 基於這個原因，請務必將所有資料都放在同一個位置，然後監視整個堆疊。 

## <a name="next-steps"></a>後續步驟

- [合作夥伴主題概觀](partner-topics-overview.md)
- [如何使用 Auth0 合作夥伴主題](auth0-how-to.md)
- [Auth0 檔](https://auth0.com/docs/azure-tutorial)
- [變成事件方格合作夥伴](partner-onboarding-overview.md)

