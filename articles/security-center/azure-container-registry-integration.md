---
title: Azure 安全中心和 Azure 容器註冊表
description: 瞭解 Azure 安全中心與 Azure 容器註冊表的集成
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/19/2019
ms.author: memildin
ms.openlocfilehash: 069ce6ca1e76a9bd954031708702c973387abbaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78205988"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure 容器註冊表與安全中心的集成

Azure 容器註冊表 （ACR） 是一種託管的專用 Docker 註冊表服務，用於在中央註冊表中存儲和管理 Azure 部署的容器映射。 它基於開源 Docker 註冊表 2.0。

如果您位於 Azure 安全中心的標準層，則可以添加容器註冊表捆綁包。 此可選功能可更深入地瞭解基於 ARM 的註冊表中圖像的漏洞。 在訂閱級別啟用或禁用捆綁包，以涵蓋訂閱中的所有註冊表。 此功能按圖像收費，如[定價頁上](security-center-pricing.md)所示。 啟用容器註冊表包，可確保安全中心準備好掃描推送到註冊表的圖像。 

每當圖像推送到註冊表時，安全中心會自動掃描該圖像。 要觸發映射的掃描，請將其推送到存儲庫。

掃描完成後（通常在大約 10 分鐘後），安全中心建議中提供了調查結果，如下所示：

[![有關 Azure 容器註冊表 （ACR） 託管映射中發現的漏洞的 Azure 安全中心建議示例](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>整合的優點

安全中心在訂閱中標識基於 ARM 的 ACR 註冊表，並無縫提供：

* 針對所有推送的 Linux 映射的**Azure 本機漏洞掃描**。 安全中心使用業界領先的漏洞掃描供應商 Qualys 的掃描器掃描圖像。 預設情況下，此本機解決方案是無縫集成的。

* 具有已知漏洞的 Linux 映射**的安全建議**。 安全中心提供每個報告漏洞的詳細資訊和嚴重性分類。 此外，它還提供了如何修復推送到註冊表的每個映射上發現的特定漏洞的指導。

![Azure 安全中心和 Azure 容器註冊表 （ACR） 高級概述](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>後續步驟

要瞭解有關安全中心的容器安全功能的更多，請參閱：

* [Azure 安全中心和容器安全性](container-security.md)

* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)

* [虛擬機器保護](security-center-virtual-machine-protection.md)- 描述安全中心的建議
