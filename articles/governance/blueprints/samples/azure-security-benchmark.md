---
title: Azure 安全性效能評定藍圖範例概觀
description: Azure 安全性效能評定藍圖範例的概觀。 此藍圖範例可協助客戶評定特定控制措施。
ms.date: 06/02/2020
ms.topic: sample
ms.openlocfilehash: 4113f350c21087dbda5f69d9c3981e2d169ffc00
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651903"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Azure 安全性效能評定藍圖範例

Azure 安全性效能評定藍圖範例使用 [Azure 原則](../../policy/overview.md)提供治理防護措施，協助您存取特定 [Azure 安全性效能評定](../../../security/benchmarks/overview.md)控制措施。 此藍圖可協助客戶針對任何必須實作 Azure 安全性效能評定控制措施的已部署 Azure 架構，部署一組核心原則。

## <a name="control-mapping"></a>控制項對應

[Azure 原則控制項對應](../../policy/samples/azure-security-benchmark.md)提供此藍圖中所包含原則定義的相關詳細資料，以及這些原則定義如何對應至 Azure 安全性效能評定中的**合規性網域**和**控制項**。 將資源指派給架構時，Azure 原則會評估不符合已指派原則定義的資源。 如需詳細資訊，請參閱 [Azure 原則](../../policy/overview.md)。

## <a name="deploy"></a>部署

若要部署 Azure 藍圖 Azure 安全性效能評定藍圖範例，必須採取下列步驟：

> [!div class="checklist"]
> - 從範例建立新的藍圖
> - 將您的範例複本標記為**已發佈**
> - 將您的藍圖複本指派給現有的訂用帳戶

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free)。

### <a name="create-blueprint-from-sample"></a>從範例建立藍圖

首先，若要實作藍圖範例，請使用範例作為起點，在您的環境中建立新藍圖。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 在左側的 [快速入門]  頁面上，選取 [建立藍圖]  **下方的 [建立]** 按鈕。

1. 在「其他範例」下，尋找 **Azure 安全性效能評定**藍圖範例，然後選取名稱以選取此範例。

1. 輸入藍圖範例的 [基本資料]  ：

   - **藍圖名稱**：為 Azure 安全性效能評定藍圖範例的複本提供名稱。
   - **定義位置**：使用省略符號，然後選取要作為範例複本儲存位置的管理群組。

1. 在頁面頂端選取 [成品]  索引標籤，或在頁面底部選取 **[下一步：成品]** 。

1. 檢閱構成此藍圖範例的成品清單。 許多成品都具有我們稍後會定義的參數。 當您檢閱完藍圖範例時，請選取 [儲存草稿]  。

### <a name="publish-the-sample-copy"></a>發佈範例複本

您的環境中現已建立了藍圖範例複本。 該複本會以**草稿**模式建立，而且必須先**發佈**，才能進行指派和部署。 您可以根據環境和需求來自訂藍圖範例複本，但是這樣的修改可能會使其與 Azure 安全性效能評定建議不一致。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取頁面頂端的 [發佈藍圖]  。 在右側的新窗格中，提供藍圖範例複本的**版本**。 如果您稍後會進行修改，此屬性十分實用。 提供**變更附註**，例如「從 Azure 安全性效能評定藍圖範例發佈的第一版」。 然後選取頁面底部的 [發佈]  。

### <a name="assign-the-sample-copy"></a>指派範例複本

成功**發佈**藍圖範例複本後，可以將藍圖定義指派給其所在管理群組中的訂用帳戶。 此步驟用於提供參數，以建立每個專屬的藍圖範例複本部署。

1. 在左側窗格中選取 [所有服務]  。 搜尋並選取 [藍圖]  。

1. 選取左側的 [藍圖定義]  頁面。 使用篩選來尋找您的藍圖範例複本，然後將其選取。

1. 選取藍圖定義頁面頂端的 [指派藍圖]  。

1. 提供用於指派藍圖的參數值：

   - 基本概念

     - **訂用帳戶**：在用來儲存藍圖範例複本的管理群組中，選取一或多個訂用帳戶。 如果您選取多個訂用帳戶，輸入的參數就會用來為每個訂用帳戶建立指派。
     - **指派名稱**：名稱會根據藍圖名稱預先填入。
       視需要變更，或保持原狀。
     - **位置**：選取要在其中建立受控識別的區域。 Azure 藍圖會使用此受控識別來部署指派的藍圖之中的所有成品。 若要深入了解，請參閱[適用於 Azure 資源的受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **藍圖定義版本**：挑選**已發佈**版本的藍圖範例複本。

   - 鎖定指派

     為您的環境選取藍圖鎖定設定。 如需詳細資訊，請參閱[藍圖資源鎖定](../concepts/resource-locking.md)。

   - 受控識別

     保留預設的 [系統指派] 受控識別選項。

   - 構件參數

     本節中定義的參數會套用至其定義所屬的成品。 這些參數是[動態參數](../concepts/parameters.md#dynamic-parameters)，因為定義於藍圖指派期間。 如需完整清單或成品參數及其說明，請參閱[成品參數資料表](#artifact-parameters-table)。

1. 輸入所有參數後，選取頁面底部的 [指派]  。 藍圖指派會隨即建立，並且開始部署成品。 部署需要大約一小時的時間。 若要檢查部署的狀態，請開啟藍圖指派。

> [!WARNING]
> Azure 藍圖服務和內建藍圖範例皆是**免費**項目。 Azure 資源會[依據產品計價](https://azure.microsoft.com/pricing/)。 請使用[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估此藍圖範例所部署資源的執行成本。

### <a name="artifact-parameters-table"></a>成品參數資料表

下表提供藍圖成品參數的清單：

|成品名稱|成品類型|參數名稱|描述|
|-|-|-|-|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|從 Windows VM 系統管理員群組排除的使用者清單|應從系統管理員本機群組中排除之成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|必須包含在 Windows VM 系統管理員群組中的使用者清單|應納入系統管理員本機群組中之成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|Windows VM 系統管理員群組必須且「只能」包含的使用者清單|管理員本機群組所有預期成員的分號分隔清單。 例如：Administrator; myUser1; myUser2|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|應啟用網路監看員的區域清單|若要查看使用 Get-AzLocation 的區域完整清單|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|應連線 VM 的虛擬網路|範例：/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|虛擬網路應使用的網路閘道|範例：/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|Log Analytics 代理程式應連線的工作區識別碼清單|Log Analytics 代理程式應連線的工作區識別碼清單 (以分號分隔)|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|應啟用診斷記錄的資源類型清單|稽核所選資源類型的診斷設定|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|PHP 最新版本|應用程式服務的最新支援 PHP 版本|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|Java 最新版本|應用程式服務的最新支援 Java 版本|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|最新 Windows Python 版本|應用程式服務的最新支援 Python 版本|
|稽核 Azure 安全性效能評定建議，以及部署特定的受支援 VM 延伸模組|原則指派|最新 Linux Python 版本|應用程式服務的最新支援 Python 版本|

## <a name="next-steps"></a>後續步驟

有關藍圖及其使用方式的其他文件：

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 了解如何[更新現有的指派](../how-to/update-existing-assignments.md)。