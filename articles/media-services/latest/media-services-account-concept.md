---
title: 管理 Azure 媒體服務 v3 帳戶 |微軟文檔
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 本文介紹如何管理 Azure 媒體服務 v3 帳戶。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980370"
---
# <a name="manage-azure-media-services-v3-accounts"></a>管理 Azure 媒體服務 v3 帳戶

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 有關詳細資訊，請參閱[存儲帳戶](storage-account-concept.md)。

## <a name="moving-a-media-services-account-between-subscriptions"></a>在訂閱之間移動媒體服務帳戶 

如果需要將媒體服務帳戶移動到新訂閱，則需要首先將包含媒體服務帳戶的整個資源組移動到新訂閱。 必須移動所有附加資源：Azure 存儲帳戶、Azure CDN 設定檔等。有關詳細資訊，請參閱[將資源移動到新資源組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 與 Azure 中的任何資源一樣，資源組移動可能需要一些時間才能完成。

> [!NOTE]
> 媒體服務 v3 支援多租戶模型。

### <a name="considerations"></a>考量

* 在遷移到其他訂閱之前，創建帳戶中所有資料的備份。
* 您需要停止所有流式處理終結點和即時流式處理資源。 在資源組移動期間，您的使用者將無法訪問您的內容。 

> [!IMPORTANT]
> 在移動成功完成之前，不要啟動流式處理終結點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或關聯的 Azure 存儲帳戶在資源組移動後"斷開連接"，請嘗試旋轉存儲帳戶金鑰。 如果旋轉存儲帳戶金鑰無法解決媒體服務帳戶的"斷開連接"狀態，請從媒體服務帳戶中的"支援 + 故障排除"功能表提交新的支援請求。  

## <a name="next-steps"></a>後續步驟

[創建帳戶](create-account-cli-quickstart.md)
