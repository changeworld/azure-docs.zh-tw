---
title: 將 Azure VM 的 SUSE Linux Enterprise Server 升級至 SUSE 15 SP1 |Microsoft Docs
description: 本文提供有關如何使用 SUSE 發佈遷移系統，為 Azure 虛擬機器將 SUSE Linux Enterprise server 升級至 SUSE 15 SP1 的一般步驟。
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359506"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>將 Azure VM 的 SLES 12 升級至 SLES 15 SP1

本文提供有關如何針對 Azure 虛擬機器 (VM) 升級 SUSE Linux Enterprise server (SLES) 12至 SLES 15 SP1 的一般步驟。 如需詳細資訊，請參閱[使用 SUSE 發佈遷移系統](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html)和 [SUSE Linux Enterprise Server 15 SP1 升級指南](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update)。

## <a name="supported-upgrade-paths"></a>支援的升級路徑
目前的 SLES 版本必須是 SLES 12 SP4 或 12 SP5，才能升級至 SLES 15 SP1。

![有關支援的升級路徑的螢幕擷取畫面](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>必要條件

- 根據核准的停機時間範圍規劃遷移活動。 這是因為 VM 在遷移期間會重新啟動。
- 在進行遷移活動前，請先對 VM 執行完整備份。
- 如果未設定備份，請進行 OS 磁碟的快照集備份。
- [檢查 VM 為 V1 或 V2 世代](#check-the-generation-version-for-a-vm)。

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>從 SUSE 12 SP4 或 SP5 升級至 SUSE 15 SP1

1. 安裝 VM 的最新封裝：

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. 在安裝完成後，請重新啟動 VM。

3. 確認核心和 OS 版本。 請確定版本是 SUSE 12 SP4 或 SUSE 12 SP5。

    ```
    uname -a
    cat /etc/os-release
    ```

4. 安裝 **suse-migration-sle15-activation**。 安裝 **suse-sle15-activation** 套件後，會將另一個套件 **SLES15-Migration** 自動安裝為相依性套件。 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. 安裝完成後，請執行 `reboot` 命令以重新啟動 VM。

6. 移至 [Azure 入口網站](https://portal.azure.com)，選取 VM，然後選取 [序列主控台]。 您會看到系統停止在「重新啟動：正在重新啟動系統」。 此流程大約需要 15 到 45 分鐘。 如果是第 2 代 VM，可能會停滯在「重新啟動：正在重新啟動系統」畫面。 在此情況下，請等待大約 45 分鐘的時間。 如果仍然沒有進展，請移至 Azure 入口網站中 VM 的**概覽**頁面，停止 VM，然後重新啟動它。

     ![序列主控台中有關訊息的螢幕擷取畫面。](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. 使用新的核心重新啟動系統之後，您會看到下列訊息。

     ![使用新的核心重新開機系統之後，序列主控台中有關訊息的螢幕擷取畫面。](./media/linux-upgrade-suse-15sp1/output-message.png)
9. 確認核心和 OS 版本，以檢查系統是否已成功升級。

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>檢查 VM 的世代版本

您可以使用下列方法之一來檢查世代版本：

- 在 SLES 終端機中，執行命令 `dmidecode | grep -i hyper`。 如果是世代 V1 的 VM，則不會傳回任何輸出。 如果是世代 V2 的 VM，則會看到下列輸出：

     ![有關第 2 代 VM 輸出的螢幕擷取畫面](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- 在 [Azure 入口網站](https://portal.azure.com)中，移至 VM 的 [屬性]，然後勾選 [VM 世代] 欄位。
