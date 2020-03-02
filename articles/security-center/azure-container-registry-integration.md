---
title: Azure 資訊安全中心和 Azure Container Registry
description: 瞭解 Azure 資訊安全中心與 Azure Container Registry 的整合
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
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/01/2020
ms.locfileid: "78205988"
---
# <a name="azure-container-registry-integration-with-security-center"></a>Azure Container Registry 與資訊安全中心整合

Azure Container Registry （ACR）是受控的私用 Docker Registry 服務，可在中央登錄中儲存及管理 Azure 部署的容器映射。 它是以開放原始碼的 Docker Registry 2.0 為基礎。

如果您是在 Azure 資訊安全中心的標準層，您可以新增容器登錄套件組合。 這項選擇性功能可讓您更深入瞭解 ARM 型登錄中的映射弱點。 啟用或停用訂用帳戶層級的配套，以涵蓋訂用帳戶中的所有登錄。 這項功能是按映射收費，如[定價頁面](security-center-pricing.md)所示。 啟用容器登錄庫組合，可確保資訊安全中心已準備好掃描已推送至登錄的映射。 

當映射推送至您的登錄時，資訊安全中心會自動掃描該映射。 若要觸發映射掃描，請將其推送至您的存放庫。

當掃描完成時（通常在大約10分鐘之後），資訊安全中心建議中會提供結果，如下所示：

[![範例 Azure 資訊安全中心有關在 Azure Container Registry （ACR）主控映射中探索到的弱點的建議](media/azure-container-registry-integration/container-security-acr-page.png)](media/azure-container-registry-integration/container-security-acr-page.png#lightbox)

## <a name="benefits-of-integration"></a>整合的優點

資訊安全中心可識別您訂用帳戶中以 ARM 為基礎的 ACR 登錄，並順暢地提供：

* **Azure-原生弱點掃描**所有已推送的 Linux 映射。 資訊安全中心使用領先業界的弱點掃描廠商 Qualys 掃描影像。 這個原生解決方案預設會緊密整合。

* 具有已知弱點之 Linux 映射的**安全性建議**。 資訊安全中心提供每個回報的弱點和嚴重性分類的詳細資料。 此外，它也提供指引，說明如何補救每個推送至登錄的映射上找到的特定弱點。

![Azure 資訊安全中心和 Azure Container Registry （ACR）高階總覽](./media/azure-container-registry-integration/aks-acr-integration-detailed.png)

## <a name="next-steps"></a>後續步驟

若要深入瞭解資訊安全中心的容器安全性功能，請參閱：

* [Azure 資訊安全中心和容器安全性](container-security.md)

* [與 Azure Kubernetes Service 整合](azure-kubernetes-service-integration.md)

* [虛擬機器保護](security-center-virtual-machine-protection.md)-描述資訊安全中心的建議
