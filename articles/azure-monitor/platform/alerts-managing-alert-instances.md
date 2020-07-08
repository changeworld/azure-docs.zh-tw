---
title: 管理 Azure 監視器中的警示實例
description: 管理整個 Azure 中的警示執行個體
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77667613"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>使用整合警示管理警示實例

透過 Azure 監視器中的[整合警示體驗](https://aka.ms/azure-alerts-overview)，您可以在 Azure 中查看所有不同類型的警示。 這會跨越單一窗格中的多個訂用帳戶。 本文說明如何查看您的警示實例，以及如何尋找特定的警示實例以進行疑難排解。

> [!NOTE]
> 您只能存取過去30天內產生的警示。

## <a name="go-to-the-alerts-page"></a>移至 [警示] 頁面

您可以透過下列任何方式移至 [警示] 頁面：

- 在 [ [Azure 入口網站](https://portal.azure.com/)中，選取 [**監視**] [  >  **警示**]。  

     ![監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- 使用特定資源的內容。 開啟資源，移至 [**監視**] 區段，然後選擇 [**警示**]。 已針對該特定資源的警示預先篩選登陸頁面。

     ![資源監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-resource.JPG)

- 使用特定資源群組的內容。 開啟資源群組，移至 [**監視**] 區段，然後選擇 [**警示**]。 已針對該特定資源群組的警示預先篩選登陸頁面。    

     ![資源群組監視警示的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>尋找警示實例

[**警示摘要**] 頁面可讓您大致瞭解 Azure 中的所有警示實例。 您可以選取**多個**訂用帳戶（最多5個），或跨**資源群組**、特定**資源**或**時間範圍**進行篩選，以修改摘要視圖。 選取 [**警示總計**] 或任何嚴重性群組，以移至警示的清單視圖。     

![警示摘要頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
在 [**所有警示**] 頁面上，會列出 Azure 上的所有警示實例。 如果您是從警示通知來到入口網站，則可以使用提供的篩選條件，將範圍縮小至該特定警示執行個體。

> [!NOTE]
> 如果您藉由選取任何嚴重性區段來進入頁面，則會針對該嚴重性預先篩選清單。

除了上一頁提供的篩選之外，您也可以根據監視服務（例如，適用于計量的平臺）、監視條件（已引發或已解決）、嚴重性、警示狀態（新的/已認可/已關閉）或智慧群組識別碼來進行篩選。

![[所有警示] 頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> 如果您藉由選取任何嚴重性區段來進入頁面，則會針對該嚴重性預先篩選清單。

選取任何警示實例會開啟 [**警示詳細資料**] 頁面，讓您可以查看有關該特定警示實例的更多詳細資料。   

![[警示詳細資料] 頁面的螢幕擷取畫面](media/alerts-managing-alert-instances/alert-details.jpg)  

