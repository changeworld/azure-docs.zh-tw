---
title: 使用 Azure 分析服務模型的邏輯應用刷新 |微軟文檔
description: 本文介紹如何使用 Azure 邏輯應用為 Azure 分析服務編寫非同步刷新代碼。
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126970"
---
# <a name="refresh-with-logic-apps"></a>使用 Logic Apps 重新整理

通過使用邏輯應用和 REST 調用，可以在 Azure 分析方格模型上執行自動資料刷新操作，包括查詢橫向擴展的唯讀副本的同步。

要瞭解有關將 REST API 與 Azure 分析服務一起使用的更多資訊，請參閱[使用 REST API 進行非同步刷新](analysis-services-async-refresh.md)。

## <a name="authentication"></a>驗證

所有調用都必須使用有效的 Azure 活動目錄 （OAuth 2） 權杖進行身份驗證。  本文中的示例將使用服務主體 （SPN） 對 Azure 分析服務進行身份驗證。 要瞭解詳細資訊，請參閱[使用 Azure 門戶創建服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="design-the-logic-app"></a>設計邏輯應用

> [!IMPORTANT]
> 以下示例假定 Azure 分析服務防火牆已禁用。 如果啟用了防火牆，則必須在 Azure 分析服務防火牆中白名單請求開始器的公共 IP 位址。 要瞭解有關每個區域的 Azure 邏輯應用 IP 範圍的詳細資訊，請參閱[Azure 邏輯應用的限制和配置資訊](../logic-apps/logic-apps-limits-and-config.md#configuration)。

### <a name="prerequisites"></a>Prerequisites

#### <a name="create-a-service-principal-spn"></a>創建服務主體 （SPN）

要瞭解如何創建服務主體，請參閱使用[Azure 門戶創建服務主體](../active-directory/develop/howto-create-service-principal-portal.md)。

#### <a name="configure-permissions-in-azure-analysis-services"></a>在 Azure 分析服務中配置許可權
 
您創建的服務主體必須具有伺服器上的伺服器管理員許可權。 若要深入了解，請參閱[將服務主體新增至伺服器管理員角色](analysis-services-addservprinc-admins.md)。

### <a name="configure-the-logic-app"></a>配置邏輯應用

在此示例中，邏輯應用旨在在收到 HTTP 要求時觸發。 這將啟用使用業務流程工具（如 Azure 資料工廠）來觸發 Azure 分析服務模型刷新。

創建邏輯應用後：

1. 在邏輯應用設計器中，選擇第**一個操作作為"何時收到 HTTP 要求**"。

   ![添加 HTTP 接收的活動](./media/analysis-services-async-refresh-logic-app/1.png)

保存邏輯應用後，此步驟將使用 HTTP POST URL 填充。

2. 添加新步驟並搜索**HTTP**。  

   ![添加 HTTP 活動](./media/analysis-services-async-refresh-logic-app/9.png)

   ![添加 HTTP 活動](./media/analysis-services-async-refresh-logic-app/10.png)

3. 選擇**HTTP**以添加此操作。

   ![添加 HTTP 活動](./media/analysis-services-async-refresh-logic-app/2.png)

按如下方式配置 HTTP 活動：

|屬性  |值  |
|---------|---------|
|**方法**     |POST         |
|**URI**     | HTTPs://*伺服器區域*/伺服器 */as 伺服器名稱*/模型/*資料庫名稱*/刷新 <br /> <br /> 例如： HTTPs：\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**標頭**     |   內容類型，應用程式/json <br /> <br />  ![headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**內文**     |   要瞭解有關形成請求正文的更多資訊，請參閱使用[REST API - POST /refresh 的非同步刷新](analysis-services-async-refresh.md#post-refreshes)。 |
|[驗證]****     |Active Directory OAuth         |
|**租戶**     |填寫 Azure 活動目錄租戶 Id         |
|**觀眾**     |HTTPs：//.asazure.windows.net         |
|**用戶端 ID**     |輸入服務主體名稱用戶端 ID         |
|**憑據類型**     |祕密         |
|**秘密**     |輸入服務主體名稱機密         |

範例：

![已完成的 HTTP 活動](./media/analysis-services-async-refresh-logic-app/7.png)

現在測試邏輯應用。  在邏輯應用設計器中，按一下"**運行**"。

![測試邏輯應用程式](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>使用 Azure 資料工廠的邏輯應用

保存邏輯應用後，查看**何時收到 HTTP 要求**的活動，然後複製現在生成的 HTTP POST **URL。**  這是 Azure 資料工廠可用於進行非同步調用以觸發邏輯應用的 URL。

下面是執行此操作的 Azure 資料工廠 Web 活動示例。

![資料工廠 Web 活動](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>使用自包含的邏輯應用

如果您不打算使用業務流程工具（如資料工廠）來觸發模型刷新，則可以設置邏輯應用以基於計畫觸發刷新。

使用上面的示例，刪除第一個活動並將其替換為**計畫**活動。

![計畫活動](./media/analysis-services-async-refresh-logic-app/12.png)

![計畫活動](./media/analysis-services-async-refresh-logic-app/13.png)

此示例將使用 **"重複"。**

添加活動後，配置間隔和頻率，然後添加新參數，然後選擇 **"在這些時間**"。

![計畫活動](./media/analysis-services-async-refresh-logic-app/16.png)

選擇想要的小時數。

![計畫活動](./media/analysis-services-async-refresh-logic-app/15.png)

保存邏輯應用。

## <a name="next-steps"></a>後續步驟

[樣品](analysis-services-samples.md)  
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)
