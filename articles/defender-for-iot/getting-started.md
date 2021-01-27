---
title: 開始使用
description: 開始瞭解 Defender for IoT 部署的基本工作流程。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/26/2020
ms.author: shhazam
ms.openlocfilehash: 82fcf7f129d5caba471d6d72da83a317b26c638c
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98807898"
---
# <a name="get-started-with-defender-for-iot"></a>開始使用適用于 IoT 的 Defender

本文概述設定適用于 IoT 的 Azure Defender 所需的步驟。 處理常式需要您：

- 在適用于 IoT 的 Azure Defender 入口網站上註冊您的訂用帳戶和感應器。
- 安裝感應器和內部部署管理主控台軟體。
- 執行感應器和管理主控台的初始啟用。

## <a name="permission-requirements"></a>權限需求

部分設定步驟需要特定的使用者權限。

需要系統管理使用者權限，才能啟動感應器和管理主控台、上傳 SSL/TLS 憑證，以及產生新的密碼。

下表說明 Azure Defender for IoT 入口網站工具的使用者存取權限：

| 權限 | 安全性讀取者 | 安全性系統管理員 | 訂用帳戶參與者 | 訂用帳戶擁有者 |
|--|--|--|--|--|
| 查看 IoT 畫面和資料的所有 Defender | ✓ | ✓ | ✓ | ✓ |
| 將感應器上線  |  |  ✓ | ✓ | ✓ |
| 更新定價  |  |  ✓ | ✓ | ✓ |
| 復原密碼  | ✓  |  ✓ | ✓ | ✓ |

## <a name="1-identify-the-solution-infrastructure"></a>1. 找出解決方案基礎結構

**明確說明您的網路設定需求**

研究您的網路架構、受監視的頻寬和其他網路詳細資料。 如需詳細資訊，請參閱 [關於適用于 IoT 的 Azure Defender 網路設定](how-to-set-up-your-network.md)。

**澄清需要哪些感應器和管理主控台設備來處理網路負載**

適用于 IoT 的 Azure Defender 支援實體和虛擬部署。 針對實體部署，您可以購買各種認證的裝置。 如需詳細資訊，請參閱 [識別需要的設備](how-to-identify-required-appliances.md)。

建議您計算將要監視的大約裝置數目。 稍後，當您向入口網站註冊您的 Azure 訂用帳戶時，系統會要求您輸入此號碼。 您可以在1000秒的間隔中新增數位。 受監視裝置的數目稱為「 *認可的裝置*」。

## <a name="2-register-with-azure-defender-for-iot"></a>2. 向適用于 IoT 的 Azure Defender 註冊

註冊包括：

- 將您的 Azure 訂用帳戶上架到適用于 IoT 的 Defender。
- 定義認可的裝置。
- 下載內部部署管理主控台的啟用檔。

註冊：

1. 移至適用于 IoT 的 Azure Defender 入口網站。
1. 選取 [上 **架訂閱**]。
1. 在 [ **定價** ] 頁面上，選取訂用帳戶或建立一個新的訂用帳戶，並新增已認可的裝置數目。
1. 選取 [ **下載內部部署管理主控台** ] 索引標籤，然後儲存下載的啟用檔案。 此檔案包含您定義的匯總認可裝置。 在初始登入之後，檔案將會上傳到管理主控台。

## <a name="3-install-and-set-up-the-on-premises-management-console"></a>3. 安裝和設定內部部署管理主控台

取得您的內部部署管理主控台設備之後：

- 從適用于 IoT 的 Azure Defender 入口網站下載 ISO 套件。
- 安裝軟體。
- 啟動並執行初始管理主控台設定。

若要安裝和設定：

1. 從適用于 IoT 的 Defender 入口網站選取 [ **開始使用** ]。
1. 選取 [內部 **部署管理主控台** ] 索引標籤。
1. 選擇版本，然後選取 [ **下載**]。
1. 安裝內部部署管理主控台軟體。 如需詳細資訊，請參閱 [Defender For IoT 安裝](how-to-install-software.md)。
1. 啟用並設定管理主控台。 如需詳細資訊，請參閱 [啟用和設定您的內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md)。

## <a name="4-onboard-a-sensor"></a>4. 將感應器上架

向適用于 IoT 的 Azure Defender 註冊感應器，並下載感應器啟用檔案，以將感應器上線：

1. 定義感應器名稱，並將它與訂用帳戶產生關聯。
1. 選擇感應器管理模式：

   - **雲端連線感應器**：感應器所偵測到的資訊會顯示在感應器主控台中。 此外，警示資訊會透過 IoT 中樞傳遞，並可與其他 Azure 服務共用，例如 Azure Sentinel。

   - **本機管理的感應器**：感應器所偵測到的資訊會顯示在感應器主控台中。 如果您是在有空調的網路中工作，而且想要統一查看多個本機受控感應器偵測到的所有資訊，請使用內部部署管理主控台。 

1. 下載感應器啟用檔。

如需詳細資訊，請參閱在 [Defender For IoT 入口網站中上架和管理感應器](how-to-manage-sensors-on-the-cloud.md)。

## <a name="5-install-and-set-up-the-sensor"></a>5. 安裝和設定感應器

從適用于 IoT 的 Azure Defender 入口網站下載 ISO 套件、安裝軟體，以及設定感應器。

1. 從適用于 IoT 的 Defender 入口網站選取 [ **開始使用** ]。
1. 選取 [ **設定感應器**]。
1. 選擇版本，然後選取 [ **下載**]。
1. 安裝感應器軟體。 如需詳細資訊，請參閱 [Defender For IoT 安裝](how-to-install-software.md)。
1. 啟用並設定您的感應器。 如需詳細資訊，請參閱登 [入並啟動感應器](how-to-activate-and-set-up-your-sensor.md)。

## <a name="6-connect-sensors-to-an-on-premises-management-console"></a>6. 將感應器連線至內部部署管理主控台

將感應器連線到管理主控台，以確定：

- 感應器會將警示和裝置清查資訊傳送至內部部署管理主控台。

- 內部部署管理主控台可以執行感應器備份、管理感應器偵測到的警示、調查感應器中斷連線，以及在連線的感應器上執行其他活動。

建議您將監視相同網路的多個感應器群組在一個區域中。 這麼做會合並多個感應器所收集的資訊。

如需詳細資訊，請參閱將 [感應器連接至內部部署管理主控台](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)。

## <a name="7-populate-azure-sentinel-with-alert-information-optional"></a>7. 使用警示資訊填入 Azure Sentinel (選擇性) 

藉由設定 Azure Sentinel，將警示資訊傳送至 Azure Sentinel。 請參閱 [將您的資料從 Defender For IoT 連接到 Azure Sentinel](how-to-configure-with-sentinel.md)。

## <a name="see-also"></a>另請參閱

- [歡迎使用適用于 IoT 的 Azure Defender](overview.md)

- [適用于 IoT 的 Azure Defender 架構](architecture.md)
