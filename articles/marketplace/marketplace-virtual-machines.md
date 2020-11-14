---
title: 規劃虛擬機器供應專案-Microsoft 商業 marketplace
description: 本文說明將虛擬機器供應專案發佈至 Azure Marketplace 的需求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 433875e52e77a8992197b47fe0424cd0b7e9f50c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628283"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>如何規劃虛擬機器供應專案

本文說明將虛擬機器 (VM) 供應專案發佈至商用 marketplace 的不同選項和需求。 VM 供應專案是透過 Azure Marketplace 部署和計費的可交易優惠。

開始之前，請 [在合作夥伴中心中建立商業 marketplace 帳戶](./partner-center-portal/create-account.md) ，並確定您的帳戶已在商業 marketplace 方案中註冊。

### <a name="technical-fundamentals"></a>技術基礎

設計、建立和測試供應專案的程式需要一些時間，而且需要在 Azure 平臺和用來建立您的供應專案的技術方面提供專業知識。 您的工程小組應具備 [Azure 虛擬機器](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 儲存體](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 網路](https://azure.microsoft.com/services/?filter=networking#networking)的實用知識，以及 [azure 應用程式的設計和架構](https://azure.microsoft.com/solutions/architecture/)的熟練度。 請參閱下列額外的技術資源： 

- 教學課程
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- 範例
  - [適用於 Linux 虛擬機器的 Azure CLI 範例](../virtual-machines/linux/cli-samples.md) \(部分機器翻譯\)
  - [適用於 Linux 虛擬機器的 Azure PowerShell](../virtual-machines/linux/powershell-samples.md) \(部分機器翻譯\)
  - [適用於 Windows 虛擬機器的 Azure CLI 範例](../virtual-machines/windows/cli-samples.md) \(部分機器翻譯\)
  - [適用於 Windows 虛擬機器的 Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>技術需求

VM 供應專案具有下列技術需求：

- 您必須準備一個作業系統虛擬硬碟 (VHD) 。 資料磁片 Vhd 是選擇性的。 以下將更詳細地說明這一點。
- 客戶可隨時取消您的供應專案。
- 您必須為您的供應專案建立至少一個方案。 您的方案是根據您選取的 [授權選項](#licensing-options) 來定價。
   > [!IMPORTANT]
   > 方案中每個 VM 映像都必須具有相同數目的資料磁碟。

VM 包含兩個元件：

- **操作 VHD** –包含隨您的供應專案部署的作業系統和解決方案。 準備 VHD 的程式會因其為 Linux、Windows 或自訂型 VM 而有所不同。
- **資料磁片 vhd** (選用的) –適用于 VM 的專屬永久儲存體。 請勿使用作業系統 VHD (例如 C: 磁碟機) 來儲存永續性資訊。 
    - 您最多可以包含16個數據磁片。
    - 針對每個資料磁碟使用一個 VHD，即使磁碟是空的也一樣。

    > [!NOTE]
    > 無論使用哪一種作業系統，只要新增解決方案所需的最少數目資料磁碟。 客戶無法在部署時移除屬於映像一部分的磁碟，但可隨時在部署期間或之後新增磁碟。

如需準備技術資產的詳細指示，請參閱使用 [已核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md) ，或 [使用您自己的映射建立虛擬](azure-vm-create-using-own-image.md)機。

## <a name="preview-audience"></a>預覽對象

預覽物件可以先存取您的 VM 供應專案，然後才會在 Azure Marketplace 發佈，以便在您發佈之前測試端對端功能。 在 [ **預覽物件** ] 頁面上，您可以定義有限的預覽物件。 

> [!NOTE]
> 預覽物件與私人方案不同。 私人方案是您只提供給您所選擇之特定物件的方案。 這可讓您與特定客戶協調自訂方案。 如需詳細資訊，請參閱下一節：方案。

您可以將邀請傳送給 Microsoft 帳戶 (MSA) 或 Azure Active Directory (Azure AD) 電子郵件地址。 手動新增10個電子郵件地址，或使用 .csv 檔案最多匯入20。 如果您的供應專案已上線，您仍然可以定義預覽物件來測試您的供應專案的任何變更或更新。

## <a name="plans-and-pricing"></a>方案和定價

VM 供應專案至少需要一個方案。 方案會定義方案範圍和限制，以及相關聯的定價。 您可以為您的供應專案建立多個方案，為您的客戶提供不同的技術和授權選項，以及免費試用版。 如需方案的一般指引，包括定價模型、免費試用和私人方案，請參閱商業 marketplace 供應專案的 [方案和定價](plans-pricing.md) 。 

使用隨用隨付或自備授權 (BYOL) 授權模型，即可完全啟用商務的 Vm。 Microsoft 會主控商務交易，並代表您向客戶收費。 您獲得的好處是，可以使用客戶與 Microsoft 之間慣用的付款關係，包括任何 Enterprise 合約。 如需詳細資訊，請參閱 [商業 marketplace 交易功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)。

> [!NOTE]
> 與 Enterprise 合約相關聯的貨幣承諾可用於 VM 的 Azure 使用量，但不能用於您的軟體授權費用。

### <a name="licensing-options"></a>授權選項

當您準備發佈新的 VM 供應專案時，您必須決定要選擇哪一個授權選項。 這會決定當您在合作夥伴中心中建立供應專案時，您稍後需要提供的額外資訊。

以下是 VM 供應專案的可用授權選項：

| 授權選項 | 交易處理 |
| --- | --- |
| 免費試用 | 為您的客戶提供一、三個月的免費試用。 |
| 試用產品 | 此選項可讓您的客戶評估 Vm，而不需額外付費。 他們不需要是現有的 Azure 客戶，也能與試用體驗進行互動。 如需詳細資訊，請參閱 [什麼是試用產品？](./what-is-test-drive.md) |
| BYOL | 「攜帶您自己的授權」選項可讓您的客戶將現有的軟體授權帶到 Azure。\* |
| 以使用量為基礎 | 也稱為隨用隨付，此選項可讓您的客戶每小時支付費用。 |
| 互動式示範  | 使用互動式示範，為客戶提供您解決方案的引導式體驗。 好處是您可以提供試用版體驗，而不需要提供複雜解決方案的複雜設定。 |
|

\* 作為發行者，您支援軟體授權交易的所有層面，包括 (但不限於) 訂單、履行、計量、計費、發票付款、付款及集合。

下列範例顯示 Azure Marketplace 中的 VM 供應專案，其具有以使用量為基礎的定價。

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="範例 VM 供應專案畫面。":::

### <a name="private-plans"></a>私人方案

您可以將映射和定價發佈為私用方案，以限制對一組特定的客戶探索和部署您的 VM。 私用計畫可讓您為最接近的客戶建立專屬供應專案，並提供自訂軟體和條款。 這些自訂條款可讓您突顯各式各樣的案例，包括具有特殊定價和條款的實地主導交易，以及限制版軟體的優先存取權。 私人方案可讓您將特定定價或產品提供給一組有限的客戶。

如需詳細資訊，請參閱[Microsoft 商業市集中](private-offers.md)的商業 marketplace 供應專案和私人優惠[方案和定價](plans-pricing.md)。

## <a name="test-drives"></a>試用產品

您可以選擇啟用 VM 的試用產品。 試用產品可讓客戶在固定的時數記憶體取預先設定的環境。 您可以啟用任何發佈選項的試用產品，不過這項功能有其他需求。 若要深入瞭解試用產品，請參閱 [什麼是試用產品？](what-is-test-drive.md)。 如需設定不同類型的試用產品的相關資訊，請參閱 [試用產品技術](test-drive-technical-configuration.md)設定。

> [!TIP]
> [試用產品不同于免費試用版](plans-pricing.md#free-trials)。 您可以提供試用產品、免費試用或兩者。 它們都能為您的客戶提供解決方案一段固定時間。 但是，試用產品也包含實際操作的自我引導式導覽，說明您產品的主要功能，以及在實際執行案例中所示範的優點。

## <a name="customer-leads"></a>潛在客戶

您必須將您的供應專案連線到客戶關係管理 (CRM) 系統才能收集客戶資訊。 系統會要求客戶提供權限，以分享客戶資訊。 這些客戶詳細資料，以及供應專案名稱、識別碼和線上商店找到您的供應專案，將會傳送至您所設定的 CRM 系統。 商業 marketplace 支援各種 CRM 系統，以及使用 Azure 資料表或使用 Power Automate 設定 HTTPS 端點的選項。

在供應專案建立期間或之後，您可以隨時新增或修改 CRM 連接。 如需詳細指引，請參閱 [您的商業 marketplace 供應專案客戶潛在客戶](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="legal-contracts"></a>法律合約

為了簡化客戶的採購程式，並降低軟體廠商的法律複雜性，Microsoft 提供了一種標準合約，可供您在商業市集中提供供應專案使用。 當您在標準合約下提供您的軟體時，客戶只需要讀取並接受一次，您就不需要建立自訂條款及條件。

如果您選擇使用標準合約，您可以選擇將通用修訂條款以及最多10個自訂修訂新增至標準合約。 您也可以使用自己的條款及條件，而不是標準合約。 您將在 [ **屬性** ] 頁面中管理這些詳細資料。 如需詳細資訊，請參閱 [Microsoft 商業 marketplace 的標準合約](standard-contract.md)。

> [!NOTE]
> 使用商業 marketplace 的標準合約發佈供應專案之後，您就無法使用自己的自訂條款及條件。 這是「擇一」案例。 您可以在標準合約或您自己的條款及條件下提供您的解決方案。 如果您想要修改標準合約的條款，您可以透過標準合約的修正。

## <a name="cloud-solution-providers"></a>雲端解決方案提供者

在合作夥伴中心中建立您的供應專案時，您會看到 [ **轉售給 csp** ] 索引標籤。此選項可讓身為 Microsoft 雲端解決方案提供者一部分的合作夥伴 (CSP) 方案，將您的 VM 轉售為配套供應專案的一部分。 所有自備授權 (BYOL) 方案都會自動加入方案中。 您也可以選擇加入非 BYOL 方案。 如需詳細資訊，請參閱 [雲端解決方案提供者程式](cloud-solution-providers.md) 。 

> [!NOTE]
> 現在已可使用雲端解決方案提供者 (CSP) 合作夥伴通道加入宣告。 如需透過 Microsoft CSP 合作夥伴通路行銷供應專案的詳細資訊，請參閱 [**雲端解決方案提供者**](./cloud-solution-providers.md)。

## <a name="next-steps"></a>後續步驟

- [在 Azure Marketplace 上建立虛擬機器供應專案](azure-vm-create.md)
- [使用已核准的基底建立虛擬機器](azure-vm-create-using-approved-base.md) ，或 [使用您自己的映射建立虛擬機器](azure-vm-create-using-own-image.md)。
- [陳列供應項目的最佳做法](gtm-offer-listing-best-practices.md)