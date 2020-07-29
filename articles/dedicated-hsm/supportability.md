---
title: 支援能力 - Azure 專用 HSM | Microsoft Docs
description: 不同案例中「Azure 專用 HSM」的支援選項和責任範圍
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: d83d688707baf6098d63dfde9b4181eb04fb9729
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "70881013"
---
# <a name="azure-dedicated-hsm-supportability"></a>Azure 專用 HSM 支援能力

Azure 專用 HSM 服務提供實體裝置以供單獨客戶使用，其具有完整的系統管理控制權和管理責任。 提供的裝置為 [Gemalto SafeNet Luna 7 HSM 型號 A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)。 除了做為監控之用的實體序列連接埠附加設備以外，Microsoft 在客戶佈建後即無管理存取權。  如果沒有存取權，Microsoft 無法持續進行軟體層級維護或系統管理責任。 因此，客戶需負責一般作業活動。
客戶需完全負責使用 HSM 的應用程式，並且應該與 Gemalto 搭配使用，以提供支援或諮詢協助。 有鑑於客戶對作業檢查的擁有權程度，Microsoft 無法對此服務提供任何形式的高可用性保證。 客戶有責任確保正確設定其應用程式，以實現高可用性。 Microsoft 會監視並維護裝置的健康情況和網路連線能力。

## <a name="getting-support"></a>取得支援

客戶對專用 HSM 的支援是 Microsoft 與 Gemalto 之間的共同工作。 所有硬體問題或網路路徑問題都會由 Microsoft 解決，而與實際 HSM （例如設定、軟體、固件和應用程式開發）進行的任何作業，都會由 Gemalto 處理。 這種支援模型可確保最快速的路由傳送至最有效率的支援。 如果不確定有特定問題，請向 Microsoft 提出支援要求，我們將確保您有適當的指示。 Microsoft 將持續參與所有支援案例，並致力於為客戶提供最佳的支援體驗。

## <a name="gemalto-support"></a>Gemalto 支援

使用專用 HSM 服務的客戶根據其 Plus 支援方案，符合 Gemalto 支援的資格。 這只需要使用 Gemalto 支援入口網站的註冊程式。 客戶識別碼和指示將會在與 Microsoft 的初始參與期間提供，以取得專用 HSM 服務的存取權。 此機制可透過[客戶支援入口網站](https://supportportal.gemalto.com/csm/)，從 Gemalto 獲得支援。
重點是，Gemalto 會透過客戶支援入口網站上的下載，提供使用 HSM 所需的所有軟體和檔（例如，用戶端存取軟體和 Sdk）。

### <a name="software-components"></a>軟體元件

在 HSM 裝置的組態中，使用各種軟體元件：

* 用戶端軟體
* SDK
* 工具

### <a name="guidance"></a>指引

Gemalto 透過[客戶支援入口網站](https://supportportal.gemalto.com/csm/)提供管理和設定指導方針。 使用有效的客戶 ID 登入後，即可下載這些文件。 Gemalto 也提供一系列的整合指南，以協助客戶進行不同的案例和軟體整合。 如需更多資訊，請參閱 [Microsoft 的 Gemalto 合作夥伴網站](https://safenet.gemalto.com/partners/microsoft/)。

### <a name="support"></a>支援

使用 HSM 作為專用 HSM 服務一部分的任何相關軟體層級問題，都應該直接由 Gemalto 支援處理。 以上列出的所有軟體元件，以及任何佈建後續的自訂 HSM 設定，將由 Gemalto 處理。 如需更多資訊，請參閱 [Gemalto 客戶支援入口網站](https://supportportal.gemalto.com/csm/)。

### <a name="consulting-services"></a>諮詢服務

如需設計、開發和部署使用 HSM 之自訂應用程式的任何協助，請連絡 Gemalto 客戶代表。

## <a name="microsoft-support"></a>Microsoft 支援服務

Microsoft 會確保實體 HSM 裝置可透過網路存取，並在運作狀態中，以供單一客戶獨佔使用。 客戶須負責設定、管理和管理裝置。 Microsoft 職責包括：

* 確定裝置有電源和散熱功能
* 維護 HSM 的運作狀態 (例如，中斷/修復方案)
* 此裝置可透過網路存取。

應該向 Microsoft 呈報下列問題：

* 元件故障
* 裝置完全故障
* 網路存取問題
* 佈建和解除佈建的問題。

Microsoft 透過監控角色 (即非管理角色) 對裝置進行實體序列連接埠存取，以啟用基本的健康情況遙測。  這可讓 Microsoft 向客戶主動通知問題，除非客戶選擇限制此權限。 

### <a name="provisioning-and-decommissioning"></a>佈建和解除委任

在客戶獲得專用 HSM 服務的核准註冊後，便可建立 HSM 資源 (目前透過 PowerShell 或命令行介面，而非 Azure 入口網站)。 資源經過分配程序，該程序會將指定區域中的實體裝置對應到客戶預先定義的虛擬網路 (VNet)。 一旦在 VNet 上顯示，客戶即可存取裝置，並視需求進行進一步的設定。 客戶使用 Gemalto 用戶端軟體和工具存取其專用的 HSM。 Microsoft 支援資源建立程序。 Gemalto 支援自訂設定程序及更高版本。 (請參閱上述的 Gemalto 支援)。 當客戶使用 HSM 完成後，必須重設 (或歸零)，以確保沒有持續性的資料。 重設裝置的程序將移除所有自訂設定和資料。 Microsoft 會取消分配裝置，並將其以初始狀態傳回到集區。 這表示當裝置傳回集區時，沒有先前客戶活動的辨識項。 

### <a name="hardware-issues"></a>硬體問題

HSM 裝置具有備援和可更換的電源供應器和風扇裝置。  不過，風扇單元移除仍會造成篡改事件。 發生元件故障時，Microsoft 會使用最適合的程序來解決元件層級問題，將中斷的情況和客戶服務可用性的風險降至最低。
任何更嚴重的裝置故障，會導致該裝置更換為可用集區的新裝置。 客戶只需將新裝置包含在現有 HA 配對中，即可使其同步，並傳回完整的運作狀態。 故障的裝置必須移除其資料承載裝置，並在資料中心現場進行銷毀。 只有底座會傳回 Gemalto 以供回收。


### <a name="networking-issues"></a>網路問題

如果客戶遇到 HSM 裝置的網路存取問題，應該連絡 Microsoft 支援服務。 網路存取的簡單測試是使用 SSH 連線至 HSM 裝置。 如果失敗，請連絡 Microsoft 支援服務。

## <a name="service-level-expectations-for-support"></a>服務層級的預期支援

對於 Microsoft 支援服務層級，請參閱 [Azure 支援方案](https://azure.microsoft.com/support/plans/)。
對於 Gemalto 支援服務層級，請參閱 [Gemalto 支援基本事項](https://azure.microsoft.com/support/plans/)。

## <a name="next-steps"></a>後續步驟

建議在裝置佈建、應用程式設計或部署之前，先充分了解重要概念，例如高可用性和安全性。

* [部署架構](deployment-architecture.md)
* [高可用性](high-availability.md)
* [實體安全性](physical-security.md)
* [網路功能](networking.md)

