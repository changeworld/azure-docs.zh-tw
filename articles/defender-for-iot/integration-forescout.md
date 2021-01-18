---
title: 關於 Forescout 整合
titleSuffix: Azure Defender for IoT
description: 適用于 IoT 的 Azure Defender 與 Forescout 平臺整合提供了新層級的集中式可見度、監視及控制，適用于 IoT 和 OT 的環境。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: faa53c770d0d6caac471e770c80b4dfd5c5ff603
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557605"
---
# <a name="about-the-forescout-integration"></a>關於 Forescout 整合

適用于 IoT 的 Azure Defender 提供由 blue team 專家打造的 ICS 和 IoT 網路安全性平臺，並提供可協助您保護重要國家基礎結構的追蹤記錄。 適用于 IoT 的 Defender 是具備專利之 ICS 感知威脅分析和機器學習的唯一平臺。 適用于 IoT 的 Defender 提供：

- 有關 ICS 裝置環境的即時深入解析，以及有關屬性的廣泛詳細資料。
- 以 ICS 感知的深度內嵌知識，以及關於通訊協定、裝置、應用程式及其行為的知識。
- 立即深入瞭解弱點，以及已知和零時差的威脅。
- 自動化 ICS 威脅模型化技術，可透過專屬分析來預測目標 ICS 攻擊最可能的路徑。

適用于 IoT 的 Defender 與 Forescout 平臺整合提供了新層級的集中式可見度、監視及控制，適用于 IoT 和 OT。

這些橋接的平臺可讓您對先前無法連線的 ICS 裝置和孤立的工作流程進行自動化的裝置可見度和管理

此整合為 SOC 分析師提供了在產業環境中部署之通訊協定的多層級可見度。 詳細資料適用于以專屬 Azure Defender for IoT 技術為基礎的專案，例如固件、裝置類型、作業系統和風險分析分數。

> [!Note]
> CyberX 的參考會參考適用于 IoT 的 Azure Defender。
## <a name="devices"></a>裝置

### <a name="device-visibility-and-management"></a>裝置可見度與管理

裝置的清查會使用 Defender for IoT 平臺所偵測到的重要屬性進行擴充。 這可確保您：

- 從單一窗格的裝置環境中，以全方位且持續的可見度來查看。
- 取得有關承擔風險的即時情報。

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="裝置清查":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="裝置詳細資訊":::

### <a name="device-control"></a>裝置控制

Forescout 整合有助於減少產業和重要基礎結構組織偵測、調查及因應網路威脅所需的時間。

- 使用適用于 IoT 的 Azure Defender 裝置智慧，藉由觸發 Forescout 原則動作來關閉安全性週期。 例如，您可以在偵測到特定的通訊協定，或當固件詳細資料變更時，自動傳送警示電子郵件給 SOC 系統管理員。

- 將適用于 IoT 的 Defender 資訊與其他監督監視、事件管理和裝置控制的 *Forescout eyeExtended* 模組相互關聯。

## <a name="system-requirements"></a>系統需求

- 適用于 IoT 的 Azure Defender 2.4 版或更新版本
- Forescout 8.0 版或更新版本
- 適用于 Azure Defender for IoT 平臺的 *Forescout eyeExtend* 模組授權。

### <a name="getting-more-forescout-information"></a>取得更 Forescout 的資訊

如需 Forescout 平臺的詳細資訊，請參閱 [Forescout Resource Center](https://www.forescout.com/company/resources/#resource_filter_group)。

## <a name="system-setup"></a>系統設定

本文說明如何設定 Defender for IoT 平臺與 Forescout 平臺之間的通訊。

### <a name="set-up-the-defender-for-iot-platform"></a>設定適用于 IoT 的 Defender 平臺

若要確保從 Defender for IoT 到 Forescout 的通訊，請在 Defender 中為 IoT 產生存取權杖。

存取權杖可讓外部系統存取 Defender 針對 IoT 探索到的資料，並透過 SSL 連線使用外部 REST API 對該資料執行動作。 您可以產生存取權杖，以存取適用于 IoT 的 Azure Defender REST API。

若要產生權杖：

1. 登入 Forescout 將查詢的 Defender for IoT 感應器。

1. 選取 [**系統設定**]，然後從 [**一般**] 區段中選取 [**存取權杖**]。 [ **存取權杖** ] 對話方塊隨即開啟。
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="存取權杖":::
1. 選取 [**存取權杖**] 對話方塊中的 [**產生新的權杖**]。
1. 在 [ **新存取權杖** ] 對話方塊中輸入權杖描述。
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="新的存取權杖":::
1. 選取 [下一步] 。 此權杖會顯示在對話方塊中。 :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="查看權杖":::
   > [!NOTE]
   > 將 *權杖記錄在安全的位置。設定 Forescout 平臺時，您將會需要它*。
1. 選取 [完成]。

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="完成新增權杖":::

### <a name="set-up-the-forescout-platform"></a>設定 Forescout 平臺

您可以設定 Forescout 平臺，以與適用于 IoT 感應器的 Defender 進行通訊。

若要設定：

1. 在 Forescout 平臺上安裝 *適用于 CyberX 的 Forescout eyeExtend 模組* 。

1. 登入 [信號主控台]，然後從 [**工具**] 功能表中選取 [**選項**]。 [ **選項** ] 對話方塊隨即開啟。

1. 流覽至 [ **模組** ] 資料夾並加以選取。

1. 在 [ **模組** ] 窗格中，選取 [ **CyberX 平臺**]。 [Defender for IoT 平臺] 窗格隨即開啟。

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="適用于 IoT 的 Azure Defender 模組設定":::

   輸入以下資訊：

   - [**伺服器位址**]-輸入將由 Forescout 設備查詢的 Defender for IOT 感應器 IP 位址。
   - **存取權杖** -輸入針對將連線至 Forescout 設備之 Defender for IoT 感應器產生的存取權杖。 若要產生權杖，請參閱 [設定適用于 IoT 的 Defender 平臺](#set-up-the-defender-for-iot-platform)。

1. 選取 [套用]  。

如果您想要讓 Forescout 平臺與另一個感應器通訊：

1. 在適用于 IoT 感應器的相關 Defender 中建立新的存取權杖。

1. 在 [ **Forescout 模組**  >  **CyberX 平臺**] 對話方塊中：

   - 刪除顯示的資訊。
   
   - 輸入新的感應器 IP 位址和新的存取權杖資訊。

### <a name="verify-communication"></a>驗證通訊

設定適用于 IoT 和 Forescout 的 Defender 之後，請在 Defender 中開啟適用于 IoT 的感應器存取權杖對話方塊。

如果此權杖的 [已 **使用**] 欄位中顯示 **N/A** ，表示感應器與 Forescout 設備之間的連接無法運作。

[已 **使用**] 表示最後一次收到此權杖的外部呼叫。

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="確認已收到權杖":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>在 Forescout 中查看適用于 IoT 的 Defender 偵測

若要查看裝置的屬性：

1. 登入 Forescout 平臺，然後流覽至 **資產清查**。

1. 流覽至 **CyberX 平臺**。 Defender 針對 IoT 偵測到的裝置會顯示下列裝置屬性。

   | 項目 | 描述 |
   |--|--|
   | 適用于 IoT 的 Azure Defender 授權 | 在網路學習期間，Defender 針對 IoT 在您網路上偵測到的裝置。 |
   | 韌體 | 裝置的固件詳細資料。 例如，模型和版本詳細資料。 |
   | Name | 裝置的名稱。 |
   | 作業系統 | 裝置的作業系統。 |
   | 類型 | 裝置的類型。 例如，PLC、Historian 或工程站。 |
   | 廠商 | 裝置的廠商。 例如，幼圓的自動化。 |
   | 風險層級 | Defender 針對 IoT 所計算的風險層級。 |
   | 通訊協定 | 在裝置產生的流量中偵測到的通訊協定。 |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="查看 [固件] 屬性。":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="查看廠商屬性。":::

### <a name="viewing-more-details"></a>查看更多詳細資料

針對由 Defender 針對 IoT 導向的裝置，查看額外的裝置資訊。 例如，Forescout 合規性和原則資訊。

若要完成此動作，請以滑鼠右鍵按一下 [ **裝置清查主機** ] 區段中的裝置。 [主機詳細資料] 對話方塊隨即開啟，並提供其他資訊。

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="主機詳細資料":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>在 Forescout 中建立適用于 IoT 的 Azure Defender 原則

Forescout 原則可用來將 Defender 針對 IoT 偵測到的裝置，自動控制及管理。 例如，

- 當偵測到特定的固件版本時，自動傳送 SOC 系統管理員的電子郵件。

- 將適用于 IoT 的特定 Defender 偵測到的裝置新增至 Forescout 群組，以進一步處理事件和安全性工作流程，例如與其他 SIEM 整合。

使用 [條件] 屬性來建立 Forescout 自訂原則，以及使用 Defender for IoT。

若要存取適用于 IoT 的 Defender 屬性：

1. 從 [**原則條件**] 對話方塊流覽至 [**屬性] 樹狀目錄**。

1. 展開 [**屬性] 樹狀結構** 中的 [ **CyberX Platform** ] 資料夾。 可使用適用于 IoT 的 Defender 下列屬性。

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="屬性":::

## <a name="next-steps"></a>後續步驟

瞭解如何 [轉寄警示資訊](how-to-forward-alert-information-to-partners.md)。
