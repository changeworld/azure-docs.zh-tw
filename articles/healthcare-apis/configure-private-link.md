---
title: Azure API for FHIR 的 Private link
description: 本文說明如何設定 Azure API for FHIR 服務的私人端點
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: bfbdb98e691312db5665261743f8ce698541d4cc
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398210"
---
# <a name="configure-private-link"></a>設定 private link

> [!IMPORTANT]
> 此公開預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Private link 可讓您透過私人端點存取 Azure API for FHIR，這是一種網路介面，可讓您以私人且安全的方式使用虛擬網路中的私人 IP 位址連接到您的網路介面。 您可以使用 private link，以第一方服務的形式安全地從您的 Vnet 存取我們的服務，而不需要經過公用 DNS。 本文將逐步引導您瞭解如何建立、測試及管理 Azure API for FHIR 的私人端點。

## <a name="prerequisites"></a>必要條件

建立私人端點之前，您必須先建立一些 Azure 資源：

- 資源群組–將包含虛擬網路和私人端點的 Azure 資源群組。
- Azure API for FHIR –您想要放在私人端點後方的 FHIR 資源。
- 虛擬網路–用戶端服務和私人端點將連接的 VNet。

如需詳細資訊，請參閱 [Private Link 檔](../private-link/index.yml)。

## <a name="disable-public-network-access"></a>停用公用網路存取

為您的 FHIR 資源建立私人端點時，不會自動停用其公用流量。 若要這樣做，您必須更新 FHIR 資源，以將新的「公用存取」屬性從「已啟用」設定為「已停用」。 停用公用網路存取時，請務必小心，因為所有對您 FHIR 服務的要求不是來自正確設定的私人端點，因此將會遭到拒絕。 只會允許來自私人端點的流量。

![停用公用網路存取](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>建立私人端點

若要建立私人端點，具有 FHIR 資源之 RBAC 許可權的開發人員可以使用 Azure 入口網站、 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)或 [Azure CLI](../private-link/create-private-endpoint-cli.md)。 本文將逐步引導您使用 Azure 入口網站的步驟。 建議使用 Azure 入口網站，因為它會自動建立及設定私人 DNS 區域。 您可以參考 [Private Link 的快速入門指南](../private-link/create-private-endpoint-portal.md) ，以取得詳細資料。

有兩種方式可以建立私人端點。 自動核准流程可讓具有 FHIR 資源之 RBAC 許可權的使用者建立私人端點，而不需要核准。 手動核准流程可讓使用者不需要 FHIR 資源的許可權，就能要求 FHIR 資源擁有者核准私人端點。

### <a name="auto-approval"></a>自動核准

請確定新私人端點的區域與您虛擬網路的區域相同。 FHIR 資源的區域可能會不同。

![Azure 入口網站基本] 索引標籤](media/private-link/private-link-portal2.png)

針對 [資源類型]，搜尋並選取 [HealthcareApis/服務]。 針對 [資源]，選取 FHIR 資源。 針對 [目標子資源]，選取 [fhir]。

![Azure 入口網站資源] 索引標籤](media/private-link/private-link-portal1.png)

如果您沒有設定現有的私人 DNS 區域，請選取 [ (New) privatelink.azurehealthcareapis.com]。 如果您已設定私人 DNS 區域，您可以從清單中選取它。 它的格式必須是 "privatelink.azurehealthcareapis.com"。

![Azure 入口網站設定] 索引標籤](media/private-link/private-link-portal3.png)

部署完成之後，您可以返回 [私人端點連線] 索引標籤，您會看到「已核准」為線上狀態。

### <a name="manual-approval"></a>手動核准

若要手動核准，請選取 [資源] 下的第二個選項： [依資源識別碼或別名連線至 Azure 資源]。 針對 [目標子資源]，輸入 "fhir" 作為 [自動核准]。

![手動核准](media/private-link/private-link-manual.png)

部署完成之後，您可以返回 [私人端點連線] 索引標籤，您可以在此索引標籤上核准、拒絕或移除您的連接。

![選項](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>測試私人端點

若要確定您的 FHIR 伺服器在停用公用網路存取後未收到公用流量，請嘗試從您的電腦叫用伺服器的/metadata 端點。 您應該會收到403禁止的。 請注意，在封鎖公用網路存取旗標之後，最多可能需要5分鐘的時間，才會封鎖公用流量。

若要確保您的私人端點可將流量傳送至您的伺服器：

1. 建立連線到您私人端點設定所在之虛擬網路和子網的 VM。 為了確保來自 VM 的流量只會使用私人網路，您可以透過 NSG 規則停用輸出網際網路流量。
2. RDP 至 VM。
3. 嘗試從 VM 叫用您的 FHIR 伺服器/metadata 端點，您應該會收到「功能」語句作為回應。

## <a name="manage-private-endpoint"></a>管理私人端點

### <a name="view"></a>檢視

私人端點和相關聯的 NIC 會顯示在其建立所在的資源群組 Azure 入口網站中。

![在資源中查看](media/private-link/private-link-view.png)

### <a name="delete"></a>刪除

私人端點只能透過 [總覽] 分頁 (從 Azure 入口網站中刪除，如下所示) 或透過 [網路 (預覽] 下的 [刪除] 選項，) 的 [私人端點連線] 索引標籤。按一下 [刪除] 按鈕將會刪除私人端點和相關聯的 NIC。 如果您刪除 FHIR 資源的所有私人端點，且公用網路存取已停用，則沒有任何要求會將它設為您的 FHIR 伺服器。 在刪除或移動 FHIR 資源之前，必須先從 FHIR 資源刪除所有私人端點。

![刪除私人端點](media/private-link/private-link-delete.png)