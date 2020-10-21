---
title: 使用客戶受控金鑰來加密 Azure HPC Cache 中的資料
description: 如何搭配使用 Azure Key Vault 與 Azure HPC Cache 來控制加密金鑰存取，而不使用預設的 Microsoft 管理加密金鑰
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: 8bce19e384e600e95b8306dcbba38652b4432bc9
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340524"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>針對 Azure HPC Cache 使用客戶管理的加密金鑰

您可以使用 Azure Key Vault 來控制用來加密 Azure HPC Cache 中資料的金鑰擁有權。 本文說明如何使用客戶管理的金鑰進行快取資料加密。

> [!NOTE]
> 所有儲存在 Azure 中的資料（包括在快取磁片上）預設會使用 Microsoft 管理的金鑰進行靜態加密。 如果您想要管理用來加密資料的金鑰，您只需要遵循本文中的步驟。

即使您為快取磁片新增客戶金鑰，Azure HPC Cache 也會在保存您快取資料的受控磁片上受到 [VM 主機加密](../virtual-machines/linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) 的保護。 新增客戶管理的金鑰進行雙重加密，為具有高安全性需求的客戶提供額外的安全性層級。 如需詳細資料，請參閱 [Azure 磁片儲存體的伺服器端加密](../virtual-machines/linux/disk-encryption.md) 。

