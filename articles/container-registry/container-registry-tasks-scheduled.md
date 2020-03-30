---
title: 教程 - 安排 ACR 任務
description: 在本教程中，瞭解如何通過設置一個或多個計時器觸發器，在定義的計畫上運行 Azure 容器註冊表任務
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402873"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>按計劃運行 ACR 任務

本教程演示如何按計劃運行[ACR 任務](container-registry-tasks-overview.md)。 通過設置一個或多個*計時器觸發器*來安排任務。 計時器觸發器可以單獨使用，也可以與其他任務觸發器結合使用。

在本教程中，瞭解計畫任務和：

> [!div class="checklist"]
> * 使用計時器觸發器創建任務
> * 管理計時器觸發器

計畫任務對於以下方案非常有用：

* 為計畫維護操作運行容器工作負載。 例如，運行容器化應用以從註冊表中刪除不需要的圖像。
* 作為現場監控的一部分，在工作日對生產映射運行一組測試。

可以使用 Azure 雲外殼或 Azure CLI 的本地安裝來運行本文中的示例。 如果您想在本地使用它，則需要版本 2.0.68 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。


## <a name="about-scheduling-a-task"></a>關於安排任務

* **使用 cron 運算式觸發器**- 任務的計時器觸發器使用*cron 運算式*。 運算式是一個字串，有五個欄位，指定觸發任務的分鐘、小時、天、月和星期幾。 支援每分鐘最多一次的頻率。

  例如，運算式`"0 12 * * Mon-Fri"`在每個工作日中午 UTC 觸發任務。 請參閱本文後面[的詳細資訊](#cron-expressions)。
* **多個計時器觸發器**- 允許向任務添加多個計時器，只要計畫不同。
    * 在創建任務時指定多個計時器觸發器，或稍後添加它們。
    * 可以選擇命名觸發器以方便管理，或者 ACR 任務將提供預設觸發器名稱。
    * 如果計時器計畫一次重疊，ACR 任務在每個計時器的計畫時間觸發任務。
* **其他任務觸發器**- 在計時器觸發的任務中，還可以基於[原始程式碼提交](container-registry-tutorial-build-task.md)或[基本映射更新](container-registry-tutorial-base-image-update.md)啟用觸發器。 與其他 ACR 任務一樣，您還可以[手動觸發][az-acr-task-run]計畫的任務。

## <a name="create-a-task-with-a-timer-trigger"></a>使用計時器觸發器創建任務

使用[az acr 任務創建][az-acr-task-create]命令創建任務時，可以選擇添加計時器觸發器。 添加參數`--schedule`並傳遞計時器的 cron 運算式。

作為一個簡單的示例，以下命令每天在 21：00 UTC 觸發從 Docker Hub 運行`hello-world`映射。 任務在沒有原始程式碼上下文的情況下運行。

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

運行[az acr 任務顯示][az-acr-task-show]命令以查看計時器觸發器已配置。 預設情況下，基本映射更新觸發器也已啟用。

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

通過[運行 az acr 任務][az-acr-task-run]手動觸發任務，以確保正確設置任務：

```azurecli
az acr task run --name mytask --registry myregistry
```

如果容器成功運行，則輸出類似于以下內容：

```output
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

在計畫時間之後，運行[az acr 工作清單運行][az-acr-task-list-runs]命令，以驗證計時器是否按預期觸發了任務：

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

當計時器成功時，輸出類似于以下內容：

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>管理計時器觸發器

使用[az acr 任務計時器][az-acr-task-timer]命令管理 ACR 任務的計時器觸發器。

### <a name="add-or-update-a-timer-trigger"></a>添加或更新計時器觸發器

創建任務後，可以選擇使用[az acr 任務計時器添加][az-acr-task-timer-add]命令添加計時器觸發器。 下面的示例將計時器觸發器名稱*計時器2*添加到之前創建*的任務*。 此計時器每天在 10：30 UTC 觸發任務。

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

使用[az acr 任務計時器更新][az-acr-task-timer-update]命令更新現有觸發器的計畫或更改其狀態。 例如，更新名為*timer2*的觸發器以在 11：30 UTC 觸發任務：

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>清單計時器觸發器

[az acr 任務計時器清單][az-acr-task-timer-list]命令顯示為任務設置的計時器觸發器：

```azurecli
az acr task timer list --name mytask --registry myregistry
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

### <a name="remove-a-timer-trigger"></a>刪除計時器觸發器

使用[az acr 任務計時器刪除][az-acr-task-timer-remove]命令從任務中刪除計時器觸發器。 以下示例從*我的任務*中刪除*計時器2*觸發器：

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>克朗運算式

ACR 任務使用[NCronTab](https://github.com/atifaziz/NCrontab)庫來解釋 cron 運算式。 ACR 任務中受支援的運算式有五個必要欄位，由空格分隔：

`{minute} {hour} {day} {month} {day-of-week}`

與 cron 運算式一起使用的時區是協調通用時間 （UTC）。 小時以 24 小時格式使用。

> [!NOTE]
> ACR 任務不支援 cron `{second}` `{year}`運算式中的 或 欄位。 如果複製在另一個系統中使用的 cron 運算式，請確保刪除這些欄位（如果使用了這些欄位）。

每個欄位可以具備下列類型的值：

|類型  |範例  |觸發時間  |
|---------|---------|---------|
|特定值 |<nobr>`"5 * * * *"`</nobr>|每小時5分鐘超過小時|
|所有值 (`*`)|<nobr>`"* 5 * * *"`</nobr>|從 5：00 UTC 開始每小時每分鐘（每天 60 次）|
|範圍 (`-` 運算子)|<nobr>`"0 1-3 * * *"`</nobr>|每天 3 次，1：00、2：00 和 3：00 UTC|
|一組值 (`,` 運算子)|<nobr>`"20,30,40 * * * *"`</nobr>|每小時3次，20分鐘，30分鐘，40分鐘超過小時|
|間隔值 (`/` 運算子)|<nobr>`"*/10 * * * *"`</nobr>|每小時6次，10分鐘，20分鐘，等等，超過小時

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>克朗示例

|範例|觸發時間  |
|---------|---------|
|`"*/5 * * * *"`|每隔 5 分鐘一次|
|`"0 * * * *"`|每小時開始時一次|
|`"0 */2 * * *"`|每隔 2 小時一次|
|`"0 9-17 * * *"`|從 9：00 到 17：00 UTC 每小時一次|
|`"30 9 * * *"`|每天 9：30 UTC|
|`"30 9 * * 1-5"`|每個工作日 9：30 UTC|
|`"30 9 * Jan Mon"`|1月每週一上午 9：30 UTC|

## <a name="clean-up-resources"></a>清除資源

要刪除在本教程系列中創建的所有資源，包括容器註冊表或註冊表、容器實例、金鑰保存庫和服務主體，請發出以下命令：

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>後續步驟

在本教程中，您學習了如何創建由計時器自動觸發的 Azure 容器註冊表任務。 

有關使用計畫任務清理註冊表中的存儲庫的示例，請參閱[從 Azure 容器註冊表中自動清除圖像](container-registry-auto-purge.md)。

有關由原始程式碼提交或基本映射更新觸發的任務的示例，請參閱[ACR 任務教程系列](container-registry-tutorial-quick-task.md)中的其他文章。



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
