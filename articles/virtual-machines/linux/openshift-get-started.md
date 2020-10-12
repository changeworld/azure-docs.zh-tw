---
title: Azure 中的 OpenShift 總覽
description: Azure 中的 OpenShift 概觀。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 989c67d0d1b1cd8257da089ec9c31f247228018e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87367940"
---
# <a name="openshift-in-azure"></a>Azure 中的 OpenShift

OpenShift 為企業帶來 Docker 和 Kubernetes 開放且可延伸的容器應用程式平台。  

OpenShift 包含容器協調流程與管理的 Kubernetes。 它新增以開發人員和以作業為中心的工具，可達成：

- 應用程式快速開發。
- 部署及調整更容易。
- 小組和應用程式的長期生命週期維護。

有多個版本的 OpenShift 可用。  在這些版本中，目前只有兩個可供客戶部署至 Azure： OpenShift 容器平臺和 OKD (先前 OpenShift 的原始) 。

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift 是在 Azure 中執行之 OpenShift 的完全受控服務。 這項服務是由 Microsoft 與 Red Hat 共同管理和支援。 如需詳細資訊，請參閱 [Azure Red Hat OpenShift 服務](../../openshift/index.yml) 檔。

## <a name="openshift-container-platform"></a>Red Hat OpenShift 容器平台

「容器平台」是由 Red Hat 提供及支援、符合企業需求的[商業版](https://www.openshift.com)。 使用這個版本，客戶要購買 OpenShift 容器平台的必要權利，並負責安裝與管理整個基礎結構。

由於客戶「擁有」整個平臺，因此可以將它安裝在內部部署資料中心或公用雲端 (（例如 Azure) ）。

## <a name="okd"></a>OKD

OKD 是由社群支援的 OpenShift [開放原始碼](https://www.okd.io/)上游專案。 OKD 可以安裝在 CentOS 或 Red Hat Enterprise Linux (RHEL) 上。

## <a name="next-steps"></a>接下來的步驟

- [在 Azure 中設定 OpenShift 的一般必要條件](./openshift-container-platform-3x-prerequisites.md)
- [在 Azure 中部署 OpenShift 容器平臺](./openshift-container-platform-3x.md)
- [將 OpenShift 容器平臺部署 Self-Managed Marketplace 供應專案](./openshift-container-platform-3x-marketplace-self-managed.md)
- [在 Azure Stack 中部署 OpenShift](./openshift-azure-stack.md)
- [部署後工作](./openshift-container-platform-3x-post-deployment.md)
- [針對 OpenShift 部署進行疑難排解](./openshift-container-platform-3x-troubleshooting.md)
