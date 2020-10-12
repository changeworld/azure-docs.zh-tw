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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82232967"
---
# <a name="self-hosted-gateway-overview"></a>自我裝載閘道概觀

本文說明 Azure API 管理的自我裝載閘道功能如何啟用混合式和多重雲端 API 管理、呈現其高階架構，以及醒目顯示其功能。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合式和多重雲端 API 管理

自我裝載閘道功能擴充了混合式和多重雲端環境的 API 管理支援，並可讓組織在 Azure 中的單一 API 管理服務中，有效率且安全地管理裝載于內部部署和跨雲端的 Api。

透過自我裝載的閘道，客戶可以彈性地將容器化版本的 API 管理閘道元件部署至裝載其 Api 的相同環境。 所有自我裝載的閘道都是由其所同盟的 API 管理服務所管理，藉此提供客戶跨所有內部和外部 Api 的可見度和統一管理體驗。 將閘道放在 Api 附近可讓客戶將 API 流量優化，並解決安全性和合規性需求。

每個 API 管理服務都是由下列重要元件所組成：

-   公開為 API 的管理平面，用來透過 Azure 入口網站、PowerShell 和其他支援的機制設定服務。
-   閘道 (或資料平面) 負責對 API 要求進行 proxy 處理、套用原則，以及收集遙測
-   開發人員入口網站，可供開發人員用來探索、學習和上架以使用 Api

根據預設，所有這些元件都會部署在 Azure 中，導致所有 API 流量 (在) 下圖中顯示為實心黑色箭號，而不論裝載 Api 的後端是在何處執行。 此模型的作業簡易性是增加延遲、合規性問題，以及在某些情況下，額外的資料傳輸費用的成本。

![沒有自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/without-gateways.png)

將自我裝載閘道部署至裝載後端 API 裝載的相同環境中，可讓 API 流量直接流向後端 Api，進而改善延遲、將資料傳輸成本優化，以及啟用合規性，同時保有在組織內的所有 Api 的管理、可檢視性和探索的優點，而不論其裝載位置為何。

![使用自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>封裝和功能

自我裝載閘道是一種容器化、功能相當的受控閘道版本，部署至 Azure 作為每個 API 管理服務的一部分。 自我裝載閘道可從 Microsoft Container Registry 作為以 Linux 為基礎的 Docker [容器](https://aka.ms/apim/sputnik/dhub) 。 您可以將其部署到 Docker、Kubernetes 或任何其他在內部部署、雲端基礎結構上的伺服器叢集上執行的容器協調流程解決方案，或部署至個人電腦上的評估和開發用途。

在受控閘道中的下列功能無法在自我裝載的閘道中 **使用** ：

- Azure 監視器記錄
- 上游 (後端) TLS 版本和密碼管理
- 使用上傳至 API 管理服務的 [CA 根憑證](api-management-howto-ca-certificates.md) 來驗證服務器和用戶端憑證。 若要新增自訂 CA 的支援，請將圖層新增至自我裝載的閘道容器映射，以安裝 CA 的根憑證。
- 與[Service Fabric](../service-fabric/service-fabric-api-management-overview.md)整合
- TLS 工作階段繼續
- 重新協商用戶端憑證。 這表示，針對工作 API 取用者的 [用戶端憑證驗證](api-management-howto-mutual-certificates-for-clients.md) ，必須在初始 TLS 信號交換過程中出示其憑證。 若要確定，請在設定自我裝載閘道自訂主機名稱時，啟用 negotiate 用戶端憑證設定。
- 內建快取。 請參閱這 [份檔](api-management-howto-cache-external.md) ，以瞭解如何在自我裝載閘道中使用外部快取。

## <a name="connectivity-to-azure"></a>Azure 的連線能力

自我裝載閘道需要在埠443上對 Azure 進行輸出 TCP/IP 連線。 每個自我裝載的閘道都必須與單一 API 管理服務相關聯，而且是透過其管理平面進行設定。 自我裝載閘道會使用與 Azure 的連線能力：

-   每分鐘傳送一次信號訊息來報告其狀態
-   每隔10秒定期檢查一次 () ，並在可用時套用設定更新
-   將要求記錄和計量傳送至 Azure 監視器（如果已設定）
-   將事件傳送至 Application Insights （如果設定的話）

當 Azure 的連線中斷時，自我裝載的閘道將無法接收設定更新、回報其狀態或上傳遙測。

自我裝載閘道的設計目的是「失敗的靜態」，而且可能會暫時失去與 Azure 的連線能力。 您可以使用或不使用本機設定備份來部署它。 在先前的案例中，自我裝載的閘道會定期將最新下載設定的備份複本，儲存在附加至其容器或 pod 的永久磁片區上。

關閉設定備份並中斷與 Azure 的連線時：

-   執行自我裝載的閘道將繼續使用設定的記憶體中複本來運作
-   停止的自我裝載閘道將無法啟動

當設定備份已開啟且 Azure 的連線中斷時：

-   執行自我裝載的閘道將繼續使用設定的記憶體中複本來運作
-   已停止的自我裝載閘道將能夠使用設定的備份副本來開始

當連線恢復時，受中斷影響的每個自我裝載閘道都會自動重新連線至其相關聯的 API 管理服務，並下載閘道「離線」時所發生的所有設定更新。

## <a name="next-steps"></a>接下來的步驟

-   [閱讀本主題的其他背景白皮書](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [將自我裝載閘道部署至 Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [將自我裝載閘道部署至 Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
