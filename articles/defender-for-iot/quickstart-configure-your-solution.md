---
title: 快速入門：設定您的解決方案
description: 在本快速入門中，您將了解如何使用適用於 IoT 的 Defender 來設定端對端 IoT 解決方案。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: 3ecb2e2022cab80abf6a8692ac8cc3ba54eff1e4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944075"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>快速入門：設定適用於 IoT 的 Azure Defender 解決方案

本文說明如何使用適用於 IoT 的 Defender 來執行 IoT 安全性解決方案的初始設定。

## <a name="what-is-defender-for-iot"></a>什麼是適用於 IoT 的 Defender？

適用於 IoT 的 Defender 可為 Azure 型 IoT 解決方案提供完整的端對端安全性。

使用適用於 IoT 的 Defender 時，您便可以在一個儀表板內監視整個 IoT 解決方案，裡面會顯示您在 Azure 中的所有 IoT 裝置、IoT 平台和後端資源。

在 IoT 中樞上啟用後，適用於 IoT 的 Defender 就會自動識別其他同樣連線至 IoT 中樞，並與 IoT 解決方案相關的 Azure 服務。

除了自動偵測關聯性外，您還可以挑出並選擇要將哪些其他 Azure 資源群組標記為 IoT 解決方案的一部分。

您所做的選擇會讓您可以新增整個訂用帳戶、資源群組或單一資源。

在定義所有資源關聯性後，適用於 IoT 的 Defender 便會利用 Defender 來為這些資源提供安全性建議和警示。

## <a name="add-azure-resources-to-your-iot-solution"></a>將 Azure 資源新增至 IoT 解決方案

若要將新的資源新增至 IoT 解決方案，請執行下列步驟：

1. 在 Azure 入口網站中開啟您的 **IoT 中樞**。
1. 從左側功能表的**安全性**區段中，選取並開啟**設定**，然後選取 [受監視的資源]。
1. 選取**編輯**，然後選取屬於您 IoT 解決方案的受監視資源。
1. 按一下 [新增]  。

恭喜！ 您已將新的資源群組新增至 IoT 解決方案。

現在，適用於 IoT 的 Defender 會監視您剛新增了資源群組，並且在 IoT 解決方案內顯示相關的安全性建議和警示。

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，以了解如何建立安全性模組...

> [!div class="nextstepaction"]
> [建立安全性模組](quickstart-create-security-twin.md)
