---
title: 在 Azure 中部署 OpenShift 容器 Platform 4。x
description: 在 Azure 中部署 OpenShift 容器 Platform 4.x。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: f2fb1657c26fce3e1fdd67f36b0c6511b78dd216
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373533"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器 Platform 4。x

Azure 現在透過 Installer-Provisioned 基礎結構 (IPI) 模型，支援 OpenShift 容器平臺 (OCP) 4.2 的部署。  嘗試 OpenShift 4 的登陸頁面為 [try.openshift.com](https://try.openshift.com/)。 若要在 Azure 中安裝 OCP 4.2，請造訪 [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) 頁面。  需要 Red Hat 認證才能存取此網站。


## <a name="notes"></a>注意 

 - 需要 Azure Active Directory (AAD) 服務主體 (SP) ，才能在 Azure 中安裝和執行 OCP 4。x
     - 必須將 Azure Active Directory Graph 的 **應用程式 OWNEDBY** API 許可權授與 SP
     - AAD 租用戶系統管理員必須授與系統管理員同意，此 API 許可權才會生效
     - SP 必須被授與訂用帳戶的 **參與者** 和 **使用者存取系統管理員** 角色
 - OCP 4.x 的安裝模型不同于3.x，而且沒有可在 Azure 中部署 OCP 4.x 的 Azure Resource Manager 範本
 - 如果在安裝過程中遇到問題，請洽詢適當的公司 (Microsoft 或 Red Hat) 

| 問題描述 | 連絡人點 |
|-------------------|---------------|
| Azure 特定問題 (AAD、SP、Azure 訂用帳戶等 )                               | Microsoft |
| OpenShift 特定問題 (安裝失敗/錯誤、Red Hat 訂用帳戶等 )  |  Red Hat  |




## <a name="next-steps"></a>接下來的步驟

- [開始使用 OpenShift 容器平臺](https://docs.openshift.com)
