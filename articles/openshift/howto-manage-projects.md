---
title: 管理 Azure 紅帽開放移位中的資源 |微軟文檔
description: 在 Azure 紅帽 OpenShift 群集中管理專案、範本和圖像流
services: openshift
keywords: 紅帽開檔專案請求自編
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139108"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>在 Azure 紅帽 OpenShift 群集中管理專案、範本和圖像流 

在 OpenShift 容器平臺中，專案用於對相關物件進行分組和隔離。 作為管理員，您可以授予開發人員對特定專案的許可權，允許他們創建自己的專案，並授予他們各個專案的管理許可權。

## <a name="self-provisioning-projects"></a>自調配專案

您可以使開發人員創建自己的專案。 API 終結點負責根據名為專案請求的範本預配專案。 當開發人員創建新專案時`oc new-project`，Web 主控台和命令使用此終結點。

提交專案請求時，API 將替換範本中的以下參數：

| 參數               | 描述                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 專案的名稱。 必要。             |
| PROJECT_DISPLAYNAME     | 專案的顯示名稱。 可以是空的。 |
| PROJECT_DESCRIPTION     | 專案的描述。 可以是空的。  |
| PROJECT_ADMIN_USER      | 管理使用者的使用者名。       |
| PROJECT_REQUESTING_USER | 請求使用者的使用者名。           |

使用自預配器群集角色綁定授予開發人員對 API 的訪問。 預設情況下，此功能可供所有經過身份驗證的開發人員使用。

## <a name="modify-the-template-for-a-new-project"></a>修改新專案的範本 

1. 以具有`customer-admin`許可權的使用者身份登錄。

2. 編輯預設專案請求範本。

   ```
   oc edit template project-request -n openshift
   ```

3. 通過添加以下注釋，從 Azure 紅帽 OpenShift （ARO） 更新流程中刪除預設專案範本：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   專案請求範本不會由 ARO 更新過程更新。 這使客戶能夠自訂範本並在更新群集時保留這些自訂項。

## <a name="disable-the-self-provisioning-role"></a>禁用自我預配角色

您可以阻止經過身份驗證的使用者組自行預配新專案。

1. 以具有`customer-admin`許可權的使用者身份登錄。

2. 編輯自預配器群集角色綁定。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 通過添加以下注釋從 ARO 更新過程中刪除角色： `openshift.io/reconcile-protect: "true"`。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 更改群集角色綁定以防止`system:authenticated:oauth`創建專案：

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>管理預設範本和圖像流

在 Azure 紅帽 OpenShift 中，可以禁用命名空間內`openshift`任何預設範本和圖像流的更新。
要禁用 ALL`Templates`和`ImageStreams`命名`openshift`空間中的更新，

1. 以具有`customer-admin`許可權的使用者身份登錄。

2. 編輯`openshift`命名空間：

   ```
   oc edit namespace openshift
   ```

3. 通過`openshift`添加以下注釋從 ARO 更新過程中刪除命名空間：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   可以通過向更新進程添加`openshift`注釋`openshift.io/reconcile-protect: "true"`來從更新過程中刪除命名空間中的任何單個物件。

## <a name="next-steps"></a>後續步驟

請嘗試本教程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
