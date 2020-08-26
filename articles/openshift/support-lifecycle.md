---
title: Azure Red Hat OpenShift 支援生命週期
description: 瞭解 Azure Red Hat OpenShift 的支援週期和支援版本
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: c066931cece60d14767b86254020ea5ba4bad1be
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854505"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 的支援生命週期

Red Hat 會釋出 Red Hat OpenShift 容器平臺的次要版本， (OCP 大約每三個月) 。 這些版本均包含新功能和增強功能。 修補程式版本更頻繁 (通常是每週) ，而且僅適用于次要版本內的重大 bug 修正。 這些修補程式版本可能包含安全性弱點或主要 bug 的修正。

Azure Red Hat OpenShift 是從特定的 OCP 版本所建立。 本文涵蓋 Azure Red Hat OpenShift 支援的 OCP 版本，以及有關升級、棄用功能和支援原則的詳細資料。

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift 版本

Red Hat OpenShift 容器平臺使用語義版本控制。 語義版本控制會使用不同層級的版本號碼來指定不同層級的版本控制。 下表說明語義版本號碼的不同部分（在此案例中，使用範例版本號碼4.4.11）。

|主要版本 (x) |次要版本 (y) |Patch (z) |
|-|-|-|
|4|4|11|

版本中的每個數位表示與舊版的一般相容性：

* **主要版本**：目前未規劃任何主要版本版本。 當不相容的 API 變更或回溯相容性可能中斷時，主要版本會變更。
* **次要版本**：大約每隔三個月發行一次。 次要版本升級可能包括新增功能、增強功能、棄用功能、移除、bug 修正、安全性增強功能，以及其他改善。
* **修補程式**：通常是每週發行一次，或視需要進行。 修補程式版本升級可能包含 bug 修正、安全性增強功能和其他改善。

客戶的目標是要執行他們所執行之主要版本的最新次要版本。 例如，如果您的生產叢集是在4.4，而4.5 是4系列最新正式推出的次要版本，您應該儘快升級為4.5。

### <a name="upgrade-channels"></a>升級頻道

升級通道會系結至 Red Hat OpenShift 容器平臺 (OCP) 的次要版本。 比方說，OCP 4.4 升級通道絕對不會包含4.5 版的升級。 升級通道只控制發行選項，且不會影響叢集的版本。

Azure Red Hat OpenShift 4 僅支援穩定通道。 例如：穩定-4.4。

您可以使用穩定-4.5 通道，從舊版的 Azure Red Hat OpenShift 進行升級。 不支援使用 fast、搶鮮版和候選版通道升級的叢集。

如果您變更為不包含您目前版本的通道，則會顯示警示，而且不建議任何更新，但您可以放心地隨時變更回原始通道。

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift 容器平臺版本支援原則

Azure Red Hat OpenShift 支援兩種正式推出的 (GA) Red Hat OpenShift 容器平臺的次要版本：
* Azure Red Hat OpenShift 中發行的最新 GA 次要版本 (，我們會將其稱為 N) 
* 前一個次要版本 (N-1) 

如果可在穩定的升級通道中使用，則可能也會支援上游 OCP 中較新的次要版本 (N + 1、N + 2) 。

重大修補程式更新會由 Azure Red Hat OpenShift Site 可靠性工程師 (SRE) 自動套用至叢集。 希望事先安裝修補程式更新的客戶可以自行進行。

例如，如果 Azure Red Hat OpenShift 推出了 4.5. z，則會提供下列版本的支援：

|新的次要版本|支援的版本清單|
|-|-|
|4.5. z|4.5. z、4.4. z|

". z" 代表修補程式版本。 如果可在穩定的升級通道中使用，客戶也可以升級至 4.6. z。

引進新的次要版本時，會取代和移除最舊的次要版本。 例如，假設目前支援的版本清單是 4.5. z 和 4.4. z。 當 Azure Red Hat OpenShift 釋放 4.6. z 時，將會移除 4.4. z 版本，並在30天內不受支援。

> [!NOTE]
> 請注意，如果客戶正在執行不受支援的 Red Hat OpenShift 版本，則在要求支援叢集時可能會要求您升級。 Azure Red Hat OpenShift SLA 未涵蓋執行不受支援的 Red Hat OpenShift 版本的叢集。

