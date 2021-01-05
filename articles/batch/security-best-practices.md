---
title: Batch 安全性與合規性的最佳作法
description: 學習利用您的 Azure Batch 解決方案強化安全性的最佳作法和實用秘訣。
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: b9732ffb810a1038a6f402a46fa8b809f180f0a8
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802281"
---
# <a name="batch-security-and-compliance-best-practices"></a>Batch 安全性與合規性的最佳作法

本文提供使用 Azure Batch 時增強安全性的指引和最佳作法。

根據預設，Azure Batch 帳戶具有公用端點，而且可公開存取。 建立 Azure Batch 集區時，會在 Azure 虛擬網路的指定子網中布建集區。 Batch 集區中的虛擬機器是透過 Batch 建立的公用 IP 位址來存取。 集區中的計算節點可以在需要時彼此通訊，例如執行多重實例工作，但集區中的節點無法與集區外部的虛擬機器進行通訊。

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="顯示一般 Batch 環境的圖表。":::

有許多功能可協助您建立更安全的 Azure Batch 部署。 您可以在 [不使用公用 IP 位址](batch-pool-no-public-ip-address.md)的情況下布建集區，以限制對節點的存取，並減少從網際網路探索節點的能力。 計算節點可以透過在 [Azure 虛擬網路的子網中](batch-virtual-network.md)布建集區，安全地與其他虛擬機器或內部部署網路通訊。 您可以從 Azure Private Link 所支援的服務，啟用 [虛擬網路的私人存取](private-connectivity.md) 。

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="此圖顯示更安全的批次環境。":::

## <a name="general-security-related-best-practices"></a>一般安全性相關的最佳作法

### <a name="pool-configuration"></a>集區設定

