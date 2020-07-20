---
title: 管理 Azure Red Hat OpenShift 中的安全性內容條件約束 |Microsoft Docs
description: Azure Red Hat OpenShift 叢集系統管理員的安全性內容條件約束
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 24163adcec889e9eedc2362ff1f01f00257a98f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80063183"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>在 Azure Red Hat OpenShift 中管理安全性內容條件約束 

安全性內容條件約束（SCCs）可讓叢集系統管理員控制 pod 的許可權。 若要深入瞭解此 API 類型，請參閱[SCCs 的架構檔](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)。 您可以使用 CLI，以一般 API 物件的形式管理實例中的 SCCs。

## <a name="list-security-context-constraints"></a>列出安全性內容條件約束

若要取得目前的 SCCs 清單，請使用此命令： 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>檢查物件是否有安全性內容條件約束

若要檢查特定的 SCC，請使用 `oc get` 、 `oc describe` 或 `oc edit` 。  例如，若要檢查**受限制**的 SCC，請使用下列命令：
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [建立 Azure Red Hat OpenShift 叢集](tutorial-create-cluster.md) 
