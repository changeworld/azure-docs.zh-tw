---
title: 部署選項
description: 開始瞭解 Defender for IoT 功能和服務的基本工作流程。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090058"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>開始使用適用于 IoT 的 Azure Defender

本文說明讓 Azure Defender for IoT 執行所需的部署和上架程式。 此外也需要額外的步驟。 建議您瞭解這些步驟，並熟悉隨附檔中的資訊。

完成所有步驟之後，適用于 IoT 的 Azure Defender 感應器將會監視您的網路。 根據您設定解決方案的方式而定，偵測也可以傳送至內部部署管理主控台或 IoT 中樞。

完成下列步驟，以讓適用于 IoT 的 Azure Defender 正常運作。

## <a name="1-set-up-azure"></a>1. 設定 Azure

- 設定 Azure 帳戶。 如需詳細資訊，請參閱 [建立 Azure 帳戶](https://docs.microsoft.com/learn/modules/create-an-azure-account/)。

- 防火牆或 proxy：如果您有設定為允許特定連線的防火牆或類似的中間網路裝置，請確認已開啟防火牆或 proxy 的 *. azure-devices.net:443。 如果不支援萬用字元，或您想要更多控制，則應在您的 FW 或 proxy 中開啟特定的 IoT 中樞 FQDN。 如需詳細資訊，請參閱 [參考-IoT 中樞端點](/azure/iot-hub/iot-hub-devguide-endpoints)。

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. 部署硬體、軟體和上架至感應器

- 購買感應器硬體和安裝軟體。 遵循此處所述的步驟，如需詳細資訊，請參閱這篇文章和適用于 [IoT 的 Defender 硬體指南](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 及 [安裝指南](https://aka.ms/AzureDefenderforIoTInstallSensorISO)。

  - 在您安裝感應器之後，請安全地記錄感應器登入認證。 您將需要用來將啟用檔案上傳至感應器的認證。

  - 如果您正在使用本機管理的感應器，請安全地記錄感應器的 IP 位址或安裝中定義的感應器名稱。 在 IoT 入口網站的 Defender 註冊期間建立感應器名稱時，您可能會想要使用它。 您可以稍後使用它們，以確保在適用于 IoT 的 Azure Defender 入口網站中的註冊名稱與感應器主控台中顯示的已部署感應器 IP 位址之間進行更輕鬆的追蹤和一致的命名。

- 向適用于 IoT 的 Defender 註冊入口網站，並下載感應器啟用檔案。

- 將啟用檔案上傳至您的感應器。

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. 執行網路設定以進行感應器監視和管理

- 將您的感應器連線到網路。 請參閱 [網路設定指南](https://aka.ms/AzureDefenderForIoTNetworkSetup)中的。

## <a name="4-start-discovering-your-network"></a>4. 開始探索您的網路

- 調整感應器主控台中的系統設定。

- 將感應器連接至內部部署管理主控台。

如需詳細資訊，請參閱適用于 iot 的 [Azure Defender 感應器使用者指南](https://aka.ms/AzureDefenderforIoTUserGuide) 和 [適用于 iot 的 defender 內部部署管理主控台的使用者指南](https://aka.ms/DefenderForIoTManagementConsole)。

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. 使用警示資訊填入 Azure Sentinel

- 若要將警示資訊傳送至 Azure Sentinel，請設定 Azure Sentinel： [將您的資料從 Defender For IoT 連接到 Azure Sentinel](how-to-configure-with-sentinel.md)。
 

## <a name="next-steps"></a>後續步驟

- [為 IoT 啟用 Defender](quickstart-onboard-iot-hub.md)
- 設定您的 [解決方案](quickstart-configure-your-solution.md)
- [建立安全性模組](quickstart-create-security-twin.md)
- 設定 [自訂警示](quickstart-create-custom-alerts.md)
- [部署安全性代理程式](how-to-deploy-agent.md)
