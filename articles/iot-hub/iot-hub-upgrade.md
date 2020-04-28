---
title: 升級 Azure IoT 中樞 | Microsoft Docs
description: 變更「IoT 中樞」的定價與級別層，以取得更多傳訊和裝置管理功能。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "61440200"
---
# <a name="how-to-upgrade-your-iot-hub"></a>如何升級 IoT 中樞

隨著您的 IoT 解決方案日漸成長，「Azure IoT 中樞」已準備好協助您擴大規模。 「Azure IoT 中樞」提供基本 (B) 和標準 (S) 兩個層級，可配合想要使用不同功能的使用者需求。 在每個層級內都有三個大小 (1、2 及 3)，用來決定每天可傳送的訊息數。

當您有更多裝置且需要更多功能時，有三種方式可調整 IoT 中樞以符合您的需求：

* 在 IoT 中樞內增加單位。 例如，在 B1 IoT 中樞內每增加一個單位可允許每天額外 400,000 則訊息。

* 變更 IoT 中樞的大小。 例如，從 B1 層遷移到 B2 層，以增加每個單位每天可支援的訊息數目。

* 升級至較高層級。 例如，從 B1 層升級至 S1 層，以存取具有相同訊息容量的先進功能。

這些變更全都可以在不中斷現有作業的情況下進行。

如果您想要降級您的 IoT 中樞，您可以移除單位並減少 IoT 中樞的大小，但無法降級為較低的層級。 例如，您可以從 S2 層移至 S1 層，但無法從 S2 層移至 B1 層。 只有一種層級的[Iot 中樞版本](https://azure.microsoft.com/pricing/details/iot-hub/)可以根據 IoT 中樞來選擇。 例如，您建立的 IoT 中樞可以具有多個 S1 單位，但不能具有來自不同版本 (例如 S1 和 B3 或 S1 和 S2) 的混合單位。

這些範例是用來協助您了解如何隨著解決方案的變更，以調整您的 IoT 中樞。 如需有關每一層功能的特定資訊，請務必參閱[Azure IoT 中樞定價](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="upgrade-your-existing-iot-hub"></a>升級現有的 IoT 中樞

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 IoT 中樞。

2. 選取 [定價與級別]****。

   ![定價與級別](./media/iot-hub-upgrade/pricing-scale.png)

3. 若要變更中樞的層級，請選取 [定價與級別層]****。 選取新的層級，然後按一下 [選取]****。

   ![定價與級別層](./media/iot-hub-upgrade/select-tier.png)

4. 若要變更您中樞內的單位數，請在 [IoT 中樞單位]**** 底下輸入新的值。

5. 選取 [儲存]  來儲存變更。

您的 IoT 中樞現已調整完成，而設定則未變更。

基本層 IoT 中樞和標準層 IoT 中樞的最大分割區限制為32。 大部分的 IoT 中樞只需要 4 個分割區。 分割區限制會在建立 IoT 中樞時選擇，就是裝置到雲端訊息數對同時閱讀這些訊息的人員數比例。 當您從基本層遷移到標準層時，這個值保持不變。

## <a name="next-steps"></a>後續步驟

取得有關[如何選擇適合的 IoT 中樞層](iot-hub-scaling.md)的更多詳細資料。