許多安全性功能只適用于使用 [虛擬機器](nodes-and-pools.md#configurations)設定所設定的集區，而不適用於具有雲端服務設定的集區。 建議您盡可能使用虛擬機器設定集區，以利用 [虛擬機器擴展集](../virtual-machine-scale-sets/overview.md)。

### <a name="batch-account-authentication"></a>Batch 帳戶驗證

Batch 帳戶存取支援兩種驗證方法：共用金鑰和 [Azure Active Directory (Azure AD) ](batch-aad-auth.md)。

我們強烈建議使用 Azure AD 進行 Batch 帳戶驗證。 某些批次功能需要這種驗證方法，包含這裡所討論的許多安全性相關功能。

### <a name="batch-account-pool-allocation-mode"></a>Batch 帳戶集區配置模式

建立 Batch 帳戶時，您可以選擇兩個 [集區配置模式](accounts.md#batch-accounts)：

- **Batch 服務**：預設選項，用來配置和管理集區節點的基礎雲端服務或虛擬機器擴展集資源會在內部訂用帳戶中建立，而且不會直接在 Azure 入口網站中顯示。 只有 Batch 集區和節點是可見的。 
- **使用者訂** 用帳戶：基礎雲端服務或虛擬機器擴展集資源會建立在與 Batch 帳戶相同的訂用帳戶中。 因此，在訂用帳戶中，除了對應的批次資源之外，還會顯示這些資源。

使用使用者訂用帳戶模式，建立集區時，Batch Vm 和其他資源會直接建立在您的訂用帳戶中。 如果您想要使用 Azure 保留的 VM 執行個體建立 Batch 集區、在虛擬機器擴展集資源上使用 Azure 原則，以及/或管理訂用帳戶的核心配額 (在訂用帳戶) 的所有 Batch 帳戶之間共用，則需要使用者訂用帳戶模式。 若要在使用者訂用帳戶模式中建立 Batch 帳戶，您也必須向 Azure Batch 註冊訂用帳戶，並與 Azure Key Vault 中的帳戶建立關聯。

## <a name="restrict-network-endpoint-access"></a>限制網路端點存取

### <a name="batch-network-endpoints"></a>Batch 網路端點

請注意，根據預設，具有公用 IP 位址的端點是用來與 Batch 帳戶、Batch 集區和集區節點進行通訊。

### <a name="batch-account-api"></a>Batch 帳戶 API

 建立 Batch 帳戶時，會建立一個公用端點，用來對使用 [REST API](/rest/api/batchservice/)的帳戶叫用大部分的作業。 帳戶端點具有使用格式的基底 URL `https://{account-name}.{region-id}.batch.azure.com` 。 對 Batch 帳戶的存取會受到保護，而且會與使用 HTTPS 加密的帳戶端點進行通訊，而每個使用共用金鑰或 Azure Active Directory (Azure AD) 驗證的要求進行驗證。

### <a name="azure-resource-manager"></a>Azure Resource Manager

除了 Batch 帳戶特定的作業之外， [管理作業](/rest/api/batchmanagement/) 也適用于單一和多個 batch 帳戶。 這些管理作業是透過 Azure Resource Manager 存取。

透過 Azure Resource Manager 的 Batch 管理作業會使用 HTTPS 進行加密，而且每個要求都會使用 Azure AD 驗證進行驗證。

### <a name="batch-pool-nodes"></a>Batch 集區節點

Batch 服務會與在集區中每個節點上執行的批次節點代理程式進行通訊。 例如，服務會指示節點代理程式執行工作、停止工作，或取得工作的檔案。 與節點代理程式的通訊是由一或多個負載平衡器所啟用，其數目取決於集區中的節點數目。 負載平衡器會將通訊轉送至所需的節點，而每個節點都是由唯一的埠號碼定址。 根據預設，負載平衡器具有與其相關聯的公用 IP 位址。 您也可以透過 RDP 或 SSH 從遠端存取集區節點 (預設會啟用此存取，並透過負載平衡器) 進行通訊。

### <a name="restricting-access-to-batch-endpoints"></a>限制對批次端點的存取 

有幾項功能可限制對各種批次端點的存取，特別是當解決方案使用虛擬網路時。 

#### <a name="use-private-endpoints"></a>使用私人端點

[Azure Private Link](../private-link/private-link-overview.md) 可讓您透過虛擬網路中的私人端點，存取 Azure PaaS 服務和 azure 裝載的客戶擁有/合作夥伴服務。 您可以使用 Private Link，從虛擬網路或任何對等互連的虛擬網路中，限制對 Batch 帳戶的存取。 對應至 Private Link 的資源也可透過 VPN 或 Azure ExpressRoute，以私人對等互連方式在內部部署環境存取。

若要使用私人端點，您必須在建立時適當地設定 Batch 帳戶;必須停用公用網路存取設定。 建立之後，就可以建立私人端點，並與 Batch 帳戶相關聯。 如需詳細資訊，請參閱 [使用私人端點搭配 Azure Batch 帳戶](private-connectivity.md)。

#### <a name="create-pools-in-virtual-networks"></a>在虛擬網路中建立集區

Batch 集區中的計算節點可以彼此通訊，例如執行多重實例工作，而不需要 (VNET) 的虛擬網路。 不過，根據預設，集區中的節點無法與位於虛擬網路上集區以外的虛擬機器進行通訊，而且具有私人 IP 位址，例如授權伺服器或檔案伺服器。

若要允許計算節點與其他虛擬機器或內部部署網路安全地通訊，您可以將集區設定為位於 Azure VNET 的子網中。

當集區具有公用 IP 端點時，子網必須允許來自 Batch 服務的輸入通訊，才能在計算節點上排程工作和執行其他作業，以及根據您工作負載所需的 Azure 儲存體或其他資源來進行輸出通訊。 針對虛擬機器設定中的集區，Batch 會在連結至計算節點的網路介面層級 (Nsg) 新增網路安全性群組。 這些 Nsg 具有可啟用的規則：

- 來自 Batch 服務 IP 位址的輸入 TCP 流量
- 用於遠端存取的輸入 TCP 流量
- 任何埠到虛擬網路的輸出流量 (可能會針對每個子網層級的 NSG 規則進行修改) 
- 任何埠到網際網路的輸出流量 (可能會針對每個子網層級的 NSG 規則進行修改) 

您不需要在虛擬網路子網層級指定 Nsg，因為 Batch 會設定其本身的 Nsg。 如果您的 NSG 與已部署 Batch 計算節點的子網相關聯，或您想要套用自訂 NSG 規則以覆寫套用的預設值，您必須至少使用輸入和輸出安全性規則設定此 NSG，才能讓集區節點和集區節點通訊的 Batch 服務與 Azure 儲存體。

如需詳細資訊，請參閱 [在虛擬網路中建立 Azure Batch 集](batch-virtual-network.md)區。

#### <a name="create-pools-with-static-public-ip-addresses"></a>建立具有靜態公用 IP 位址的集區

依預設，與集區相關聯的公用 IP 位址是動態的，它們是在建立集區時建立，而且當集區調整大小時，可以新增或移除 IP 位址。 當集區節點上執行的工作應用程式需要存取外部服務時，可能需要將這些服務的存取限制為特定的 Ip。  在此情況下，將無法管理動態 IP 位址。

您可以在建立集區之前，在與 Batch 帳戶相同的訂用帳戶中建立靜態公用 IP 位址資源。 然後，您可以在建立集區時指定這些位址。

如需詳細資訊，請參閱 [使用指定的公用 IP 位址建立 Azure Batch 集](create-pool-public-ip.md)區。

#### <a name="create-pools-without-public-ip-addresses"></a>建立不含公用 IP 位址的集區

根據預設，Azure Batch 虛擬機器設定集區中的所有計算節點都會被指派一或多個公用 IP 位址。 Batch 服務會使用這些端點來排程工作，以及與計算節點進行通訊，包括對網際網路的輸出存取。

若要限制這些節點的存取權，並減少這些節點在網際網路上的可探索性，您可以布建沒有公用 IP 位址的集區。

如需詳細資訊，請參閱 [建立不具公用 IP 位址的集](batch-pool-no-public-ip-address.md)區。

#### <a name="limit-remote-access-to-pool-nodes"></a>限制對集區節點的遠端存取

根據預設，Batch 可讓具有網路連線能力的節點使用者使用 RDP 或 SSH 連線到 Batch 集區中的計算節點。

若要限制對節點的遠端存取，請使用下列其中一種方法：

- 設定 [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) 拒絕存取。 適當的網路安全性群組 (NSG) 將與集區相關聯。
- 建立不具 [公用 IP 位址](batch-pool-no-public-ip-address.md)的集區。 根據預設，這些集區無法在 VNet 之外存取。
- 將 NSG 與 VNet 建立關聯，以拒絕存取 RDP 或 SSH 埠。
- 請勿在節點上建立任何使用者。 若沒有任何節點使用者，便無法進行遠端存取。

## <a name="encrypt-data"></a>加密資料

### <a name="encrypt-data-in-transit"></a>加密傳輸中的資料

所有與 Batch 帳戶端點的通訊 (或透過 Azure Resource Manager) 都必須使用 HTTPS。 `https://`連接到 batch 服務時，您必須在 api 中指定的 batch 帳戶 url 中使用。

與 Batch 服務進行通訊的用戶端應該設定為使用傳輸層安全性 (TLS) 1.2。

### <a name="encrypt-batch-data-at-rest"></a>加密待用批次資料

Batch Api 儲存的某些資訊（例如帳戶憑證、作業和工作中繼資料以及工作命令列）會在 Batch 服務儲存時自動加密。 根據預設，這項資料會使用每個 Batch 帳戶唯一的 Azure Batch 平臺管理金鑰進行加密。

您也可以使用 [客戶管理的金鑰](batch-customer-managed-key.md)來加密此資料。 [Azure Key Vault](../key-vault/general/overview.md) 用來產生和儲存金鑰，並以您的 Batch 帳戶註冊金鑰識別碼。

### <a name="encrypt-compute-node-disks"></a>加密計算節點磁片

依預設，Batch 計算節點有兩個磁片：作業系統磁片和本機暫存 SSD。 Batch 管理的檔案[和目錄](files-and-directories.md)位於暫存 SSD 上，也就是工作輸出檔案等檔案的預設位置。 Batch 工作應用程式可以使用 SSD 或 OS 磁片上的預設位置。

為了增加安全性，請使用下列其中一種 Azure 磁片加密功能來加密這些磁片：

- [使用平臺管理的金鑰進行靜態受控磁片加密](../virtual-machines/windows/disk-encryption.md#platform-managed-keys)
- [使用平臺管理的金鑰在主機上加密](../virtual-machines/windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure 磁碟加密](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>從計算節點安全地存取服務

Batch-節點可以 [安全地存取](credential-access-key-vault.md) 儲存在 [Azure Key Vault](../key-vault/general/overview.md)中的認證和密碼，可供工作應用程式用來存取其他服務。 憑證是用來授與集區節點對 Key Vault 的存取權。

## <a name="governance-and-compliance"></a>治理和合規性

### <a name="compliance"></a>法規遵循

為了協助客戶符合其在全球受管制產業和市場的合規性義務，Azure 會維護一 [系列大型的合規性供應專案](https://azure.microsoft.com/overview/trusted-cloud/compliance)。 

這些供應專案是以各種類型的保證為基礎，包括由獨立的協力廠商審核公司所產生的正式認證、證明、驗證、授權及評量，以及由 Microsoft 產生的契約修訂、自我評量及客戶指引檔。 請參閱 [完整的合規性供應](https://aka.ms/AzureCompliance) 專案，以判斷哪些專案可能與您的 Batch 解決方案有關。

### <a name="azure-policy"></a>Azure 原則

[Azure 原則](../governance/policy/overview.md) 有助於強制執行組織標準以及大規模評估合規性。 Azure 原則的常見使用案例包括針對資源一致性、法規合規性、安全性、成本和管理來進行治理。

根據您的集區配置模式以及應套用原則的資源而定，請使用下列其中一種方式搭配 Batch 使用 Azure 原則：

- 直接使用 Microsoft.Batch/batchAccounts 資源。 您可以使用 Batch 帳戶的屬性子集。 例如，您的原則可以包含有效的 Batch 帳戶區域、允許的集區配置模式，以及是否為帳戶啟用公用網路。
- 使用 Microsoft Compute/virtualMachineScaleSets 資源間接進行。 具有使用者訂用帳戶集區配置模式的 Batch 帳戶，可以在 Batch 帳戶訂用帳戶中建立的虛擬機器擴展集資源上設定原則。 例如，允許的 VM 大小，並確保特定的擴充功能會在每個集區節點上執行。

## <a name="next-steps"></a>後續步驟

- 檢查 [Batch 的 Azure 安全性基準](security-baseline.md)。
- 閱讀更多 [Azure Batch 的最佳做法](best-practices.md)。
