---
title: 管理 Azure 監視器中的警示實例
description: 管理整個 Azure 中的警示執行個體
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102865"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>使用統一警示管理警示實例

透過 Azure 監視器中的 [整合警示體驗](./alerts-overview.md) ，您可以在 Azure 中查看所有不同類型的警示。 這會跨越單一窗格中的多個訂用帳戶。 本文說明如何查看您的警示實例，以及如何尋找特定的警示實例以進行疑難排解。

> [!NOTE]
> 您只能存取過去30天內產生的警示。

## <a name="go-to-the-alerts-page"></a>移至 [警示] 頁面

您可以利用下列任何一種方式移至 [警示] 頁面：

- 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [**監視**  >  **警示**]。  

     ![監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 使用特定資源的內容。 開啟資源，移至 [ **監視** ] 區段，然後選擇 [ **警示**]。 登陸頁面會預先篩選該特定資源上的警示。

     ![資源監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-resource.JPG)

- 使用特定資源群組的內容。 開啟資源群組，移至 [ **監視** ] 區段，然後選擇 [ **警示**]。 登陸頁面會預先篩選該特定資源群組上的警示。    

     ![資源群組監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>尋找警示實例

[ **警示摘要** ] 頁面可讓您瞭解整個 Azure 中的所有警示實例。 您可以選取 **多個** 訂用帳戶 (多個訂用帳戶，最多5個) ，或透過跨 **資源群組**、特定 **資源**或 **時間範圍**進行篩選，來修改摘要視圖。 選取 **警示的總計**或任何嚴重性區段，以移至您的警示的清單視圖。     

![警示摘要頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
在 [ **所有警示** ] 頁面上，會列出 Azure 上的所有警示實例。 如果您是從警示通知來到入口網站，則可以使用提供的篩選條件，將範圍縮小至該特定警示執行個體。

> [!NOTE]
> 如果您選取任何一個嚴重性區段來進入頁面，系統就會針對該嚴重性預先篩選該清單。

除了上一頁提供的篩選之外，您還可以根據監視服務的基礎進行篩選 (例如，[計量的平臺]) 、[監視條件] ([已引發] 或 [已解決]) 、[嚴重性]、[警示狀態] ([新增/認可/關閉) ] 或 [智慧群組識別碼]。

![[所有警示] 頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 如果您選取任何一個嚴重性區段來進入頁面，系統就會針對該嚴重性預先篩選該清單。

選取任何警示實例會開啟 [ **警示詳細資料** ] 頁面，可讓您查看更多有關該特定警示實例的詳細資料。   

![警示詳細資料頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-details.jpg)