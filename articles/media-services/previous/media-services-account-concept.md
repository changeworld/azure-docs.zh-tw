---
title: 管理 Azure 媒體服務 v2 帳戶 |微軟文檔
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 本文介紹如何管理 Azure 媒體服務 v2 帳戶。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981936"
---
# <a name="manage-azure-media-services-v2-accounts"></a>管理 Azure 媒體服務 v2 帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶 

如果需要將媒體服務帳戶移動到新訂閱，則需要首先將包含媒體服務帳戶的整個資源組移動到新訂閱。 必須移動所有附加資源：Azure 存儲帳戶、Azure CDN 設定檔等。有關詳細資訊，請參閱[將資源移動到新資源組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 與 Azure 中的任何資源一樣，資源組移動可能需要一些時間才能完成。

媒體服務 v2 不支援多租戶模型。 如果需要將媒體服務帳戶移動到新租戶中的訂閱，請在新租戶中創建新的 Azure 活動目錄 （Azure AD） 應用程式。 然後，將您的帳戶移動到新租戶中的訂閱。 租戶移動完成後，可以使用新租戶的 Azure AD 應用程式使用 v2 API 訪問媒體服務帳戶。 

> [!IMPORTANT]
> 您需要重置 Azure [AD 身份驗證](media-services-portal-get-started-with-aad.md)資訊才能訪問媒體服務 v2 API。  
### <a name="considerations"></a>考量

* 在遷移到其他訂閱之前，創建帳戶中所有資料的備份。
* 您需要停止所有流式處理終結點和即時流式處理資源。 在資源組移動期間，您的使用者將無法訪問您的內容。 

> [!IMPORTANT]
> 在移動成功完成之前，不要啟動流式處理終結點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或關聯的 Azure 存儲帳戶在資源組移動後"斷開連接"，請嘗試旋轉存儲帳戶金鑰。 如果旋轉存儲帳戶金鑰無法解決媒體服務帳戶的"斷開連接"狀態，請從媒體服務帳戶中的"支援 + 故障排除"功能表提交新的支援請求。  
 
## <a name="next-steps"></a>後續步驟

[創建帳戶](media-services-portal-create-account.md)
