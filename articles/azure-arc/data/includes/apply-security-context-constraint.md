---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696054"
---
本節說明如何將安全性內容條件約束套用 (SCC) 。 針對預覽版本，這些會放寬安全性限制。 

1.  (SCC) 下載自訂安全性內容條件約束。 您可以使用下列其中一項： 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   -  ([原始](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)) 
   - `curl` 下列命令會下載 arc 資料-yaml：

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. 建立 SCC。

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. 將 SCC 套用至服務帳戶。

   > [!NOTE]
   > 在這裡和下列命令中，使用相同的命名空間 `azdata arc dc create` 。 範例為 `arc` 。

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```