---
title: JavaScript 應用程式的來源對應支援-Azure 監視器 Application Insights
description: 瞭解如何使用 Application Insights 將來源對應上傳至您自己的儲存體帳戶 Blob 容器。
ms.topic: conceptual
author: DavidCBerry13
ms.author: daberry
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 79462dcb503e5835254b89f7ebaa7a9ee34946d3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933937"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript 應用程式的來源對應支援

Application Insights 支援將來源對應上傳至您自己的儲存體帳戶 Blob 容器。
來源對應可以用來美化在端對端交易詳細資料頁面上找到的呼叫堆疊。 [JAVASCRIPT SDK][ApplicationInsights-JS]或[Node.js SDK][ApplicationInsights-Node.js]所傳送的任何例外狀況都可以使用來源對應 unminified。

![使用儲存體帳戶連結來美化呼叫堆疊](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>建立新的儲存體帳戶和 Blob 容器

如果您已經有現有的儲存體帳戶或 blob 容器，您可以略過此步驟。

1. [建立新的儲存體帳戶][create storage account]
2. 在您的儲存體帳戶中[建立 blob 容器][create blob container]。 務必將 [公用存取層級] 設定為 `Private` ，以確保您的來源對應無法公開存取。

> [!div class="mx-imgBorder"]
>![您的容器存取層級必須設為私用](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>將您的來源對應推送至 Blob 容器

您應該將持續部署管線設定為自動將您的來源對應上傳至已設定的 Blob 容器，以將您的持續部署管線整合到您的儲存體帳戶。

您可以將來源對應上傳至您的 Blob 儲存體容器，其具有 & 部署時所編譯的相同資料夾結構。 常見的使用案例是在部署資料夾之前加上其版本，例如 `1.2.3/static/js/main.js` 。 透過稱為的 Azure Blob 容器 unminifying 時 `sourcemaps` ，它會嘗試提取位於的來源對應 `sourcemaps/1.2.3/static/js/main.js.map` 。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a> (建議的 Azure Pipelines 上傳來源對應) 

如果您使用 Azure Pipelines 持續建立及部署您的應用程式，請將 [Azure 檔案複製][azure file copy] 工作新增至您的管線，以自動上傳您的來源對應。

> [!div class="mx-imgBorder"]
> ![將 Azure 檔案複製工作新增至您的管線，以將您的來源對應上傳至 Azure Blob 儲存體](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>使用來源對應儲存體帳戶設定您的 Application Insights 資源

### <a name="from-the-end-to-end-transaction-details-page"></a>從端對端交易詳細資料頁面

在 [端對端交易詳細資料] 索引標籤中，您可以按一下 [ *美化* ]，它會顯示提示以設定您的資源是否未設定。

1. 在入口網站中，查看縮減之例外狀況的詳細資料。
2. 按一下 [*美化*]
3. 如果您的資源尚未設定，則會出現一則訊息，提示您進行設定。

### <a name="from-the-properties-page"></a>從 [屬性] 頁面

如果您想要設定或變更連結至 Application Insights 資源的儲存體帳戶或 Blob 容器，您可以藉由查看 Application Insights 資源的 [ *內容] 索引* 標籤來進行。

1. 流覽至 Application Insights 資源的 [ *屬性* ] 索引標籤。
2. 按一下 [ *變更來源對應 blob 容器*]。
3. 選取不同的 Blob 容器作為來源對應容器。
4. 按一下 [ `Apply`]。

> [!div class="mx-imgBorder"]
> ![藉由流覽至屬性分頁來重新設定選取的 Azure Blob 容器](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="required-azure-role-based-access-control-azure-rbac-settings-on-your-blob-container"></a>必要的 Azure 角色型存取控制 (您 Blob 容器上的 Azure RBAC) 設定

使用此功能的入口網站上的任何使用者都必須至少指派為 Blob 容器的 [儲存體 Blob 資料讀取器][storage blob data reader] 。 您必須將此角色指派給任何其他將透過這項功能使用來源對應的其他人。

> [!NOTE]
> 視容器的建立方式而定，這可能尚未自動指派給您或您的小組。

### <a name="source-map-not-found"></a>找不到來源對應

1. 確認對應的來源對應已上傳至正確的 blob 容器
2. 確認來源對應檔是以它所對應的 JavaScript 檔案命名，並加上尾碼 `.map` 。
    - 例如， `/static/js/main.4e2ca5fa.chunk.js` 會搜尋名為 `main.4e2ca5fa.chunk.js.map`
3. 檢查瀏覽器的主控台，查看是否有任何錯誤正在記錄。 在任何支援票證中包含此項。

## <a name="next-steps"></a>後續步驟

* [Azure 檔案複製工作](/azure/devops/pipelines/tasks/deploy/azure-file-copy)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
