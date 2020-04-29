---
title: 使用 Azure 虛擬機器修復命令修復 Windows VM |Microsoft Docs
description: 本文詳細說明如何使用 Azure VM 修復命令將磁片連線至另一個 Windows VM，以修正任何錯誤，然後重建您的原始 VM。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80060680"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>使用 Azure 虛擬機器修復命令修復 Windows VM

如果您在 Azure 中的 Windows 虛擬機器（VM）發生開機或磁片錯誤，您可能需要在磁片本身執行緩和措施。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure VM 修復命令將磁片連線至另一個 Windows VM，以修正任何錯誤，然後重建您的原始 VM。

> [!IMPORTANT]
> 本文中的腳本僅適用于使用[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的 vm。

## <a name="repair-process-overview"></a>修復程式總覽

您現在可以使用 Azure VM 修復命令來變更 VM 的 OS 磁片，而不再需要刪除並重新建立 VM。

請遵循下列步驟來針對 VM 問題進行疑難排解：

1. 啟動 Azure Cloud Shell
2. 執行 az extension add/update。
3. 執行 az vm repair create。
4. 執行 az vm repair run。
5. 執行 az vm repair restore。

如需其他檔和指示，請參閱[az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)。

## <a name="repair-process-example"></a>修復程式範例

> [!NOTE]
> * 需要 VM 的輸出連線能力（埠443），腳本才能執行。
> * 一次只能執行一個腳本。
> * 無法取消執行中的腳本。
> * 腳本可執行檔時間上限為90分鐘，之後就會超時。

1. 啟動 Azure Cloud Shell

   Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 其中包含預先安裝和設定的通用 Azure 工具，可與您的帳戶搭配使用。

   若要開啟 Cloud Shell，請選取程式碼區塊右上角的 [**試試看**]。 您也可以造訪[https://shell.azure.com](https://shell.azure.com)，在另一個瀏覽器索引標籤中開啟 Cloud Shell。

   選取 [**複製**] 以複製程式碼區塊，然後將程式碼貼入 Cloud Shell 中，然後選取**Enter**加以執行。

   如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.30 版或更新版本。 執行 ``az --version`` 以尋找版本。 如果您需要安裝或升級您的 Azure CLI，請參閱[安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

2. 如果這是您第一次使用這些`az vm repair`命令，請新增 vm-修復 CLI 擴充功能。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   如果您先前已使用這些`az vm repair`命令，請將任何更新套用至 vm-修復延伸模組。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. 執行 `az vm repair create`。 此命令會為非功能性 VM 建立 OS 磁片的複本、建立修復 VM，以及連接磁片。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 執行 `az vm repair run`。 此命令會透過修復 VM，在連接的磁片上執行指定的修復腳本。

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. 執行 `az vm repair restore`。 此命令會將已修復的 OS 磁片與 VM 的原始 OS 磁片交換。

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>確認並啟用開機診斷

下列範例會在資源群組 ``myResourceGroup`` 中的 VM ``myVMDeployed`` 上啟用診斷擴充：

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>後續步驟

* 如果連接至 VM 時發生問題，請參閱[針對 Azure VM 的 RDP 連接進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)。
* 針對存取在 VM 上執行之應用程式的問題，請參閱針對[Azure 中的虛擬機器上的應用程式連線問題進行疑難排解](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)。
* 如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。
