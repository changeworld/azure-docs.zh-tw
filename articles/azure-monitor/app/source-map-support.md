---
title: JavaScript 應用程式的來源對應支援-Azure 監視器 Application Insights
description: 瞭解如何使用 Application Insights 將來源對應上傳至您自己的儲存體帳戶 Blob 容器。
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 06/23/2020
ms.openlocfilehash: 60653a43046276592878b3af91242e7e1b4ecd71
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516697"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript 應用程式的來源對應支援

Application Insights 支援將來源對應上傳至您自己的儲存體帳戶 Blob 容器。
來源對應可以用來 unminify 在端對端交易詳細資料頁面上找到的呼叫堆疊。 [JAVASCRIPT SDK][ApplicationInsights-JS]或[Node.js SDK][ApplicationInsights-Node.js]所傳送的任何例外狀況，都可以使用來源對應來 unminified。

![藉由連結儲存體帳戶來 Unminify 呼叫堆疊](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>建立新的儲存體帳戶和 Blob 容器

如果您已經有現有的儲存體帳戶或 blob 容器，您可以略過此步驟。

1. [建立新的儲存體帳戶][create storage account]
2. 在您的儲存體帳戶內[建立 blob 容器][create blob container]。 請務必將 [公用存取層級] 設定為 `Private` ，以確保您的來源對應不是可公開存取的。

> [!div class="mx-imgBorder"]
>![您的容器存取層級必須設定為 [私人]](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>將您的來源對應推送至 Blob 容器

您應該將持續部署管線與您的儲存體帳戶整合，方法是將它設定為自動將您的來源對應上傳至已設定的 Blob 容器。

您可以使用與一起部署 & 的資料夾結構，將來源對應上傳到您的 Blob 儲存體容器。 常見的使用案例是在部署資料夾及其版本前面加上前置詞，例如 `1.2.3/static/js/main.js` 。 透過名為的 Azure Blob 容器 unminifying 時 `sourcemaps` ，它會嘗試提取位於的來源對應 `sourcemaps/1.2.3/static/js/main.js.map` 。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>透過 Azure Pipelines 上傳來源對應（建議）

如果您使用 Azure Pipelines 持續建立及部署應用程式，請將 Azure 檔案[複製][azure file copy]工作新增至您的管線，以自動上傳您的來源對應。

> [!div class="mx-imgBorder"]
> ![將 Azure 檔案複製工作新增至您的管線，以將您的來源對應上傳至 Azure Blob 儲存體](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>使用來源對應儲存體帳戶來設定您的 Application Insights 資源

### <a name="from-the-end-to-end-transaction-details-page"></a>從端對端交易詳細資料頁面

從 [端對端交易詳細資料] 索引標籤中，您可以按一下 [ *Unminify* ]，它會在您的資源未設定時顯示提示。

1. 在入口網站中，查看已縮減之例外狀況的詳細資料。
2. 按一下 [ *Unminify* ]
3. 如果您尚未設定您的資源，則會出現一則訊息，提示您進行設定。

### <a name="from-the-properties-page"></a>從 [屬性] 頁面

如果您想要設定或變更連結至 Application Insights 資源的儲存體帳戶或 Blob 容器，您可以藉由查看 Application Insights 資源的 [*屬性*] 索引標籤來執行此動作。

1. 流覽至 Application Insights 資源的 [*屬性*] 索引標籤。
2. 按一下 [*變更來源對應 blob 容器*]。
3. 選取不同的 Blob 容器做為您的來源對應容器。
4. 按一下 [ `Apply`]。

> [!div class="mx-imgBorder"]
> ![流覽至 [屬性] 分頁來重新設定選取的 Azure Blob 容器](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>疑難排解

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 容器上的必要角色型存取控制（RBAC）設定

使用這項功能的入口網站上的任何使用者，都必須至少指派為 Blob 容器的[儲存體 Blob 資料讀取器][storage blob data reader]。 您必須將此角色指派給任何會透過此功能使用來源對應的其他人。

> [!NOTE]
> 視容器的建立方式而定，這可能不會自動指派給您或您的小組。

### <a name="source-map-not-found"></a>找不到來源對應

1. 確認對應的來源對應已上傳至正確的 blob 容器
2. 確認來源對應檔案是在其對應的 JavaScript 檔案之後命名，並加上尾碼 `.map` 。
    - 例如， `/static/js/main.4e2ca5fa.chunk.js` 會搜尋名為的 blob`main.4e2ca5fa.chunk.js.map`
3. 檢查瀏覽器的主控台，查看是否已記錄任何錯誤。 將此納入任何支援票證。

## <a name="next-steps"></a>後續步驟

* [Azure 檔案複製工作](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: ../../storage/common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: ../../storage/blobs/storage-quickstart-blobs-portal.md
[storage blob data reader]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme
