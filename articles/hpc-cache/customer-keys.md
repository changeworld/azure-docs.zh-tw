---
title: 使用客戶受控金鑰來加密 Azure HPC Cache 中的資料
description: 如何使用 Azure Key Vault 搭配 Azure HPC 快取來控制加密金鑰存取，而不是使用預設 Microsoft 管理的加密金鑰
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: v-erkel
ms.openlocfilehash: 2d10241b8395c33767ffeeb550d9d8060bde3ce3
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597734"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>針對 Azure HPC 快取使用客戶管理的加密金鑰

您可以使用 Azure Key Vault 來控制在 Azure HPC 快取中用來加密資料的金鑰擁有權。 本文說明如何使用客戶管理的金鑰來進行快取資料加密。

> [!NOTE]
> 儲存在 Azure 中的所有資料（包括在快取磁片上）預設都會使用 Microsoft 管理的金鑰進行待用加密。 如果您想要管理用來加密資料的金鑰，您只需要遵循本文中的步驟。

這項功能僅適用于可使用 Azure HPC 快取的部分 Azure 區域。 如需詳細資訊，請參閱[區域可用性](hpc-cache-overview.md#region-availability)清單。

針對 Azure HPC 快取啟用客戶管理的金鑰加密有三個步驟：

1. 設定用來儲存金鑰的 Azure Key Vault。
1. 建立 Azure HPC 快取時，請選擇 [客戶管理的金鑰加密]，並指定要使用的金鑰保存庫和金鑰。
1. 建立快取之後，請授權它來存取金鑰保存庫。

在您從新建立的快取（步驟3）授權之後，加密才會完整設定。 這是因為您必須將快取的身分識別傳遞給金鑰保存庫，使其成為授權的使用者。 在建立快取之前，您無法執行此動作，因為在建立快取之前，識別不存在。

建立快取之後，您就無法在客戶管理的金鑰與 Microsoft 管理的金鑰之間進行變更。 不過，如果您的快取使用客戶管理的金鑰，您可以視需要[變更](#update-key-settings)加密金鑰、金鑰版本和金鑰保存庫。

## <a name="understand-key-vault-and-key-requirements"></a>瞭解金鑰保存庫和金鑰需求

金鑰保存庫和金鑰必須符合這些需求，才能與 Azure HPC 快取搭配使用。

金鑰保存庫屬性：

* **訂**用帳戶-使用用於快取的相同訂用帳戶。
* **區域**-金鑰保存庫必須位於與 Azure HPC 快取相同的區域中。
* **定價層**-標準層足以與 Azure HPC 快取搭配使用。
* 虛**刪除**-如果金鑰保存庫尚未設定 Azure HPC Cache，將會啟用虛刪除。
* **清除保護**-必須啟用 [清除保護]。
* **存取原則**-預設設定已足夠。
* **網路**連線-不論您選擇的端點設定為何，Azure HPC 快取都必須能夠存取金鑰保存庫。

索引鍵屬性：

* **金鑰類型**-RSA
* **RSA 金鑰大小**-2048
* **已啟用**-是

金鑰保存庫存取權限：

* 建立 Azure HPC 快取的使用者必須具有相當於「 [Key Vault 參與者」角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)的許可權。 設定和管理 Azure Key Vault 需要相同的許可權。

  如需詳細資訊，請參閱[金鑰保存庫的安全存取](../key-vault/key-vault-secure-your-key-vault.md)。

## <a name="1-set-up-azure-key-vault"></a>1. 設定 Azure Key Vault

您可以在建立快取之前設定金鑰保存庫和金鑰，或在快取建立過程中執行。 請確定這些資源符合[上面](#understand-key-vault-and-key-requirements)所述的需求。

在快取建立時，您必須指定要用於快取加密的保存庫、金鑰和金鑰版本。

如需詳細資訊，請參閱[Azure Key Vault 檔](../key-vault/key-vault-overview.md)。

> [!NOTE]
> Azure Key Vault 必須使用相同的訂用帳戶，且位於與 Azure HPC 快取相同的區域中。 請確定您選擇的區域[支援客戶管理的金鑰功能](hpc-cache-overview.md#region-availability)。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 建立已啟用客戶管理金鑰的快取

當您建立 Azure HPC 快取時，必須指定加密金鑰來源。 遵循[建立 AZURE HPC](hpc-cache-create.md)快取中的指示，並在 [**磁片加密金鑰**] 頁面中指定金鑰保存庫和金鑰。 您可以在快取建立期間建立新的金鑰保存庫和金鑰。

> [!TIP]
> 如果未出現 [**磁片加密金鑰**] 頁面，請確定您的快取位於其中一個支援的區域。

建立快取的使用者，其許可權必須等於[Key Vault 參與者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)或更高。

1. 按一下此按鈕以啟用私下管理的金鑰。 變更此設定之後，就會顯示金鑰保存庫設定。

1. 按一下 [**選取金鑰保存庫**] 以開啟 [金鑰選擇] 頁面。 選擇或建立金鑰保存庫和金鑰，以加密此快取磁片上的資料。

   如果您的 Azure Key Vault 沒有出現在清單中，請檢查下列需求：

   * 快取與金鑰保存庫位於相同的訂用帳戶中嗎？
   * 快取位於與金鑰保存庫相同的區域中嗎？
   * Azure 入口網站與金鑰保存庫之間是否有網路連線能力？

1. 選取保存庫之後，請從可用的選項中選取個別金鑰，或建立新的金鑰。 金鑰必須是2048位的 RSA 金鑰。

1. 指定所選金鑰的版本。 若要深入瞭解版本設定，請參閱[Azure Key Vault 檔](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。

繼續進行其餘的規格，並建立快取，如[建立 AZURE HPC](hpc-cache-create.md)快取中所述。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 授權快取中的 Azure Key Vault 加密
<!-- header is linked from create article, update if changed -->

幾分鐘後，新的 Azure HPC 快取就會出現在您的 Azure 入口網站中。 請移至 [**總覽**] 頁面，授權其存取您的 Azure Key Vault 並啟用客戶管理的金鑰加密。

> [!TIP]
> 快取可能會出現在「部署進行中」訊息之前的資源清單中。 請在一或兩分鐘後檢查資源清單，而不是等待成功通知。

這兩個步驟的程式是必要的，因為 Azure HPC 快取實例需要身分識別才能傳遞給 Azure Key Vault 以進行授權。 快取身分識別在其初始建立步驟完成後才會存在。

> [!NOTE]
> 建立快取之後，您必須在90分鐘內授權加密。 如果您未完成此步驟，快取將會超時並失敗。 失敗的快取必須重新建立，無法加以修正。

快取會顯示**等待金鑰**的狀態。 按一下頁面頂端的 [**啟用加密**] 按鈕，以授權快取存取指定的金鑰保存庫。

![入口網站中快取總覽頁面的螢幕擷取畫面，並反白顯示 [啟用加密] 按鈕（頂端列）和 [狀態：正在等候金鑰]](media/waiting-for-key.png)

按一下 [**啟用加密**]，然後按一下 [**是]** 按鈕，以授權快取使用加密金鑰。 此動作也會在金鑰保存庫上啟用虛刪除和清除保護（如果尚未啟用）。

![入口網站中 [快取總覽] 頁面的螢幕擷取畫面，其頂端有橫幅訊息，要求使用者按一下 [是] 來啟用加密](media/enable-keyvault.png)

在快取要求存取金鑰保存庫之後，它可以建立並加密儲存快取資料的磁片。

在您授權加密之後，Azure HPC 快取會經歷數分鐘的設定，以建立加密的磁片和相關的基礎結構。

## <a name="update-key-settings"></a>更新金鑰設定

您可以從 Azure 入口網站變更快取的金鑰保存庫、金鑰或金鑰版本。 按一下快取的 [**加密**設定] 連結，以開啟 [**客戶金鑰設定**] 頁面。

您無法變更客戶管理的金鑰與系統管理的金鑰之間的快取。

![[客戶金鑰設定] 頁面的螢幕擷取畫面，從 Azure 入口網站的 [快取] 頁面按一下 [設定] > [加密]。](media/change-key-click.png)

按一下 [**變更金鑰**] 連結，然後按一下 [**變更金鑰保存庫、金鑰或版本**] 以開啟金鑰選取器。

![[從 Azure Key Vault 選取金鑰] 頁面的螢幕擷取畫面，其中包含三個下拉式選取器來選擇金鑰保存庫、金鑰和版本](media/select-new-key.png)

清單中會顯示與此快取相同的訂用帳戶和相同區域中的金鑰保存庫。

選擇新的加密金鑰值之後，請按一下 [**選取**]。 [確認] 頁面隨即出現，其中包含新的值。 按一下 [**儲存**] 以完成選取。

![[確認] 頁面的螢幕擷取畫面，其中的 [儲存] 按鈕位於左上方](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>深入瞭解 Azure 中客戶管理的金鑰

這些文章將詳細說明如何使用 Azure Key Vault 和客戶管理的金鑰來加密 Azure 中的資料：

* [Azure 儲存體加密總覽](../storage/common/storage-service-encryption.md)
* 使用[客戶管理的金鑰進行磁片加密](../virtual-machines/linux/disk-encryption.md#customer-managed-keys)-使用受控磁片 Azure Key Vault 的檔，這是 Azure HPC 快取的類似案例

## <a name="next-steps"></a>後續步驟

建立 Azure HPC 快取和授權的 Key Vault 為基礎的加密之後，請繼續設定您的快取，方法是提供資料來源的存取權。

* [新增儲存體目標](hpc-cache-add-storage.md)
