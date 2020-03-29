---
title: Azure 資訊安全中心的容器建議 | Microsoft Docs
description: 本文件說明 Azure 資訊安全中心建議如何協助保護您的容器。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912355"
---
# <a name="understand-azure-security-center-container-recommendations"></a>了解 Azure 資訊安全中心容器建議

當您在生產環境中移轉您的單體應用程式，以執行關鍵任務、容器化的雲端原生應用程式時，您可以充分運用容器的功能，包括簡單又快速的部署和更新功能。 隨著部署的容器數目持續增加，安全性解決方案必須準備就緒，才能讓您看見容器的安全性狀態，並協助保護容器抵禦威脅。

Azure 資訊安全中心提供下列功能協助您保護您的容器：

- **可看見在 IaaS Linux 電腦上裝載的容器**<br>在 Azure 資訊安全中心，[容器] 索引標籤會顯示透過 Docker 部署的所有虛擬機器。 在虛擬機器上探索安全性問題時，資訊安全中心會提供與電腦上容器相關的其他資訊，例如 Docker 版本及在主機上執行的映像數目。

    ![容器索引標籤](./media/security-center-container-recommendations/docker-recommendation.png)


- **根據 Docker 的 CIS 基準提出安全性建議**<br>資訊安全中心會掃描您的 Docker 組態，並透過提供一份所有經評估為失敗規則的清單，讓您能夠看見錯誤的組態。 資訊安全中心提供指導方針協助您快速解決這些問題，並節省時間。 資訊安全中心會持續評估 Docker 設定，並提供給您其最新狀態。

    ![容器索引標籤](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **即時容器威脅保護**<br> 安全中心為具有 AuditD 元件的 Linux 電腦上容器提供即時威脅保護。 警示會找出數個可疑的 Docker 活動，例如在主機上建立特殊權限的容器、在 Docker 容器內執行安全殼層 (SSH) 伺服器的跡象，或使用密碼編譯採礦程式。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

    ![容器索引標籤](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>建議
使用下表作為參考，協助您了解裝載於 IaaS Linux 機器上的可用容器，以及 Docker 設定的安全性評估。

| 建議 | 描述 | 補救 |
| --- | --- | --- |
|補救容器安全性設定中的弱點 |根據設定最佳做法，補救容器安全性設定中的弱點。| 補救容器安全性設定中的弱點：<br>1. 查看失敗規則的清單。<br>2. 根據指定的說明修復每個規則。|


## <a name="next-steps"></a>後續步驟
若要深入了解適用於其他 Azure 資源類型的建議，請參閱下列文章︰

* [在 Azure 資訊安全中心監視身分識別和存取](security-center-identity-access.md)
* [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
* [保護 Azure 資訊安全中心內的 Azure SQL 服務](security-center-sql-service-recommendations.md)

如要深入了解資訊安全中心，請參閱下列主題：

* [保護 Azure 資訊安全中心內的機器和應用程式](security-center-virtual-machine-protection.md)
* [在 Azure 安全中心設置安全性原則](tutorial-security-policy.md)-- 瞭解如何為 Azure 訂閱和資源組配置安全性原則。
* [管理和回應 Azure 安全中心中的安全警報](security-center-managing-and-responding-alerts.md)-- 瞭解如何管理和回應安全警報。