---
title: Azure 資訊安全中心建議完整參考資料表
description: 本文列出 Azure 資訊安全中心的安全性建議，可協助您強化及保護您的資源。
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 01/26/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: f6d881036b0ebaa79d1052e41b8ec649bb1e8d6d
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797163"
---
# <a name="security-recommendations---a-reference-guide"></a>安全性建議 - 參考指南

本文列出您可能會在 Azure 資訊安全中心看到的建議。 在您的環境中顯示的建議，取決於您要保護的資源和自訂設定。

資訊安全中心的建議是以 [Azure 安全性基準測試](../security/benchmarks/introduction.md)為基礎。 Azure 安全性效能評定是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的一組 Azure 特定指導方針。 這項廣泛的基準測試建基於 [網際網路安全性 (CIS) ](https://www.cisecurity.org/benchmark/azure/) 的控制，以及美國的 [標準與技術局 (NIST) ](https://www.nist.gov/) ，並著重于以雲端為中心的安全性。

若要了解如何回應這些建議，請參閱 [Azure 資訊安全中心的補救建議](security-center-remediate-recommendations.md)。

您的安全分數是根據您已完成的資訊安全中心建議數目。 若要決定要先解決的建議，請查看每個建議的嚴重性，以及它對您的安全分數可能會有的影響。

> [!TIP]
> 如果建議的描述顯示「無相關原則」，通常是因為該建議取決於其他建議及其原則。 例如「應補救失敗的端點保護健康情況...」這項建議，是取決於另一項負責檢查是否已安裝端點保護解決方案的建議 (「應安裝端點保護解決方案...」)。 基礎建議則確實具備原則。
> 將原則限制為只有基本建議，可簡化原則管理。

## <a name="compute-recommendations"></a><a name='recs-compute'></a>計算建議

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>資料建議

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess 建議

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>網路功能的建議

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>遭取代的建議

|建議|描述與相關原則|Severity|是否啟用快速修正？([深入了解](security-center-remediate-recommendations.md#quick-fix-remediation))|資源類型|
|----|----|----|----|----|
|**應限制對應用程式服務的存取**|藉由變更網路設定，限制對應用程式服務的存取，以拒絕範圍太廣的輸入流量。<br>(相關原則：[預覽]：應限制對應用程式服務的存取)|高|N|App Service|
|**應強化 IaaS NSG 上 Web 應用程式的規則**|若在您的虛擬機器上，執行的 Web 應用程式 NSG 規則就 Web 應用程式連接埠來說過於寬鬆，請強化虛擬機器的網路安全性群組 (NSG)。<br>(相關原則：應在 IaaS 上強化 Web 應用程式的 NSG 規則)|高|N|虛擬機器|
|**應定義 Pod 安全性原則，藉由移除不必要的應用程式權限，減少攻擊媒介 (預覽)**|移除不必要的應用程式權限來定義 Pod 安全性原則，以減少攻擊媒介。 建議設定 Pod 安全性原則，如此 Pod 只能存取有權存取的資源。<br>(相關原則：[預覽]：Kubernetes Services 上應定義 Pod 安全性原則)|中|N|計算資源 (容器)|
|**安裝適用於 IoT 安全性模組的 Azure 資訊安全中心，以深入了解您的 IoT 裝置**|安裝適用於 IoT 安全性模組的 Azure 資訊安全中心，以深入了解您的 IoT 裝置。|低|N|IoT 裝置|

## <a name="next-steps"></a>後續步驟

若要深入了解建議，請參閱下列文章：

- [Azure 資訊安全中心的安全性建議](security-center-recommendations.md)
- [保護 Azure 資訊安全中心內的網路](security-center-network-recommendations.md)
