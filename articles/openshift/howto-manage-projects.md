---
title: 管理 Azure Red Hat OpenShift 中的資源 |Microsoft Docs
description: 管理 Azure Red Hat OpenShift 叢集中的專案、範本、影像串流
services: openshift
keywords: red hat openshift projects 要求自我布建程式
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79139108"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>管理 Azure Red Hat OpenShift 叢集中的專案、範本、影像串流 

在 OpenShift 容器平臺中，專案是用來將相關物件分組並加以隔離。 身為系統管理員，您可以為開發人員提供特定專案的存取權，讓他們可以建立自己的專案，並將系統管理許可權授與個別的專案。

## <a name="self-provisioning-projects"></a>自我布建專案

您可以讓開發人員建立自己的專案。 API 端點會負責根據名為專案要求的範本來布建專案。 `oc new-project`當開發人員建立新的專案時，web 主控台和命令會使用此端點。

提交專案要求時，API 會替代範本中的下列參數：

| 參數               | 說明                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 專案的名稱。 必要。             |
| PROJECT_DISPLAYNAME     | 專案的顯示名稱。 可以是空的。 |
| PROJECT_DESCRIPTION     | 專案的描述。 可以是空的。  |
| PROJECT_ADMIN_USER      | 管理使用者的使用者名稱。       |
| PROJECT_REQUESTING_USER | 要求使用者的使用者名稱。           |

API 的存取權會授與具有自我 provisioners 叢集角色系結的開發人員。 根據預設，所有已驗證的開發人員都可以使用這項功能。

## <a name="modify-the-template-for-a-new-project"></a>修改新專案的範本 

1. 以具有許可權的使用者身分登入 `customer-admin` 。

2. 編輯 [預設專案-要求] 範本。

   ```
   oc edit template project-request -n openshift
   ```

3. 藉由新增下列注釋，從 Azure Red Hat OpenShift （ARO）更新程式中移除預設專案範本：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   ARO 更新程式將不會更新專案要求範本。 這可讓客戶自訂範本，並在叢集更新時保留這些自訂專案。

## <a name="disable-the-self-provisioning-role"></a>停用自我布建角色

您可以防止已驗證的使用者群組自行布建新專案。

1. 以具有許可權的使用者身分登入 `customer-admin` 。

2. 編輯自我 provisioners 叢集角色系結。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 藉由新增下列注釋，從 ARO 更新程式中移除角色： `openshift.io/reconcile-protect: "true"` 。

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. 變更叢集角色系結以防止 `system:authenticated:oauth` 建立專案：

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

## <a name="manage-default-templates-and-imagestreams"></a>管理預設範本和 imageStreams

在 Azure Red Hat OpenShift 中，您可以停用命名空間內任何預設範本和影像串流的更新 `openshift` 。
若要 `Templates` `ImageStreams` 在命名空間中停用所有和的更新 `openshift` ：

1. 以具有許可權的使用者身分登入 `customer-admin` 。

2. 編輯 `openshift` 命名空間：

   ```
   oc edit namespace openshift
   ```

3. 藉 `openshift` 由新增下列注釋，從 ARO 更新程式中移除命名空間：`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   命名空間中的任何個別物件 `openshift` 都可以藉由在其中加入批註，從更新程式中移除 `openshift.io/reconcile-protect: "true"` 。

## <a name="next-steps"></a>後續步驟

嘗試教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
