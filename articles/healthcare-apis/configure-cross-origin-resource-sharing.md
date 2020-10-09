---
title: 在 Azure API for FHIR 中設定跨原始來源資源分享
description: 本文說明如何在 Azure API for FHIR 中設定跨原始來源的資源分享。
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84870938"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中設定跨原始來源資源分享

適用于快速健康照護互通資源 (FHIR) 的 Azure API 支援 [跨原始來源資源分享 (CORS) ](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 CORS 可讓您進行設定，讓某個網域 (來源) 的應用程式可以從不同的網域（稱為跨網域要求）存取資源。

CORS 通常用於必須將 RESTful API 呼叫至不同網域的單一頁面應用程式。

若要在 Azure API for FHIR 中設定 CORS 設定，請指定下列設定：

- **來源 (存取控制-允許來源) **。 允許對 Azure API for FHIR 進行跨原始來源要求的網域清單。 您必須在個別的行中輸入每個網域 (源) 。 您可以輸入星號 ( * ) 來允許來自任何網域的呼叫，但我們不建議您這麼做，因為它有安全性風險。

- **標頭 (存取控制-允許標頭) **。 來源要求將包含的標頭清單。 若要允許所有標頭，請輸入星號 ( * ) 。

- **方法 (存取控制-允許方法) **。 在 API 呼叫中，允許的方法 (PUT、GET、POST 等) 。 針對所有方法選擇 [全 **選** ]。

- **最長使用期限 (存取控制-最大壽命) **。 快取預檢要求結果的值（以秒為單位），適用于存取控制-允許標頭和存取控制-允許方法。

- **允許認證 (存取控制-允許認證) **。 CORS 要求通常不包含 cookie，以防止 [跨網站偽造要求 (CSRF) ](https://en.wikipedia.org/wiki/Cross-site_request_forgery) 攻擊。 如果您選取此設定，則可提出要求以包含認證，例如 cookie。 如果您已經設定具有星號的來源 ( * ) ，就無法設定此設定。

![跨原始資源分享 (CORS) 設定](media/cors/cors.png)

>[!NOTE]
>您無法為不同的網域來源指定不同的設定。 所有設定 (**標頭**、 **方法**、 **最大壽命**和 **允許認證**) 套用至 [來源] 設定中指定的所有來源。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure API for FHIR 中設定跨原始來源共用。 接下來，部署完全受控的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)