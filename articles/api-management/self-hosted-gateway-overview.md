---
title: 自我裝載的 Azure API 管理閘道總覽 |Microsoft Docs
description: 瞭解自我裝載的 Azure API 管理閘道如何協助組織管理混合式和多重雲端環境中的 Api。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "73513714"
---
# <a name="self-hosted-api-management-gateway-overview"></a>自我裝載 API 管理閘道總覽

本文說明自我裝載閘道功能如何啟用混合式和多重雲端 API 管理、呈現其高階架構，以及強調其基本功能。

> [!NOTE]
> 自我裝載閘道功能目前為預覽狀態。 在預覽期間，自我裝載閘道僅適用于開發人員和進階層，不需要額外付費。 開發人員層僅限於單一自我裝載的閘道部署。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合式和多雲端 API 管理

自我裝載閘道功能可擴充混合式和多重雲端環境的 API 管理支援，並可讓組織有效率地管理裝載于內部部署的 Api，以及從 Azure 中的單一 API 管理服務跨雲端的應用程式。

透過自我裝載閘道，客戶可以彈性地將 API 管理閘道元件的容器化版本部署到其主控其 Api 的相同環境。 所有自我裝載的閘道都是由其同盟的 API 管理服務所管理，因此可為客戶提供所有內部和外部 Api 的可見度和統一管理體驗。 將閘道放在 Api 附近可讓客戶優化 API 流量流程，並解決安全性和合規性需求。

每個 API 管理服務都是由下列主要元件所組成：

-   以 API 形式公開的管理平面，用來透過 Azure 入口網站、PowerShell 和其他支援的機制來設定服務。
-   閘道（或資料平面）負責代理 API 要求、套用原則和收集遙測
-   開發人員用來探索、學習及上架以使用 Api 的開發人員入口網站

根據預設，所有這些元件都會部署在 Azure 中，導致所有的 API 流量（在下圖中顯示為實心的黑色箭號）流經 Azure，而不論裝載後端 Api 的位置為何。 此模型的作業簡單，會以增加的延遲、合規性問題，以及在某些情況下額外的資料傳輸費用為代價。

![沒有自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/without-gateways.png)

將自我裝載閘道部署到與後端 API 整合相同的環境中，並將其新增至 API 管理服務，可讓 API 流量直接流向後端 Api，這樣可改善延遲、優化資料傳輸成本，並啟用合規性，同時保有組織內所有 Api 的單一管理和探索優勢，不論其裝載的位置為何。

![使用自我裝載閘道的 API 流量流程](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>封裝和功能

自我裝載閘道是一種容器化、功能相同的受控閘道版本，可在每個 API 管理服務中部署至 Azure。 自我裝載閘道是以 Linux 為基礎的 Docker 容器，從 Microsoft Container Registry 提供。 它可以部署到 Docker、Kubernetes 或任何其他在桌上型電腦、伺服器叢集或雲端基礎結構上執行的容器協調流程解決方案。

> [!IMPORTANT]
> 受管理閘道中提供的某些功能尚未提供預覽。 最值得注意的是：記錄到事件中樞原則，Service Fabric 整合，下游 HTTP/2。 沒有計劃可以讓內建快取在自我裝載閘道中提供使用。

## <a name="connectivity-to-azure"></a>Azure 的連線能力

自我裝載閘道需要端口443上對 Azure 的輸出 TCP/IP 連線能力。 每個自我裝載閘道都必須與單一 API 管理服務相關聯，並透過其管理平面進行設定。 自我裝載閘道會使用 Azure 的連線能力來：

-   每分鐘傳送一則訊息，以報告其狀態
-   定期檢查（每隔10秒），並在每次有應用程式可用時套用設定更新
-   將要求記錄和計量傳送至 Azure 監視器（若已設定）
-   將事件傳送至 Application Insights，如果設定為這樣做

當 Azure 的連線中斷時，自我裝載閘道將無法接收設定更新、回報其狀態或上傳遙測。

自我裝載閘道的設計是「失敗的靜態」，並可在暫時中斷 Azure 連線時存留。 您可以使用或不開啟本機設定備份來部署它。 在先前的案例中，自我裝載閘道會定期將設定的備份複本儲存在附加至容器或 pod 的持續性磁片區上。

當設定備份已關閉，且與 Azure 的連線中斷時：

-   執行的自我裝載閘道將繼續使用設定的記憶體中複本來運作
-   已停止的自我裝載閘道將無法啟動

當設定備份已開啟，且與 Azure 的連線中斷時：

-   執行的自我裝載閘道將繼續使用設定的記憶體中複本來運作
-   已停止的自我裝載閘道將會開始使用設定的備份複本

當連線恢復時，受到中斷影響的每個自我裝載閘道都會自動重新連接其相關聯的 API 管理服務，並下載閘道為「離線」時所發生的所有設定更新。

## <a name="next-steps"></a>後續步驟

-   [閱讀本主題的其他背景白皮書](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [將自我裝載閘道部署至 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [將自我裝載閘道部署至 Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
