---
title: 虛擬機器和虛擬機器縮放集的 Azure 磁片加密
description: 本文概述了 Azure 磁片加密
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062115"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>虛擬機器和虛擬機器縮放集的 Azure 磁片加密

Azure 磁片加密可以應用於 Linux 和 Windows 虛擬機器，以及虛擬機器縮放集。 

## <a name="linux-virtual-machines"></a>Linux 虛擬機器

以下文章提供了加密 Linux 虛擬機器的指導。

### <a name="current-version-of-azure-disk-encryption"></a>當前版本的 Azure 磁片加密

- [適用於 Linux 虛擬機器的 Azure 磁碟加密概觀](../../virtual-machines/linux/disk-encryption-overview.md)
- [Linux VM 上的 Azure 磁碟加密案例](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure CLI 來建立和加密 Linux VM](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Linux 虛擬機器](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [使用 Azure 入口網站來建立和加密 Linux VM](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Linux 的 Azure 磁片加密擴展架構](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure 磁碟加密範例指令碼](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure 磁碟加密常見問題集](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁片加密與 Azure AD（早期版本）

- [Linux 虛擬機器使用 Azure AD 進行 Azure 磁片加密概述](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [在 Linux VM 上使用 Azure AD 方案進行 Azure 磁片加密](../../virtual-machines/linux/disk-encryption-linux.md)
- [使用 Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本）](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows 虛擬機器

以下文章提供了加密 Windows 虛擬機器的指導。

### <a name="current-version-of-azure-disk-encryption"></a>當前版本的 Azure 磁片加密

- [適用於 Windows 虛擬機器的 Azure 磁碟加密概觀](../../virtual-machines/windows/disk-encryption-overview.md)
- [Windows VM 上的 Azure 磁碟加密案例](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure CLI 建立和加密 Windows 虛擬機器](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [使用 Azure PowerShell 建立和加密 Windows 虛擬機器](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [使用 Azure 入口網站建立和加密 Windows VM](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [適用于 Windows 的 Azure 磁片加密擴展架構](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure 磁碟加密範例指令碼](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure 磁碟加密的疑難排解](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure 磁碟加密常見問題集](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure 磁片加密與 Azure AD（早期版本）

- [Windows 虛擬機器使用 Azure AD 進行 Azure 磁片加密概述](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Windows VM 上具有 Azure AD 方案的 Azure 磁片加密](../../virtual-machines/windows/disk-encryption-windows.md)
- [使用 Azure AD 創建和配置 Azure 磁片加密的金鑰保存庫（上一版本）](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>虛擬機器擴展集

以下文章提供了加密虛擬機器規模集的指導。

- [虛擬機器規模集的 Azure 磁片加密概述](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [使用 Azure CLI 將虛擬機器擴展集加密](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [使用 Azure Powershell 加密虛擬機器縮放集](../../virtual-machine-scale-sets/disk-encryption-powershell.md)。
- [使用 Azure 資源管理器加密虛擬機器規模集](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [針對 Azure 磁碟加密建立及設定金鑰保存庫](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [使用搭配虛擬機器擴展集擴充功能排序的 Azure 磁碟加密](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>後續步驟

- [Azure 加密概述](encryption-overview.md)
- [靜態資料加密](encryption-atrest.md)
- [資料安全性與加密的最佳做法](data-encryption-best-practices.md)
