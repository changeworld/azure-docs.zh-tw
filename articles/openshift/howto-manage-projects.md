---
title: 管理 Azure Red Hat OpenShift 中的資源 |Microsoft Docs
description: 管理 Azure Red Hat OpenShift 叢集中的專案、範本、影像資料流程
services: openshift
keywords: red hat openshift projects 要求自行布建程式
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 0abc086553f5e903a71bcfd0b6322bcee56d2d8b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216924"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>管理 Azure Red Hat OpenShift 叢集中的專案、範本、影像資料流程

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 將于2022年6月30日淘汰。 支援建立新的 Azure Red Hat OpenShift 3.11 叢集會繼續到2020年11月30日。 淘汰之後，剩餘的 Azure Red Hat OpenShift 3.11 叢集將會關閉以防止安全性弱點。
> 
> 遵循本指南來 [建立 Azure Red Hat OpenShift 4](tutorial-create-cluster.md)叢集。
> 如果您有特定問題， [請洽詢我們](mailto:arofeedback@microsoft.com)。

在 OpenShift 容器平臺中，專案是用來群組和隔離相關的物件。 如果您是系統管理員，您可以讓開發人員存取特定專案，讓他們建立自己的專案，並將系統管理許可權授與個別專案。

## <a name="self-provisioning-projects"></a>自我布建專案

您可以讓開發人員建立自己的專案。 API 端點負責根據名為 project-要求的範本來布建專案。 `oc new-project`當開發人員建立新的專案時，web 主控台和命令就會使用此端點。

提交專案要求時，API 會替代範本中的下列參數：

| 參數               | 描述                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | 專案的名稱。 必要。             |
| PROJECT_DISPLAYNAME     | 專案的顯示名稱。 可以是空的。 |
| PROJECT_DESCRIPTION     | 專案的描述。 可以是空的。  |
| PROJECT_ADMIN_USER      | 管理使用者的使用者名稱。       |
| PROJECT_REQUESTING_USER | 提出要求之使用者的使用者名稱。           |

使用自我 provisioners 叢集角色系結將 API 的存取權授與開發人員。 依預設，所有已驗證的開發人員都可以使用這項功能。

## <a name="modify-the-template-for-a-new-project"></a>修改新專案的範本 

1. 以具有許可權的使用者登入 `customer-admin` 。

2. 編輯預設專案要求範本。

   ```
   oc edit template project-request -n openshift
   ```

3. 藉由新增下列注釋，從 Azure Red Hat OpenShift (ARO) 更新程式來移除預設專案範本： `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   專案要求範本將不會由 ARO 更新程式更新。 這可讓客戶自訂範本，並在叢集更新時保留這些自訂專案。

## <a name="disable-the-self-provisioning-role"></a>停用自我布建角色

您可以防止已驗證的使用者群組提供自行布建的新專案。

1. 以具有許可權的使用者登入 `customer-admin` 。

2. 編輯自我 provisioners 叢集角色系結。

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. 藉由新增下列注釋，從 ARO 更新程式移除角色： `openshift.io/reconcile-protect: "true"` 。

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

在 Azure Red Hat OpenShift 中，您可以停用命名空間內部任何預設範本和影像資料流程的更新 `openshift` 。
若要停用所有 `Templates` 和 `ImageStreams` 在 `openshift` 命名空間中的更新：

1. 以具有許可權的使用者登入 `customer-admin` 。

2. 編輯 `openshift` 命名空間：

   ```
   oc edit namespace openshift
   ```

3. 藉 `openshift` 由新增下列注釋來移除 ARO 更新程式中的命名空間： `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   在命名空間中的任何個別物件 `openshift` 都可以藉由新增批註來從更新程式中移除 `openshift.io/reconcile-protect: "true"` 。

## <a name="next-steps"></a>後續步驟

試用教學課程：
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md)
