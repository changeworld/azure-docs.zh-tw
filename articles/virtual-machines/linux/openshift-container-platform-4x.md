---
title: 在 Azure 中部署 OpenShift 容器平臺 4.x
description: 在 Azure 中部署 OpenShift 容器平臺 4.x。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 213c02b76f822d134729ebc4c0e6bff40f62089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035445"
---
# <a name="deploy-openshift-container-platform-4x-in-azure"></a>在 Azure 中部署 OpenShift 容器平臺 4.x

現在，Azure 中通過安裝程式配置基礎結構 （IPI） 模型支援部署 OpenShift 容器平臺 （OCP） 4.2。  嘗試 OpenShift 4 的著陸頁[try.openshift.com](https://try.openshift.com/)。 要在 Azure 中安裝 OCP 4.2，請訪問[紅帽 OpenShift 群集管理器](https://cloud.redhat.com/openshift/install/azure/installer-provisioned)頁面。  訪問此網站需要紅帽憑據。


## <a name="notes"></a>注意 

 - 在 Azure 中安裝和運行 OCP 4.x 需要 Azure 活動目錄 （AAD） 服務主體 （SP）
     - 必須授予 SP 應用程式的 API 許可權 **。**
     - AAD 租戶管理員必須授予管理員同意才能使此 API 生效
     - 必須授予訂閱**的"參與者**"和 **"使用者訪問管理員"** 角色
 - OCP 4.x 的安裝模型與 3.x 不同，並且沒有可用於在 Azure 中部署 OCP 4.x 的 Azure 資源管理器範本
 - 如果在安裝過程中遇到問題，請聯繫相應的公司（微軟或紅帽）

| 問題描述 | 聯繫點 |
|-------------------|---------------|
| Azure 特定問題（AAD、SP、Azure 訂閱等）                              | Microsoft |
| OpenShift 特定問題（安裝失敗/錯誤、紅帽訂閱等） |  Red Hat  |




## <a name="next-steps"></a>後續步驟

- [使用 OpenShift 容器平臺入門](https://docs.openshift.com)
