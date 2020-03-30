---
title: 在 Azure 監視器中管理警報實例
description: 管理整個 Azure 中的警示執行個體
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667613"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>使用統一警報管理警報實例

借助 Azure 監視器中的[統一警報體驗](https://aka.ms/azure-alerts-overview)，您可以在 Azure 中查看所有不同類型的警報。 這將跨單個窗格中的多個訂閱。 本文演示如何查看警報實例，以及如何查找用於故障排除的特定警報實例。

> [!NOTE]
> 您只能訪問過去 30 天內生成的警報。

## <a name="go-to-the-alerts-page"></a>轉到警報頁面

您可以通過以下任何方式轉到警報頁：

- 在[Azure 門戶](https://portal.azure.com/)中，選擇 **"監視** > **警報**"。  

     ![監視器警報的螢幕截圖](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 使用特定資源的上下文。 打開資源，轉到 **"監視**"部分，然後選擇 **"警報**"。 對著陸頁進行預篩選，以查看該特定資源上的警報。

     ![資源監視警報的螢幕截圖](media/alerts-managing-alert-instances/alert-resource.JPG)

- 使用特定資源組的上下文。 打開資源組，轉到 **"監視**"部分，然後選擇 **"警報**"。 對著陸頁進行預篩選，以查看該特定資源組中的警報。    

     ![資源組監視警報的螢幕截圖](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>查找警報實例

"**警報摘要"** 頁概述了整個 Azure 中的所有警報實例。 您可以通過選擇**多個訂閱**（最多 5 個）或通過跨**資源組**、特定**資源**或**時間範圍**進行篩選來修改摘要視圖。 選擇 **"總警報**"或任何嚴重性範圍，以轉到警報的清單視圖。     

![警報摘要頁面的螢幕截圖](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
在"**所有警報"** 頁上，將列出 Azure 中的所有警報實例。 如果您是從警示通知來到入口網站，則可以使用提供的篩選條件，將範圍縮小至該特定警示執行個體。

> [!NOTE]
> 如果通過選擇任何嚴重性範圍來到該頁面，則對清單進行了該嚴重性的預篩選。

除了上一頁上可用的篩選器外，您還可以根據監視器服務（例如，指標平臺）、監視條件（已觸發或已解決）、嚴重性、警報狀態（新/確認/關閉）或智慧組 ID進行篩選。

![所有警報頁面的螢幕截圖](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 如果通過選擇任何嚴重性範圍來到該頁面，則對清單進行了該嚴重性的預篩選。

選擇任何警報實例將打開 **"警報詳細資訊"** 頁，從而查看有關該特定警報實例的更多詳細資訊。   

![警報詳細資訊頁面的螢幕截圖](media/alerts-managing-alert-instances/alert-details.jpg)  