## <a name="release-and-deprecation-process"></a>發行和淘汰程式

您可以參考即將發行的版本，並棄用功能 Azure Red Hat OpenShift 發行行事曆。

針對 Red Hat OpenShift 容器平臺的新次要版本：
* Azure Red Hat OpenShift SRE 團隊發行預先公告，內含新版本發行的計畫日期，以及 [Azure Red Hat OpenShift 版本](https://github.com/Azure/OpenShift/releases) 資訊在移除之前至少30天的舊版本取代。
* Azure Red Hat OpenShift SRE 小組會將服務健康狀態通知發佈給所有客戶使用 Azure Red Hat OpenShift 和入口網站存取，並傳送電子郵件給訂用帳戶管理員，並提供已規劃的版本移除日期。
* 客戶有30天的從版本移除升級為支援的次要版本，以繼續接收支援。

針對 Red Hat OpenShift 容器平臺的新修補程式版本：
* 因為修補程式版本的緊急本質，所以 Azure Red Hat OpenShift SRE 小組可以在服務中引進這些版本。
* 一般而言，Azure Red Hat OpenShift SRE 團隊不會針對新的修補程式版本安裝執行廣泛的通訊。 不過，小組會持續監視並驗證可用的 CVE 修補程式，以及時支援這些修補程式。 如果需要客戶動作，小組會通知客戶升級。

## <a name="supported-versions-policy-exceptions"></a>支援的版本原則例外狀況

Azure Red Hat OpenShift SRE 團隊保留新增或移除新的/現有版本的許可權，或延遲即將發行的次要發行版本，這些版本已識別為有一或多個重大的生產影響 bug 或安全性問題，而不會事先通知。

您可以略過特定的修補程式版本，或根據 bug 或安全性問題的嚴重性來加速推出。

## <a name="azure-portal-and-cli-versions"></a>Azure 入口網站和 CLI 版本

當您在入口網站或 Azure CLI 中部署 Azure Red Hat OpenShift 叢集時，叢集預設為最新的 (N) 次要版本和最新的重大修補程式。 例如，如果 Azure Red Hat OpenShift 支援 4.5. z 和 4.4. z，則新安裝的預設版本為 4.5. z。 如果客戶想要使用最新的上游 OCP 次要版本 (N + 1、N + 2) 可隨時將其叢集升級至穩定升級通道中可用的任何版本。

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift 發行行事曆

請參閱下列指南，以瞭解 [過去的 Red Hat OpenShift 容器平臺 (上游) 發行歷程記錄](https://access.redhat.com/support/policy/updates/openshift/#dates)。

|OCP 版本|上游版本|Azure Red Hat OpenShift 正式推出|生命週期結束|
|-|-|-|-|
|4.3|2020 年 2 月|2020 年 5 月|2020 年 8 月|
|4.4|2020 年 5 月|2020 年 8 月|4.6 GA|
|4.5|2020 年 7 月|2020年10月|4.7 GA
|4.6|* 2020 年4月初|* 2020 年4月底|4.8 GA|

\*_暫止上游發行日期確認。_

## <a name="faq"></a>常見問題集

**當使用者使用不支援的次要版本升級 OpenShift 叢集時，會發生什麼事？**

如果您是在 N-2 版或更舊版本，則表示您不在支援範圍內，因此將會要求您進行升級。 當您從第2版升級為 N-1 時，您將會回到我們的支援原則中。 例如：
* 如果最舊支援的 Azure Red Hat OpenShift 版本為 4.4. z，而您是在 4.3. z 或更舊版本，則表示您不在支援範圍內。
* 從 4.3. z 到 4.4. z 或更高版本升級成功時，您會回到我們的支援原則中。

不支援將您的叢集還原至先前的版本或復原。 僅支援升級至較新的版本。

**「支援以外的」是什麼意思？**

「不支援」表示您正在執行的版本不在支援的版本清單中，而且在要求支援時可能會要求您將叢集升級至支援的版本，除非您是在版本淘汰後30天的寬限期內。 此外，Azure Red Hat OpenShift 不會在30天的寬限期結束時，對支援版本清單以外的叢集進行任何執行時間或 SLA 保證。
