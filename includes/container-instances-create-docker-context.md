---
title: 包含檔案
description: 包含檔案
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708252"
---
## <a name="create-azure-context"></a>建立 Azure 內容

若要使用 Docker 命令來執行 Azure 容器執行個體中的容器，請先登入 Azure：

```bash
docker login azure
```

出現提示時，輸入或選取您的 Azure 認證。


執行 `docker context create aci` 以建立 ACI 內容。 此內容會將 Docker 與您的 Azure 訂用帳戶和資源群組建立關聯，讓您可以建立和管理容器執行個體。 例如，若要建立名為「myacicontext」的內容：

```
docker context create aci myacicontext
```

出現提示時，選取您的 Azure 訂用帳戶識別碼，然後選取現有的資源群組，或**建立新的資源群組**。 如果您選擇新的資源群組，則會使用系統產生的名稱來建立。 Azure 容器執行個體和所有 Azure 資源相同，都必須部署到資源群組中。 資源群組可讓您組織和管理相關的 Azure 資源。


執行 `docker context ls` 以確認您已將 ACI 內容新增至您的 Docker 內容：

```
docker context ls
```