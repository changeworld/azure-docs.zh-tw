---
title: 在 Azure API for FHIR 中設定跨原始來源資源分享
description: 本文說明如何在 Azure API for FHIR 中設定跨原始來源資源分享。
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84870938"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中設定跨原始來源資源分享

適用于快速健康照護互通資源的 Azure API （FHIR）支援[跨原始來源資源分享（CORS）](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)。 CORS 可讓您設定設定，讓一個網域（原始）中的應用程式可以從不同的網域（稱為跨網域要求）存取資源。

CORS 通常用於單一頁面應用程式，必須將 RESTful API 呼叫至不同的網域。

若要在 Azure API for FHIR 中設定 CORS 設定，請指定下列設定：

- **來源（存取控制-允許-來源）**。 允許對 Azure API for FHIR 進行跨原始來源要求的網域清單。 每個網域（原點）都必須在不同的行中輸入。 您可以輸入星號（*）以允許來自任何網域的呼叫，但我們不建議這麼做，因為這是安全性風險。

- **標頭（存取控制-允許-標頭）**。 原始要求將包含的標頭清單。 若要允許所有標頭，請輸入星號（*）。

- **方法（存取控制-允許-方法）**。 API 呼叫中的允許方法（PUT、GET、POST 等等）。 針對 [所有方法] 選擇 [全**選**]。

- **最長存留期（存取控制-最大壽命）**。 以秒為單位的值，可快取存取控制-允許標頭和存取控制允許方法的預檢要求結果。

- **允許認證（存取控制-允許-認證）**。 CORS 要求通常不會包含 cookie 來防止[跨網站偽造要求（CSRF）](https://en.wikipedia.org/wiki/Cross-site_request_forgery)攻擊。 如果您選取此設定，就可以提出要求以包含認證，例如 cookie。 如果您已經設定具有星號（*）的原始來源，就無法進行此設定。

![跨原始來源資源分享（CORS）設定](media/cors/cors.png)

>[!NOTE]
>您不能針對不同的網域來源指定不同的設定。 所有設定（**標頭**、**方法**、**最大存留期**和**允許認證**）會套用至 [來源] 設定中指定的所有來源。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure API for FHIR 中設定跨原始來源共用。 接下來，部署完全受控的 Azure API for FHIR：
 
>[!div class="nextstepaction"]
>[部署 Azure API for FHIR](fhir-paas-portal-quickstart.md)