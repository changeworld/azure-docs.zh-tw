---
title: 部署架構 - Azure 專用 HSM | Microsoft Docs
description: 使用「Azure 專用 HSM」作為應用程式架構之一部分時的設計考量
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77048558"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Azure 專用 HSM 部署架構

Azure 專用硬體安全模組 (HSM) 可在 Azure 中提供密碼編譯金鑰儲存功能。 它符合嚴格的安全性需求。 當客戶符合下列條件時，即可從使用 Azure 專用硬體安全模組 (HSM) 中獲得助益：

* 必須符合 FIPS 140-2 等級 3 認證
* 客戶必須具有硬體安全模組 (HSM) 的獨佔存取權
* 應可完全控制其裝置

硬體安全模組 (HSM) 是分散在各 Microsoft 資料中心，並可輕鬆佈建為一組裝置以作為高可用性解決方案的基礎。 它們也可以跨區域部署以用於災害復原解決方案。 目前提供專用硬體安全模組 (HSM) 的區域如下：

* 美國東部
* 美國東部 2
* 美國西部
* 美國中南部
* 東南亞
* 東亞
* 印度中部
* 印度南部
* 日本東部
* 日本西部
* 北歐
* 西歐
* 英國南部
* 英國西部
* 加拿大中部
* 加拿大東部
* 澳大利亞東部
* 澳大利亞東南部

其中每個區域都會將硬體安全模組 (HSM) 機架部署在兩個獨立資料中心或至少兩個獨立可用性區域中。 東南亞有三個可用性區域，而美國東部 2 有兩個。 歐洲、亞洲和美國共有八個地區提供專用硬體安全模組 (HSM) 服務。 如需 Azure 區域的詳細資訊，請參閱官方 [Azure 區域資訊](https://azure.microsoft.com/global-infrastructure/regions/)。
專用硬體安全模組 (HSM) 解決方案的設計因素包括位置/延遲、高可用性，以及對其他分散式應用程式的支援。

## <a name="device-location"></a>裝置位置

最佳的硬體安全模組 (HSM) 裝置位置，應該最接近執行密碼編譯作業的應用程式。 區域內延遲應該為個位數的毫秒。 跨區域的延遲可能比這高 5 到 10 倍。

## <a name="high-availability"></a>高可用性

若要達到高可用性，客戶必須在區域中使用兩個硬體安全模組 (HSM) 裝置，且裝置已使用 Gemalto 軟體將其設定為高可用性配對。 如果單一裝置發生問題而無法處理金鑰作業，則這種部署類型可確保金鑰的可用性。 它也可以大幅降低執行更換電源供應器這類協助修正維護時的風險。 在設計時，請務必考慮到各種區域層級的故障。 區域層級的故障可能會在颶風、洪水或地震等天然災害時發生。 您應該將硬體安全模組 (HSM) 裝置佈建在其他區域中，以避免這些類型的事件。 其他區域中部署的裝置也可以透過 Gemalto 軟體設定一起配對。 這表示可保障高可用性和災害復原解決方案的最小部署為跨兩個地區、四個硬體安全模組 (HSM) 裝置。 您可以將本機備援性和跨區域備援作為基準，新增任何其他的硬體安全模組 (HSM) 裝置部署以支援延遲、容量或滿足其他應用程式的特定需求。

## <a name="distributed-application-support"></a>支援分散式應用程式

一般來說，我們會部署專用硬體安全模組 (HSM) 裝置，以支援需要執行金鑰儲存和金鑰擷取作業的應用程式。 專用硬體安全模組 (HSM) 裝置會有 10 個分割區以用於獨立的應用程式支援。 裝置位置應該依據需要使用服務之所有應用程式的全面檢視而定。

## <a name="next-steps"></a>後續步驟

一旦決定部署架構之後，即會由 Gemalto 提供大部分的設定活動以實作該架構。 這包括裝置設定以及應用程式整合案例。 如需詳細資訊，請使用 [Gemalto 客戶支援](https://supportportal.gemalto.com/csm/)入口網站，並下載管理和設定指南。 Microsoft 合作夥伴網站提供多種整合指南。
建議您在佈建裝置或設計與部署應用程式之前，先充分了解服務的所有重要概念，例如高可用性和安全性。
更多概念層級的主題：

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
* [監視](monitoring.md)
