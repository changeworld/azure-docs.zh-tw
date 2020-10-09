---
title: 監視選項 - Azure 專用 HSM | Microsoft Docs
description: 「Azure 專用 HSM」監視選項和監視責任概觀
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "70881413"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Azure 專用 HSM 監視功能

Azure 專用 HSM 服務提供實體裝置以供單獨客戶使用，其具有完整的系統管理控制權和管理責任。 提供的裝置為 [Gemalto SafeNet Luna 7 HSM 型號 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)。  除了做為監控之用的實體序列連接埠附加設備以外，Microsoft 在客戶佈建後即無管理存取權。 因此，客戶必須負責一般作業的活動，包括全面的監視和記錄分析。
使用 HSM 的應用程式需由客戶負全責，且客戶需與 Gemalto 合作，以獲得支援服務或諮詢方面的協助。 有鑑於客戶對作業檢查的擁有權程度，Microsoft 無法對此服務提供任何形式的高可用性保證。 客戶有責任確保應用程式設定正確，以實現高可用性。 Microsoft 會監視並維護裝置的健康情況和網路連線能力。

## <a name="microsoft-monitoring"></a>Microsoft 監視

使用的 Gemalto SafeNet 裝置依預設會以 SNMP 和序列連接埠做為監視裝置的選項。 Microsoft 使用序列連接埠來實體連接裝置，以擷取裝置健康情況的基本遙測資料。 這類資料涵蓋了溫度和元件狀態等項目，例如電源供應器和風扇。
為了實現此目的，Microsoft 使用的是 Gemalto 裝置上所設的非管理「監控者」角色。 此角色提供了擷取遙測資料的能力，但在管理工作方面不會提供任何裝置存取權，也完全無法檢視加密資訊。 我們的客戶可以放心確定裝置完全是由自己管理，並可作為敏感的加密金鑰儲存裝置使用。 如果客戶對於用來監視基本健康情況的最低存取權有所顧慮，也可以選擇停用監視帳戶。 如此一來，Microsoft 顯然不會擁有任何資訊，也因此無法主動通知裝置健康情況方面的問題。 在此情況下，裝置的健康情況需由客戶負責。
監視器功能本身設定為每 10 分鐘輪詢一次裝置，以取得健康情況資料。 由於序列通訊容易出錯，只有在一小時內出現多個負面健康情況指標後，才會發出警示。 警示最後會主動與客戶通訊，以通知問題。
根據問題的性質不同，需採取適當的動作以減少影響，確保實行低風險的補救方案。 舉例而言，電源供應器故障是熱交換過程，並未產生任何篡改事件，因此執行時可以少對作業的影響，盡可能將作業風險降至翠低。 其他程序可能會要求將裝置歸零，並取消佈建，以將客戶的任何安全風險降至最低。 在這種情況下，客戶會佈建備用裝置，重新加入高可用性的配對，進而觸發裝置同步。 如此會在最短的時間內恢復正常運作，也會盡可能減少服務中斷的情形，並將安全風險降至最低。  

## <a name="customer-monitoring"></a>客戶監視

專用 HSM 服務的價值主張在於客戶對裝置的控制權，尤其考量到這是台雲端傳遞裝置。 隨控制權而來的是監視與管理裝置健康情況的責任。 Gemalto SafeNet 裝置隨附 SNMP 和 Syslog 實作指引。 即使 Microsoft 監視帳戶仍處於作用中狀態，仍建議使用專用 HSM 服務的客戶使用此技術，而且如果停用了 Microsoft 監視帳戶，則應將其視為必要服務。
這兩種技術都可以讓客戶識別問題，並致電 Microsoft 支援服務，以啟動適當的補救工作。

## <a name="next-steps"></a>後續步驟

建議在任何裝置佈建和應用程式設計或部署之前，先充分了解服務的所有重要概念，例如高可用性和安全性。 更多概念層級的主題：

* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)
* [支援能力](supportability.md)
