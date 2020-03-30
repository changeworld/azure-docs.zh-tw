---
title: Azure 紅帽開放移位的支援資源
description: 瞭解 Microsoft Azure 紅帽 OpenShift 支援哪些 Azure 區域和虛擬機器大小。
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 1b794852390141d31a3218a1a434c21133db914c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243662"
---
# <a name="azure-red-hat-openshift-resources"></a>Azure 紅帽開放移位資源

本主題列出了 Microsoft Azure 紅帽 OpenShift 服務支援的 Azure 區域和虛擬機器大小。

## <a name="azure-regions"></a>Azure 區域

有關可以部署 Azure 紅帽開放Shift 群集的區域當前清單，請參閱[按區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)。

## <a name="virtual-machine-sizes"></a>虛擬機器大小

以下是可以為 Azure 紅帽 OpenShift 群集中的計算節點指定的受支援的虛擬機器大小。

> [!Important]
> 每個 VM 都有不同數量的磁碟機可以連接。 這可能不能像記憶體或 CPU 大小那樣立即清晰。
> 並非所有 VM 大小在所有區域都可供使用。 即使 API 支援您指定的大小，如果大小在指定的區域中不可用，也可能收到錯誤。
> 有關詳細資訊[，請參閱每個區域支援的 VM 大小的當前清單](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)。

## <a name="compute-node-sizes"></a>計算節點大小

Azure 紅帽 OpenShift REST API 支援以下計算節點大小：

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

## <a name="master-node-sizes"></a>主節點大小

Azure 紅帽 OpenShift REST API 支援以下主節點/基礎結構節點大小：

|大小|vCPU|RAM|
|-|-|-|
|標準 D4s v3|4|16 GB|
|標準 D8s v3|8|32 GB|
|標準 D16s v3|16|64 GB|
|標準 D32s v3|32|128 GB|

## <a name="next-steps"></a>後續步驟

嘗試[創建 Azure 紅帽 OpenShift 群集](tutorial-create-cluster.md)教程。
