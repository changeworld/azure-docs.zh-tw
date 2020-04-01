---
title: 在 Azure 資訊安全中心中套用磁碟加密 | Microsoft Docs
description: 此文件展示如何實現 Azure 安全中心建議**套用磁碟加密**。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473267"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>在 Azure 資訊安全中心中套用磁碟加密

Azure 安全中心建議在 Windows 和 Linux VM 上的未加密磁碟上使用 Azure 磁碟加密。 磁碟加密可讓您替 Windows 和 Linux IaaS VM 磁碟加密。  建議您的 VM 上的作業系統和資料磁碟區都進行加密。

磁碟加密使用 Windows 的業界標準 [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 功能和 Linux 的 [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)功能。 這些功能為 OS 和資料提供加密來協助保護資料安全，以符合您的組織安全性和合規性承諾。 磁碟加密與 [Azure 金鑰保存庫](https://azure.microsoft.com/documentation/services/key-vault/)整合，可幫助您控制和管理您的金鑰保存庫訂用帳戶中的磁碟加密金鑰和密碼，同時確保 VM 磁碟中的所有資料會在您的 [Azure 儲存體](https://azure.microsoft.com/documentation/services/storage/)中輕鬆加密。

有關受支援的 Windows 與 Linux 版本的清單,請參閱 Azure 磁碟加密文件支援 VM[和作業系統](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems)。

## <a name="implement-the-recommendation"></a>實作建議
1. 在 **「建議」** 頁中,選擇**磁碟加密應用於虛擬機器**。
2. 從**不正常的資源**中,選擇建議為其提供磁碟加密的 VM。
3. 依照指示將加密套用至這些 VM。

![套用磁碟加密](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

若要加密已由資訊安全中心識別為需要加密的 Azure 虛擬機器，建議您執行下列步驟︰

* 安裝並設定 Azure PowerShell。 這可讓您執行必要的 PowerShell 命令，以便設定用來加密 Azure 虛擬機器的必要條件。
* 取得並執行 Azure 磁碟加密先決條件 Azure PowerShell 指令碼。
* 加密虛擬機器。

[使用 Azure PowerShell 加密 Windows IaaS VM](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - 引導您完成這些步驟,並假定您正在使用 Windows 用戶端電腦,從中可以設定磁碟加密。

有許多方法可以用於 Azure 虛擬機器。 如果您已經精通 Azure PowerShell 或 Azure CLI,則您可能更喜歡使用替代方法。 若要深入了解其他這些方法，請參閱 [Azure 磁碟加密](../security/fundamentals/encryption-overview.md)。

## <a name="see-also"></a>另請參閱
本文件說明如何實作 Azure 資訊安全中心建議的「套用磁碟加密」。 要瞭解有關磁碟加密的更多詳細資訊,請參閱:

* [使用 Azure 金鑰保管庫進行加密和密鑰管理](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)(視頻,36 分鐘 39 秒)-- 瞭解如何對 IaaS VM 和 Azure 密鑰保管庫使用磁碟加密管理來幫助保護和保護您的數據。
* [Azure 磁碟加密](../security/fundamentals/encryption-overview.md)(文檔)--瞭解如何為 Windows 和 Linux VM 啟用磁碟加密。