---
title: 適用於 Azure Data Factory 的 Azure Private Link
description: 瞭解 Azure Private Link 在 Azure Data Factory 中的運作方式。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 6fb460c65d37723b03c1dfd4fba2c353c19455bd
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944579"
---
# <a name="azure-private-link-for-azure-data-factory"></a>適用於 Azure Data Factory 的 Azure Private Link

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

藉由使用 Azure Private Link，您可以在 Azure 中透過私人端點，以服務形式連接至各種平臺， (PaaS) 部署。 私人端點是特定虛擬網路和子網內的私人 IP 位址。 如需支援 Private Link 功能的 PaaS 部署清單，請參閱 [Private Link 檔](../private-link/index.yml)。 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>在客戶網路與 Azure Data Factory 之間進行安全通訊 
您可以將 Azure 虛擬網路設定為雲端中網路的邏輯標記法。 這麼做可提供下列優點：
* 您可以協助保護您的 Azure 資源免于遭受公用網路中的攻擊。
* 您可以讓網路和 Data Factory 安全地彼此通訊。 

您也可以藉由設定網際網路通訊協定安全性 (IPsec) VPN (站對站) 連線或 Azure ExpressRoute (私人對等互連) 連線，將內部部署網路連線到您的虛擬網路。 

您也可以在內部部署機器或虛擬網路中的虛擬機器上安裝自我裝載整合執行時間。 這樣做可讓您：
* 在雲端資料存放區與私人網路中的資料存放區之間執行複製活動。
* 針對內部部署網路或 Azure 虛擬網路中的計算資源分派轉換活動。 

Azure Data Factory 與客戶虛擬網路之間必須有數個通道，如下表所示：

| 網域 | 連接埠 | 描述 |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Data Factory 撰寫和監視所需的控制平面。 |
| `*.{region}.datafactory.azure.net` | 443 | 必須提供此資訊，自我裝載整合執行階段才能連線到 Data Factory 服務。 |
| `*.servicebus.windows.net` | 443 | 必須提供此資訊，自我裝載整合執行階段才能進行互動式撰寫。 |
| `download.microsoft.com` | 443 | 自我裝載整合執行階段所需，以用於下載更新。 |

由於支援 Azure Data Factory 的 Private Link，您可以：
* 在您的虛擬網路中建立私人端點。
* 啟用特定 data factory 實例的私用連接。 

Azure Data Factory 服務的通訊會通過 Private Link 並協助提供安全的私用連線能力。 您不需要在虛擬網路或公司防火牆中設定上述的網域和埠，即可提供更安全的方式來保護您的資源。  

![Azure Data Factory 架構的 Private Link 圖表。](./media/data-factory-private-link/private-link-architecture.png)

針對上述每個通道啟用 Private Link 服務可提供下列功能：
- **支援**：
   - 即使您封鎖所有輸出通訊，您也可以在虛擬網路中撰寫和監視資料處理站。
   - 自我裝載整合執行時間和 Azure Data Factory 服務之間的命令通訊可以安全地在私人網路環境中執行。 自我裝載整合執行時間和 Azure Data Factory 服務之間的流量會經歷 Private Link。 
- **目前不支援**：
   - 使用自我裝載整合執行時間（例如測試連線、流覽資料夾清單和資料表清單、取得架構和預覽資料）的互動式撰寫會經歷 Private Link。
   - 如果您啟用自動更新，則可以從 Microsoft 下載中心自動下載新版的自我裝載整合執行時間。

   > [!NOTE]
   > 針對目前不支援的功能，您仍然需要在虛擬網路或公司防火牆中設定先前提及的網域和埠。 

> [!WARNING]
> 當您建立連結服務時，請確定您的認證儲存在 Azure key vault 中。 否則，當您在 Azure Data Factory 中啟用 Private Link 時，認證將無法運作。

