---
title: Azure 通訊服務的區域可用性和資料落地
description: 了解 Azure 通訊服務上的資料存放區和隱私權相關事項
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 10/03/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 6b8145666ab92432d01b2f039685afe5534f0958
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043564"
---
# <a name="region-availability-and-data-residency"></a>區域可用性與資料落地

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通訊服務致力於協助我們的客戶符合其隱私權和個人資料的需求。 身為使用應用程式與人類直接關係之通訊服務的開發人員，您扮演著控管資料的控制器角色。 因為 Azure 通訊服務會代表您儲存這項資料，我們就像是是此資料的處理器。 此頁面摘要說明服務如何保留資料，以及如何識別、匯出和刪除此資料。

## <a name="data-residency"></a>資料存留處

建立通訊服務資源時，您可以指定 **地理位置** (而非 Azure 資料中心)。 待用通訊服務所儲存的所有資料都會保留在該地理位置，位於通訊服務內部選取的資料中心。 不過，資料可能會在其他地理位置傳輸或處理，而這些全域端點可為使用者提供高效能、低延遲的體驗，而不需考慮其所在位置。

## <a name="relating-humans-to-azure-communication-services-identities"></a>將人類與 Azure 通訊服務身分識別相關聯

您的應用程式會管理人類使用者與通訊服務身分識別之間的關聯性。 要刪除個人使用者的資料時，必須刪除涉及使用者相互關聯之所有通訊服務身分識別的資料。

通訊服務資料分為兩種類別：
- **API 資料。** 這項資料是由通訊服務 API 所建立和管理，這是透過聊天 API 管理交談訊息的典型範例。
- **Azure 監視器記錄** 這項資料是由服務所建立，並透過 Azure 監視器資料平台管理。 此資料包含遙測和計量，可協助您了解通訊服務使用量。 這不是由通訊服務 API 管理。

## <a name="api-data"></a>API 資料

### <a name="identities"></a>Identities

Azure 通訊服務會維護一個身分識別目錄，使用 [DeleteIdentity](https://docs.microsoft.com/rest/api/communication/communicationidentity/delete) API 來加以移除。 刪除身分識別將會撤銷所有相關聯的存取權杖，並刪除其交談訊息。 如需如何移除身分識別的詳細資訊，[參閱此頁面](../quickstarts/access-tokens.md)。

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

透過通訊服務使用 Azure 入口網站或 Azure Resource Manager API，可以建立個人資料。 [使用此頁面了解如何管理 Azure Resource Manager 系統中的個人資料。](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-manager-personal-data)

### <a name="telephone-number-management"></a>電話號碼管理

Azure 通訊服務會維護與通訊服務資源相關聯的電話號碼目錄。 使用這些 API 來取出電話號碼並刪除：
- `Release Phone Number`

### <a name="chat"></a>聊天

聊天執行緒和訊息會一直保留到明確刪除為止。 30 天後，系統將會自動刪除完全閒置執行緒。 使用[聊天 API](https://docs.microsoft.com/rest/api/communication/chat/deletechatmessage/deletechatmessage) 來取得、列出、更新和刪除訊息。

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>sms

已傳送和接收的 SMS 訊息會由服務短暫處理，而不會保留。 

### <a name="pstn-voice-calling"></a>PSTN 語音通話

音訊和影片通訊是由服務短暫處理，而且不會在 Azure 監視器記錄的資源中保留任何資料。

### <a name="internet-voice-and-video-calling"></a>網際網路語音和視訊通話

音訊和影片通訊是由服務短暫處理，而且不會在 Azure 監視器記錄的資源中保留任何資料。

## <a name="azure-monitor-and-log-analytics"></a>Azure 監視器與 Log Analytics

Azure 通訊服務會提供 Azure 監視器記錄資料，以司解服務的操作健康情況和使用方式。 其中有些記錄包含通訊服務身分識別和電話號碼作為欄位資料。 若要刪除所有可能的個人資料，[請使用 Azure 監視器的這些程序](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)。 建議您設定 [Azure 監視器的預設保留期限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)。

## <a name="additional-resources"></a>其他資源

- [適用於 GDPR 和 CCPA 的 Azure 資料主體要求](https://docs.microsoft.com/microsoft-365/compliance/gdpr-dsr-azure?view=o365-worldwide&preserve-view=true)
- [Microsoft 信任中心](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure 互動式地圖 - 我的客戶資料在哪裡？](https://azuredatacentermap.azurewebsites.net/)