---
title: Azure 上的 IBM 工作負載 |Microsoft Docs
description: 使用來自 Microsoft 合作夥伴的大型主機模擬器和其他服務，使用 Microsoft Azure 重新裝載您的 IBM z/OS 工作負載。
services: virtual-machines-linux
ms.service: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 13c83c53cdad719d6a4bed4cc1852b85d62082e8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68834594"
---
# <a name="ibm-workloads-on-azure"></a>Azure 上的 IBM 工作負載

許多以 z/OS 為基礎的 IBM 大型主機工作負載都可以在 Azure 中進行複寫，而不會遺失任何功能，而且使用者甚至不會注意到其基礎系統中的變更。 Azure 上的重新裝載應用程式可提供您所需的大型主機功能，以及雲端的彈性、可用性和潛在的成本節約。

Azure 支援與現有的 IBM 大型主機環境整合，讓您能夠遷移合理的 applicates、視需要執行混合式解決方案，以及在一段時間後遷移。 雖然您可以完全重寫 Azure 的現有大型主機型程式，但更常見的方法是重新裝載它們。 重寫會增加成本、複雜度和遷移專案的時間。 透過重新裝載，您可以：

- 將應用程式移至雲端型模擬器。

- 將資料庫移轉至以雲端為基礎的資料庫。

- 使用程式碼轉換引擎取代模組和程式碼。

此外，IBM 軟體（包括 WebSphere 和 MQ）現在已在 Azure Marketplace 中。 透過 IBM 軟體的授權，您可以利用 Azure 所提供的隨選基礎結構調整來快速啟動虛擬機器。

有廣泛的合作夥伴生態系統可協助您將 IBM 大型主機系統移轉至 Azure。 在登機重寫或取代應用程式的階段式部署之前，最常遵循實際的重複使用方法。 在[Azure 大型主機遷移中心](https://azure.microsoft.com/migration/mainframe/)取得更多有關合作夥伴的指引和協助。

**後續步驟**

- [大型主機遷移：誤解和事實](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [在 Azure 上安裝 IBM zD&T 開發/測試環境](./install-ibm-z-environment.md)
- [在 IBM zD&T v1 中設定由應用程式開發人員控制的散發（ADCD）](./demo.md)
- [Azure 上的 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
