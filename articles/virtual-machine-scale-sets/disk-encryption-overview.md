---
title: 啟用虛擬機器擴展集的 Azure 磁碟加密
description: 本文提供針對虛擬機器擴展集啟用 Microsoft Azure 磁片加密的指示。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 6b9805d66149a18216a200bc89a79b3e06106c9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83195117"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>虛擬機器擴展集的 Azure 磁碟加密

Azure 磁碟加密為您虛擬機器的 OS 和資料磁片提供磁片區加密，以協助保護及保護您的資料，以符合組織的安全性和合規性承諾。 若要深入瞭解，請參閱 [Azure 磁碟加密： Linux vm](../virtual-machines/linux/disk-encryption-overview.md) 和 [Azure 磁碟加密： Windows vm](../virtual-machines/windows/disk-encryption-overview.md)  

在這些情況下，Azure 磁碟加密也可以套用至 Windows 和 Linux 虛擬機器擴展集：
- 使用受控磁片建立的擴展集。 原生 (或非受控) 磁片擴展集不支援 Azure 磁片加密。
- Windows 擴展集中的作業系統和資料磁片區。
- Linux 擴展集中的資料磁片區。 Linux 擴展集目前不支援 OS 磁片加密。

您可以在短短幾分鐘內瞭解虛擬機器擴展集 Azure 磁碟加密的基本概念，並使用 [Azure CLI 來加密](disk-encryption-cli.md) 虛擬機器擴展集，或使用 Azure PowerShell 教學課程來 [加密虛擬機器擴展集](disk-encryption-powershell.md) 。

## <a name="next-steps"></a>接下來的步驟

- [使用 Azure Resource Manager 加密虛擬機器擴展集](disk-encryption-azure-resource-manager.md)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)
- [使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密](disk-encryption-extension-sequencing.md)
