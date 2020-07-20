---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74451664"
---
## <a name="deploy-template-from-cloud-shell"></a>從 Cloud Shell 部署範本

您可以使用 [Cloud Shell](../articles/cloud-shell/overview.md) 來部署您的範本。 若要部署外部範本，請提供範本的 URI，就如同您對任何外部部署所做的一樣。 若要部署本機範本，您必須先將範本載入 Cloud Shell 的儲存體帳戶。 本節說明如何將範本載入至您的 Cloud Shell 帳戶，並將其部署為本機檔案。 如果您尚未使用 Cloud Shell，請參閱 [Azure Cloud Shell 概觀](../articles/cloud-shell/overview.md)以取得如何設定的相關資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 選取您的 Cloud Shell 資源群組。 名稱模式為 `cloud-shell-storage-<region>`。

   ![選取資源群組](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. 選取 Cloud Shell 的儲存體帳戶。

   ![選取儲存體帳戶](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. 選取 [Blob] 。

   ![選取 Blob](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. 選取 [+ 容器]。

   ![新增容器](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. 為您的容器提供名稱和存取層級。 本文中的範例範本不包含機密資訊，因此允許匿名讀取權限。 選取 [確定]。

   ![提供容器值](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. 選取您建立的容器。

   ![選取新容器](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. 選取 [上傳] 。

   ![上傳 Blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. 尋找並上傳您的範本。

   ![上傳檔案](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. 上傳之後，選取範本。

   ![選取新範本](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. 複製 URL。

   ![複製 URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. 開啟提示字元。

   ![開啟 Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
