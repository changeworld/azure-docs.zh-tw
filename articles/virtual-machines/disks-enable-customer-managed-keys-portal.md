---
title: Azure 入口網站-使用 SSE 管理的磁片來啟用客戶管理的金鑰
description: 透過 Azure 入口網站，在您的受控磁片上啟用客戶管理的金鑰。
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 1b40eef0a4cae8fa68631426ff72003db43d7530
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88817464"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>使用 Azure 入口網站針對受控磁片使用客戶管理的金鑰啟用伺服器端加密

如果您選擇，Azure 磁碟儲存體可讓您在使用伺服器端加密 (SSE) 的受控磁片時，管理您自己的金鑰。 如需使用客戶管理的金鑰和其他受控磁片加密類型之 SSE 的概念資訊，請參閱磁片加密文章中的 **客戶管理金鑰** 一節：

- 針對 Linux： [客戶管理的金鑰](./linux/disk-encryption.md#customer-managed-keys)
- 針對 Windows： [客戶管理的金鑰](./windows/disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>限制

目前，客戶管理的金鑰具有下列限制：

- 如果您的磁碟已啟用此功能，則無法停用。
    如果您需要解決此問題，您必須將所有資料複製到完全不同且未使用客戶管理金鑰的受控磁片：

    - 針對 Linux： [複製受控磁片](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - 若為 Windows： [複製受控磁片](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- 僅支援大小為2048位、3072位和4096位的 [軟體和 HSM RSA 金鑰](../key-vault/keys/about-keys.md) ，不支援其他金鑰或大小。
    - [HSM](../key-vault/keys/hsm-protected-keys.md) 金鑰需要 Azure 金鑰保存庫的 **premium** 層。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

下列各節涵蓋如何針對受控磁片啟用和使用客戶管理的金鑰：

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>部署 VM

現在您已建立並設定金鑰保存庫和磁片加密集，您可以使用加密來部署 VM。
VM 部署程式類似于標準部署程式，唯一的差異在於您需要將 VM 部署在與其他資源相同的區域中，並選擇使用客戶管理的金鑰。

1. 搜尋 **虛擬機器** ，然後選取 [ **+ 新增** ] 以建立 VM。
1. 在 [ **基本** ] 分頁上，選取與您的磁片加密集相同的區域，並 Azure Key Vault。
1. 視需要在 **基本** 分頁上填入其他值。

    ![VM 建立體驗的螢幕擷取畫面，其中已反白顯示區域值。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. 在 [ **磁片** ] 分頁上，選取 [ **使用客戶管理的金鑰進行待用加密**]。
1. 在 [ **磁片加密集** ] 下拉式清單中選取您的磁片加密集。
1. 依您的需要進行其餘的選取專案。

    ![VM 建立體驗的螢幕擷取畫面，也就是磁片分頁。 已反白顯示 [磁片加密集] 下拉式清單。](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>在現有磁片上啟用

> [!CAUTION]
> 在任何連接至 VM 的磁片上啟用磁片加密，將需要您停止 VM。
    
1. 流覽至與您的其中一個磁片加密集位於相同區域的 VM。
1. 開啟 VM，然後選取 [ **停止**]。

    ![您範例 VM 的主要重迭螢幕擷取畫面，其中已反白顯示 [停止] 按鈕。](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. 在 VM 完成停止之後，請選取 [ **磁片** ]，然後選取您要加密的磁片。

    ![範例 VM 的螢幕擷取畫面，其中已開啟 [磁片] 分頁。 系統會反白顯示 OS 磁片，作為您要選取的範例磁片。](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. 選取 [ **加密** ]，並選取 [ **使用客戶管理的金鑰進行待用加密** ]，然後在下拉式清單中選取您的磁片加密集。
1. 選取 [儲存]****。

    ![範例 OS 磁片的螢幕擷取畫面。 加密分頁已開啟、已選取使用客戶管理的金鑰進行待用加密，以及您的範例 Azure Key Vault。 進行這些選擇之後，就會選取 [儲存] 按鈕。](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 針對連接至您想要加密之 VM 的任何其他磁片，重複執行此程式。
1. 當您的磁片完成切換至客戶管理的金鑰時，如果沒有任何其他您想要加密的連接磁片，您可以啟動 VM。

> [!IMPORTANT]
> 客戶管理的金鑰須依賴 Azure 資源的受控識別 (Azure Active Directory (Azure AD) 的一項功能)。 當您設定客戶管理的金鑰時，受控識別會在幕後自動指派給您的資源。 如果您之後將訂用帳戶、資源群組或受控磁片從一個 Azure AD 目錄移至另一個目錄，與受控磁片相關聯的受控身分識別不會傳送至新的租使用者，因此客戶管理的金鑰可能無法再運作。 如需詳細資訊，請參閱[在 Azure AD 目錄之間移轉訂用帳戶](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)。

## <a name="next-steps"></a>後續步驟

- [探索 Azure Resource Manager 範本，以使用客戶管理的金鑰來建立加密的磁碟](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)
- [使用已啟用客戶管理的金鑰的磁碟來複寫機器](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [使用 PowerShell 設定 VMWare VM 至 Azure 的災害復原](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [使用 PowerShell 和 Azure Resource Manager，針對 Hyper-V VM 設定 Azure 的災害復原](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
