---
title: Azure 角色和許可權
description: 使用 Azure 角色型存取控制 (Azure RBAC) 和身分識別和存取管理 (IAM) ，以提供更細微的許可權給 Azure container registry 中的資源。
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: b8562d3e33cd49082d4ba4d8567d5f0c816070b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88661379"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry 角色和權限

Azure Container Registry 服務支援一組 [內建的 azure 角色](../role-based-access-control/built-in-roles.md) ，可對 azure Container Registry 提供不同層級的許可權。 使用 [azure 角色型存取控制 (AZURE RBAC) ](../role-based-access-control/index.yml) 將特定許可權指派給需要與登錄互動的使用者、服務主體或其他身分識別。 您也可以針對不同的作業，以更細緻的許可權來定義 [自訂角色](#custom-roles) 。

| 角色/權限       | [存取 Resource Manager](#access-resource-manager) | [建立/刪除登錄](#create-and-delete-registry) | [推送映像](#push-image) | [提取映像](#pull-image) | [刪除映像資料](#delete-image-data) | [變更原則](#change-policies) |   [簽署映像](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| 擁有者 | X | X | X | X | X | X |  |  
| 參與者 | X | X | X |  X | X | X |  |  
| 讀取者 | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>區分使用者和服務

套用權限時，最佳的做法是提供人員或服務完成工作的最低權限。 下列權限集合代表人員或無周邊服務可使用的一組功能。

### <a name="cicd-solutions"></a>CI/CD 解決方案

自動執行 CI/CD 解決方案的 `docker build` 命令時，您需要 `docker push` 功能。 對於這些無周邊服務案例，建議指派 **AcrPush** 角色。 此角色不同於更廣泛**參與者**角色，可防止帳戶執行其他登錄作業或存取 Azure Resource Manager。

### <a name="container-host-nodes"></a>容器主機節點

同樣地，執行容器的節點需要 **AcrPull** 角色，但是應該不需要**讀者**功能。

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker 擴充功能

對於 Visual Studio Code [Docker 擴充功能組](https://code.visualstudio.com/docs/azure/docker)之類的工具，需要額外的資源提供者存取權，才能列出可用的 Azure 容器登錄。 在此情況下，請對於使用者提供**讀者**或**參與者**角色的存取權。 這些角色允許 `docker pull`、`docker push`、`az acr list`、`az acr build` 和其他功能。 

## <a name="access-resource-manager"></a>存取 Resource Manager

必須具備 Azure Resource Manager 存取權，才能使用 [Azure CLI](/cli/azure/) 來進行 Azure 入口網站和登錄管理。 例如，若要使用 `az acr list` 命令取得登錄的清單，您需要此權限集合。 

## <a name="create-and-delete-registry"></a>建立和刪除登錄

建立和刪除 Azure 容器登錄的能力。

## <a name="push-image"></a>推送映像

透過 `docker push` 推送映像或將另一個[支援的成品](container-registry-image-formats.md) (例如 Helm 圖表) 推送到登錄的能力。 需要使用授權的身分識別對於登錄進行[驗證](container-registry-authentication.md)。 

## <a name="pull-image"></a>提取映像

透過 `docker pull` 提取非隔離映像或從登錄提取另一個[支援的成品](container-registry-image-formats.md) (例如 Helm 圖表) 的能力。 需要使用授權的身分識別對於登錄進行[驗證](container-registry-authentication.md)。

## <a name="delete-image-data"></a>刪除映像資料

從登錄 [刪除容器映射](container-registry-delete.md)，或刪除其他 [支援](container-registry-image-formats.md) 的成品（例如 Helm 圖表）的能力。

## <a name="change-policies"></a>變更原則

對於登錄設定原則的能力。 原則包括映像清除、啟用隔離和映像簽署。

## <a name="sign-images"></a>簽署映像

簽署映像 (通常指派給自動化程序，以便使用服務主體) 的能力。 此權限通常會結合[推送映像](#push-image)，以便將受信任的映像推送至登錄。 如需詳細資訊，請參閱 [Azure Container Registry 中的內容信任](container-registry-content-trust.md)。

## <a name="custom-roles"></a>自訂角色

如同其他 Azure 資源，您可以建立具有更細緻許可權的 [自訂角色](../role-based-access-control/custom-roles.md) ，以 Azure Container Registry。 然後，將自訂角色指派給需要與登錄互動的使用者、服務主體或其他身分識別。 

若要判斷要將哪些許可權套用至自訂角色，請參閱 >microsoft.containerregistry [動作](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)清單、檢查 [內建 ACR 角色](../role-based-access-control/built-in-roles.md)的允許動作，或執行下列命令：

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

若要定義自訂角色，請參閱 [建立自訂角色的步驟](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)。

> [!IMPORTANT]
> 在自訂角色中，Azure Container Registry 目前不支援 `Microsoft.ContainerRegistry/*` `Microsoft.ContainerRegistry/registries/*` 將存取權授與所有相符動作的萬用字元（例如或）。 在角色中個別指定任何必要的動作。

### <a name="example-custom-role-to-import-images"></a>範例：匯入影像的自訂角色

例如，下列 JSON 會定義允許將 [映射匯入](container-registry-import-images.md) 至登錄之自訂角色的最小動作。

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

若要使用 JSON 描述來建立或更新自訂角色，請使用 [Azure CLI](../role-based-access-control/custom-roles-cli.md)、 [Azure Resource Manager 範本](../role-based-access-control/custom-roles-template.md)、 [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)或其他 Azure 工具。 以您管理內建 Azure 角色的角色指派的相同方式，新增或移除自訂角色的角色指派。

## <a name="next-steps"></a>接下來的步驟

* 深入瞭解如何使用 [Azure 入口網站](../role-based-access-control/role-assignments-portal.md)、 [Azure CLI](../role-based-access-control/role-assignments-cli.md)或其他 Azure 工具，將 Azure 角色指派給 azure 身分識別。

* 深入了解 Azure Container Registry 的[驗證選項](container-registry-authentication.md)。

* 瞭解如何在容器登錄中啟用存放 [庫範圍的許可權](container-registry-repository-scoped-permissions.md) (預覽) 。