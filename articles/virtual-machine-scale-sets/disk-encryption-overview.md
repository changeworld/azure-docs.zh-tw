---
title: 為虛擬機器縮放集啟用 Azure 磁片加密
description: 本文提供有關為虛擬機器規模集啟用 Microsoft Azure 磁片加密的說明
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278979"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>虛擬機器縮放集的 Azure 磁片加密

Azure 磁片加密為虛擬機器的作業系統和資料磁片提供卷加密，有助於保護資料以滿足組織安全和合規性承諾。 要瞭解更多資訊，請參閱[Azure 磁片加密：Linux VM](../virtual-machines/linux/disk-encryption-overview.md)和[Azure 磁片加密：Windows VM](../virtual-machines/windows/disk-encryption-overview.md)  

Azure 磁片加密還可以應用於 Windows 和 Linux 虛擬機器縮放集，在這些情況下：
- 使用託管磁片創建的縮放集。 本機（或非託管）磁片規模集不支援 Azure 磁片加密。
- Windows 縮放集中的作業系統和資料卷。
- Linux 比例集中的資料卷。 目前不支援 Linux 規模集的作業系統磁片加密。

使用 Azure CLI 或[使用 Azure PowerShell 教程加密虛擬機器規模集](disk-encryption-powershell.md)的[虛擬機器縮放集](disk-encryption-cli.md)，只需幾分鐘即可瞭解虛擬機器縮放集的 Azure 磁片加密基礎知識。

## <a name="next-steps"></a>後續步驟

- [使用 Azure 資源管理器加密虛擬機器規模集](disk-encryption-azure-resource-manager.md)
- [針對 Azure 磁碟加密建立及設定金鑰保存庫](disk-encryption-key-vault.md)
- [使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密](disk-encryption-extension-sequencing.md)
