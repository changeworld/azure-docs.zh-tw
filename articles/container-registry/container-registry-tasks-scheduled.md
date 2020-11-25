---
title: 教學課程-排程 ACR 工作
description: 在本教學課程中，您將瞭解如何藉由設定一或多個計時器觸發程式，以定義的排程執行 Azure Container Registry 工作
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: 13a4ccac4ea97538583c1c063a6dc61e4d25686a
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030606"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>教學課程：依定義的排程執行 ACR 工作

本教學課程說明如何依排程執行 [ACR](container-registry-tasks-overview.md) 工作。 藉由設定一或多個 *計時器觸發* 程式來排程工作。 計時器觸發程式可以單獨使用，也可以與其他工作觸發程式搭配使用。

在本教學課程中，您將瞭解排程工作和：

> [!div class="checklist"]
> * 建立具有計時器觸發程式的工作
> * 管理計時器觸發程式

排程工作適用于下列案例：

* 執行排程維護作業的容器工作負載。 例如，執行容器化應用程式以從登錄中移除不必要的映射。
* 在 workday 期間于生產映射上執行一組測試，做為即時網站監視的一部分。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>關於排程工作

* **具有 cron 運算式的觸發** 程式-工作的計時器觸發程式會使用 *cron 運算式*。 運算式是具有五個欄位的字串，可指定要觸發工作的分鐘、小時、日、月和星期幾。 支援每分鐘最多一次的頻率。

  例如，運算式會在 `"0 12 * * Mon-Fri"` 每個工作日的中午（UTC）觸發工作。 請參閱本文稍後的 [詳細資料](#cron-expressions) 。
* **多個計時器觸發** 程式-只要排程不同，就可以將多個計時器新增至工作。
    * 當您建立工作時，請指定多個計時器觸發程式，或稍後再新增。
    * 選擇性地命名觸發程式以方便管理，或 ACR 工作會提供預設的觸發程式名稱。
    * 如果計時器排程一次重迭，ACR 工作會在排程的時間針對每個計時器觸發工作。
* **其他工作觸發** 程式-在計時器觸發的工作中，您也可以根據 [原始程式碼認可](container-registry-tutorial-build-task.md) 或 [基底映射更新](container-registry-tutorial-base-image-update.md)來啟用觸發程式。 如同其他 ACR 工作，您也可以 [手動執行][az-acr-task-run] 排程工作。

## <a name="create-a-task-with-a-timer-trigger"></a>建立具有計時器觸發程式的工作

### <a name="task-command"></a>工作命令

首先，使用適合您環境的值填入下列 shell 環境變數。 此步驟並不是必要動作，但可簡化在本教學課程中執行多行 Azure CLI 命令的作業。 如果您未填入環境變數，則必須手動將每個值取代為範例命令中的任何位置。

[![內嵌啟動](https://shell.azure.com/images/launchcloudshell.png "啟動 Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

當您使用 [az acr task create][az-acr-task-create] 命令來建立工作時，您可以選擇性地新增計時器觸發程式。 加入 `--schedule` 參數，並傳遞計時器的 cron 運算式。

簡單的範例中，下列工作會 `hello-world` 從每日 21:00 UTC 開始觸發 Microsoft Container Registry 的映射。 工作會在沒有原始程式碼內容的情況下執行。

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

執行 [az acr task show][az-acr-task-show] 命令來查看計時器觸發程序是否已設定。 根據預設，基底映射更新觸發程式也會啟用。

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>觸發工作

使用 [az acr task run][az-acr-task-run] 手動觸發工作，以確保其已正確設定：

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

如果容器成功執行，則輸出如下所示。 輸出會扼要地顯示關鍵步驟

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

在排程的時間之後，執行 [az acr task list-][az-acr-task-list-runs] run 命令，確認計時器是否如預期般觸發工作：

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

當計時器成功時，輸出如下所示：

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>管理計時器觸發程式

使用 [az acr task timer][az-acr-task-timer] 命令來管理 acr 工作的計時器觸發程式。

### <a name="add-or-update-a-timer-trigger"></a>新增或更新計時器觸發程式

建立工作之後，您可以選擇性地使用 [az acr task timer add][az-acr-task-timer-add] 命令新增計時器觸發程式。 下列範例會將計時器觸發程式名稱 *timer2* 新增至先前建立的 *timertask* 。 此計時器每天會以 10:30 UTC 觸發工作。

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

使用 [az acr task timer update][az-acr-task-timer-update] 命令，更新現有觸發程式的排程，或變更其狀態。 例如，更新名為 *timer2* 的觸發程式，以在 11:30 UTC 時間觸發工作：

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>列出計時器觸發程式

[Az acr task timer list][az-acr-task-timer-list]命令會顯示為工作設定的計時器觸發程式：

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

範例輸出︰

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>移除計時器觸發程式

使用 [az acr task timer remove][az-acr-task-timer-remove] 命令移除工作中的計時器觸發程式。 下列範例會從 *timertask* 中移除 *timer2* 觸發程式：

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 運算式

ACR 工作使用 [NCronTab](https://github.com/atifaziz/NCrontab) 程式庫來解讀 cron 運算式。 ACR 工作中支援的運算式具有以空白字元分隔的五個必要欄位：

`{minute} {hour} {day} {month} {day-of-week}`

與 cron 運算式搭配使用的時區是國際標準時間 (UTC) 。 小時的格式為24小時。

> [!NOTE]
> ACR 工作不支援 `{second}` `{year}` cron 運算式中的或欄位。 如果您複製另一個系統中所使用的 cron 運算式，請務必移除這些欄位（如果使用的話）。

每個欄位可以具備下列類型的值：

|類型  |範例  |觸發時間  |
|---------|---------|---------|
|特定值 |<nobr>`"5 * * * *"`</nobr>|每小時在一小時前的5分鐘|
|所有值 (`*`)|<nobr>`"* 5 * * *"`</nobr>|每一小時從 5:00 UTC 開始 (60 次的分鐘) |
|範圍 (`-` 運算子)|<nobr>`"0 1-3 * * *"`</nobr>|每天3次，1:00、2:00 和 3:00 UTC|
|一組值 (`,` 運算子)|<nobr>`"20,30,40 * * * *"`</nobr>|每小時3次，在該小時內的20分鐘、30分鐘和40分鐘|
|間隔值 (`/` 運算子)|<nobr>`"*/10 * * * *"`</nobr>|每小時6次，在過去一小時的10分鐘、20分鐘等等

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 範例

|範例|觸發時間  |
|---------|---------|
|`"*/5 * * * *"`|每隔 5 分鐘一次|
|`"0 * * * *"`|每小時開始時一次|
|`"0 */2 * * *"`|每隔 2 小時一次|
|`"0 9-17 * * *"`|每小時從9:00 到 17:00 UTC|
|`"30 9 * * *"`|每天 9:30 UTC|
|`"30 9 * * 1-5"`|每個工作日 9:30 UTC|
|`"30 9 * Jan Mon"`|每月每星期一 9:30 UTC|

## <a name="clean-up-resources"></a>清除資源

若要移除您在本教學課程系列中建立的所有資源（包括容器登錄或登錄、容器實例、金鑰保存庫和服務主體），請發出下列命令：

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解如何建立由計時器自動觸發的 Azure Container Registry 工作。 

如需使用排定的工作來清除登錄中存放庫的範例，請參閱 [自動清除 Azure container registry](container-registry-auto-purge.md)中的映射。

如需原始程式碼認可或基底映射更新所觸發的工作範例，請參閱 [ACR 工作教學課程系列](container-registry-tutorial-quick-task.md)中的其他文章。



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
