---
title: App Service 環境總覽
description: App Service 環境總覽
author: ccompy
ms.assetid: 3d37f007-d6f2-4e47-8e26-b844e47ee919
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: fbc498fcd654d16936c2548528e2600be68a2ad9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663504"
---
# <a name="app-service-environment-overview"></a>App Service 環境總覽 

> [!NOTE]
> 本文說明 App Service 環境 v3 (preview) 
> 

Azure App Service Environment 是 Azure App Service 的功能，可提供完全隔離和專用的環境，以便安全地大規模執行 App Service 應用程式。 此功能可以裝載您的：

- Windows Web 應用程式
- Linux Web 應用程式
- Docker 容器
- 函式

App Service Environment (ASE) 適合需要下列項目的應用程式工作負載：

- 高規模。
- 隔離和安全的網路存取。
- 高記憶體使用率。
- 每秒的高要求 (RPS) 。 您可以在單一 Azure 區域或跨多個 Azure 區域中進行多個 Ase。 這種彈性讓 Ase 適用于水準調整無狀態應用程式，且具有高 RPS 需求。

ASE 的主機應用程式只來自一個客戶，在其中一個 Vnet。 客戶可以精確控制輸入和輸出的應用程式網路流量。 應用程式可以透過 VPN 建立內部部署公司資源的高速安全連線。

ASEv3 隨附自己的定價層（獨立模式 V2）。
App Service 環境 v3 提供一個周圍的保護，以保護您網路子網中的應用程式，並提供您自己的 Azure App Service 私用部署。
您可以使用多個 ASE 進行水平調整。 在 ASE 中執行之應用程式的存取權可能會受到 Web 應用程式防火牆 (WAF) 等上游裝置的管制。 如需詳細資訊，請參閱 Web 應用程式防火牆 (WAF)。

## <a name="usage-scenarios"></a>使用方式情節

App Service 環境有許多使用案例，包括：

- 內部企業營運應用程式
- 需要超過30個 ASP 實例的應用程式
- 單一租使用者系統來滿足內部合規性或安全性需求
- 網路隔離應用程式裝載
- 多層式應用程式

有幾個網路功能可讓多租使用者 App Service 中的應用程式連線到網路隔離的資源，或變成網路隔離。 這些功能會在應用層級啟用。  使用 ASE 時，應用程式上不會有其他設定可在 VNet 中進行。 應用程式會部署到已存在於 VNet 中的網路隔離環境。 在裝載網路隔離應用程式的 ASE 之上，它也是單一租使用者系統。 沒有其他使用 ASE 的客戶。 如果您真的需要完整的隔離案例，也可以讓您的 ASE 部署到專用硬體上。 在網路隔離應用程式裝載、單一租用和能力之間 

## <a name="dedicated-environment"></a>專用的環境
ASE 專門用於單一訂用帳戶，而且可以裝載 200 App Service 方案實例。 不管是單一 App Service 方案中的 100 個執行個體或 100 個單一執行個體的 App Service 方案，只要加總之執行個體數在 100 以下皆可。

ASE 是由前端和背景工作角色所組成。 前端負責處理 HTTP/HTTPS 終止和 ASE 中應用程式要求的自動負載平衡。 前端會隨 ASE 中的 App Service 方案相應放大而自動新增。

背景工作角色是裝載客戶應用程式的角色。 背景工作角色可以三個固定的大小提供：

- 兩個 vCPU/8 GB RAM
- 四 vCPU/16 GB RAM
- 八 vCPU/32 GB RAM

客戶不需要管理前端和背景工作角色。 所有基礎結構都會自動進行。 隨著 App Service 方案建立或相應縮小 ASE，會視需要將基礎結構新增或移除。

隔離 V2 App Service 計畫實例需要付費。 如果您的 ASE 中沒有任何 App Service 計畫，您的計費方式就如同您有一個 App Service 的方案，其中包含兩個核心工作者的一個實例。

## <a name="virtual-network-support"></a>虛擬網路支援
ASE 功能是將 Azure App Service 直接部署到客戶 Azure Resource Manager 虛擬網路的部署。 ASE 一律存在於虛擬網路的子網中。 您可以使用虛擬網路的安全性功能控制應用程式的輸入和輸出網路通訊。

網路安全性群組會將輸入網路通訊限定於 ASE 所在的子網路。 您可以使用 NSG 在上游裝置和服務 (例如 WAF 和網路 SaaS 提供者) 背後執行應用程式。

應用程式也經常需要存取公司資源，例如內部資料庫和 Web 服務。 如果您在具有內部部署網路 VPN 連線的虛擬網路中部署 ASE，ASE 中的應用程式便可以存取內部部署資源。 無論 VPN 是站對站或 Azure ExpressRoute VPN，此功能都可適用。

## <a name="preview"></a>預覽
App Service 環境 v3 處於公開預覽狀態。  在預覽階段中，會新增一些功能。 ASEv3 目前的限制包括：

- 無法調整超過五個實例的 App Service 方案
- 無法從私人登錄取得容器
- 目前無法透過客戶 VNet 進行不支援的 App Service 功能
- 沒有具有網際網路可存取端點的外部部署模型
-  (AZ CLI 和 PowerShell) 不支援命令列
- 沒有從 ASEv2 到 ASEv3 的升級功能
- 沒有 FTP 支援
- 不支援透過客戶 VNet 進行某些 App Service 功能。 備份/還原、使用私人容器登錄的應用程式設定中的 Key Vault 參考，以及儲存體的診斷記錄將無法與服務端點或私人端點搭配運作
    
### <a name="asev3-preview-architecture"></a>ASEv3 預覽架構
在 ASEv3 preview 中，ASE 將使用私人端點來支援輸入流量。 私人端點將會由 GA 取代為負載平衡器。 在預覽期間，ASE 不會有網際網路可存取端點的內建支援。 您可以新增應用程式閘道來進行這類用途。 ASE 需要兩個子網中的資源。  輸入流量會流經私人端點。 私人端點可以放在任何子網中，只要它具有可供私人端點使用的可用位址即可。  輸出子網必須是空的，並委派給 Microsoft. Web/hostingEnvironments。 ASE 使用時，輸出子網不能用於任何其他用途。

使用 ASEv3，ASE 子網上沒有任何輸入或輸出網路需求。 您可以使用網路安全性群組和路由表來控制流量，它只會影響您的應用程式流量。 請勿刪除與您的 ASE 相關聯的私人端點，因為該動作無法復原。 Ase 所使用的私人端點可用於 ASE 中的所有應用程式。 
