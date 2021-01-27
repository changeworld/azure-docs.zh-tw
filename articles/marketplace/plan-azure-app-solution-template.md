---
title: 規劃 Azure 應用程式供應專案的解決方案範本
description: 瞭解在 Microsoft 合作夥伴中心中使用商用 marketplace 入口網站為新的 Azure 應用程式供應專案建立解決方案範本方案時所需的專案。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876506"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>規劃 Azure 應用程式供應專案的解決方案範本

本文說明針對 Azure 應用程式供應專案發佈方案範本方案的需求。 解決方案範本方案是 Azure 應用程式供應專案所支援的兩種方案類型之一。 如需這兩種計畫類型之間差異的詳細資訊，請參閱 [方案類型](plan-azure-application-offer.md#plans)。 如果您尚未這麼做，請參閱 [Azure 應用程式供應專案的方案](plan-azure-application-offer.md)。

解決方案範本方案類型需要 [Azure Resource Manager 範本 (ARM 範本) ](../azure-resource-manager/templates/overview.md) ，以自動部署您的解決方案基礎結構。

## <a name="solution-template-requirements"></a>解決方案範本需求

| 需求 | 詳細資料 |
| ------------ | ------------- |
| 計費和計量 | 解決方案範本方案並不可交易，但可以用來部署透過 Microsoft 商用 marketplace 計費的付費 VM 供應專案。 解決方案的 ARM 範本部署的資源會在客戶的 Azure 訂用帳戶中設定。 隨用隨付的虛擬機器會透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。 <br><br> 針對自備授權 (BYOL) 帳單，雖然 Microsoft 會收取客戶訂用帳戶所產生的基礎結構成本，但您會直接向客戶收取軟體授權費用。 |
| Azure 相容的虛擬硬碟 (VHD) | VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱<ul><li>建立適用于 Windows Vhd 的[AZURE VM 技術資產](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) () </li><li>Linux[發行版本背書于 Azure](../virtual-machines/linux/endorsed-distros.md) (linux vhd) 。</li></ul> |
| 客戶使用狀況屬性 | 在 Azure Marketplace 上發行的所有解決方案範本都需要啟用客戶使用狀況屬性。 如需客戶使用方式屬性和如何啟用的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況](azure-partner-customer-usage-attribution.md)屬性。 |
| 使用受控磁碟 | [受控磁片](../virtual-machines/managed-disks-overview.md) 是 Azure 中的基礎結構即服務 (IaaS) vm 之保存磁片的預設選項。 您必須在解決方案範本中使用受控磁片。<ul><li>若要更新您的解決方案範本，請遵循在 [Azure Resource Manager 範本中使用受控磁片](../virtual-machines/using-managed-disks-template-deployments.md)的指導方針，並使用提供的 [範例](https://github.com/Azure/azure-quickstart-templates)。</li><li>若要將 VHD 發佈為 Azure Marketplace 中的映射，請使用[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd)或[Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) ，將受控磁片的基礎 vhd 匯入至儲存體帳戶。</ul> |
| 部署封裝 | 您將需要一個可讓客戶部署方案的部署套件。 如果您建立多個需要相同技術配置的方案，您可以使用相同的方案套件。 如需詳細資訊，請參閱下一節：部署套件。 |
|||

## <a name="deployment-package"></a>部署封裝

部署套件包含此方案所需的所有範本檔案，以及封裝為 .zip 檔案的任何其他資源。

所有 Azure 應用程式都必須在 .zip 封存的根資料夾中包含這兩個檔案：

- Resource Manager 範本檔案，名為 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)。 此範本會定義要部署到客戶 Azure 訂用帳戶中的資源。 如需 Resource Manager 範本的範例，請參閱 [Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/)或對應的 [GitHub：Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates)存放庫。
- Azure 應用程式建立體驗的使用者介面定義，名為 [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)。 在使用者介面中，您可以指定讓取用者提供參數值的項目。

支援的檔案大小上限為：

- 總計壓縮的 .zip 封存大小上限為 1 Gb
- .Zip 封存中任何個別未壓縮檔案最多 1 Gb

所有新的 Azure 應用程式供應項目也必須包含一個 [Azure 合作夥伴客戶使用方式屬性 GUID](azure-partner-customer-usage-attribution.md) \(部分機器翻譯\)。

## <a name="azure-regions"></a>Azure 區域

您可以將您的方案發佈至 Azure 公用區域、Azure Government 區域或兩者。 發佈至 [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) 之前，請先在環境中測試並驗證您的方案，因為部分端點可能會有所不同。 若要設定並測試您的方案，請從 [Microsoft Azure 政府機構試用版](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

身為發佈者的您必須負責任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。

如需商用 marketplace 所支援的國家/地區和區域清單，請參閱 [地理可用性和貨幣支援](marketplace-geo-availability-currencies.md)。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以是直接指向程式清單的連結，或是指向您自己網站上相關合規性描述的連結。 只有 Azure Government 客戶可以看到這些連結。

## <a name="choose-who-can-see-your-plan"></a>選擇可查看您方案的人員

您可以將每個方案設定為每個人都可以看見 (公用) 或僅 (私用) 的特定物件。 您最多可以建立100個方案，而且最多可以是45。 您可能會想要建立私人方案，以針對特定客戶提供不同的定價選項或技術設定。

您可以使用 Azure 訂用帳戶識別碼來授與私人方案的存取權，並可選擇包含您指派的每個訂用帳戶識別碼的描述。 您可以使用手動新增最多10個訂用帳戶識別碼或最多10000個訂用帳戶識別碼。CSV 檔案。 Azure 訂用帳戶識別碼會以 GUID 表示，而且字母必須是小寫。

> [!NOTE]
> 如果您發行私人方案，您可以在稍後將其可見度變更為 public。 不過，一旦您發行公用方案之後，就無法將其可見度變更為私用。

針對方案範本方案，您也可以選擇從 Azure Marketplace 隱藏方案。 如果只透過另一個解決方案範本或受控應用程式間接部署計畫，您可能會想要這樣做。

> [!NOTE]
> Azure 訂用帳戶不支援私人方案，此訂用帳戶是透過雲端解決方案提供者方案 (CSP) 的轉銷商所建立。

如需詳細資訊，請參閱 [Microsoft 商業市集中的私人優惠](private-offers.md)。

## <a name="next-steps"></a>後續步驟

- [如何在商業市場中建立 Azure 應用程式供應專案](create-new-azure-apps-offer.md)