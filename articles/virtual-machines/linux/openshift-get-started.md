---
title: Azure 概覽中的打開移位
description: Azure 中的 OpenShift 概觀。
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
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035415"
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 為企業帶來 Docker 和 Kubernetes 開放且可延伸的容器應用程式平台。  

OpenShift 包含容器協調流程與管理的 Kubernetes。 它新增以開發人員和以作業為中心的工具，可達成：

- 應用程式快速開發。
- 部署及調整更容易。
- 小組和應用程式的長期生命週期維護。

OpenShift 有多個版本可用。  在這些版本中，目前只有兩個可供客戶在 Azure 中部署：OpenShift 容器平臺和 OKD（以前是 OpenShift 源）。

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure 紅帽 OpenShift 是 Azure 中運行的完全託管的 OpenShift 產品。 這項服務是由 Microsoft 與 Red Hat 共同管理和支援。 有關詳細資訊，請參閱 Azure[紅帽開放移位服務](https://docs.microsoft.com/azure/openshift/)文檔。

## <a name="openshift-container-platform"></a>Red Hat OpenShift 容器平台

「容器平台」是由 Red Hat 提供及支援、符合企業需求的[商業版](https://www.openshift.com)。 使用這個版本，客戶要購買 OpenShift 容器平台的必要權利，並負責安裝與管理整個基礎結構。

由於客戶"擁有"整個平臺，他們可以將其安裝在其本地資料中心或公共雲（如 Azure）中。

## <a name="okd"></a>OKD

OKD 是由社群支援的 OpenShift [開放原始碼](https://www.okd.io/)上游專案。 OKD 可以安裝在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上。

## <a name="next-steps"></a>後續步驟

- [在 Azure 中設定 OpenShift 的一般必要條件](./openshift-container-platform-3x-prerequisites.md)
- [在 Azure 中部署 OpenShift 容器平臺](./openshift-container-platform-3x.md)
- [部署 OpenShift 容器平臺自管理市場產品/服務](./openshift-container-platform-3x-marketplace-self-managed.md)
- [在 Azure Stack 中部署 OpenShift](./openshift-azure-stack.md)
- [部署後工作](./openshift-container-platform-3x-post-deployment.md)
- [針對 OpenShift 部署進行疑難排解](./openshift-container-platform-3x-troubleshooting.md)
