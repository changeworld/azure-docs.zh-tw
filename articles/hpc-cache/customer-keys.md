---
title: 使用客戶管理金鑰加密 Azure HPC 快取中的資料
description: 如何使用 Azure 金鑰保管庫與 Azure HPC 快取一起控制加密金鑰存取,而不是使用預設的 Microsoft 管理的加密金鑰
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538865"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>使用客戶管理的加密金鑰進行 Azure HPC 快取

可以使用 Azure 金鑰保管庫控制用於在 Azure HPC 快取中加密資料的金鑰的所有權。 本文介紹如何使用客戶管理的密鑰進行緩存數據加密。

> [!NOTE]
> 默認情況下,存儲在 Azure 中的所有數據(包括在快取磁碟上)均使用 Microsoft 管理的密鑰進行靜態加密。 如果想要管理用於加密數據的密鑰,只需按照本文中的步驟操作。

此功能只有在以下 Azure 區域中可用:

* 美國東部
* 美國中南部
* 美國西部 2

為 Azure HPC 快取啟用客戶管理金鑰加密有三個步驟:

1. 設置 Azure 金鑰保管庫以儲存金鑰。
1. 創建 Azure HPC 快取時,請選擇客戶管理的密鑰加密,並指定要使用的金鑰保管庫和金鑰。
1. 建立快取後,授權它存取金鑰保管庫。

加密不會完全設置,直到您從新創建的緩存授權後(步驟 3)。 這是因為您必須將緩存的標識傳遞給密鑰保管庫,才能使其成為授權使用者。 在創建緩存之前,不能執行此操作,因為在創建緩存之前,標識不存在。

