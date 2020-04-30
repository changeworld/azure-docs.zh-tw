---
title: Azure Red Hat OpenShift 4 叢集支援原則
description: 瞭解 Red Hat OpenShift 4 的支援原則需求。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205205"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift 支援原則

Azure Red Hat OpenShift 4 叢集的某些設定可能會影響您叢集的可支援性。 Azure Red Hat OpenShift 4 可讓叢集系統管理員對內部叢集元件進行變更，但不支援所有的變更。 以下的支援原則會共用違反原則的修改，以及 Microsoft 和 Red Hat 的無效支援。

> [!NOTE]
> Azure Red Hat OpenShift 不支援 OpenShift 容器平臺中標示為「技術預覽」的功能。

## <a name="cluster-configuration-requirements"></a>叢集設定需求

* 所有 OpenShift 叢集操作員都必須保持在受控狀態。 執行可傳回叢集操作員的清單`oc get clusteroperators`。
* 請勿移除或修改叢集 Prometheus 和 Alertmanager 服務。
* 請勿移除服務 Alertmanager 規則。
* 請勿修改 OpenShift 叢集版本。
* 請勿移除或修改 Azure Red Hat OpenShift service 記錄（mdsd pod）。
* 請勿移除或修改 ' arosvc.azurecr.io ' 叢集提取密碼。
* 所有叢集虛擬機器都必須具有輸出網際網路存取權，至少要有 Azure Resource Manager （ARM）和服務記錄（Geneva）端點。
* Azure Red Hat OpenShift 服務會透過私用連結服務存取您的叢集。  請勿移除或修改服務存取。
* 不支援非 RHCOS 計算節點。 例如，您無法使用 RHEL 計算節點。

## <a name="supported-virtual-machine-sizes"></a>支援的虛擬機器大小

Azure Red Hat OpenShift 4 支援下列虛擬機器大小的背景工作節點實例：

### <a name="general-purpose"></a>一般用途

|數列|Size|vCPU|記憶體：GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>記憶體最佳化

|數列|Size|vCPU|記憶體：GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>計算最佳化

|數列|Size|vCPU|記憶體：GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
