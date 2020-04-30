---
title: 自我裝載閘道總覽 |Microsoft Docs
description: 瞭解 Azure API 管理的自我裝載閘道功能如何協助組織管理混合式和多重雲端環境中的 Api。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232967"
---
# <a name="self-hosted-gateway-overview"></a>自我裝載閘道概觀

本文說明 Azure API 管理的自我裝載閘道功能如何啟用混合式和多雲端 API 管理、呈現其高階架構，以及強調其功能。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合式和多雲端 API 管理

自我裝載閘道功能可擴充混合式和多重雲端環境的 API 管理支援，並可讓組織有效率地管理裝載于內部部署的 Api，以及從 Azure 中的單一 API 管理服務跨雲端的應用程式。

透過自我裝載閘道，客戶可以彈性地將 API 管理閘道元件的容器化版本部署到其主控其 Api 的相同環境。 所有自我裝載的閘道都是由其同盟的 API 管理服務所管理，因此可為客戶提供所有內部和外部 Api 的可見度和統一管理體驗。 將閘道放在 Api 附近可讓客戶優化 API 流量流程，並解決安全性和合規性需求。

每個 API 管理服務都是由下列主要元件所組成：

-   以 API 形式公開的管理平面，用來透過 Azure 入口網站、PowerShell 和其他支援的機制來設定服務。
-   閘道（或資料平面）負責代理 API 要求、套用原則和收集遙測
-   開發人員用來探索、學習及上架以使用 Api 的開發人員入口網站

根據預設，所有這些元件都會部署在 Azure 中，導致所有的 API 流量（在下圖中顯示為實心的黑色箭號）流經 Azure，而不論裝載後端 Api 的位置為何。 此模型的作業簡單，會以增加的延遲、合規性問題，以及在某些情況下額外的資料傳輸費用為代價。

![沒有自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/without-gateways.png)

將自我裝載閘道部署到裝載後端 API 裝載的相同環境中，可讓 API 流量直接流向後端 Api，這樣可改善延遲、優化資料傳輸成本，並啟用合規性，同時保有組織內所有 Api 的單一管理、可檢視性和探索優勢，而不論其裝載位置為何。

![使用自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>封裝和功能

自我裝載閘道是一種容器化、功能相同的受控閘道版本，可在每個 API 管理服務中部署至 Azure。 自我裝載閘道是以 Linux 為基礎的 Docker[容器](https://aka.ms/apim/sputnik/dhub)，從 Microsoft container Registry 提供。 它可以部署到 Docker、Kubernetes 或任何其他在內部部署的伺服器叢集上執行的容器協調流程解決方案、雲端基礎結構，或用於個人電腦上的評估和開發用途。

在受控閘道上找到的下列功能無法在自我裝載閘道上**使用**：

- Azure 監視器記錄
- 上游（後端） TLS 版本和密碼管理
- 使用上傳至 API 管理服務的[CA 根憑證](api-management-howto-ca-certificates.md)來驗證服務器和用戶端憑證。 若要新增自訂 CA 的支援，請將圖層新增至自我裝載閘道容器映射以安裝 CA 的根憑證。
- 與[Service Fabric](../service-fabric/service-fabric-api-management-overview.md)整合
- TLS 工作階段繼續
- 用戶端憑證重新協商。 這表示，若要讓[用戶端憑證驗證](api-management-howto-mutual-certificates-for-clients.md)工作 API 取用者，必須將其憑證呈現為初始 TLS 信號交換的一部分。 若要確保，請在設定自我裝載閘道自訂主機名稱時，啟用 [協商用戶端憑證] 設定。
- 內建快取。 請參閱這[份檔](api-management-howto-cache-external.md)，以瞭解如何在自我裝載閘道中使用外部快取。

## <a name="connectivity-to-azure"></a>Azure 的連線能力

自我裝載閘道需要端口443上的輸出 TCP/IP 連線能力。 每個自我裝載閘道都必須與單一 API 管理服務相關聯，並透過其管理平面進行設定。 自我裝載閘道會使用 Azure 的連線能力來：

-   每分鐘傳送一則訊息，以報告其狀態
-   定期檢查（每隔10秒），並在每次有應用程式可用時套用設定更新
-   將要求記錄和計量傳送至 Azure 監視器（若已設定）
-   將事件傳送至 Application Insights，如果設定為這樣做

當 Azure 的連線中斷時，自我裝載閘道將無法接收設定更新、回報其狀態或上傳遙測。

自我裝載閘道的設計是「失敗的靜態」，而且可以暫時失去與 Azure 的連線能力。 您可以使用或不進行本機設定備份來部署它。 在先前的案例中，自我裝載閘道會定期將最新下載設定的備份複本儲存在附加至其容器或 pod 的持續性磁片區上。

當設定備份已關閉，且與 Azure 的連線中斷時：

-   執行自我裝載的閘道將繼續使用設定的記憶體中複本來運作
-   已停止的自我裝載閘道將無法啟動

當設定備份已開啟，且與 Azure 的連線中斷時：

-   執行自我裝載的閘道將繼續使用設定的記憶體中複本來運作
-   已停止的自我裝載閘道將能夠開始使用設定的備份複本

當連線恢復時，受到中斷影響的每個自我裝載閘道都會自動重新連接其相關聯的 API 管理服務，並下載閘道為「離線」時所發生的所有設定更新。

## <a name="next-steps"></a>後續步驟

-   [閱讀本主題的其他背景白皮書](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [將自我裝載閘道部署至 Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [將自我裝載閘道部署至 Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
