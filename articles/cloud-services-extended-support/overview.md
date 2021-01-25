---
title: '關於 Azure 雲端服務 (延伸支援) '
description: 深入瞭解服務設定檔中網路設定元素的子項目，以指定虛擬網路和 DNS 值。
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 969f60cd92e8c1cbe93f1646cccd08c942ad9923
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762825"
---
# <a name="about-azure-cloud-services-extended-support"></a>關於 Azure 雲端服務 (延伸支援) 

> [!IMPORTANT]
> 雲端服務 (延伸支援) 目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

雲端服務 (延伸支援) 是 [Azure 雲端服務](https://azure.microsoft.com/services/cloud-services/)產品的新 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)型部署模型，目前為公開預覽狀態。 雲端服務 (延伸支援) 具有提供區域恢復功能的主要優點，以及使用 Azure Service Manager 所部署 Azure 雲端服務的功能同位檢查。 它也提供一些 ARM 功能，例如以角色為基礎的存取和控制 (RBAC) 、標記、原則，以及支援部署範本。  

透過這種變更，雲端服務的 Azure Service Manager 型部署模型將會重新命名為 [雲端服務 (傳統) ](../cloud-services/cloud-services-choose-me.md)。 您將保有建立和快速部署 web 和雲端應用程式和服務的能力。 您可以根據目前的需求調整您的雲端服務基礎結構，並確保您的應用程式效能能保持在最新狀態，同時降低成本。  

## <a name="what-does-not-change"></a>不會變更的內容 
- 您可以建立程式碼、定義設定，並將其部署到 Azure。 Azure 會設定計算環境、執行您的程式碼，然後為您進行監視及維護。
- 雲端服務 (延伸支援) 也支援兩種類型的角色： [web 和背景工作](../cloud-services/cloud-services-choose-me.md)角色。 Web 角色和背景工作角色的設計、架構或元件沒有任何變更。 
- 雲端服務的三個元件、服務定義 ( 的) 、服務設定 ( .cscfg) 以及服務套件 (. .cspkg) 繼續進行，而且其 [格式](cloud-services-model-and-package.md)不會變更。 
- 執行時間程式碼不需要進行任何變更，因為資料平面是相同的，而且控制平面只會變更。 
- Azure GuestOS 版本和相關更新會與雲端服務 (傳統) 
- 更新網域相關的基礎更新程式、升級的進行方式、回復和在更新期間所允許的服務變更不會變更

## <a name="changes-in-deployment-model"></a>部署模型中的變更

服務設定 ( .cscfg) 和服務定義 () 的變更都需要進行基本變更，以部署雲端服務 (延伸支援) 。 執行時間程式碼不需要進行任何變更。 不過，部署腳本需要更新，以呼叫新的 Azure Resource Manager 型 Api。 

:::image type="content" source="media/overview-image-1.png" alt-text="影像會顯示傳統雲端服務設定，並新增範本區段。 ":::

雲端服務 (傳統) 與雲端服務之間的主要差異 (針對部署的延伸支援) ： 

- Azure Resource Manager 部署會使用 [ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) ，這是 JavaScript 物件標記法 (的 JSON) 檔案，可定義專案的基礎結構和設定。 範本會使用宣告式語法，可讓您陳述您要部署的項目，而不需要撰寫一連串程式設計命令來加以建立。 部署雲端服務時，服務設定和服務定義檔必須與 [ARM 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) 一致， (延伸支援) 。 這可以藉由 [手動建立 ARM 範本](deploy-template.md) 或使用 [PowerShell](deploy-powershell.md)、 [入口網站](deploy-portal.md) 和 [Visual Studio](deploy-visual-studio.md)來達成。  

- 客戶必須使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) 來 [管理雲端服務中的憑證 (延伸支援) ](certificates-and-key-vault.md)。 Azure Key Vault 可讓您在集中且安全的雲端存放庫中安全地儲存和管理應用程式認證，例如秘密、金鑰和憑證。 您的應用程式可以在執行時間進行驗證 Key Vault，以取得認證。 

- 透過 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) 部署的所有資源都必須位於虛擬網路內。 虛擬網路和子網會使用現有的 Azure Resource Manager Api 在 Azure Resource Manager 中建立，且在部署雲端服務 (延伸支援) 時，將需要在 .cscfg 的 NetworkConfiguration 區段中參考。   

- 每個雲端服務 (延伸支援) 是單一獨立部署。 雲端服務 (延伸支援) 不支援單一雲端服務內的多個位置。  
    - 您 <sup>*</sup> 可以使用 VIP 交換功能，在兩個雲端服務之間交換 (延伸支援) 。 若要測試和預備新的雲端服務版本，請將雲端服務部署 (延伸支援) ，並將它標記為可透過其他雲端服務 (延伸支援的 VIP 交換)   

- 功能變數名稱服務 (DNS) 標籤是雲端服務 (延伸支援) 的選擇性選項。 在 Azure Resource Manager 中，DNS 標籤是與雲端服務相關聯的公用 IP 資源的屬性。 


<sup>*</sup> 公開預覽期間不提供雲端服務 (延伸支援) 的 VIP 交換。  

## <a name="migration-to-azure-resource-manager"></a>遷移至 Azure Resource Manager

雲端服務 (延伸支援) 提供兩個路徑，可讓您從 [Azure Service Manager](https://docs.microsoft.com/powershell/azure/servicemanagement/overview?view=azuresmps-4.0.0&preserve-view=true ) 遷移至 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)。 
1) 客戶會直接在 Azure Resource Manager 中部署雲端服務，然後在 Azure Service Manager 中刪除舊的雲端服務。 
2) 就地遷移支援將雲端服務 (傳統) 的功能遷移到雲端服務 (延伸支援) 的最短時間。 

### <a name="additional-migration-options"></a>其他遷移選項

從雲端服務評估遷移計畫時 (傳統) 至雲端服務 (延伸支援) 您可能會想要調查額外的 Azure 服務，例如： [虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)、 [App Service](https://docs.microsoft.com/azure/app-service/overview)、 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes)和 [Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)。 這些服務將繼續提供額外的功能，而雲端服務 (延伸支援) 主要會維持與雲端服務 (傳統的功能同位。 )  

視應用程式而定，雲端服務 (延伸支援) 可能需要比其他選項更少的時間來移至 Azure Resource Manager。 如果您的應用程式不在演進中，雲端服務 (延伸支援) 是可行的選項，因為它會提供快速的遷移路徑。 相反地，如果您的應用程式持續演進，而且需要更新式的功能集，請探索其他 Azure 服務，以更妥善解決您目前和未來的需求。 

## <a name="next-steps"></a>後續步驟
- 請參閱雲端服務的 [部署必要條件](deploy-prerequisite.md) (延伸支援) 。
- 使用 [Azure 入口網站](deploy-portal.md)、 [PowerShell](deploy-powershell.md)、 [範本](deploy-template.md) 或 [Visual Studio](deploy-visual-studio.md)，將雲端服務部署 (延伸支援) 。
- 請 [參閱雲端服務的常見問題，](faq.md) (延伸支援) 。
