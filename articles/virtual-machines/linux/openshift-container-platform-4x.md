---
title: 在 Azure 中部署 OpenShift 容器平臺 4.x
description: 在 Azure 中部署 OpenShift 容器平臺 4.x。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 14af110b5cf50f167d0c4961e26454bc33c6ed7d
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759487"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺 4.x

現在,Azure 中透過安裝程式配置基礎結構 (IPI) 模型支援部署 OpenShift 容器平臺 (OCP) 4.2。  試著 OpenShift 4 的著陸頁[try.openshift.com](https://try.openshift.com/)。 要在 Azure 中安裝 OCP 4.2,請造[訪紅帽 OpenShift 叢集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)頁面。  訪問此站點需要紅帽憑據。


## <a name="notes"></a>注意 

 - 在 Azure 中安裝與執行 OCP 4.x 需要 Azure 活動目錄 (AAD) 服務主體 (SP)
     - 必須設定式 API 權限 **。**
     - AAD 租戶管理員必須授予管理員同意才能使此 API 生效
     - 必須給訂閱**的參與者**與 **「使用者存取管理員」** 角色
 - OCP 4.x 的安裝模型與 3.x 不同,並且沒有可用於在 Azure 中部署 OCP 4.x 的 Azure 資源管理員範本
 - 如果在安裝過程中遇到問題,請聯繫相應的公司(微軟或紅帽)

| 問題描述 | 連絡點 |
|-------------------|---------------|
| Azure 特定問題(AAD、SP、Azure 訂閱等)                              | Microsoft |
| OpenShift 特定問題(安裝失敗/錯誤、紅帽訂閱等) |  Red Hat  |




## <a name="next-steps"></a>後續步驟

- [使用 OpenShift 容器平台入門](https://docs.openshift.com)
