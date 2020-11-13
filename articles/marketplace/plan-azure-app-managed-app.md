---
title: 針對 Azure 應用程式供應專案規劃 Azure 受控應用程式
description: 瞭解在 Microsoft 合作夥伴中心中使用商用 marketplace 入口網站為新的 Azure 應用程式供應專案建立受控應用程式的必要專案。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 2f32fc9ffb8be5b71bfe84a4f0e946e68e8fcd03
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577816"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>針對 Azure 應用程式供應專案規劃 Azure 受控應用程式

Azure _受控應用程式_ 方案是在 Azure Marketplace 中發佈 azure 應用程式供應專案的一種方式。 如果您還沒有這麼做，請參閱 [規劃商業市場 Azure 應用程式供應](plan-azure-application-offer.md)專案。

受控應用程式是透過 Azure Marketplace 部署和計費的交易供應專案。 使用者看到的清單選項是立即取得。

當需要下列條件時，請使用 Azure 應用程式：受控應用程式方案：

- 您將使用虛擬機器 (VM) 或整個基礎結構即服務 (IaaS) 型解決方案，為您的客戶部署以訂用帳戶為基礎的解決方案。
- 您或您的客戶需要由合作夥伴管理解決方案。 例如，夥伴可以是系統整合者或受控服務提供者 (MSP) 。

## <a name="managed-application-offer-requirements"></a>受控應用程式供應專案需求

