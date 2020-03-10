---
title: Azure API 管理的頁面控制項 | Microsoft Docs
description: 了解在 Azure API 管理的開發人員入口網站範本中可使用的頁面控制項。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374463"
---
# <a name="azure-api-management-page-controls"></a>Azure API 管理的頁面控制項
Azure API 管理提供下列可在開發人員入口網站範本中使用的控制項。  
  
若要使用控制項，請將它放在開發人員入口網站範本中所要的位置。 某些控制項 (例如 [app-actions](#app-actions) 控制項) 具有參數，如下列範例所示：  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
參數的值會以範本資料模型組件的形式來傳入。 大部分情況下，您只要在針對每個控制項所提供的範例中貼上，即可讓其正確運作。 如需參數值的詳細資訊，請參閱控制項可能使用所在之各個範本的資料模型區段。  

如需有關使用範本的詳細資訊，請參閱[如何使用範本自訂 API 管理開發人員入口網站](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>開發人員入口網站範本頁面控制項  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [提供者](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a>app-actions  
 `app-actions` 控制項會提供使用者介面，以便與開發人員入口網站中 [使用者設定檔] 頁面上的應用程式互動。  
  
 ![應用&#45;程式動作控制](./media/api-management-page-controls/APIM-app-actions-control.png "APIM 應用程式動作控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>參數  
  
|參數|描述|  
|---------------|-----------------|  
|appId|應用程式的識別碼。|  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `app-actions` 控制項可用於下列開發人員入口網站範本：  
  
-   [應用程式](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>basic-signin  
 `basic-signin` 控制項會提供用來在開發人員入口網站的登入頁面中收集使用者登入資訊的控制項。  
  
 ![基本&#45;登入控制](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM 基本-登錄控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `basic-signin` 控制項可用於下列開發人員入口網站範本：  
  
-   [登入](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>paging-control  
 `paging-control` 會在顯示項目清單的開發人員入口網站頁面上提供分頁功能。  
  
 ![分頁控制項](./media/api-management-page-controls/APIM-paging-control.png "APIM 分頁控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `paging-control` 控制項可用於下列開發人員入口網站範本：  
  
-   [API 清單](api-management-api-templates.md#APIList)  
  
-   [問題清單](api-management-issue-templates.md#IssueList)  
  
-   [產品清單](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>providers  
 `providers` 控制項會提供用來在開發人員入口網站的登入頁面中選取驗證提供者的控制項。  
  
 ![提供者控制項](./media/api-management-page-controls/APIM-providers-control.png "APIM 提供者控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `providers` 控制項可用於下列開發人員入口網站範本：  
  
-   [登入](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>search-control  
 `search-control` 會在顯示項目清單的開發人員入口網站頁面上提供搜尋功能。  
  
 ![搜尋控制項](./media/api-management-page-controls/APIM-search-control.png "APIM 搜尋控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `search-control` 控制項可用於下列開發人員入口網站範本：  
  
-   [API 清單](api-management-api-templates.md#APIList)  
  
-   [產品清單](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>sign-up  
 `sign-up` 控制項會提供用來在開發人員入口網站的註冊頁面中收集使用者設定檔資訊的控制項。  
  
 ![註冊&#45;控制項](./media/api-management-page-controls/APIM-sign-up-control.png "APIM 註冊控制")  
  
### <a name="usage"></a>使用量  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `sign-up` 控制項可用於下列開發人員入口網站範本：  
  
-   [註冊](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>subscribe-button  
 `subscribe-button` 會提供用來為使用者訂閱產品的控制項。  
  
 ![訂閱&#45;按鈕控制項](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM 訂閱按鈕控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>參數  
 無。  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `subscribe-button` 控制項可用於下列開發人員入口網站範本：  
  
-   [產品](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>subscription-cancel  
 `subscription-cancel` 控制項會提供可在開發人員入口網站的 [使用者設定檔] 頁面中取消產品訂閱的控制項。  
  
 ![訂&#45;用帳戶取消控制](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM 訂用帳戶-取消控制項")  
  
### <a name="usage"></a>使用量  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>參數  
  
|參數|描述|  
|---------------|-----------------|  
|subscriptionId|要取消之訂用帳戶的識別碼。|  
|cancelUrl|訂用帳戶取消了 URL。|  
  
### <a name="developer-portal-templates"></a>開發人員入口網站範本  
 `subscription-cancel` 控制項可用於下列開發人員入口網站範本：  
  
-   [產品](api-management-product-templates.md#Product)

## <a name="next-steps"></a>後續步驟
如需有關使用範本的詳細資訊，請參閱[如何使用範本自訂 API 管理開發人員入口網站](api-management-developer-portal-templates.md)。
