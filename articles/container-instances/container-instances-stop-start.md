---
title: 手動停止或啟動容器群組
description: 瞭解如何在 Azure 容器實例中手動停止或啟動容器群組。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: f697f18459959cf40e71d3ca90cd8b42aaa74239
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88799072"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>在 Azure 容器執行個體中手動停止或啟動容器

容器群組的 [重新開機原則](container-instances-restart-policy.md) 設定會決定容器實例預設啟動或停止的方式。 您可以手動停止或啟動容器群組，以覆寫預設設定。

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>停止

手動停止執行中的容器群組-例如，使用 [az container stop][az-container-stop] 命令或 Azure 入口網站。 針對特定的容器工作負載，您可能會想要在定義的期間內停止長時間執行的容器群組，以節省成本。 

*當容器群組進入已停止狀態時，它會終止並回收群組中的所有容器。它不會保留容器的狀態。*

回收容器時，系統會將 [資源](container-instances-container-groups.md#resource-allocation) 解除配置，並停止容器群組的計費。

如果容器群組已經終止 (處於成功或失敗狀態) ，停止動作將沒有任何作用。 例如，具有執行一次之容器工作的容器群組（執行成功）會在成功狀態終止。 嘗試停止處於該狀態的群組不會變更狀態。 

## <a name="start"></a>Start

當容器群組停止時-可能是因為容器自行終止，或您手動停止了群組-您可以啟動容器。 例如，使用 [az 容器 start][az-container-start] 命令或 Azure 入口網站手動啟動群組中的容器。 如果更新任何容器的容器映像，會提取新映像。 

啟動容器群組會開始相同容器設定的新部署。 這個動作可協助您快速重複使用已知的容器群組設定，按照預期的方式運作。 您不需要建立新的容器群組，即可執行相同的工作負載。

此動作會啟動容器群組中的所有容器。 您無法啟動群組中的特定容器。

您手動啟動或重新啟動的容器群組之後，容器群組會根據設定的重新啟動原則執行。
  
## <a name="restart"></a>重新啟動

您可以重新開機執行中的容器群組-例如，使用 [az 容器 restart][az-container-restart] 命令。 此動作會重新啟動容器群組中的所有容器。 如果更新任何容器的容器映像，會提取新映像。 

您想要對部署問題進行疑難排解時，重新啟動容器群組很有幫助。 例如，如果暫存資源限制導致您的容器無法成功執行，重新啟動群組可能會解決此問題。

此動作會重新開機容器群組中的所有容器。 您無法重新開機群組中的特定容器。

當您手動重新開機容器群組之後，容器群組會根據設定的重新開機原則執行。

## <a name="next-steps"></a>接下來的步驟

深入瞭解 Azure 容器實例中的 [重新開機原則設定](container-instances-restart-policy.md) 。

除了以現有設定手動停止和啟動容器群組之外，您還可以更新執行中容器群組的 [設定](container-instances-update.md) 。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