創建快取後,無法在客戶管理的金鑰和 Microsoft 管理的密鑰之間進行更改。 但是,如果快取使用客戶管理的密鑰,則可以根據需要[更改](#update-key-settings)加密金鑰、密鑰版本和密鑰保管庫。

## <a name="understand-key-vault-and-key-requirements"></a>瞭解金鑰保存庫與關鍵要求

金鑰保管庫和密鑰必須滿足這些要求才能使用 Azure HPC 緩存。

金鑰保存庫屬性:

* **訂閱**- 使用用於緩存的相同訂閱。
* **區域**- 密鑰保管庫必須與 Azure HPC 緩存位於同一區域中。
* **定價層**- 標準層足以與 Azure HPC 緩存一起使用。
* **軟刪除**- Azure HPC 快取將在密鑰保管庫中尚未配置軟刪除時啟用軟刪除。
* **清除保護**- 必須啟用清除保護。
* **存取策略**- 預設設定已足夠。
* **網路連接**- Azure HPC 緩存必須能夠存取金鑰保管庫,而不考慮您選擇的終結點設置。

主要屬性:

* **金鑰類型**- RSA
* **RSA 金鑰大小**- 2048
* **開啟**- 是

金鑰保存許可權:

* 創建 Azure HPC 快取的使用者必須具有與[密鑰保管庫參與者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)等效的許可權。 設置和管理 Azure 密鑰保管庫需要相同的許可權。

  關於詳細資訊[,請閱讀金鑰保管庫的安全存取](../key-vault/key-vault-secure-your-key-vault.md)。

## <a name="1-set-up-azure-key-vault"></a>1. 設定 Azure 金鑰保存庫

您可以在創建快取之前設置金鑰保管庫和密鑰,也可以將其作為緩存創建的一部分進行。 確保這些資源符合[上述](#understand-key-vault-and-key-requirements)要求。

在創建快取時,必須指定用於快取加密的保管庫、密鑰和密鑰版本。

關於詳細資訊,請閱讀[Azure 金鑰保管庫文件](../key-vault/key-vault-overview.md)。

> [!NOTE]
> Azure 密鑰保管庫必須使用相同的訂閱,並且與 Azure HPC 緩存位於同一區域。 使用本文開頭列出的受支持區域之一。

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. 啟用客戶管理的金鑰後建立快取

建立 Azure HPC 快取時,必須指定加密金鑰來源。 按照創建 Azure [HPC 快取](hpc-cache-create.md)中的說明操作,並在 **「磁碟加密密鑰」** 頁中指定金鑰保管庫和金鑰。 您可以在快取建立期間建立新密鑰保管庫和密鑰。

> [!TIP]
> 如果未顯示 **「磁碟加密密鑰」** 頁,請確保快取位於受支援的區域之一。

創建快取的使用者必須具有等於[金鑰保管庫參與者角色](../role-based-access-control/built-in-roles.md#key-vault-contributor)或更高許可權的許可權。

1. 按下該按鈕可啟用私有管理金鑰。 更改此設置後,將顯示密鑰保管庫設置。

1. 按下 **「選擇金鑰保管庫**」以打開金鑰選擇頁。 選擇或創建用於加密此快取磁碟上資料的金鑰保管庫和密鑰。

   如果 Azure 金鑰保管庫未顯示在清單中,請檢查以下要求:

   * 快取是否與金鑰保管庫處於同一訂閱中?
   * 快取是否與金鑰保管庫位於同一區域?
   * Azure 門戶和密鑰保管庫之間是否有網路連接?

1. 選擇保管庫后,從可用選項中選擇單個鍵,或創建新密鑰。 金鑰必須為 2048 位 RSA 密鑰。

1. 指定選取金鑰的版本。 在[Azure 金鑰保管庫文檔中](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)瞭解有關版本控制的更多詳細資訊。

繼續執行其餘規範,並創建緩存,如創建 Azure [HPC 緩存](hpc-cache-create.md)中所述。

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. 從快取授權 Azure 金鑰保管庫加密
<!-- header is linked from create article, update if changed -->

幾分鐘后,新的 Azure HPC 緩存將顯示在 Azure 門戶中。 跳到 **「概述」** 頁以授權它存取 Azure 密鑰保管庫並啟用客戶管理的金鑰加密。 (在清除"正在進行部署"消息之前,緩存可能會顯示在資源清單中。

此兩步過程是必需的,因為 Azure HPC 緩存實例需要一個標識才能傳遞到 Azure 密鑰保管庫以進行授權。 緩存標識在初始創建步驟完成之前不會存在。

> [!NOTE]
> 您必須在建立快取後的 90 分鐘內授權加密。 如果不完成此步驟,緩存將超時並失敗。 必須重新建立失敗的緩存,無法修復它。

緩存顯示狀態 **「等待鍵**」。 按一下頁面頂部的 **「啟用加密**」按鈕,授權快取指定的密鑰保管庫。

![門戶中快取概覽頁的螢幕截圖,在啟用加密按鈕(上一行)和狀態:等待金鑰上突出顯示](media/waiting-for-key.png)

按下 **「啟用加密**」,然後按下「**是**」按鈕以授權快取使用加密金鑰。 此操作還支援金鑰保管庫上的軟刪除和清除保護(如果尚未啟用)。

![門戶中緩存概述頁的螢幕截圖,頂部有一條橫幅消息,要求用戶通過單擊「是」來啟用加密](media/enable-keyvault.png)

快取請求存取金鑰保管庫後,它可以創建和加密儲存快取資料的磁碟。

授權加密後,Azure HPC 緩存將再經過幾分鐘的設置,以創建加密磁碟和相關基礎結構。

## <a name="update-key-settings"></a>更新金鑰設定

可以從 Azure 門戶更改緩存的密鑰保管庫、密鑰或密鑰版本。 按下快取的**加密**設定連結以開啟 **「客戶金鑰設定」** 頁。 (您不能在客戶管理的密鑰和系統管理的密鑰之間更改快取。

!["客戶金鑰設置"頁的螢幕截圖,通過單擊 Azure 門戶中緩存頁的"設置>加密"到達](media/change-key-click.png)

按下 **「更改」鍵**連結,然後按下 **「更改金鑰保管庫、密鑰或版本**」以打開金鑰選擇器。

!["從 Azure 金鑰保存式庫中選擇金鑰"頁的螢幕截圖,其中有三個下拉選擇器可選擇金鑰保管庫、金鑰和版本](media/select-new-key.png)

清單中將顯示與此快取相同的訂閱和同一區域中的密鑰保管庫。

選擇新的加密金鑰值後,按一下「**選擇**」。。 將顯示一個確認頁,其中顯示新值。 按下 **「保存**」以完成所選內容。

![確認頁面的螢幕截圖,左上角有「儲存」按鈕](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>閱讀有關 Azure 中客戶管理金鑰的基礎知識

這些文章將介紹有關使用 Azure 金鑰保管庫和客戶管理金鑰加密 Azure 中的數據的介紹:

* [Azure 儲存加密概述](../storage/common/storage-service-encryption.md)
* [使用客戶管理的金鑰進行磁碟加密](../virtual-machines/linux/disk-encryption.md#customer-managed-keys)- 使用 Azure 金鑰保管庫和託管磁碟的文件,這與 Azure HPC 快取使用的過程類似

## <a name="next-steps"></a>後續步驟

創建 Azure HPC 快取並授權基於金鑰保管庫的加密後,繼續通過授予快取對資料來源的存取許可權來設定快取。

* [新增儲存體目標](hpc-cache-add-storage.md)
