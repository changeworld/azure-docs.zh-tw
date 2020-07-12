---
title: 使用用戶端憑證驗證來保護後端服務
titleSuffix: Azure API Management
description: 了解如何在 Azure API 管理中使用用戶端憑證驗證來保護後端服務。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 8e02a47cd6ae6e4883b5113b07d4049cd723232d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250187"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>如何在 Azure API 管理中使用用戶端憑證驗證來保護後端服務

API 管理可讓您使用用戶端憑證來保護對 API 後端服務的存取。 本指南說明如何在 Azure 入口網站中管理 Azure API 管理服務執行個體中的憑證。 此外也會說明如何設定 API 以使用憑證來存取後端服務。

如需使用 API 管理 REST API 來管理憑證的詳細資訊，請參閱 <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Azure API 管理 REST API 憑證實體</a>。

## <a name="prerequisites"></a><a name="prerequisites"> </a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

本指南將示範如何設定 API 管理服務執行個體，以使用用戶端憑證驗證來存取 API 的後端服務。 在遵循本文中的步驟之前，您應該先設定後端服務以進行用戶端憑證驗證 ([在 Azure App Service 中設定憑證驗證，請參閱這篇文章][to configure certificate authentication in Azure WebSites refer to this article]) 。 您必須存取憑證和密碼以將它上傳至 API 管理服務。

## <a name="upload-a-certificate"></a><a name="step1"> </a>上傳憑證

> [!NOTE]
> 您可以使用儲存在[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)服務中的憑證，而不是上傳的憑證，如下列[範例](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)所示。

![新增用戶端憑證](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

請依照下列步驟來上傳新的用戶端憑證。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][Create an API Management service instance]教學課程。

1. 在 Azure 入口網站中瀏覽至您的 Azure API 管理服務執行個體。
2. 從功能表中選取 [**憑證**]。
3. 按一下 [+新增]**** 按鈕。
    ![新增用戶端憑證](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. 瀏覽憑證，並提供其識別碼和密碼。
5. 按一下 [建立]。

> [!NOTE]
> 憑證必須是 **.pfx** 格式。 可接受自我簽署憑證。

憑證上傳後，它會顯示在 [**憑證**] 中。  如果您有許多憑證，請記下所需憑證的指紋，以[設定 API 以使用用戶端憑證來驗證閘道][Configure an API to use a client certificate for gateway authentication]。

> [!NOTE]
> 若要在使用自我簽署的憑證時關閉憑證鏈結驗證，請遵循此常見問題集[項目](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)中所述的步驟。

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>刪除用戶端憑證

若要刪除憑證，請按一下內容功能表的 **...**，然後選取憑證旁的 [刪除]****。

![刪除用戶端憑證](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

如果有 API 佔用憑證，系統會顯示警告畫面。 若要刪除憑證，您必須先從已設定為使用該憑證的 API 中將其移除。

![刪除用戶端憑證失敗](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>設定 API 以使用用戶端憑證來驗證閘道

1. 從左側的 [API 管理]**** 功能表按一下 [API]****，然後瀏覽至 API。
    ![啟用用戶端憑證](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. 在 [設計]**** 索引標籤中，按一下 [後端]**** 區段的鉛筆圖示。
3. 將 [閘道認證]**** 變更為 [用戶端憑證]****，並從下拉式清單中選取您的憑證。
    ![啟用用戶端憑證](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. 按一下 [儲存]。

> [!WARNING]
> 此變更將立即生效，且該 API 之作業的呼叫將使用憑證以在後端伺服器上進行驗證。


> [!TIP]
> 當您將憑證指定用於 API 後端服務的閘道驗證時，憑證遂成為該 API 之原則的一部分，因此可以在原則編輯器中檢視。

## <a name="self-signed-certificates"></a>自我簽署憑證

如果您使用自我簽署憑證，則需要停用信任鏈結驗證，API 管理才能與後端系統通訊。 否則將會傳回 500 錯誤碼。 若要進行此設定，您可以使用 [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) 新後端) 的 (，或 [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) 現有後端) PowerShell Cmdlet 的 (，並將 `-SkipCertificateChainValidation` 參數設定為 `True` 。

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