這項功能僅適用于一些可用 Azure HPC Cache 的 Azure 區域。 請參閱 [區域可用性](hpc-cache-overview.md#region-availability) 清單以取得詳細資料。

針對 Azure HPC Cache 啟用客戶管理金鑰加密的步驟有三個：

1. 設定用來儲存金鑰的 Azure Key Vault。
1. 建立 Azure HPC Cache 時，請選擇 [客戶管理的金鑰加密]，並指定要使用的金鑰保存庫和金鑰。
1. 建立快取之後，授權該快取以存取金鑰保存庫。

加密在您從新建立的快取中授權之後，才會完成設定 (步驟 3) 。 這是因為您必須將快取的身分識別傳遞給金鑰保存庫，才能讓它成為授權的使用者。 建立快取之前，您無法執行此動作，因為在建立快取之前，不會有身分識別存在。

在您建立快取之後，就無法在客戶管理的金鑰和 Microsoft 管理的金鑰之間進行變更。 不過，如果您的快取使用客戶管理的金鑰，您可以視需要 [變更](#update-key-settings) 加密金鑰、金鑰版本和金鑰保存庫。

## <a name="understand-key-vault-and-key-requirements"></a>瞭解金鑰保存庫和金鑰需求

金鑰保存庫和金鑰必須符合這些需求，才能使用 Azure HPC Cache。

Key vault 屬性：

* **訂** 用帳戶-使用用於快取的相同訂用帳戶。
* **區域** -金鑰保存庫必須位於與 Azure HPC Cache 相同的區域中。
* **定價層** -標準層足以搭配 Azure HPC Cache 使用。
* 虛**刪除**-Azure HPC Cache 將會啟用虛刪除（如果尚未在金鑰保存庫上設定）。
* **清除保護** -必須啟用清除保護。
* **存取原則** -預設設定已足夠。
* **Network connectivity**無論您選擇的端點設定為何，Azure HPC Cache 都必須能夠存取金鑰保存庫。

索引鍵屬性：

* **金鑰類型** -RSA
* **RSA 金鑰大小** -2048
* **已啟用** -是

Key vault 存取權限：

* 建立 Azure HPC Cache 的使用者必須具有相當於 [Key Vault 參與者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)的許可權。 設定和管理 Azure Key Vault 需要相同的許可權。

  如需詳細資訊，請參閱 [金鑰保存庫的安全存取](../key-vault/general/secure-your-key-vault.md) 。

## <a name="1-set-up-azure-key-vault"></a>1. 設定 Azure Key Vault

您可以在建立快取之前，先設定金鑰保存庫和金鑰，或在建立快取的過程中設定金鑰保存庫和金鑰。 請確定這些資源符合 [上述](#understand-key-vault-and-key-requirements)需求。

在快取建立期間，您必須指定要用於快取加密的保存庫、金鑰和金鑰版本。

如需詳細資料，請參閱 [Azure Key Vault 檔](../key-vault/general/overview.md) 。

> [!NOTE]
> Azure Key Vault 必須使用相同的訂用帳戶，而且與 Azure HPC Cache 位於相同的區域中。 請確定您選擇的區域 [支援客戶管理的金鑰功能](hpc-cache-overview.md#region-availability)。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 建立已啟用客戶管理金鑰的快取

當您建立 Azure HPC Cache 時，必須指定加密金鑰來源。 遵循 [建立 Azure HPC Cache](hpc-cache-create.md)中的指示，並在 [ **磁片加密金鑰** ] 頁面中指定金鑰保存庫和金鑰。 您可以在快取建立期間建立新的金鑰保存庫和金鑰。

> [!TIP]
> 如果未顯示 [ **磁片加密金鑰** ] 頁面，請確定您的快取是在其中一個支援的區域中。

建立快取的使用者必須具有等於 [Key Vault 參與者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor) 或更高的許可權。

1. 按一下按鈕以啟用私下管理的金鑰。 變更此設定之後，就會出現金鑰保存庫設定。

1. 按一下 [ **選取金鑰保存庫** ] 以開啟 [金鑰選取] 頁面。 選擇或建立金鑰保存庫和金鑰，以加密此快取磁片上的資料。

   如果您的 Azure Key Vault 未出現在清單中，請檢查這些需求：

   * 快取是否位於與金鑰保存庫相同的訂用帳戶中？
   * 快取是否位於與金鑰保存庫相同的區域中？
   * Azure 入口網站與金鑰保存庫之間是否有網路連線能力？

1. 選取保存庫之後，請從可用的選項中選取個別的金鑰，或建立新的金鑰。 金鑰必須是2048位 RSA 金鑰。

1. 指定所選金鑰的版本。 若要深入瞭解 [Azure Key Vault 檔](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)中的版本設定，請參閱。

繼續進行其餘的規格，並依照 [建立 Azure HPC Cache](hpc-cache-create.md)所述的方式建立快取。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 從快取授權 Azure Key Vault 加密
<!-- header is linked from create article, update if changed -->

幾分鐘後，新的 Azure HPC Cache 會出現在您的 Azure 入口網站中。 移至 [ **總覽** ] 頁面以授權它存取您的 Azure Key Vault，並啟用客戶管理的金鑰加密。

> [!TIP]
> 快取可能會在「部署進行中」訊息清除之前出現在資源清單中。 請在一分鐘或兩分鐘後檢查資源清單，而不是等候成功通知。

這兩個步驟的程式是必要的，因為 Azure HPC Cache 實例需要身分識別才能傳遞給 Azure Key Vault 以進行授權。 快取身分識別在初始建立步驟完成後才會存在。

> [!NOTE]
> 建立快取之後，您必須在90分鐘內授權加密。 如果您未完成此步驟，快取將會超時且失敗。 無法重新建立失敗的快取，無法修正。

快取會顯示 **等待金鑰**的狀態。 按一下頁面頂端的 [ **啟用加密** ] 按鈕，以授權快取存取指定的金鑰保存庫。

![入口網站中快取總覽頁面的螢幕擷取畫面，其中顯示 [啟用加密] 按鈕 (頂端資料列) 和狀態：等候金鑰](media/waiting-for-key.png)

按一下 [ **啟用加密** ]，然後按一下 [ **是]** 按鈕，以授權快取使用加密金鑰。 如果尚未在金鑰保存庫上啟用) ，此動作也會啟用虛刪除和清除保護 (。

![入口網站中快取總覽頁面的螢幕擷取畫面，其中包含頂端的橫幅訊息，要求使用者按一下 [是] 來啟用加密](media/enable-keyvault.png)

快取要求存取金鑰保存庫之後，就可以建立和加密儲存快取資料的磁片。

授權加密之後，Azure HPC Cache 會經過幾分鐘的安裝程式，以建立加密的磁片和相關的基礎結構。

## <a name="update-key-settings"></a>更新金鑰設定

您可以從 Azure 入口網站變更快取的金鑰保存庫、金鑰或金鑰版本。 按一下快取的 [ **加密** 設定] 連結，開啟 [ **客戶金鑰設定** ] 頁面。

您無法變更客戶管理的金鑰與系統管理的金鑰之間的快取。

![從 Azure 入口網站的 [快取] 頁面按一下 [設定 > 加密]，即可達到 [客戶金鑰設定] 頁面的螢幕擷取畫面](media/change-key-click.png)

按一下 [ **變更金鑰** ] 連結，然後按一下 [ **變更金鑰保存庫、金鑰或版本** ] 以開啟金鑰選取器。

![[從 Azure Key Vault 選取金鑰] 頁面的螢幕擷取畫面，其中有三個下拉式選單可選擇 key Vault、金鑰和版本](media/select-new-key.png)

清單中會顯示與此快取位於相同訂用帳戶和相同區域中的金鑰保存庫。

選擇新的加密金鑰值之後，請按一下 [ **選取**]。 [確認] 頁面隨即出現，其中包含新的值。 按一下 [ **儲存** ] 以完成選取專案。

![使用左上角的 [儲存] 按鈕確認頁面的螢幕擷取畫面](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>深入瞭解 Azure 中客戶管理的金鑰

這些文章會詳細說明如何使用 Azure Key Vault 和客戶管理的金鑰來加密 Azure 中的資料：

* [Azure 儲存體加密總覽](../storage/common/storage-service-encryption.md)
* 使用[客戶管理的金鑰進行磁片加密](../virtual-machines/linux/disk-encryption.md#customer-managed-keys)-搭配受控磁片使用 Azure Key Vault 的檔，這是類似于 Azure HPC Cache 的案例

## <a name="next-steps"></a>後續步驟

在您建立 Azure HPC Cache 和授權的 Key Vault 式加密之後，請將您的資料來源存取權授與您的快取，以繼續設定您的快取。

* [新增儲存體目標](hpc-cache-add-storage.md)