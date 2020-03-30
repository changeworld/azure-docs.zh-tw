---
title: JavaScript 應用程式的源映射支援 - Azure 監視器應用程式見解
description: 瞭解如何使用應用程式見解將源映射上載到您自己的存儲帳戶 Blob 容器。
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474878"
---
# <a name="source-map-support-for-javascript-applications"></a>對 JavaScript 應用程式的源映射支援

應用程式見解支援將源映射上載到您自己的存儲帳戶 Blob 容器。
源映射可用於解分端到端事務詳細資訊頁上的呼叫堆疊。 [JavaScript SDK][ApplicationInsights-JS]或[Node.js SDK][ApplicationInsights-Node.js]發送的任何異常都可以使用源映射進行解碼化。

![通過連結存儲帳戶來解化呼叫堆疊](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>創建新的存儲帳戶和 Blob 容器

如果您已有現有的存儲帳戶或 Blob 容器，則可以跳過此步驟。

1. [建立新的儲存體帳戶][create storage account]
2. [在][create blob container]存儲帳戶中創建 Blob 容器。 請確保將"公共存取層級"設置為`Private`，以確保源地圖不公開訪問。

> [!div class="mx-imgBorder"]
>![容器存取層級必須設置為專用](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>將源映射推送到 Blob 容器

應通過配置連續部署管道自動將源映射上載到配置的 Blob 容器，將連續部署管道與存儲帳戶集成。 不應將源映射上載到 Blob 容器中的子資料夾;因此，不應將源映射上載到"Blob"容器中的子資料夾。目前，源映射將僅從根資料夾提取。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>通過 Azure 管道上載源映射（推薦）

如果使用 Azure 管道持續生成和部署應用程式，請將[Azure 檔案複製][azure file copy]任務添加到管道以自動上載源映射。

> [!div class="mx-imgBorder"]
> ![將 Azure 檔案複製任務添加到管道，以將源映射上載到 Azure Blob 存儲](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>使用源地圖存儲帳戶配置應用程式見解資源

### <a name="from-the-end-to-end-transaction-details-page"></a>從端到端交易記錄詳細資訊頁

在端到端事務詳細資訊選項卡中，可以按一下 *"取消分鐘化"，* 並且將顯示一個提示，用於配置資源是否未配置。

1. 在門戶中，查看已詳細化的異常的詳細資訊。
2. 按一下 *"取消最小化"*
3. 如果資源尚未配置，將顯示一條消息，提示您進行配置。

### <a name="from-the-properties-page"></a>從屬性頁

如果要配置或更改連結到應用程式見解資源的存儲帳戶或 Blob 容器，可以通過查看應用程式見解資源的 *"屬性*"選項卡來執行此操作。

1. 導航到應用程式見解資源的"*屬性*"選項卡。
2. 按一下 *"更改源映射 blob 容器*"。
3. 選擇其他 Blob 容器作為源映射容器。
4. 按一下 [ `Apply`]。

> [!div class="mx-imgBorder"]
> ![通過導航到屬性邊欄選項卡重新配置選定的 Azure Blob 容器](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 容器上所需的基於角色的存取控制 （RBAC） 設置

門戶上使用此功能的任何使用者必須至少作為[存儲 Blob 資料讀取器][storage blob data reader]分配給 Blob 容器。 您必須將此角色指派給將通過此功能使用源映射的任何其他角色。

> [!NOTE]
> 根據容器的創建方式，這可能尚未自動分配給您或您的團隊。

### <a name="source-map-not-found"></a>源映射未找到

1. 驗證相應的源映射是否上載到正確的 blob 容器
2. 驗證源映射檔是否以映射到的 JavaScript 檔命名，並加上`.map`。
    - 例如，`/static/js/main.4e2ca5fa.chunk.js`將搜索名為`main.4e2ca5fa.chunk.js.map`
3. 檢查瀏覽器的主控台，查看是否記錄任何錯誤。 將其包含在任何支援票證中。

## <a name="next-steps"></a>後續步驟

* [Azure 檔案複製任務](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme