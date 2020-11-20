---
title: 在已啟用 Python 3 的 Linux Azure 虛擬機器系統中使用 VM 擴充功能的問題
description: 瞭解如何在已啟用 Python 3 的 Linux 系統中使用 VM 擴充功能
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: c5593257fa17944eebce6346a1eb9e88e7af2c06
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965947"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>在已啟用 Python 3 的 Linux Azure 虛擬機器系統中使用 VM 擴充功能的問題

> [!NOTE]
> 除非您的工作負載需要 **python** 2.x 支援，否則 Microsoft 鼓勵使用者在其系統中採用 **python** 3.x。 這項需求的範例可能包括舊版管理腳本或延伸模組，例如 **Azure 磁碟加密** 和 **Azure 監視器**。
>
> 在生產環境中安裝 **python** 2.x 之前，請考慮 Python 2.x 長期支援的問題，特別是他們接收安全性更新的能力。 隨著產品（包括部分延伸模組）的更新，請使用 **python 3.8** 支援進行更新，您應該不再使用 python 2.x。

某些 Linux 發行版本已轉換成 Python 3.8，並已 `/usr/bin/python` 完全移除適用于 python 的舊版 entrypoint。 這項轉換會在下列兩種情況下，影響特定虛擬機器 (VM) 擴充功能的預設自動部署：

- 仍在轉換為 Python 3.x 支援的延伸模組
- 使用舊版 entrypoint 的延伸模組 `/usr/bin/python`

轉換至 **Python** 3.x 的 Linux 散發使用者必須確定舊版的進入 `/usr/bin/python` 點存在，然後再嘗試將這些擴充功能部署至其 vm。 否則，延伸模組部署可能會失敗。 

- 受背書的 Linux 發行版本會受到影響，包括 **Ubuntu Server 20.04 LTS** 和 **UBUNTU Pro 20.04 LTS**。

- 受影響的 VM 延伸模組包括 **Azure 磁碟加密**、 **Log Analytics**、用於密碼重設) 的 **vm 存取** (，以及用於其他效能計數器) 的 **來賓診斷** (。

就地升級（例如從 **Ubuntu 18.04 LTS** 升級為 **ubuntu 20.04 LTS**）應該保留 `/usr/bin/python` 符號，且不受影響。

## <a name="resolution"></a>解決方法

在先前的摘要所述的已知受影響案例中部署擴充功能之前，請先考慮這些一般建議：

1. 部署延伸模組之前，請 `/usr/bin/python` 使用 Linux 散發廠商提供的方法恢復符號連結。

   - 例如，針對 **Python 2.7**，請使用： `sudo apt update && sudo apt install python-is-python2`

1. 這項建議適用于 Azure 客戶，在 Azure Stack 中不受支援：

   - 如果您已部署展示此問題的實例，請使用 VM 分頁中的執行命令功能執行上述命令。 執行命令延伸模組本身不會受到轉換至 Python 3.8 的影響。

1. 如果您要部署新的實例，而且需要在布建時設定擴充功能，請使用 **雲端初始** 使用者資料來安裝上述套件。

   例如，針對 Python 2.7：

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. 如果您組織的原則系統管理員判斷不應在 Vm 中部署延伸模組，您可以在布建階段停用擴充功能支援：

   - REST API

     當您可以使用此屬性來部署 VM 時，停用並啟用延伸模組：

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱 [18.04 LTS 之後的其他基底系統變更（預設為 Python 3）](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) 。
