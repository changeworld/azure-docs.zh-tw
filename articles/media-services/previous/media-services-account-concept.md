---
title: 管理 Azure 媒體服務 v2 帳戶 |Microsoft Docs
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 本文說明如何管理 Azure 媒體服務 v2 帳戶。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75981936"
---
# <a name="manage-azure-media-services-v2-accounts"></a>管理 Azure 媒體服務 v2 帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶 

如果您需要將媒體服務帳戶移至新的訂用帳戶，您必須先將包含媒體服務帳戶的整個資源群組移至新的訂用帳戶。 您必須移動所有附加的資源： Azure 儲存體帳戶、Azure CDN 設定檔等。如需詳細資訊，請參閱[將資源移到新的資源群組或訂](../../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。 如同 Azure 中的任何資源，資源群組移動可能需要一些時間才能完成。

媒體服務 v2 不支援多租使用者模型。 如果您需要將媒體服務帳戶移至新租使用者中的訂用帳戶，請在新的租使用者中建立新的 Azure Active Directory （Azure AD）應用程式。 然後將您的帳戶移至新租使用者中的訂用帳戶。 租使用者移動完成後，您就可以開始使用新租使用者中的 Azure AD 應用程式，以使用 v2 Api 來存取媒體服務帳戶。 

> [!IMPORTANT]
> 您必須重設[Azure AD authentication](media-services-portal-get-started-with-aad.md)資訊，才能存取媒體服務 v2 API。  
### <a name="considerations"></a>考量

* 在遷移至不同的訂用帳戶之前，請先在您的帳戶中建立所有資料的備份。
* 您必須停止所有串流端點和即時串流資源。 您的使用者在資源群組移動期間將無法存取您的內容。 

> [!IMPORTANT]
> 在移動順利完成之前，請勿啟動串流端點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或相關聯的 Azure 儲存體帳戶在資源群組移動後變成「已中斷連線」，請嘗試輪替儲存體帳戶金鑰。 如果輪替儲存體帳戶金鑰無法解決媒體服務帳戶的「已中斷連線」狀態，請從媒體服務帳戶的 [支援 + 疑難排解] 功能表中，提出新的支援要求。  
 
## <a name="next-steps"></a>後續步驟

[建立帳戶](media-services-portal-create-account.md)
