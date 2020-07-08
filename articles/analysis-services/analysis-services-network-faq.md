---
title: Analysis Services 網路連線的相關常見問題 |Microsoft Docs
description: 本文提供有關 Analysis Services 網路連線的一些常見問題的解答。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b60cf34e8efed2ed63b6e35cfaf7445edb701610
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82838496"
---
# <a name="frequently-asked-questions-about-analysis-services-network-connectivity"></a>Analysis Services 網路連線的相關常見問題

本文提供有關連接到儲存體帳戶、資料來源、防火牆和 IP 位址等常見問題的解答。

## <a name="backup-and-restore"></a>備份與還原

**問**：如何使用位於防火牆後方的儲存體帳戶來進行備份和還原？   
**答**： Azure Analysis Services 不會使用固定的 IP 位址或服務標記。 Analysis Services 伺服器所使用的 IP 位址範圍，可以是*Azure 區域*的 ip 位址範圍內的任何專案。 因為您的伺服器 IP 位址是可變的，而且可能會隨著時間而變更，所以您的防火牆規則必須允許您的伺服器所在的整個 Azure 區域 IP 位址範圍。

**問題**-我的 Azure 儲存體帳戶與我的 Analysis Services 伺服器位於不同的區域。 如何? 設定儲存體帳戶防火牆設定嗎？   
**回答**-如果儲存體帳戶位於不同的區域，請設定儲存體帳戶防火牆設定，以允許來自**所選網路**的存取。 在 [防火牆**位址範圍**] 中，指定 Analysis Services 伺服器所在區域的 IP 位址範圍。 若要取得 Azure 區域的 IP 範圍，請參閱[AZURE Ip 範圍和服務標籤-公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 支援將儲存體帳戶防火牆設定為允許從所有網路進行存取，但最好是選擇選取的網路並指定 IP 位址範圍。 

**問題**-我的 Azure 儲存體帳戶位於與我的 Analysis Services 伺服器相同的區域中。 如何設定儲存體帳戶的防火牆設定？   
**答**：由於您的 Analysis Services 伺服器和儲存體帳戶位於相同的區域中，因此它們之間的通訊會使用內部 IP 位址範圍，因此，將防火牆設定為使用選取的網路，並指定不支援的 IP 位址範圍。 如果組織原則需要防火牆，則必須將其設定為允許來自所有網路的存取。


## <a name="data-source-connections"></a>資料來源連接

**問**-我的資料來源系統有 VNET。 如何允許我的 Analysis Services 伺服器從 VNET 存取資料庫？   
**答**： Azure Analysis Services 無法加入 VNET。 這裡的最佳解決方案是在 VNET 上安裝和設定內部部署資料閘道，然後使用**AlwaysUseGateway**伺服器屬性來設定您的 Analysis Services 伺服器。 若要深入瞭解，請參閱[在 Azure 虛擬網路（VNet）上使用資料來源的閘道](analysis-services-vnet-gateway.md)。

**問**-我的來源資料庫位於防火牆後方。 如何設定防火牆以允許我的 Analysis Services 伺服器存取它？   
**答**： Azure Analysis Services 不會使用固定的 IP 位址或服務標記。 Analysis Services 伺服器所使用的 IP 位址範圍，可以是*Azure 區域*的 ip 位址範圍內的任何專案。 您必須在源資料庫防火牆規則中，為伺服器的 Azure 區域提供*完整範圍*的 IP 位址。 另一個可能更安全的方法是設定內部部署資料閘道。 接著，您可以使用[AlwaysUseGateway 伺服器屬性](analysis-services-vnet-gateway.md#configure-alwaysusegateway-property)來設定您的 Analysis Services 伺服器，然後確保內部部署資料閘道具有資料來源的防火牆規則所允許的 IP 位址。

## <a name="azure-apps-with-ip-address"></a>具有 IP 位址的 Azure 應用程式

**問**-我使用以 Azure 為基礎的應用程式（例如，Azure Functions、Azure Data Factory）與動態變更的 IP 位址。 如何管理 Azure Analysis Services 防火牆規則，以動態方式允許我的應用程式執行所在的 IP 位址？   
**答**： Azure Analysis Services 不支援私人連結、Vnet 或服務標記。 有一些開放原始碼解決方案（例如，會偵測 https://github.com/mathwro/Scripts/blob/master/Azure/AllowAzure-AnalysisServer.ps1) 用戶端應用程式的 IP 位址，並自動和暫時更新防火牆規則）。


## <a name="next-steps"></a>後續步驟

[安裝及設定內部部署資料閘道](analysis-services-gateway-install.md)   
[使用內部部署資料閘道連接到內部部署資料來源](analysis-services-gateway.md)   
[使用 Azure 虛擬網路 (VNet) 上的資料來源閘道](analysis-services-vnet-gateway.md)
