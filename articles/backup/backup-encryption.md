---
title: Azure 備份的加密
description: 瞭解 Azure 備份中的加密功能如何協助您保護您的備份資料，並符合您企業的安全性需求。
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions
ms.openlocfilehash: f55deba58cd7b725bd030409296794e5de911c09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017901"
---
# <a name="encryption-in-azure-backup"></a>Azure 備份的加密

當您使用 Azure 儲存體加密儲存在雲端時，所有備份的資料都會自動加密，這可協助您符合安全性和合規性承諾。 待用的資料會使用256位 AES 加密（可用的最強區塊加密之一）加密，並符合 FIPS 140-2 規範。 除了待用加密，傳輸中的所有備份資料都會透過 HTTPS 傳輸。 它一律會留在 Azure 骨幹網路上。

## <a name="levels-of-encryption-in-azure-backup"></a>Azure 備份中的加密層級

Azure 備份包含兩個層級的加密：

- **復原服務保存庫中的資料加密**
  - **使用平臺管理的金鑰**：根據預設，您的所有資料都會使用平臺管理的金鑰進行加密。 您不需要從您的一端採取任何明確的動作，就能啟用此加密。 其適用於備份到復原服務保存庫的所有工作負載。
  - **使用客戶管理的金鑰**：備份 Azure 虛擬機器時，您可以選擇使用您所擁有和管理的加密金鑰來加密您的資料。 Azure 備份可讓您使用儲存在 Azure Key Vault 中的 RSA 金鑰來加密您的備份。 用於加密備份的加密金鑰可能與用於來源的加密金鑰不同。 使用 AES 256 型資料加密金鑰來保護資料 (DEK) ，也就是使用您的金鑰加以保護。 這可讓您完整控制資料和金鑰。 若要允許加密，您必須將 Azure Key Vault 中的加密金鑰存取權授與復原服務保存庫。 您可以在需要時停用金鑰或撤銷存取權。 不過，在您嘗試保護任何專案至保存庫之前，必須先使用您的金鑰啟用加密。 若要[深入瞭解](encryption-at-rest-with-cmk.md)，請參閱。
  - **基礎結構層級加密**：除了使用客戶管理的金鑰來加密復原服務保存庫中的資料之外，您也可以選擇在儲存體基礎結構上設定額外的加密層級。 此基礎結構加密由平臺管理。 搭配使用客戶管理的金鑰進行待用加密，可讓您的備份資料進行兩層式加密。 只有在您第一次選擇使用自己的金鑰進行待用加密時，才可以設定基礎結構加密。 基礎結構加密使用平臺管理的金鑰來加密資料。
- **所要備份之工作負載的特定加密**  
  - **Azure 虛擬機器備份**： Azure 備份支援使用 [平臺管理的金鑰](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys)加密的磁片，以及由您所擁有和管理的 [客戶管理金鑰](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys) 來備份 vm。 此外，您也可以備份使用 [Azure 磁碟加密](backup-azure-vms-encryption.md#encryption-support-using-ade)加密其 OS 或資料磁片的 Azure 虛擬機器。 ADE 使用適用于 Windows Vm 的 BitLocker，以及適用于 Linux Vm 的 DM-Crypt 來執行來賓加密。

>[!NOTE]
>基礎結構加密目前處於有限預覽狀態，且僅適用于美國東部、US 西部2、美國中南部、US Gov 亞利桑那州和 US GOV 佛吉尼亞區域。 如果您想要在上述任何區域中使用此功能，請填寫 [此表單](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) ，並傳送電子郵件給我們 [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) 。

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)
- 針對加密可能有的任何問題[AZURE 備份常見問題](backup-azure-backup-faq.md#encryption)
