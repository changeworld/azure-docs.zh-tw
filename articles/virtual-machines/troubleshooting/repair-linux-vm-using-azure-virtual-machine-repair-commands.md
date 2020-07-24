---
title: 使用 Azure 虛擬機器修復命令修復 Linux VM | Microsoft Docs
description: 本文詳細說明如何使用 Azure 虛擬機器修復命令將磁碟連線至另一個 Linux VM 以修正任何錯誤，然後重建您的原始 VM。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: c7fbe46d378d45f49a8510f9fdd01a9cae665d0b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074384"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>使用 Azure 虛擬機器修復命令修復 Linux VM

如果 Azure 中的 Linux 虛擬機器 (VM) 發生開機或磁碟錯誤，您可能需要對磁碟本身執行風險降低步驟。 常見的例子是應用程式更新無效，導致 VM 無法成功開機。 本文詳細說明如何使用 Azure 虛擬機器修復命令將磁碟連線至另一個 Linux VM 以修正任何錯誤，然後重建您的原始 VM。

> [!IMPORTANT]
> * 本文中的指令碼只適用於使用 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 的 VM。
> * VM 需要輸出連線能力 (連接埠 443)，才能執行指令碼。
> * 一次只能執行一個指令碼。
> * 無法取消執行中的指令碼。
> * 指令碼可以執行的最長時間是 90 分鐘，經過這段時間後會逾時。
> * 針對使用 Azure 磁碟加密的 Vm，只支援使用單一傳遞加密（含或不含 KEK）加密的受控磁片。

## <a name="repair-process-overview"></a>修復流程概觀

您現在可以使用 Azure 虛擬機器修復命令來變更 VM 的 OS 磁碟，而不再需要先刪除再重新建立 VM。

請遵循下列步驟來針對 VM 問題進行疑難排解：

1. 啟動 Azure Cloud Shell
2. 執行 az extension add/update
3. 執行 az vm repair create
4. 執行風險降低步驟
5. 執行 az vm repair restore

如需其他文件和指示，請參閱 [az vm repair](/cli/azure/ext/vm-repair/vm/repair)。

## <a name="repair-process-example"></a>修復流程範例

1. 啟動 Azure Cloud Shell

   Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 其包含預先安裝和設定好的通用 Azure 工具，可與您的帳戶搭配使用。

   若要開啟 Cloud Shell，請選取程式碼區塊右上角的 [試試看]。 您也可以移至 [https://shell.azure.com](https://shell.azure.com)，從另一個瀏覽器索引標籤開啟 Cloud Shell。

   選取 [複製] 即可複製程式碼區塊，將程式碼貼到 Cloud Shell 中，然後選取 **Enter** 鍵加以執行。

   如果您偏好在本機安裝和使用 CLI，本快速入門需要有 Azure CLI 2.0.30 版或更新版本。 執行 ``az --version`` 以尋找版本。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。
   
   如果您需要使用不同于目前登入 Azure 入口網站的帳戶來登入 Cloud Shell，您可以使用 ``az login`` [az login reference](/cli/azure/reference-index?view=azure-cli-latest#az-login)。  若要在與您帳戶相關聯的訂閱之間切換，您可以使用 ``az account set --subscription`` [az account set reference](/cli/azure/account?view=azure-cli-latest#az-account-set)。

2. 如果這是您第一次使用 `az vm repair` 命令，請新增 vm-repair CLI 擴充功能。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   如果您先前使用過 `az vm repair` 命令，請將任何更新套用至 vm-repair 擴充功能。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. 執行 `az vm repair create`。 此命令會建立非功能性 VM 的 OS 磁碟複本、在新的資源群組中建立修復 VM，以及連結 OS 磁碟複本。  修復 VM 的大小和區域會與指定的非功能性 VM 相同。 所有步驟中使用的資源群組和 VM 名稱將適用于非功能性 VM。 如果您的 VM 使用 Azure 磁碟加密命令會嘗試解除鎖定加密的磁片，以便在連接至修復 VM 時可存取。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 在已建立的修復 VM 上執行任何所需的風險降低步驟，然後繼續進行步驟 5。

5. 執行 `az vm repair restore`。 此命令會將已修復的 OS 磁碟與 VM 的原始 OS 磁碟交換。 此處使用的資源群組和 VM 名稱適用于步驟3中使用的非功能 VM。

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

* 如果連線至 VM 時發生問題，請參閱[針對 Azure 虛擬機器的 RDP 連線進行疑難排解](./troubleshoot-rdp-connection.md)。
* 如果存取 VM 上執行的應用程式時發生問題，請參閱[針對 Azure 虛擬機器上的應用程式連線問題進行疑難排解](./troubleshoot-app-connection.md)。
* 如需使用 Resource Manager 的詳細資訊，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。
