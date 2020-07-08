---
title: Azure Batch 集區建立事件
description: Batch 集區建立事件 (在建立集區之後發出) 的參考。 記錄檔內容將公開集區的一 般資訊。
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d8e4537e0074b7af1e65ea5f13a9668483abc45e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962487"
---
# <a name="pool-create-event"></a>集區建立事件

 一旦集區建立完成，就會發出此事件。 記錄檔內容將公開集區的一 般資訊。 請注意，如果集區的目標大小大於 0 個計算節點，則集區調整開始事件將緊接在此事件之後。

 對於使用 `CloudServiceConfiguration` 屬性建立的集區，以下範例顯示集區建立事件的內文。

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|元素|類型|注意|
|-------------|----------|-----------|
|`id`|String|集區識別碼。|
|`displayName`|String|集區顯示名稱。|
|`vmSize`|String|集區中虛擬機器的大小。 集區中所有虛擬機器的大小相同。 <br/><br/> 如需雲端服務集區 (使用 cloudServiceConfiguration 建立的集區) 的虛擬機器可用大小相關資訊，請參閱[雲端服務的大小](../cloud-services/cloud-services-sizes-specs.md)。 Batch 支援 `ExtraSmall` 以外的所有雲端服務 VM 大小。<br/><br/> 如需使用 Virtual Machines Marketplace 中映像的集區 (使用 virtualMachineConfiguration 建立的集區) 其可用 VM 大小的相關資訊，請參閱[虛擬機器大小](/azure/virtual-machines/linux/sizes?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json) (英文) (Linux) 或 [Azure 中 Windows 虛擬機器的大小](/azure/virtual-machines/windows/sizes?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) (Windows)。 除了 `STANDARD_A0` 和進階儲存體的大小 (`STANDARD_GS`、`STANDARD_DS` 和 `STANDARD_DSV2` 系列) 以外，Batch 支援所有的 Azure VM 大小。|
|`imageType`|String|映像的部署方法。 支援的值為 `virtualMachineConfiguration` 或 `cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|複雜類型|集區的雲端服務組態。|
|[`virtualMachineConfiguration`](#bk_vmconf)|複雜類型|集區的虛擬機器組態。|
|[`networkConfiguration`](#bk_netconf)|複雜類型|集區的網路組態。|
|`resizeTimeout`|Time|將計算節點配置至為集區上最後一次調整大小作業所指定的集區逾時。  (建立集區時的初始大小視為一次調整大小。)|
|`targetDedicatedNodes`|Int32|向集區要求的專用計算節點數目。|
|`targetLowPriorityNodes`|Int32|向集區要求的低優先順序計算節點數目。|
|`enableAutoScale`|Bool|指定集區大小是否隨著時間自動調整。|
|`enableInterNodeCommunication`|Bool|指定是否針對節點間的直接通訊設定集區。|
|`isAutoPool`|Bool|指定是否已透過作業的 AutoPool 機制建立集區。|
|`maxTasksPerNode`|Int32|可在集區內單一計算節點上並行執行的工作數目上限。|
|`vmFillType`|String|定義 Batch 服務如何在集區中的計算節點間散發工作。 有效值為 Spread 或 Pack。|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a> cloudServiceConfiguration

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`osFamily`|String|安裝在集區中虛擬機器上的 Azure 客體 OS 系列。<br /><br /> 可能的值包括：<br /><br /> **2** – OS 系列 2，相當於 Windows Server 2008 R2 SP1。<br /><br /> **3** – OS 系列 3，相當於 Windows Server 2012。<br /><br /> **4** – OS 系列 4，相當於Windows Server 2012 R2。<br /><br /> 如需詳細資訊，請參閱[客體 ​OS 發佈新聞](../cloud-services/cloud-services-guestos-update-matrix.md#releases)。|
|`targetOSVersion`|String|安裝在集區中虛擬機器上的 Azure 客體 OS 版本。<br /><br /> 預設值為 **\*** 用於指定所指定系列的最新作業系統版本。<br /><br /> 如需其他允許的值，請參閱[客體 OS 發佈新聞](../cloud-services/cloud-services-guestos-update-matrix.md#releases)。|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a> virtualMachineConfiguration

|元素名稱|類型|注意|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|複雜類型|指定要使用的平台或 Marketplace 映像的相關資訊。|
|`nodeAgentId`|String|佈建在計算節點上的 Batch 節點代理程式其 SKU。|
|[`windowsConfiguration`](#bk_winconf)|複雜類型|指定虛擬機器上的 Windows 作業系統設定。 如果 imageReference 正在參照 Linux 作業映像，則不得指定此屬性。|

###  <a name="imagereference"></a><a name="bk_imgref"></a> imageReference

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`publisher`|String|映像的發行者。|
|`offer`|String|映像的供應項目。|
|`sku`|String|映像的 SKU。|
|`version`|String|映像的版本。|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a> windowsConfiguration

|元素名稱|類型|注意|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Boolean|指出是否已針對自動更新啟用虛擬機器。 如果未指定此屬性，則預設值為 true。|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a> networkConfiguration

|元素名稱|類型|注意|
|------------------|--------------|----------|
|`subnetId`|String|指定建立集區的計算節點所在的子網路其資源識別碼。|
