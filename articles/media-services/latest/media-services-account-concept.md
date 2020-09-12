---
title: 管理 Azure 媒體服務 v3 帳戶 |Microsoft Docs
description: 若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 本文說明如何管理 Azure 媒體服務 v3 帳戶。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 7c2cbaaf4866edc20f9745abe6759861db7834c3
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291325"
---
# <a name="manage-azure-media-services-v3-accounts"></a>管理 Azure 媒體服務 v3 帳戶

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要在 Azure 中開始管理、加密、編碼、分析和串流處理媒體內容，您需要建立媒體服務帳戶。 建立媒體服務帳戶時，您需要提供 Azure 儲存體帳戶資源的名稱。 指定的儲存體帳戶會附加到您的媒體服務帳戶。 媒體服務帳戶和所有相關聯的儲存體帳戶必須位於相同的 Azure 訂用帳戶中。 如需詳細資訊，請參閱 [儲存體帳戶](storage-account-concept.md)。

## <a name="moving-a-media-services-account-between-subscriptions"></a>在訂用帳戶之間移動媒體服務帳戶 

如果您需要將媒體服務帳戶移至新的訂用帳戶，您必須先將包含媒體服務帳戶的整個資源群組移至新的訂用帳戶。 您必須移動所有附加的資源： Azure 儲存體帳戶、Azure CDN 設定檔等。如需詳細資訊，請參閱 [將資源移至新的資源群組或訂](../../azure-resource-manager/management/move-resource-group-and-subscription.md)用帳戶。 如同 Azure 中的任何資源，資源群組移動可能需要一些時間才能完成。

> [!NOTE]
> 媒體服務 v3 支援多租使用者模型。

### <a name="considerations"></a>考量

* 在遷移至不同的訂用帳戶之前，請先建立您帳戶中所有資料的備份。
* 您必須停止所有串流端點和即時串流資源。 在資源群組移動期間，您的使用者將無法存取您的內容。 

> [!IMPORTANT]
> 除非移動成功完成，否則請勿啟動串流端點。

### <a name="troubleshoot"></a>疑難排解 

如果媒體服務帳戶或相關聯的 Azure 儲存體帳戶在資源群組移動之後變成「已中斷連線」，請嘗試輪替儲存體帳戶金鑰。 如果輪替儲存體帳戶金鑰無法解析媒體服務帳戶的「已中斷連線」狀態，請從媒體服務帳戶中的 [支援 + 疑難排解] 功能表提出新的支援要求。  

## <a name="next-steps"></a>後續步驟

[建立帳戶](./create-account-howto.md)
