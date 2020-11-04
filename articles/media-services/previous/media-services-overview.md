---
title: Azure 媒體服務概觀 | Microsoft Docs
description: Microsoft Azure 媒體服務是一個可延伸的雲端型平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。 本文提供 Azure 媒體服務的總覽。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.openlocfilehash: c6f889963e87d900040d1fe77031f943ab368837
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348739"
---
# <a name="azure-media-services-overview"></a>Azure 媒體服務概觀

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="選取您要使用的媒體服務版本："]
> * [第 3 版](../latest/media-services-overview.md)
> * [第 2 版](media-services-overview.md)

> [!NOTE]
> 媒體服務 v2 中不會新增任何新功能。 <br/>查看最新版本的[媒體服務 v3](../latest/index.yml)。 另請參閱[從 v2 變更為 v3 的移轉指導方針](../latest/migrate-from-v2-to-v3.md)

Microsoft Azure 媒體服務 (AMS) 是一個可延伸的雲端式平台，供開發人員建置可擴充的媒體管理和傳遞應用程式。 媒體服務是以 REST API 為基礎，可讓您安全地上傳、儲存、編碼和封裝視訊或音訊內容，以用於隨選和即時資料流傳遞給各種用戶端 (例如電視、電腦和行動裝置)。

您可以建置完全採用媒體服務的端對端工作流程。 您也可以選擇在工作流程的某些部分採用第三方元件。 例如，使用第三方編碼器來進行編碼； 然後使用媒體服務上傳、保護、封裝、傳遞。 您可以選擇即時串流您的內容或隨選傳遞內容。 


## <a name="compliance-privacy-and-security"></a>合規性、隱私權和安全性

重要提醒是，在使用 Azure 媒體服務時，您必須遵守所有適用的法律，且您不得以違反他人權利或可能會對他人有害的方式使用「媒體服務」或任何 Azure 服務。

將任何影片/影像上傳至「媒體服務」之前，您必須擁有使用該影片/影像的所有適當權限，包括依據法律要求須徵得影片/影像中的個人 (如果有的話) 所有必要的同意，方可在「媒體服務」和 Azure 中使用、處理和儲存其資料。 有些司法管轄區可能會對特定資料類別 (例如生物識別資料) 的收集、線上處理和儲存施加特殊的法律要求。 使用「媒體服務」和 Azure 來處理及儲存任何受到特殊法律要求約束的資料之前，您必須確定已符合您可能適用的任何此類法律要求。

若要了解「媒體服務」中的合規性、隱私權和安全性，請造訪 Microsoft [信任中心](https://www.microsoft.com/trust-center/?rtc=1)。 如需 Microsoft 的隱私權義務、資料處理和保留實務（包括如何刪除您的資料），請參閱 Microsoft 的 [隱私權聲明](https://privacy.microsoft.com/PrivacyStatement)、 [線上服務條款](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ( 「OST」 ) 和 [資料處理](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) 增補 ( 「DPA」 ) 。 一旦使用「媒體服務」，即表示您同意受到 OST、DPA 和隱私權聲明的規範。
 
## <a name="prerequisites"></a>先決條件

若要開始使用 Azure 媒體服務，您應該具備下列項目：

* 一個 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com)。
* Azure 媒體服務帳戶。 如需詳細資訊，請參閱[建立帳戶](media-services-portal-create-account.md)。
* (選擇性) 設定開發環境。 針對開發環境選擇 .NET 或 REST API。 如需詳細資訊，請參閱 [設定環境](media-services-dotnet-how-to-use.md)。

    此外，請了解如何[以程式設計方式連線至 AMS API](media-services-use-aad-auth-to-access-ams-api.md)。
* 已啟動狀態的標準或進階串流端點。  如需詳細資訊，請參閱[管理串流端點](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK 及工具

若要建置媒體服務解決方案，您可以使用：

* [媒體服務 REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* 其中一個可用的用戶端 SDK：
    * 適用于 .NET 的 Azure 媒體服務 SDK
    
        * [Nuget 套件](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub 原始程式碼](https://github.com/Azure/azure-sdk-for-media-services)
    * [適用于 JAVA 的 AZURE SDK](https://github.com/Azure/azure-sdk-for-java)、
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)、
    * [Azure Media Services for Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (這是非 Microsoft 版本的 Node.js SDK。 它是由社群維護，且目前並沒有 AMS API 的 100% 涵蓋範圍)。
* 現有的工具：
    * [Azure 入口網站](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure 媒體服務總管 (AMSE) 是適用於 Windows 的 Winforms/C# 應用程式)

> [!NOTE]
> 若要取得最新的 Java SDK 版本並開始使用 Java 進行開發，請參閱[開始使用適用於媒體服務的 Java 用戶端 SDK ](./media-services-java-how-to-use.md)。 <br/>
> 若要下載適用於媒體服務的最新 PHP SDK，請在 [Packagist 存放庫](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)中尋找 0.5.7 版的 Microsoft/WindowAzure 套件。  

## <a name="code-samples"></a>程式碼範例

在 **Azure 程式碼範例** 資源庫中找到多個程式碼範例： [Azure 媒體服務程式碼範例](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0)。

## <a name="concepts"></a>概念

如需 Azure 媒體服務概念，請參閱 [概念](media-services-concepts.md)。

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>跨資料中心支援的媒體服務情節和可用性

如需 Azure 一般案例的詳細資訊，請參閱 [AMS 案例](scenarios-and-availability.md)。
如需區域可用性的詳細資訊，請參閱 [媒體服務可用性](availability-regions-v-2.md)。

## <a name="service-level-agreement-sla"></a>服務等級協定 (SLA)

如需詳細資訊，請參閱 [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)。

## <a name="support"></a>支援

[Azure 支援](https://azure.microsoft.com/support/options/) 提供 Azure 的支援選項，包括媒體服務。

## <a name="provide-feedback"></a>提供意見反應

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
