---
title: RBAC 角色和許可權
description: Azure 角色型存取控制 (RBAC) 以及身分識別和存取管理 (IAM) 可對 Azure Container Registry 中的資源提供精確的權限。
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893479"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry 角色和權限

Azure 容器註冊表服務支援一組[內置 Azure 角色](../role-based-access-control/built-in-roles.md)，這些角色向 Azure 容器註冊表提供不同級別的許可權。 使用基於 Azure[角色的存取控制](../role-based-access-control/index.yml)（RBAC） 向需要與註冊表交互的使用者、服務主體或其他標識分配特定許可權。 

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

從註冊表[中刪除容器圖像](container-registry-delete.md)或刪除其他[受支援的專案](container-registry-image-formats.md)（如 Helm 圖表）的功能。

## <a name="change-policies"></a>變更原則

對於登錄設定原則的能力。 原則包括映像清除、啟用隔離和映像簽署。

## <a name="sign-images"></a>簽署映像

簽署映像 (通常指派給自動化程序，以便使用服務主體) 的能力。 此權限通常會結合[推送映像](#push-image)，以便將受信任的映像推送至登錄。 如需詳細資訊，請參閱[Azure Container Registry 中的內容信任](container-registry-content-trust.md)。

## <a name="custom-roles"></a>自訂角色

與其他 Azure 資源一樣，您可以創建自己的[自訂角色](../role-based-access-control/custom-roles.md)，並具有對 Azure 容器註冊表的細細微性許可權。 然後，將自訂角色指派給使用者、服務主體或其他需要與註冊表交互的標識。 

要確定要應用於自訂角色的許可權，請參閱 Microsoft.ContainerRegistry[操作](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)的清單，查看[內置 ACR 角色的](../role-based-access-control/built-in-roles.md)允許操作，或運行以下命令：

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

要定義自訂角色，請參閱[創建自訂角色的步驟](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)。

> [!IMPORTANT]
> 在自訂角色中，Azure 容器註冊表當前不支援萬用字元，例如`Microsoft.ContainerRegistry/*`或`Microsoft.ContainerRegistry/registries/*`授予對所有匹配操作的存取權限。 在角色中單獨指定任何必需的操作。

## <a name="next-steps"></a>後續步驟

* 深入了解使用[Azure 入口網站](../role-based-access-control/role-assignments-portal.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md) 或其他 Azure 工具將 RBAC 角色指派至 Azure 的身分識別。

* 深入了解 Azure Container Registry 的[驗證選項](container-registry-authentication.md)。

* 瞭解如何在容器註冊表中啟用[存儲庫範圍的許可權](container-registry-repository-scoped-permissions.md)（預覽）。