| 需求 | 詳細資料 |
| ------------ | ------------- |
| Azure 訂用帳戶 | 受控應用程式必須部署至客戶的訂用帳戶，但可由協力廠商管理。 |
| 計費和計量 | 這些資源會在客戶的 Azure 訂用帳戶中提供。 使用隨用隨付付款模型的 Vm 會透過 Microsoft 與客戶交易，並透過客戶的 Azure 訂用帳戶計費。 <br><br> 針對自備授權 Vm，Microsoft 會針對客戶訂用帳戶中所產生的任何基礎結構費用進行計費，但您會向客戶收取軟體授權費用。 |
| Azure 相容的虛擬硬碟 (VHD) | VM 必須建置在 Windows 或 Linux 上。 如需詳細資訊，請參閱<br> •建立適用于 Windows Vhd) 的 [AZURE VM 技術資產](/azure/marketplace/partner-center-portal/vm-certification-issues-solutions#how-to-address-a-vulnerability-or-exploit-in-a-vm-offer.md) (。<br> • Linux  [發行版本背書于 Azure](/azure/virtual-machines/linux/endorsed-distros) (linux vhd) 。 |
| 客戶使用狀況屬性 | 所有新的 Azure 應用程式供應項目也必須包含一個 [Azure 合作夥伴客戶使用方式屬性 GUID](azure-partner-customer-usage-attribution.md) \(部分機器翻譯\)。 如需客戶使用方式屬性和如何啟用的詳細資訊，請參閱 [Azure 合作夥伴客戶使用狀況](azure-partner-customer-usage-attribution.md)屬性。 |
| 部署封裝 | 您將需要一個可讓客戶部署方案的部署套件。 如果您建立多個需要相同技術設定的方案，您可以使用相同的套件。 如需詳細資訊，請參閱下一節：部署套件。 |
|||

> [!NOTE]
> 受控應用程式必須可透過 Azure Marketplace 來部署。 如果客戶通訊有問題，請在您啟用潛在客戶共用之後，觸及感興趣的客戶。

## <a name="deployment-package"></a>部署封裝

部署套件包含此方案所需的所有範本檔案，以及封裝為 .zip 檔案的任何其他資源。

所有 Azure 應用程式都必須在 .zip 封存的根資料夾中包含這兩個檔案：

- Resource Manager 範本檔案，名為 [mainTemplate.json](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md)。 此範本會定義要部署到客戶 Azure 訂用帳戶中的資源。 如需 Resource Manager 範本的範例，請參閱 [Azure 快速入門範本資源庫](https://azure.microsoft.com/documentation/templates/) 或對應的 [GitHub： Azure Resource Manager 快速入門範本](https://github.com/azure/azure-quickstart-templates) 存放庫。
- Azure 應用程式建立體驗的使用者介面定義，名為 [createUiDefinition.json](/azure/azure-resource-manager/managed-application-createuidefinition-overview)。 在使用者介面中，您可以指定讓取用者提供參數值的項目。

支援的檔案大小上限為：

- 總計壓縮的 .zip 封存大小上限為 1 Gb
- .Zip 封存中任何個別未壓縮檔案最多 1 Gb

所有新的 Azure 應用程式供應項目也必須包含一個 [Azure 合作夥伴客戶使用方式屬性 GUID](azure-partner-customer-usage-attribution.md) \(部分機器翻譯\)。

## <a name="azure-regions"></a>Azure 區域

您可以將您的方案發佈至 Azure 公用區域、Azure Government 區域或兩者。 發佈至 [Azure Government](/azure/azure-government/documentation-government-manage-marketplace-partners) 之前，請先在環境中測試並驗證您的方案，因為部分端點可能會有所不同。 若要設定並測試您的方案，請從 [Microsoft Azure 政府機構試用版](https://azure.microsoft.com/global-infrastructure/government/request/)要求試用帳戶。

身為發佈者的您必須負責任何合規性控制、安全性措施和最佳做法。 Azure Government 會使用實際隔離的資料中心和網路 (僅限位於美國地區)。

如需商用 marketplace 所支援的國家/地區和區域清單，請參閱 [地理可用性和貨幣支援](marketplace-geo-availability-currencies.md)。

Azure Government 服務會處理受限於特定政府法規和需求的資料。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要告知這些程式的認證，您可以提供最多 100 個描述這些認證的連結。 這些可以是直接指向程式清單的連結，或是指向您自己網站上相關合規性描述的連結。 只有 Azure Government 客戶可以看到這些連結。

## <a name="choose-who-can-see-your-plan"></a>選擇可查看您方案的人員

您可以將每個方案設定為每個人都可以看見 (公用) 或僅 (私用) 的特定物件。 您最多可以建立100個方案，而且最多可以是45。 您可能會想要建立私人方案，以針對特定客戶提供不同的定價選項或技術設定。

您可以使用 Azure 訂用帳戶識別碼來授與私人方案的存取權，並可選擇包含您指派的每個訂用帳戶識別碼的描述。 您可以使用手動新增最多10個訂用帳戶識別碼或最多10000個訂用帳戶識別碼。CSV 檔案。 Azure 訂用帳戶識別碼會以 GUID 表示，而且字母必須是小寫。

Azure 訂用帳戶不支援私人方案，此訂用帳戶是透過雲端解決方案提供者方案 (CSP) 的轉銷商所建立。 如需詳細資訊，請參閱 [Microsoft 商業市集中的私人優惠](private-offers.md)。

> [!NOTE]
> 如果您發行私人方案，您可以在稍後將其可見度變更為 public。 不過，一旦您發行公用方案之後，就無法將其可見度變更為私用。

## <a name="define-pricing"></a>定義定價

您必須為每個方案提供每月的價格。 此價格不包括此解決方案所部署資源所產生的任何 Azure 基礎結構或隨用隨付軟體成本。

除了每月價格之外，您也可以使用[計量付費計費](partner-center-portal/azure-app-metered-billing.md)，設定非標準單位耗用量的價格。 如有需要，您可以將每月價格設定為零，並只使用計量付費計費。

價格的設定單位為美元 (USD = 美國貨幣) 會在儲存時使用目前的匯率轉換成所有選定市場的當地貨幣。 但是，您可以選擇為每個市場設定客戶價格。

## <a name="just-in-time-jit-access"></a>及時 (JIT) 存取

JIT 存取可讓您對受控應用程式的資源要求更高的存取權，以進行疑難排解或維護。 您一律會擁有資源的唯讀存取權，但在特定期間內，您可以擁有更高的存取權。 如需詳細資訊，請參閱[啟用並要求 Azure 受控應用程式的 Just-In-Time 存取](/azure/managed-applications/request-just-in-time-access) \(部分機器翻譯\)。

> [!NOTE]
> 即使 skimmingBe 確定更新您 `createUiDefinition.json` 的檔案以支援這項功能，使用者也應該注意的資訊。

## <a name="deployment-mode"></a>部署模式

您可以設定受控應用程式方案，以使用 **完整****或累加** 部署模式。 在完整模式中，客戶重新部署應用程式會導致移除受控資源群組中的資源（如果 [mainTemplate.js](/azure/azure-resource-manager/managed-applications/publish-service-catalog-app?tabs=azure-powershell#create-the-arm-template.md)中未定義資源）。 在漸進式模式下，重新部署應用程式不會變更現有的資源。 若要深入瞭解，請參閱 [Azure Resource Manager 部署模式](/azure/azure-resource-manager/templates/deployment-modes)。

## <a name="notification-endpoint-url"></a>通知端點 URL

您可以選擇性地提供 HTTPS Webhook 端點，以接收有關計畫之 managed 應用程式實例上所有 CRUD 作業的通知。

## <a name="customize-allowed-customer-actions-optional"></a>自訂允許的客戶動作 (選擇性) 

您可以選擇性地指定客戶可以在受管理資源上執行的動作，以及 `*/read` 預設可用的動作。

如果您選擇此選項，則必須提供控制項動作或允許的資料動作，或兩者都是。 如需詳細資訊，請參閱[了解 Azure 資源的拒絕指派](/azure/role-based-access-control/deny-assignments) \(部分機器翻譯\)。 如需可用動作的清單，請參閱 [Azure Resource Manager 資源提供者作業](/azure/role-based-access-control/resource-provider-operations)。 例如，若要允許取用者重新啟動虛擬機器，可將 `Microsoft.Compute/virtualMachines/restart/action` 新增至允許的動作。

## <a name="choose-who-can-manage-the-application"></a>選擇可以管理應用程式的人員

您必須指出誰可以管理每個所選雲端中的受控應用程式： _公用 Azure_ 和 _Azure Government 雲端_ 。 收集下列資訊：

- **Azure Active Directory 租使用者識別碼** -Azure AD 租使用者識別碼 (也稱為目錄識別碼) ，其中包含您想要授與許可權之使用者、群組或應用程式的身分識別。 您可以在 [Azure 入口網站] 的 [屬性] 中找到 Azure AD 租使用者識別碼 [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)。
- **授權** –新增您想要授與受管理資源群組之許可權的每個使用者、群組或應用程式的 AZURE ACTIVE DIRECTORY 物件識別碼。 依使用者的主體識別碼 (可在 [Azure 入口網站上的 Azure Active Directory 使用者刀鋒視窗](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到) 識別使用者。

針對每個主體識別碼，您會將 (擁有者或參與者) 的其中一個 Azure AD 內建角色。 您選取的角色描述主體將對客戶訂用帳戶中的資源擁有的許可權。 如需詳細資訊，請參閱 [Azure 內建角色](/azure/role-based-access-control/built-in-roles)。 如需有關角色型存取控制 (RBAC) 的詳細資訊，請參閱[在 Azure 入口網站中開始使用 RBAC](/azure/role-based-access-control/overview)。

> [!NOTE]
> 雖然您可以為每個 Azure 區域新增最多100的授權，但通常更容易建立 Active Directory 使用者群組，並在「主體識別碼」中指定其識別碼。 這可讓您在部署方案之後，將更多使用者新增至管理群組，並減少只需新增更多授權才能更新方案的需求。

## <a name="policy-settings"></a>原則設定

您可以將 [Azure 原則](/azure/governance/policy) 套用至您的受控應用程式，以指定已部署解決方案的合規性需求。 如需了解原則定義和參數值格式，請參閱 [Azure 原則範例](/azure/governance/policy/samples)。

您最多可以設定五個原則，而且每個原則類型只能有一個實例。 某些原則類型需要額外的參數。

| 原則類型 | 需要原則參數 |
| ------------ | ------------- |
| Azure SQL Database 加密 | 否 |
| Azure SQL Server Audit 設定 | 是 |
| Azure Data Lake 存放區加密 | 否 |
| Audit 診斷設定 | 是 |
| Audit 資源位置合規性 | 否 |
|||

針對您新增的每個原則類型，您必須建立標準或免費原則 SKU 的關聯。 稽核原則需要標準 SKU。 原則名稱的限制為50個字元。

## <a name="next-steps"></a>後續步驟

- [如何在商業市場中建立 Azure 應用程式供應專案](create-new-azure-apps-offer.md)
