---
title: 更新威脅情報資料
description: 每個新的 Defender for IoT 版本都會提供威脅情報資料套件，或在版本之間需要。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839458"
---
# <a name="threat-intelligence-research-and-packages"></a>威脅情報研究和套件

Microsoft 中的安全性小組會執行專屬的 ICS 威脅情報和弱點研究。 這些團隊包括 MSTIC (Microsoft 威脅情報中心) 、DART (Microsoft 偵測和回應小組) 、DCU (數位犯罪單位) ，以及 52 (IoT/OT/ICS 網域專家，以追蹤 ICS 特定的零天、反向工程惡意程式碼、行銷活動和敵人) 。

這些團隊提供安全性偵測、分析以及對 Microsoft 的回應：

- 雲端基礎結構和服務。
- 傳統的產品和裝置。
- 公司內部資源。

安全性小組會獲得下列優點：

- 抵禦已知和相關威脅的保護。
- 協助您分級和排列優先順序的見解。
- 瞭解威脅在受到影響之前的完整內容。
- 更相關、精確且可採取動作的資料。

此情報新增了內容相關資訊，以擴充 Microsoft 平臺分析，並支援公司的受控服務進行事件回應和缺口調查。 威脅情報套件包含簽章 (包括惡意程式碼簽章) 、Cve 和其他安全性內容。

您可以從適用于 IoT 的 Azure Defender 入口網站中的 [ **更新** ] 頁面下載套件。

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="透過適用于 IoT 的 Azure Defender 入口網站下載更新。":::

## <a name="update-threat-intelligence-data"></a>更新威脅情報資料

每個新的 Defender for IoT 版本更新都會提供威脅情報套件，或在版本之間需要。

您從 Defender for IoT 入口網站下載的套件可以手動上傳到個別的感應器。 如果內部部署管理主控台管理您的感應器，您可以將威脅情報套件下載到管理主控台，並同時將其推送至多個感應器。

若要更新單一感應器上的封裝：

1. 移至適用于 IoT 的 Azure Defender **更新** 頁面。

2. 下載並儲存 **威脅情報** 套件。

3. 登入感應器主控台。

4. 在側邊功能表上，選取 [ **系統設定**]。

5. 選取 [ **威脅情報資料**]，然後選取 [ **更新**]。

6. 上傳新的封裝。

同時更新多個感應器上的封裝：

1. 移至適用于 IoT 的 Azure Defender **更新** 頁面。

2. 下載並儲存 **威脅情報** 套件。

3. 登入管理主控台。

4. 在側邊功能表上，選取 [ **系統設定**]。

5. 在 [ **感應器引擎** 設定] 區段中，選取應接收更新套件的感應器。  

6. 在 [ **選取威脅情報資料** ] 區段中，選取加號 (**+**) 。

7. 上傳套件。

## <a name="see-also"></a>請參閱

[更新版本](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
