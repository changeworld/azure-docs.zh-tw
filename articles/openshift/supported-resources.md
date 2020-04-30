---
title: Azure Red Hat OpenShift 3.11 支援的資源
description: 瞭解 Microsoft Azure Red Hat OpenShift 支援哪些 Azure 區域和虛擬機器大小。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82203670"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift 資源

本主題列出 Microsoft Azure Red Hat OpenShift 3.11 服務支援的 Azure 區域和虛擬機器大小。

## <a name="azure-regions"></a>Azure 區域

如需您可以部署 Azure Red Hat OpenShift 叢集的最新區域清單，請參閱[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

## <a name="virtual-machine-sizes"></a>虛擬機器大小

以下是您可以為 Azure Red Hat OpenShift 叢集中的計算節點指定的支援虛擬機器大小。

> [!Important]
> 每部 VM 都有不同數目的磁片磁碟機可以附加。 這可能不會立即清除為記憶體或 CPU 大小。
> 並非所有 VM 大小在所有區域都可供使用。 即使 API 支援您指定的大小，如果您指定的區域無法使用此大小，您可能會收到錯誤。
> 如需詳細資訊，請參閱[每個區域支援的 VM 大小的最新清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="compute-node-sizes"></a>計算節點大小

Azure Red Hat OpenShift REST API 支援下列計算節點大小：

|Size|vCPU|RAM|
|-|-|-|
|標準 D4s v3 系列|4|16 GB|
|標準 D8s v3 系列|8|32 GB|
|標準 D16s v3 系列|16|64 GB|
|標準 D32s v3 系列|32|128 GB|
|-|-|-|
|標準 E4s v3 系列|4|32 GB|
|標準 E8s v3 系列|8|64 GB|
|標準 E16s v3 系列|16|128 GB|
|標準 E32s v3 系列|32|256 GB|
|-|-|-|
|標準 F8s v2|8|16 GB|
|標準 F16s v2|16|32 GB|
|標準 F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>主要節點大小

Azure Red Hat OpenShift REST API 支援下列主要/基礎結構節點大小：

|Size|vCPU|RAM|
|-|-|-|
|標準 D4s v3 系列|4|16 GB|
|標準 D8s v3 系列|8|32 GB|
|標準 D16s v3 系列|16|64 GB|
|標準 D32s v3 系列|32|128 GB|

## <a name="next-steps"></a>後續步驟

請嘗試[建立 Azure Red Hat OpenShift](tutorial-create-cluster.md)叢集教學課程。
