---
title: Azure 認證的裝置程式 |Microsoft Docs
description: 瞭解 Azure 認證裝置方案。
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9580f1c7a29b19bb926a7079b95cb3ed0b314d05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579639"
---
# <a name="what-is-the-azure-certified-device-program"></a>什麼是 Azure 認證裝置程式？

Azure 認證裝置方案可確保客戶解決方案能與您的 Azure 服務緊密搭配運作。 此程式會使用工具、服務和 marketplace，以裝置和解決方案產生器的群體來共用產業知識和最佳作法。

此程式的設計目的是：

- **提供客戶信心：** 客戶可以安心地購買 Microsoft 認證的裝置，以便與 Azure 服務搭配使用。

- **協助客戶尋找適合其解決方案的裝置：** 裝置構建者可以在認證程式中發佈其裝置的獨特功能。 客戶可以輕鬆地找到符合其需求的產品。

- **升級認證的裝置：** 裝置產生器會提高可見度、與客戶聯絡，以及使用 Azure 認證裝置品牌。

這篇文章說明如何：

- 將您的公司上線至 Azure 認證裝置方案。
- 判斷適用于您的裝置的認證。
- 尋找程式需求和其他資源，協助您開始進行測試。
- 使用 Azure 認證裝置入口網站來驗證您的裝置。

## <a name="onboarding"></a>登入

若要在 [Azure 認證裝置入口網站](https://aka.ms/acdp)上認證您的裝置，您必須完成下列步驟：

1. 確定您的公司有使用工作或學校租使用者的 Azure Active Directory 帳戶。
2. 使用您的帳戶加入 [Microsoft 合作夥伴網路 (MPN) ](https://partner.microsoft.com/) 。
3. 在您加入 MPN 之後，請登入 [Azure 認證裝置入口網站](https://aka.ms/acdp) 。
4. 檢查並簽署公司設定檔頁面面上的[方案合約](https://aka.ms/acdagreement)

### <a name="company-profile"></a>公司設定檔

若要在 Azure 認證裝置入口網站中管理公司的設定檔，請選取 [ **公司設定檔**]。 公司設定檔包含公司 URL、電子郵件地址和標誌。 繼續進行任何認證作業之前，請先接受此頁面上的方案合約。

Azure 認證裝置目錄中的 [裝置描述] 頁面會使用公司設定檔資訊。

## <a name="choose-the-certification"></a>選擇認證

有三種不同的認證，每個都著重于提供不同的客戶價值。 根據您所建立的裝置類型和目標物件，您可以選擇最適用的認證或認證：

### <a name="azure-certified-device"></a>Azure 認證裝置

_Azure 認證裝置認證_ 會驗證裝置是否可透過裝置布建服務（ (DPS) ）連線 Azure IoT 中樞並安全地布建。 此認證會反映裝置的功能和互通性，這是更先進認證的必要基準。

- 若要深入瞭解，請參閱 [認證需求](https://aka.ms/acdrequirements)。
- 若要深入瞭解如何使用 DPS 將裝置連線至 Azure IoT 中樞，請參閱布建 [裝置總覽](../iot-dps/about-iot-dps.md)。

### <a name="iot-plug-and-play"></a>IoT 隨插即用

_IoT 隨插即用認證_（Azure 認證裝置認證的累加式認證）可簡化不使用自訂裝置程式碼建立裝置的程式。 IoT 隨插即用可讓硬體夥伴建立裝置，以根據 Azure IoT Central 和協力廠商解決方案，輕鬆地與雲端解決方案整合。

- 若要深入瞭解，請參閱 [認證需求](https://aka.ms/acdiotpnprequirements)。
- 若要深入瞭解如何準備裝置以進行 IoT 隨插即用測試，請參閱 [如何認證 IoT 隨插即用裝置](howto-certify-device.md)。

### <a name="edge-managed"></a>Edge 管理

_Edge 受控_憑證是 Azure 認證裝置認證的漸進式認證，著重于執行 Windows、LINUX 或 Rto 的 Azure IoT 裝置的裝置管理標準。 目前，此方案認證著重于模組部署與管理的 Edge 執行時間相容性。

> [!TIP]
> 此程式之前稱為 _IoT Edge 認證計畫_。

- 若要深入瞭解，請參閱 [認證需求](https://aka.ms/acdedgemanagedrequirements)。
- 若要深入瞭解 IoT Edge，請參閱 [IoT Edge 總覽](../iot-edge/about-iot-edge.md)。
- 若要深入瞭解支援的作業系統和容器，請參閱 [IoT Edge 支援的系統](../iot-edge/support.md)。

## <a name="use-the-azure-certified-device-portal"></a>使用 Azure 認證裝置入口網站

本節摘要說明如何使用 [Azure 認證裝置入口網站](https://certify.azure.com)。 若要深入瞭解，請參閱 [開始使用入口網站指南](https://aka.ms/acdhelp)。

若要在 Azure 認證裝置程式中認證裝置，請完成下列四個步驟：

1. 提供裝置詳細資料
2. 測試裝置
3. 提交並完成評論
4. 發佈至 Azure 認證裝置目錄 (選擇性) 

### <a name="provide-device-details"></a>提供裝置詳細資料

針對您想要認證的每個裝置，請使用認證入口網站中的表單，記錄裝置硬體、設定指示和行銷資料的詳細資料：

- **裝置資訊：** 收集裝置的相關資訊，例如其名稱、描述、硬體詳細資料和作業系統。
- 入門**指南**：客戶可以用來快速使用您的產品的 PDF 檔。 [範例範本](https://aka.ms/GSTemplate) 可供使用。
- **行銷詳細資料：** 為您的裝置提供客戶就緒的行銷資訊，例如圖片和散發者資訊。
- **其他產業認證：** 選擇性區段，可讓您提供裝置所擁有之任何其他認證的相關資訊。

### <a name="test-the-device"></a>測試裝置

這個階段會與您的裝置互動，並在裝置使用 DPS 連接到 IoT 中樞之後執行一系列的測試。 完成時，您可以使用裝置測試結果來查看一組記錄檔。

認證入口網站有關于如何連線至用於測試的 IoT 中樞實例的指示。 您可以透過任何 [支援的證明方法](https://aka.ms/acdAttestation)來建立 DPS 連接。

Azure 認證裝置團隊可以聯繫裝置產生器，以進一步手動驗證裝置。

### <a name="submit-and-publish"></a>提交併發行

最後一個必要階段是提交專案以供審查。 此步驟會通知 Azure 認證裝置團隊成員查看您的專案是否完整，包括裝置和行銷詳細資料，以及入門指南。 小組成員可能會在核准之前，先與您提出問題的公司電子郵件地址或編輯要求聯繫。

如果您的裝置需要進一步的手動驗證作為認證的一部分，您此時將會收到通知。

當裝置通過認證時，您可以選擇使用 [產品摘要] 頁面中的 [ **發佈至類別目錄** ] 功能，將您的產品詳細資料發佈至 Azure 認證裝置目錄。

## <a name="if-you-have-questions"></a>如果您有任何疑問

[iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question)如果您有任何關於認證方案、認證入口網站或 Azure 認證裝置目錄的問題，請洽詢團隊。

## <a name="next-steps"></a>後續步驟

既然您已大致瞭解 Azure 認證裝置方案，建議的下一個步驟是瞭解 [如何認證 IoT 隨插即用裝置](howto-certify-device.md)。
