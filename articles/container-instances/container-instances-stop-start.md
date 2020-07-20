---
title: 手動停止或啟動容器群組
description: 瞭解如何以手動方式停止或啟動 Azure 容器實例中的容器群組。
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74533431"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>在 Azure 容器執行個體中手動停止或啟動容器

容器群組的[重新開機原則](container-instances-restart-policy.md)設定會決定容器實例預設啟動或停止的方式。 您可以手動停止或啟動容器群組，以覆寫預設設定。

## <a name="stop"></a>Stop

手動停止執行中的容器群組-例如，使用[az container stop][az-container-stop]命令或 Azure 入口網站。 針對某些容器工作負載，您可能會想要在定義的期間之後停止長時間執行的容器群組，以節省成本。 

*當容器群組進入 [已停止] 狀態時，它會終止並回收群組中的所有容器。它不會保留容器狀態。*

回收容器時，系統會將[資源](container-instances-container-groups.md#resource-allocation)解除配置，並停止容器群組的計費。

如果容器群組已終止（處於 [成功] 或 [失敗] 狀態），則 [停止] 動作不會有任何作用。 例如，具有執行一次之容器工作的容器群組會成功終止，並處於成功狀態。 嘗試停止處於該狀態的群組並不會變更狀態。 

## <a name="start"></a>開始

當容器群組停止時-可能是因為容器本身自行終止，或您手動停止了群組-您可以啟動容器。 例如，使用[az container start][az-container-start]命令或 Azure 入口網站手動啟動群組中的容器。 如果更新任何容器的容器映像，會提取新映像。 

啟動容器群組會開始相同容器設定的新部署。 這個動作可協助您快速重複使用已知的容器群組設定，按照預期的方式運作。 您不需要建立新的容器群組，即可執行相同的工作負載。

此動作會啟動容器群組中的所有容器。 您無法啟動群組中的特定容器。

您手動啟動或重新啟動的容器群組之後，容器群組會根據設定的重新啟動原則執行。
  
## <a name="restart"></a>重新啟動

您可以在容器群組執行時加以重新開機-例如，使用[az container restart][az-container-restart]命令。 此動作會重新啟動容器群組中的所有容器。 如果更新任何容器的容器映像，會提取新映像。 

您想要對部署問題進行疑難排解時，重新啟動容器群組很有幫助。 例如，如果暫存資源限制導致您的容器無法成功執行，重新啟動群組可能會解決此問題。

此動作會重新開機容器群組中的所有容器。 您無法重新開機群組中的特定容器。

當您手動重新開機容器群組之後，容器群組就會根據設定的重新開機原則來執行。

## <a name="next-steps"></a>後續步驟

深入瞭解如何在 Azure 容器實例中[重新開機原則設定](container-instances-restart-policy.md)。

除了以現有設定手動停止和啟動容器群組之外，您還可以更新執行中容器群組的[設定](container-instances-update.md)。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
