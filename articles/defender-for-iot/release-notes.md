---
title: 適用于 IoT 的 Azure Defender 新功能
description: 本文可讓您知道最新版本 Defender for IoT 的新功能。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2021
ms.author: shhazam
ms.openlocfilehash: 0281ebcdf1da27513e9b9256b508d911ec7697b5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937585"
---
# <a name="whats-new"></a>最新消息

Defender for IoT 10.0 提供增強安全性、管理和可用性的功能增強功能。

## <a name="security"></a>安全性

這一版的憑證和密碼修復增強功能。

### <a name="certificates"></a>憑證
  
此版本可讓您：

- 直接將 SSL 憑證上傳至感應器和內部部署管理主控台。
- 在內部部署管理主控台和連線的感應器之間，以及在管理主控台與高可用性管理主控台之間執行驗證。 驗證是以到期日、根 CA 真品和憑證撤銷清單為基礎。  如果驗證失敗，會話將無法繼續。

在升級方面：

- 升級期間的 SSL 憑證或驗證功能沒有任何變更。
- 升級之後，感應器和內部部署管理主控台的系統管理使用者可以取代 SSL 憑證，或從 [系統設定] 的 [SSL 憑證] 視窗啟用 SSL 憑證驗證。  

針對全新安裝：

- 在首次登入時，使用者必須使用 SSL 憑證 (建議的) 或本機產生的自我簽署憑證 (不建議使用) 
- 預設會針對全新安裝開啟憑證驗證。

### <a name="password-recovery"></a>密碼復原
  
感應器和內部部署管理主控台的系統管理使用者現在可以從適用于 IoT 的 Azure Defender 入口網站復原密碼。 先前的密碼復原需要支援小組介入。

## <a name="onboarding"></a>登入

### <a name="on-premises-management-console---committed-devices"></a>內部部署管理主控台認可的裝置

在初始登入內部部署管理主控台之後，使用者現在必須上傳啟用檔。 該檔案包含要在組織網路上監視的裝置匯總數目。 此數位稱為認可的裝置數目。
認可的裝置是在適用于 IoT 的 Azure Defender 入口網站上上執行緒序期間定義的，其中會產生啟用檔。
首次使用者和使用者需要升級時，才可上傳啟用檔。
初始啟用之後，在網路上偵測到的裝置數目可能會超過認可的裝置數目。 例如，如果您將更多感應器連線到管理主控台，就可能會發生此事件。 如果偵測到的裝置數目和已認可的裝置數目之間有不一致的情況，則會在管理主控台中顯示警告。 如果發生此事件，您應該上傳新的啟用檔。

### <a name="pricing-page-options"></a>定價頁面選項

定價頁面可讓您將新訂用帳戶上線至適用于 IoT 的 Azure Defender，並在您的網路中定義認可的裝置  
此外，定價頁面現在可讓您管理與感應器相關聯的現有訂用帳戶，以及更新裝置承諾用量。

### <a name="view-and-manage-onboarded-sensors"></a>查看和管理上線感應器

新的網站和感應器入口網站頁面可讓您：

- 新增有關感應器的描述性資訊。 例如，與感應器相關聯的區域或任意文字標記。
- 查看和篩選感應器資訊。 例如，查看與雲端連線或在本機管理的感應器，或在特定區域中查看感應器相關資訊的詳細資料。  

## <a name="usability"></a>可用性

### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel 新的連接器頁面

Azure Sentinel 中的 Azure Defender for IoT data connector 頁面已重新設計。 資料連線器現在以訂用帳戶為基礎，而不是 IoT 中樞;讓客戶能夠更妥善地管理其與 Azure Sentinel 的設定連接。

### <a name="azure-portal-permission-updates"></a>Azure 入口網站許可權更新  

已新增安全性讀取者和安全性系統管理員支援。

## <a name="other-updates"></a>其他更新

### <a name="access-group---zone-permissions"></a>存取群組-區域許可權
  
內部部署管理主控台存取群組規則將不會包含授與特定區域存取權的選項。 定義使用網站、區域和業務單位的規則時，不會有任何變更。   升級之後，將會修改包含允許存取特定區域之規則的存取群組，以允許其父網站的存取權，包括其所有區域。

### <a name="terminology-changes"></a>術語變更

在感應器和內部部署管理主控台、報告和其他解決方案介面中，已將裝置的資產重新命名為裝置。
在感應器和內部部署管理主控台警示中，管理此事件的一詞已命名為補救步驟。

## <a name="next-steps"></a>後續步驟

[開始使用 Defender for IoT](getting-started.md)
