---
title: Azure Red Hat OpenShift 3.11 支援的資源
description: 瞭解 Microsoft Azure Red Hat OpenShift 支援哪些 Azure 區域和虛擬機器大小。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 38203cede755d776ba9142ad16e1bea1fd1637a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203670"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure Red Hat OpenShift 資源

本主題列出 Microsoft Azure Red Hat OpenShift 3.11 服務所支援的 Azure 區域和虛擬機器大小。

## <a name="azure-regions"></a>Azure 區域

查看 [依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) ，以取得您可以部署 Azure Red Hat OpenShift 叢集的目前區域清單。

## <a name="virtual-machine-sizes"></a>虛擬機器大小

以下是您可以為 Azure Red Hat OpenShift 叢集中的計算節點指定的支援虛擬機器大小。

> [!Important]
> 每個 VM 都有不同數量的可連接磁片磁碟機。 這可能不會像記憶體或 CPU 大小一樣立即清除。
> 並非所有 VM 大小在所有區域都可供使用。 即使 API 支援您指定的大小，如果您指定的區域中無法使用該大小，您可能會收到錯誤。
> 如需詳細資訊，請參閱 [每個區域的目前支援 VM 大小清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) 。

## <a name="compute-node-sizes"></a>計算節點大小

Azure Red Hat OpenShift REST API 支援下列計算節點大小：

|大小|vCPU|RAM|
|-|-|-|
|標準 D4s v3|4|16 GB|
|標準 D8s v3|8|32 GB|
|標準 D16s v3|16|64 GB|
|標準 D32s v3|32|128 GB|
|-|-|-|
|標準 E4s v3|4|32 GB|
|標準 E8s v3|8|64 GB|
|標準 E16s v3|16|128 GB|
|標準 E32s v3|32|256 GB|
|-|-|-|
|標準 F8s v2|8|16 GB|
|標準 F16s v2|16|32 GB|
|標準 F32s v2|32|64 GB|

## <a name="master-node-sizes"></a>主要節點大小

Azure Red Hat OpenShift REST API 支援下列主要/基礎結構節點大小：

|大小|vCPU|RAM|
|-|-|-|
|標準 D4s v3|4|16 GB|
|標準 D8s v3|8|32 GB|
|標準 D16s v3|16|64 GB|
|標準 D32s v3|32|128 GB|

## <a name="next-steps"></a>接下來的步驟

請嘗試 [建立 Azure Red Hat OpenShift](tutorial-create-cluster.md) 叢集教學課程。
