---
title: Azure 備份的加密
description: 瞭解 Azure 備份中的加密功能如何協助您保護您的備份資料，並符合您企業的安全性需求。
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: 099e736bfb321f0f92bd3a57f9c24e88293b42bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86538746"
---
# <a name="encryption-in-azure-backup"></a>Azure 備份的加密

當您使用 Azure 儲存體加密儲存在雲端時，所有備份的資料都會自動加密，以協助您符合安全性和合規性承諾。 此待用資料會使用256位 AES 加密（可用的最強區塊密碼之一）進行加密，且符合 FIPS 140-2 規範。

除了待用加密，傳輸中的所有備份資料都是透過 HTTPS 傳送。 它一律會保留在 Azure 骨幹網路上。

如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)。 請參閱[AZURE 備份常見問題](./backup-azure-backup-faq.md#encryption)，以回答您可能會有關于加密的任何問題。

## <a name="encryption-of-backup-data-using-platform-managed-keys"></a>使用平臺管理的金鑰來加密備份資料

根據預設，您的所有資料都會使用平臺管理的金鑰進行加密。 您不需要從您的端採取任何明確的動作來啟用此加密，並將其套用至您的復原服務保存庫所備份的所有工作負載。

## <a name="encryption-of-backup-data-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密備份資料

備份您的 Azure 虛擬機器時，您現在可以使用由您所擁有和管理的金鑰來加密您的資料。 Azure 備份可讓您使用儲存在 Azure Key Vault 中的 RSA 金鑰來加密您的備份。 用於加密備份的加密金鑰可能與來源所使用的不同。 資料是使用 AES 256 型資料加密金鑰（DEK）來保護，而這也是使用您的金鑰來保護。 這可讓您完全掌控資料和金鑰。 若要允許加密，復原服務保存庫必須被授與 Azure Key Vault 中加密金鑰的存取權。 您可以停用金鑰，或在需要時撤銷存取權。 不過，在您嘗試保護保存庫的任何專案之前，您必須使用金鑰來啟用加密。

[在這裡](encryption-at-rest-with-cmk.md)深入瞭解如何使用客戶管理的金鑰來加密備份資料。

## <a name="backup-of-managed-disk-vms-encrypted-using-customer-managed-keys"></a>使用客戶管理的金鑰來加密的受控磁片 Vm 備份

Azure 備份也可讓您備份使用金鑰進行[儲存體服務加密](../storage/common/storage-service-encryption.md)的 Azure vm。 用來加密磁片的金鑰會儲存在 Azure Key Vault 中，並由您管理。 使用客戶管理金鑰的儲存體服務加密（SSE）與 Azure 磁碟加密不同，因為 ADE 會利用 BitLocker （適用于 Windows）和 DM Crypt （適用于 Linux）來執行來賓加密，而 SSE 會加密儲存體服務中的資料，讓您可以對 Vm 使用任何 OS 或映射。 如需詳細資訊，請參閱[使用客戶管理的金鑰加密受控磁片](../virtual-machines/windows/disk-encryption.md#customer-managed-keys)。

## <a name="infrastructure-level-encryption-for-backup-data"></a>備份資料的基礎結構層級加密

除了使用客戶管理的金鑰來加密復原服務保存庫中的資料，您也可以選擇在儲存體基礎結構上設定額外的加密層級。 此基礎結構加密是由平臺管理，並使用客戶管理的金鑰搭配待用加密，它允許兩層式的備份資料加密。 請注意，只有當您第一次選擇使用自己的金鑰進行待用加密時，才可以設定基礎結構加密。 基礎結構加密會使用平臺管理的金鑰來加密資料。

>[!NOTE]
>基礎結構加密目前處於有限預覽狀態，僅適用于美國東部、US 西部2、美國中南部、US Gov 亞利桑那州和 US GOV 佛吉尼亞地區。 如果您想要在這些區域中使用此功能，請在此填寫[表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u)，並以電子郵件傳送給我們 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

## <a name="backup-of-vms-encrypted-using-ade"></a>使用 ADE 加密的 Vm 備份

使用 Azure 備份，您也可以備份已使用 Azure 磁碟加密加密其 OS 或資料磁片的 Azure 虛擬機器。 ADE 會使用適用于 Windows Vm 的 BitLocker，以及用於 Linux Vm 的 DM Crypt 來執行來賓加密。 如需詳細資訊，請參閱[使用 Azure 備份備份和還原已加密的虛擬機器](./backup-azure-vms-encryption.md)。

## <a name="next-steps"></a>後續步驟

- [備份和還原已加密的 Azure VM](backup-azure-vms-encryption.md)
