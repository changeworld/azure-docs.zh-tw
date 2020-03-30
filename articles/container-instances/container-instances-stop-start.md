---
title: 手動停止或啟動容器組
description: 瞭解如何在 Azure 容器實例中手動停止或啟動容器組。
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533431"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>在 Azure 容器實例中手動停止或啟動容器

容器組的[重新開機策略](container-instances-restart-policy.md)設置確定容器實例在預設情況下如何啟動或停止。 您可以通過手動停止或啟動容器組來覆蓋預設設置。

## <a name="stop"></a>Stop

手動停止正在運行的容器組 - 例如，通過使用 az[容器停止][az-container-stop]命令或 Azure 門戶。 對於某些容器工作負載，您可能希望在定義的時間段後停止長時間運行的容器組以節省成本。 

*當容器組進入"已停止"狀態時，它將終止和回收組中的所有容器。它不保留容器狀態。*

回收容器時，將處理[資源](container-instances-container-groups.md#resource-allocation)並為容器組計費停止。

如果容器組已終止（處於"成功"或"失敗"狀態），則停止操作無效。 例如，具有運行一次的容器任務的容器組在"成功"狀態下成功終止。 嘗試停止處於該狀態的組不會更改狀態。 

## <a name="start"></a>Start

當容器組停止時（因為容器自行終止或手動停止組）時，您可以啟動容器。 例如，使用[az 容器啟動][az-container-start]命令或 Azure 門戶手動啟動組中的容器。 如果更新任何容器的容器映像，會提取新映像。 

啟動容器群組會開始相同容器設定的新部署。 這個動作可協助您快速重複使用已知的容器群組設定，按照預期的方式運作。 您不需要建立新的容器群組，即可執行相同的工作負載。

容器組中的所有容器都由此操作啟動。 無法在組中啟動特定容器。

您手動啟動或重新啟動的容器群組之後，容器群組會根據設定的重新啟動原則執行。
  
## <a name="restart"></a>重新啟動

您可以在容器組運行時重新開機容器組 - 例如，通過使用[az 容器重新開機][az-container-restart]命令。 此動作會重新啟動容器群組中的所有容器。 如果更新任何容器的容器映像，會提取新映像。 

您想要對部署問題進行疑難排解時，重新啟動容器群組很有幫助。 例如，如果暫存資源限制導致您的容器無法成功執行，重新啟動群組可能會解決此問題。

此操作將重新開機容器組中的所有容器。 無法重新開機組中的特定容器。

手動重新開機容器組後，容器組將根據配置的重新開機策略運行。

## <a name="next-steps"></a>後續步驟

詳細瞭解重新開機 Azure 容器實例[中的策略設置](container-instances-restart-policy.md)。

除了使用現有配置手動停止和啟動容器組外，還可以更新正在運行的容器組的[設置](container-instances-update.md)。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
