---
title: 建立 Web 服務端點
titleSuffix: ML Studio (classic) - Azure
description: 在 Azure 機器學習工作室（經典）中創建 Web 服務終結點。 Web 服務的每個端點都是個別定址、節流以及管理。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 980ed1e54de30ec8a2dc0c1fdac6546d31f48a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218209"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>為已部署的 Azure 機器學習工作室（經典）Web 服務創建終結點

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> 本主題介紹適用于**經典**機器學習 Web 服務的技術。

部署 Web 服務之後，我們會建立該服務的預設端點。 該預設端點可使用其 API 金鑰進行呼叫。 您可以使用來自 Web 服務入口網站的其自有金鑰來新增更多端點。
Web 服務的每個端點都是個別定址、節流以及管理。 每個端點都是具有授權金鑰的唯一 URL，您可以將其散發給您的客戶。

## <a name="add-endpoints-to-a-web-service"></a>將端點新增至 Web 服務

您可以使用 Azure Machine Learning Web 服務入口網站將端點新增到 Web 服務。 建立端點之後，您就可以透過同步 API、批次 API 以及 Excel 工作表來取用該端點。

> [!NOTE]
> 如果您已在 Web 服務中新增額外的端點，就無法刪除預設端點。

1. 在機器學習工作室（經典）中，在左側導航列中，按一下 Web 服務。
2. 在 Web 服務儀表板的底部，按一下"**管理終結點**"。 Azure Machine Learning Web 服務 入口網站會開啟 Web 服務的端點頁面。
3. 按一下 **[新增]**。
4. 輸入新端點的名稱和描述。 端點名稱長度不可超過 24 個字元，而且必須由小寫字母或數字組成。 選取記錄層級，以及是否啟用範例資料。 有關日誌記錄的詳細資訊，請參閱[為機器學習 Web 服務啟用日誌記錄](web-services-logging.md)。

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> 透過新增額外端點來調整 Web 服務規模

根據預設，系統將每個發佈的 Web 服務設定為支援 20 個並行要求，而且最多可達 200 個並行要求。 Azure 機器學習工作室（經典版）自動優化設置，為 Web 服務提供最佳性能，並且忽略門戶值。

如果您打算以超過「並行呼叫數上限」值 200 可支援的負載來呼叫 API，則應該在相同的 Web 服務上建立多個端點。 然後，您就可以將負載隨機分配給所有端點。

調整 Web 服務規模一件常見的工作。 一些調整理由包括為了支援超過 200 個並行要求、透過多個端點提高可用性，或為 Web 服務提供個別的端點。 您可以通過[Azure 機器學習 Web 服務](https://services.azureml.net/)門戶為同一 Web 服務添加其他終結點來擴大規模。

請記住，如果您未以對應的高比例來呼叫 API，則使用較大的並行處理計數可能有害。 如果您將相對低的負載放在為高負載設定的 API，可能會看見延遲有零星的逾時及 (或) 突增情況。

通常在需要低度延遲的情況下，才會使用同步 API。 這裡所說的延遲意味著 API 完成一個要求所花費的時間，而未計入任何網路延遲的時間。 假設您有一個會延遲 50 毫秒的 API。 其節流層級為 [高] 且 [最大同時呼叫數目] 為 20，您必須每秒呼叫這個 API 20 * 1000 / 50 = 400 次，才能完全耗盡可用的容量。 再進一步延伸，假設會延遲 50 毫秒，則「並行呼叫數上限」200 可讓您每秒呼叫 API 4000 次。

## <a name="next-steps"></a>後續步驟

[如何使用 Azure 機器學習 Web 服務](consume-web-services.md)。