## <a name="dns-changes-for-private-endpoints"></a>私人端點的 DNS 變更
當您建立私人端點時，Data Factory 的 DNS CNAME 資源記錄會更新為首碼為 ' privatelink ' 的子域中的別名。 根據預設，我們也會建立對應至 ' privatelink ' 子域的 [私人 dns 區域](../dns/private-dns-overview.md)，並以 DNS 為私人端點的資源記錄。

當您從 VNet 外部使用私人端點來解析 data factory 端點 URL 時，它會解析為 data factory 服務的公用端點。 從裝載私人端點的 VNet 解析時，儲存體端點 URL 會解析為私人端點的 IP 位址。

在上述說明的範例中，從裝載私人端點的 VNet 外部解析時，Data Factory ' DataFactoryA ' 的 DNS 資源記錄會是：

| 名稱 | 類型 | 值 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}. datafactory | CNAME   | DataFactoryA.{region}. privatelink. datafactory |
| DataFactoryA.{region}. privatelink. datafactory | CNAME   | < data factory 服務公用端點 > |
| < data factory 服務公用端點 >  | A | < data factory 服務公用 IP 位址 > |

在裝載私人端點的 VNet 中解析時，DataFactoryA 的 DNS 資源記錄會是：

| 名稱 | 類型 | 值 |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}. datafactory | CNAME   | DataFactoryA.{region}. privatelink. datafactory |
| DataFactoryA.{region}. privatelink. datafactory   | A | < 私人端點 IP 位址 > |

如果您在網路上使用自訂 DNS 伺服器，用戶端必須能夠將 Data Factory 端點的 FQDN 解析為私人端點 IP 位址。 您應該將您的 DNS 伺服器設定為將私人連結子域委派給 VNet 的私人 DNS 區域，或設定 ' DataFactoryA ' 的 A 記錄。具有私人端點 IP 位址的 {region}. privatelink. datafactory。

如需有關設定您自己的 DNS 伺服器以支援私人端點的詳細資訊，請參閱下列文章：
- [Azure 虛擬網路中的資源名稱解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [私人端點的 DNS 設定](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>設定 Azure Data Factory 的 Private Link
您可以使用 [Azure 入口網站](../private-link/create-private-endpoint-portal.md)來建立私人端點。

您可以選擇是否要透過公用端點或私人端點，將自我裝載整合執行時間連線到 Azure Data Factory。 

![封鎖自我裝載 Integration Runtime 之公用存取的螢幕擷取畫面。](./media/data-factory-private-link/disable-public-access-shir.png)


您也可以移至 Azure 入口網站中的 Azure data factory，並建立私人端點，如下所示：

![用來建立私人端點的 [私人端點連線] 窗格螢幕擷取畫面。](./media/data-factory-private-link/create-private-endpoint.png)

在 [ **資源**] 步驟中，選取 [ **Datafactory]/** [Factory] 作為 [ **資源類型**]。 而且，如果您想要為自我裝載整合執行時間和 Azure Data Factory 服務之間的命令通訊建立私人端點，請選取 [ **datafactory** ] 作為 [ **目標子資源**]。

![選取資源的 [私人端點連線] 窗格螢幕擷取畫面。](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> 停用公用網路存取只適用于自我裝載整合執行時間，而不是 Azure Integration Runtime 和 SQL Server Integration Services (SSIS) Integration Runtime。

如果您想要建立私人端點，以在虛擬網路中撰寫和監視資料處理站，請選取 [ **入口網站** ] 作為 [ **目標子資源**]。

> [!NOTE]
> 您仍然可以在建立入口網站的私人端點之後，透過公用網路存取 Azure Data Factory 入口網站。

## <a name="next-steps"></a>後續步驟

- [使用 Azure Data Factory UI 來建立資料處理站](quickstart-create-data-factory-portal.md)
- [Azure Data Factory 簡介](introduction.md)
- [Azure Data Factory 中的視覺化撰寫](author-visually.md)