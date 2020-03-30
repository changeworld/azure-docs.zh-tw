---
title: 建立 Azure 應用程式閘道的自訂錯誤頁面
description: 本文說明如何建立應用程式閘道的自訂錯誤頁面。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129857"
---
# <a name="create-application-gateway-custom-error-pages"></a>建立應用程式閘道的自訂錯誤頁面

應用程式閘道可讓您建立自訂的錯誤頁面，而不是顯示預設的錯誤頁面。 您可以使用自訂錯誤頁面來搭配您自己的商標和版面配置。

例如，如果您的 Web 應用程式無法連線，您可以定義自己的維護頁面。 或者，如果有惡意要求傳送至 Web 應用程式，您可以建立未經授權的存取頁面。

自訂錯誤頁面可支援下列兩種案例：

- **維護頁面** - 此自訂錯誤頁面會取代「502 錯誤的閘道」頁面而傳送。 當應用程式閘道沒有後端可接受路由的流量時，就會顯示此頁面。 例如，在已有排程維護時，或在非預期的問題影響到後端集區存取時。
- **未經授權的存取** - 此自訂錯誤頁面會取代「403 未經授權的存取」頁面而傳送。 當應用程式閘道 WAF 偵測到惡意流量並加以封鎖時，就會顯示此頁面。

如果錯誤來自於後端伺服器，則會以未經修改的形式傳回至呼叫端。 此時不會顯示自訂錯誤頁面。 應用程式閘道可在要求無法送達後端時顯示自訂錯誤頁面。

自訂錯誤頁面可定義於全域層級和接聽程式層級上：

- **全域層級** - 錯誤頁面會套用至部署在該應用程式閘道上的所有 Web 應用程式的流量。
- **接聽程式層級** - 錯誤頁面會套用至該接聽程式所接收的流量。
- **兩者** - 定義於接聽程式層級上的自訂錯誤頁面會覆寫在全域層級上設定的頁面。

若要建立自訂錯誤頁面，您必須具備：

- HTTP 回應狀態碼。
- 錯誤頁面的對應位置。 
- 可公開存取的 Azure 儲存體 Blob 位置。
- *.htm 或 *.html 副檔名類型。 

錯誤頁面的大小必須小於 1 MB。 錯誤頁面中如果有連結的影像，這些影像必須是可公開存取的絕對 URL，或是內嵌在自訂錯誤頁面中的 base64 編碼影像。 目前不支援在相同的 Blob 位置中包含影像的相對連結。 

在您指定錯誤頁面之後，應用程式閘道會從儲存體 Blob 位置下載該頁面，並將其儲存至本機應用程式閘道快取。 其後，錯誤頁面將會直接從應用程式閘道提供。 若要修改現有的自訂錯誤頁面，您必須在應用程式閘道設定中指向不同的 Blob 位置。 應用程式閘道不會定期檢查 Blob 位置以提取新版本。

## <a name="portal-configuration"></a>入口網站設定

1. 在入口網站中瀏覽至 [應用程式閘道]，然後選擇應用程式閘道。

    ![ag-overview](media/custom-error/ag-overview.png)
2. 按一下 [接聽程式]****，然後瀏覽至您要指定錯誤頁面的特定接聽程式。

    ![應用程式閘道接聽程式](media/custom-error/ag-listener.png)
3. 在接聽程式層級上設定 403 WAF 錯誤的自訂錯誤頁面或 502 維護頁面。

    > [!NOTE]
    > 目前不支援從 Azure 入口網站建立全域層級的自訂錯誤頁面。

4. 為給定的錯誤狀態碼指定可公開存取的 Blob URL，然後按一下 [儲存]****。 應用程式閘道現在已設定自訂錯誤頁面。

   ![應用程式閘道錯誤碼](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell 組態

您可以使用 Azure PowerShell 設定自訂錯誤頁面。 例如，全域自訂錯誤頁面：

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

或接聽程式層級的錯誤頁面：

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

如需詳細資訊，請參閱 [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) 和 [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0)。

## <a name="next-steps"></a>後續步驟

如需應用程式閘道診斷的相關資訊，請參閱[應用程式閘道的後端健康情況、診斷記錄和計量](application-gateway-diagnostics.md)。