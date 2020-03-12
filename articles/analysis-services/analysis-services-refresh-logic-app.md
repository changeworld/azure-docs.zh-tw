---
title: 使用 Azure Analysis Services 模型的 Logic Apps 進行重新整理 |Microsoft Docs
description: 本文說明如何使用 Azure Logic Apps 為 Azure Analysis Services 進行非同步重新整理程式碼。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126970"
---
# <a name="refresh-with-logic-apps"></a>使用 Logic Apps 重新整理

藉由使用 Logic Apps 和 REST 呼叫，您可以在 Azure 分析方格式模型上執行自動化資料重新整理作業，包括查詢向外延展的唯讀複本同步處理。

若要深入瞭解如何搭配 Azure Analysis Services 使用 REST Api，請參閱[使用 REST API 進行非同步](analysis-services-async-refresh.md)重新整理。

## <a name="authentication"></a>驗證

所有呼叫都必須使用有效的 Azure Active Directory （OAuth 2）權杖進行驗證。  本文中的範例將使用服務主體（SPN）向 Azure Analysis Services 進行驗證。 若要深入瞭解，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>設計邏輯應用程式

> [!IMPORTANT]
> 下列範例假設 Azure Analysis Services 防火牆已停用。 如果已啟用防火牆，則必須在 Azure Analysis Services 防火牆中將要求啟動器的公用 IP 位址列入允許清單。 若要深入瞭解每個區域 Azure Logic Apps IP 範圍，請參閱[Azure Logic Apps 的限制和設定資訊](../logic-apps/logic-apps-limits-and-config.md#configuration)。

### <a name="prerequisites"></a>Prerequisites

#### <a name="create-a-service-principal-spn"></a>建立服務主體（SPN）

若要瞭解如何建立服務主體，請參閱[使用 Azure 入口網站建立服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure Analysis Services 中設定許可權
 
您所建立的服務主體必須具有伺服器的伺服器管理員許可權。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>設定邏輯應用程式

在此範例中，邏輯應用程式是設計來在收到 HTTP 要求時觸發。 這將可讓您使用協調流程工具（例如 Azure Data Factory）來觸發 Azure Analysis Services 模型重新整理。

建立邏輯應用程式之後：

1. 在邏輯應用程式設計工具中，選擇**收到 HTTP 要求時**的第一個動作。

   ![新增 HTTP 收到的活動](./media/analysis-services-async-refresh-logic-app/1.png)

此步驟會在邏輯應用程式儲存後填入 HTTP POST URL。

2. 新增新的步驟並搜尋**HTTP**。  

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/9.png)

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/10.png)

3. 選取 [ **HTTP** ] 以新增此動作。

   ![新增 HTTP 活動](./media/analysis-services-async-refresh-logic-app/2.png)

設定 HTTP 活動，如下所示：

|屬性  |值  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | HTTPs://*您的伺服器區域*/servers/ *.aas 伺服器名稱*/models/*您的資料庫名稱*對/refreshes <br /> <br /> 例如： HTTPs：\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**標頭**     |   內容類型、應用程式/json <br /> <br />  ![headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**本文**     |   若要深入瞭解如何形成要求主體，請參閱[使用 REST API 張貼對/refreshes 的非同步](analysis-services-async-refresh.md#post-refreshes)重新整理。 |
|**驗證**     |Active Directory OAuth         |
|**出租**     |填入您的 Azure Active Directory TenantId         |
|**目標對象**     |HTTPs：//*. asazure. net         |
|**用戶端識別碼**     |輸入您的服務主體名稱 ClientID         |
|**認證類型**     |祕密         |
|**祕密**     |輸入您的服務主體名稱秘密         |

範例：

![完成的 HTTP 活動](./media/analysis-services-async-refresh-logic-app/7.png)

現在測試邏輯應用程式。  在邏輯應用程式設計工具中，按一下 [**執行**]。

![測試邏輯應用程式](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>使用邏輯應用程式搭配 Azure Data Factory

儲存邏輯應用程式之後，請檢查 [**收到 HTTP 要求時**] 活動，然後複製現在產生的**HTTP POST URL** 。  這是可由 Azure Data Factory 用來進行非同步呼叫以觸發邏輯應用程式的 URL。

以下是執行此動作 Azure Data Factory Web 活動的範例。

![Data Factory Web 活動](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用獨立的邏輯應用程式

如果您不打算使用協調流程工具（例如 Data Factory）來觸發模型重新整理，您可以將邏輯應用程式設定為根據排程觸發重新整理。

使用上述範例，刪除第一個活動，並將它取代為**排程**活動。

![排程活動](./media/analysis-services-async-refresh-logic-app/12.png)

![排程活動](./media/analysis-services-async-refresh-logic-app/13.png)

這個範例會使用**週期**。

新增活動之後，設定 [間隔] 和 [頻率]，然後新增參數並選擇 [**在這些小時**]。

![排程活動](./media/analysis-services-async-refresh-logic-app/16.png)

選取所需的時數。

![排程活動](./media/analysis-services-async-refresh-logic-app/15.png)

儲存邏輯應用程式。

## <a name="next-steps"></a>後續步驟

[範例](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
