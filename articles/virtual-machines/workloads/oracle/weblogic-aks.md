---
title: 什麼是在 Azure Kubernetes Service 上執行 Oracle WebLogic Server 的解決方案
description: 瞭解如何在 Azure Kubernetes Service 上執行 Oracle WebLogic Server。
author: rezar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 10/28/2020
ms.author: rezar
ms.reviewer: cynthn
ms.openlocfilehash: a57d5a00fad9a17e01b96ebdf395fb7a4b857935
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968684"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>在 Azure Kubernetes Service 上執行 Oracle WebLogic Server 的解決方案有哪些？

本頁面說明在 Azure Kubernetes Service (AKS) 上執行 Oracle WebLogic Server (WLS) 的解決方案。 這些解決方案是由 Oracle 和 Microsoft 共同開發和支援的。

您也可以在 Azure 虛擬機器上執行 WebLogic 伺服器。 這項作業的解決方案將在 [這篇 Microsoft 文章](/azure/virtual-machines/workloads/oracle/oracle-weblogic)中說明。

WebLogic Server 是一部領先的 JAVA 應用程式伺服器，在全球各地執行一些最重要的關鍵企業 JAVA 應用程式。 WebLogic Server 形成 Oracle software suite 的中介軟體基礎。 Oracle 和 Microsoft 致力於讓 WebLogic 伺服器客戶享有選擇和彈性，以便在 Azure 上以領先的雲端平臺執行工作負載。

## <a name="wls-on-aks-certified-and-supported"></a>WLS on AKS 認證與支援
WebLogic Server 已通過 Oracle 和 Microsoft 認證，可在 AKS 上正常執行。 AKS 解決方案上的 WebLogic 伺服器旨在讓您盡可能輕鬆地在 Docker 和 Kubernetes 基礎結構上執行您的容器化和協調 JAVA EE 應用程式。 這些解決方案著重于可靠性、擴充性、管理性和企業支援。

WebLogic 伺服器叢集已完全啟用，可透過 WebLogic Kubernetes 運算子在 Kubernetes 上執行， (稱為「運算子」，這是) 。 運算子遵循標準 Kubernetes 運算子模式。 它藉由將手動工作自動化，並新增額外的作業可靠性功能，來簡化 Kubernetes 上 WebLogic 網域和部署的管理和作業。 操作員支援 Oracle WebLogic Server 12c、Oracle 融合中介軟體基礎結構12c。 我們已使用操作員測試了 WebLogic Server 12.2.1.3 和12.2.1.4 的官方 Docker 映射。 如需操作員的詳細資訊，請參閱 [Oracle 的官方檔](https://oracle.github.io/weblogic-kubernetes-operator/)。

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>AKS 上適用于 WLS 的指引、腳本和範例
除了在 AKS、Oracle 和 Microsoft 上認證 WebLogic 伺服器之外，還提供詳細的指示、腳本，以及在 AKS 上執行 WebLogic Server 的範例。 本指南已併入 [操作員檔](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)的 Azure Kubernetes Service 範例一節中。 本指南的目標是讓生產 WebLogic 伺服器在 AKS 部署上盡可能簡單。 本指南使用 Oracle 所提供的官方 WebLogic Server Docker 映射。 您可以透過 Kubernetes 的持續性磁片區宣告，透過存取 Azure 檔案儲存體來達成容錯移轉。 使用類型為 ' LoadBalancer ' 的 Kubernetes 服務布建時，支援 Azure 負載平衡器。 指導方針可進行高度的設定和自訂。

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="您可以使用範例腳本在 AKS 上部署 WebLogic 伺服器":::

本指南目前假設您是在 Docker 映射外部部署網域，並使用來自 Oracle 的標準 Docker 映射。 我們會新增指導方針，以在 Docker 映射中使用您的網域來啟用自訂映射。 未來可以透過 Azure 虛擬機器解決方案上的 Marketplace 供應專案鏡像 Oracle WebLogic Server，更容易使用和 Azure 服務整合。

_這些解決方案都是攜帶您自己的授權_。 他們假設您已經使用 Oracle 取得適當的授權，並已獲得適當授權，可在 Azure 中執行供應專案。

_如果您想要與開發這些解決方案的工程團隊密切合作來處理您的遷移案例，請填寫 [這份簡短問卷](https://aka.ms/wls-on-azure-survey) ，並納入您的連絡人資訊_。 計畫經理、架構設計人員和工程師會馬上接觸您，並開始密切合作。 在遷移案例中共同作業的機會是免費的，而解決方案則是主動初始開發。

## <a name="deployment-architectures"></a>部署架構

在 Azure Kubernetes Service 上執行 Oracle WebLogic Server 的解決方案，可讓您以相對簡單的方式啟用各式各樣的生產環境就緒部署架構。

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="AKS 上已啟用複雜的 WebLogic 伺服器部署":::

除了解決方案提供的客戶之外，還能提供更大的彈性來進一步自訂部署。 可能是在部署應用程式的最上層，客戶會將進一步的 Azure 資源與其部署整合。 建議客戶在問卷中提供意見反應，以進一步改善解決方案。

## <a name="next-steps"></a>後續步驟

探索 Azure Kubernetes Service 上執行的 Oracle WebLogic Server。

> [!div class="nextstepaction"]
> [在 AKS 上執行 WLS 的指引、腳本和範例](https://oracle.github.io/weblogic-kubernetes-operator/samples/simple/azure-kubernetes-service/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes 運算子](https://oracle.github.io/weblogic-kubernetes-operator/)
