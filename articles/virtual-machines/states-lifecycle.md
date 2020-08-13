---
title: Azure 中 VM 的生命週期和狀態
description: 概述 Azure 中的 VM 生命週期，包括 VM 隨時可以使用的各種狀態說明。
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 0613b4c444b9eacaaf2b9d3e0795f4872cb903f3
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88182933"
---
# <a name="virtual-machines-lifecycle-and-states"></a>虛擬機器的生命週期和狀態

Azure 虛擬機器 (VM) 會經歷不同狀態，這些狀態可以分成「佈建」** 和「電源」** 狀態。 本文的目的是要說明這些狀態，並具體指出何時會向客戶收取執行個體使用量費用。 

## <a name="power-states"></a>電源狀態

電源狀態代表 VM 的最後一個已知狀態。

![VM 電源狀態圖](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
下表提供每個執行個體狀態的描述，並指出是否會收取其執行個體使用量費用。

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="":::

   **描述**

   :::column-end:::
   :::column span="":::

   **實例使用量計費**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **啟動中**

   :::column-end:::
   :::column span="":::

   VM 正在啟動。

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **不會計費**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **執行中**

   :::column-end:::
   :::column span="":::

   VM 的正常工作狀態

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **支付**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **停止中**

   :::column-end:::
   :::column span="":::

   這是過渡狀態。 完成時，便會顯示為**已停止**。

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **支付**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **已停止**

   :::column-end:::
   :::column span="":::

   已從客體 OS 內或藉由使用 PowerOff API 關閉 VM。

   硬體仍配置給 VM，且會留在主機上。

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **支付***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **正在解除配置**

   :::column-end:::
   :::column span="":::

   過渡狀態。 完成時，VM 會顯示為**已解除配置**。

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **未計費***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **已解除配置**

   :::column-end:::
   :::column span="":::

   VM 已成功停止，並從主機中移除。

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **不會計費**

   :::column-end:::
:::row-end:::


&#42; 某些 Azure 資源（例如磁片和網路）會產生費用。 執行個體上的軟體授權不會產生費用。

## <a name="provisioning-states"></a>佈建狀態

佈建狀態是使用者在 VM 上所起始的控制平面作業狀態。 這些狀態與 VM 的電源狀態不同。

- **建立** – 建立 VM。

- **更新** – 更新現有 VM 的模型。 對 VM 所做的某些非模型變更 (例如，啟動/重新啟動) 也屬於更新。

- **刪除** – 刪除 VM。

- **解除配置** – 此狀態會停止 VM 並將其從主機中移除。 解除配置 VM 可視為更新，所以會顯示與更新相關的佈建狀態。



平台接受使用者所起始的動作後，會有以下過渡作業狀態：

:::row:::
   :::column span="":::

   **State**
   
   :::column-end:::
   :::column span="2":::

   **描述**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **建立中**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **更新**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **刪除中**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OS 佈建狀態**
   
   :::column-end:::
   :::column span="2":::

   **描述**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   如果使用 OS 映像而非特製化映像來建立 VM，則會觀察到下列子狀態：

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   VM 正在執行，且正在進行來賓 OS 的安裝。
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   短期的狀態。 除非需要安裝任何擴充功能，否則 VM 會快速過渡為**成功**狀態。 安裝擴充功能需要一些時間。
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **附註**：如果發生 OS 失敗或 OS 未及時安裝，則 OS 佈建會過渡為**失敗**狀態。 客戶必須就基礎結構上已部署的 VM 支付費用。

   :::column-end:::

:::row-end:::

作業完成後，VM 會過渡為下列其中一個狀態：

- **成功** – 使用者起始的動作已完成。

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **失敗** – 代表失敗的作業。 請參閱錯誤碼，以取得詳細資訊和可能的解決方案。

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>VM 執行個體檢視

執行個體檢視 API 會提供 VM 的執行狀態資訊。 如需詳細資訊，請參閱[虛擬機器 - 執行個體檢視](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) API。

Azure 資源總管會提供一個簡單的 UI 供您檢視 VM 的執行中狀態：[資源總管](https://resources.azure.com/)。

佈建狀態可於 VM 屬性和執行個體檢視中看到。 電源狀態可於 VM 的執行個體檢視中看到。

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何監視您的 VM，請參閱[在 Azure 中監視虛擬機器](../azure-monitor/insights/monitor-vm-azure.md)。