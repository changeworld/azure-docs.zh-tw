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
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82120778"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>在已啟用 Python 3 的 Linux Azure 虛擬機器系統中使用 VM 擴充功能的問題

> [!NOTE]
> 除非您的工作負載需要**python** 2.x 支援，否則 Microsoft 鼓勵使用者在其系統中採用**python** 3.x。 這項需求的範例可能包括舊版系統管理腳本，或**Azure 磁碟加密**和**Azure 監視器**等延伸模組。
>
> 在生產環境中安裝**Python** 2.x 之前，請考慮 Python 2.x 長期支援的問題，特別是他們接收安全性更新的能力。 如產品（包括部分擴充功能）更新為**python 3.8**支援，您應該停止使用 python 2.x。

某些 Linux 發行版本已轉換成 Python 3.8，並已`/usr/bin/python`完全移除 python 的舊版進入點。 這項轉換會影響特定虛擬機器（VM）延伸模組的預設自動部署，並具有下列條件：

- 仍轉換成 Python 3.x 支援的延伸模組
- 使用舊版`/usr/bin/python`進入點的擴充功能

轉換成**Python** 3.x 的 Linux 散發使用者必須先確定舊版`/usr/bin/python`進入點存在，然後再嘗試將這些擴充功能部署至其 vm。 否則，延伸模組部署可能會失敗。 

- 受背書的 Linux 發行版本受到影響，包括**Ubuntu Server 20.04 LTS**和**UBUNTU Pro 20.04 LTS**。

- 受影響的 VM 擴充功能包括**Azure 磁碟加密**、 **Log Analytics**、 **VM 存取**（用於密碼重設）和**來賓診斷**（用於其他效能計數器）。

就地升級（例如從**Ubuntu 18.04 LTS**升級至**ubuntu 20.04 LTS**）應保留`/usr/bin/python`符號，並保持不受影響。

## <a name="resolution"></a>解決方案

在先前于摘要中所述的已知受影響案例中部署擴充功能之前，請先考慮下列一般建議：

1.  部署擴充功能之前，請使用`/usr/bin/python` Linux 散發廠商提供的方法來恢復符號連結。

    - 例如，針對**Python 2.7**，請使用：`sudo apt update && sudo apt install python-is-python2`

2.  如果您已部署展示此問題的實例，請使用 [ **VM** ] 分頁中的 [**執行命令**] 功能來執行上面所述的命令。 執行命令延伸模組本身不會受到轉換至 Python 3.8 的影響。

3.  如果您要部署新的實例，而且需要在布建階段設定延伸模組，請使用**雲端初始化**使用者資料來安裝上述套件。

    例如，針對 Python 2.7：

    ```
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

4.  如果貴組織的原則系統管理員判斷不應在 Vm 中部署擴充功能，您可以在布建階段停用擴充功能支援：

    - REST API

      當您可以使用此屬性部署 VM 時，停用並啟用擴充功能：

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>後續步驟

如需其他資訊，請參閱[自 18.04 LTS-Python 3 之後的其他基本系統變更](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default)。
