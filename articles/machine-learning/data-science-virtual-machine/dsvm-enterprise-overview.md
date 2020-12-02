---
title: 團隊分析和 AI 環境
titleSuffix: Azure Data Science Virtual Machine
description: 在企業團隊環境中部署「資料科學 VM」的模式。
keywords: 深度學習, 資料科學工具, 資料科學虛擬機器, 地理空間分析, 小組資料科學流程
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 9126dbcfbfe9e3a94514ebf45685bdfc55dd0306
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463001"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>以資料科學虛擬機器為基礎的團隊分析和 AI 環境 
[資料科學虛擬機器](overview.md) (DSVM) 在 Azure 平台中提供豐富的環境，其中包含了適用於人工智慧 (AI) 和資料分析的預先建立軟體。

傳統上，DSVM 已用來作為個別的分析電腦。 個別資料科學家可以透過此共用、預先建立的分析環境提升生產力。 當大型分析團隊在規劃其資料科學家和 AI 開發人員的環境時，其中一個重複出現的主題便是用於開發與實驗的共用分析基礎結構。 此基礎結構的管理方式與企業 IT 原則一致，這些原則同時也協助跨資料科學和分析團隊的共同作業和一致性。

共用的基礎結構可讓 IT 能夠更充分地利用分析環境。 有些組織將團隊型資料科學/分析基礎結構稱為 *分析沙箱*。 它可讓資料科學家存取各種資料資產，以快速了解資料。 此沙箱環境也可學助資料科學家執行實驗、驗證假設及建置預測模型，而不會影響到生產環境。

由於 DSVM 在 Azure 基礎結構層級運作，因此 IT 系統管理員立即就可以將 DSVM 的運作方式設定成符合企業的 IT 原則。 DSVM 既提供可實作各種共用架構的完整彈性，又能控管對公司資料資產的存取。

本節將討論可用來將 DSVM 部署成團隊型資料科學基礎結構的一些模式與指導方針。 由這些模式的構成要素來自 Azure 基礎結構即服務 (IaaS)，因此可適用於任何 Azure VM。 此系列文章主要著重在將這些標準 Azure 基礎結構功能應用在 DSVM 上。

企業團隊分析環境的主要建構元素包括：

* [DSVM 的自動調整集區](dsvm-pools.md)
* [從集區中的任何 DSVM 使用通用身分識別並存取工作區](dsvm-common-identity.md)
* [保護對資料來源的存取](dsvm-secure-access-keys.md)


本系列提供前述各個主題的指引和指標。 其並未涵蓋在大型企業設定中部署 DSVM 的所有考量和需求。 以下是您在企業中實作 DSVM 執行個體時可使用的其他 Azure 資源：

* [網路安全性](../../security/fundamentals/network-overview.md)
* [監視](../../azure-monitor/insights/monitor-vm-azure.md)和[管理](../../virtual-machines/maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [記錄與稽核](../../security/fundamentals/log-audit.md)
* [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md)
* [原則設定和強制執行](../../governance/policy/overview.md)
* [反惡意程式碼](../../security/fundamentals/antimalware.md)
* [加密](../../virtual-machines/windows/disk-encryption-overview.md)
* [資料探索和控管](../../data-catalog/index.yml)

最後，[Azure 架構中心](/azure/architecture/)提供用於建置和管理雲端式分析基礎結構的詳細端對端架構和模型。