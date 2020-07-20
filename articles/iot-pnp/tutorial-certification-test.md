---
title: 認證您的 IoT 隨插即用預覽版裝置 | Microsoft Docs
description: 本教學課程說明如何將您的產品資訊新增至 Azure IoT 認證裝置目錄、將您的裝置連線至 Azure IoT 認證服務，然後執行 IoT 隨插即用認證測試。
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550157"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>教學課程：認證您的 IoT 隨插即用預覽版裝置

若要在 [Azure IoT 認證裝置目錄](https://aka.ms/iotdevcat)中發佈 IoT 隨插即用預覽版裝置，該裝置必須通過一組認證測試。 請使用 [Azure IoT 認證](https://aka.ms/ACFI)入口網站提交您的裝置以進行認證。 [Azure IoT 認證服務](https://aka.ms/azure-iot-aics)會執行認證測試。

在此教學課程中，您將會學到：

> [!div class="checklist"]
> * IoT 隨插即用認證的需求為何。
> * 如何將您的產品名稱和資訊新增至入口網站。
> * 如何連接及探索 IoT 隨插即用介面。
> * 如何檢閱 IoT 隨插即用介面和執行認證測試。
> * 如何將已認證的 IoT 隨插即用裝置發佈至目錄。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要：

* Microsoft 合作夥伴中心帳戶。
* Microsoft 合作夥伴網路識別碼。

如需詳細資訊，請參閱[如何上線至 Azure IoT 認證入口網站](howto-onboard-portal.md)。

## <a name="certification-requirements"></a>認證需求

若要認證您的 IoT 隨插即用裝置，您的裝置必須符合下列需求：

* 您的 IoT 隨插即用裝置程式碼必須安裝在裝置上。
* 您的 IoT 隨插即用裝置程式碼是使用 Azure IoT SDK 建置的。
* 您的裝置程式碼必須支援 [Azure IoT 中樞裝置佈建服務](../iot-dps/about-iot-dps.md)。
* 您的裝置程式碼必須實作[裝置資訊介面](concepts-common-interfaces.md)。
* 功能模型和裝置程式碼可與 IoT Central 搭配運作。

目前在目錄中的所有裝置都會被視為預先認證的 IoT 隨插即用裝置。 這並不保證 IoT 隨插即用軟體元件 (例如 SDK 和數位對應項定義語言) 最終產品的品質和合規性。

所有預先認證的 IoT 隨插即用裝置都必須根據 Microsoft 所提供的最終版認證需求和軟體元件，重新認證正式運作的 IoT 隨插即用。

## <a name="add-product-name"></a>新增產品名稱

**產品**是客戶可購買之產品型號的易記名稱。 若要新增產品：

1. 登入 [Azure IoT 認證入口網站](https://aka.ms/ACFI)。
1. 從左側功能表移至入口網站中的 [產品]  頁面，然後選取[+ 新增]  。
1. 輸入您的易記產品名稱，然後選取 [建立]  。 在此處輸入的名稱與裝置目錄中顯示的名稱不同。

## <a name="add-product-information"></a>新增產品資訊

在入口網站中成功建立產品之後，產品會顯示在 [產品]  頁面中。 若要新增產品資訊：

1. 在產品資料行的 [產品]  頁面上，選取已建立的產品連結。 其狀態應為草稿狀態。
1. 選取 [產品資訊]  標題旁的 [編輯]  連結。 在 [產品資訊] 頁面中輸入產品的相關資訊，並確定您已完成所有必要欄位。
1. 選取 [儲存]  。 只有在完成所有必要欄位後，才會出現 [儲存]  按鈕。 如果欄位未完成，則按鈕會顯示為 [儲存並於稍後完成]  。
1. 檢閱您輸入的內容。 完成所有必要欄位，以將裝置發佈至裝置目錄。 您隨時都可以按一下 [產品資訊]  標題旁的 [編輯]  連結，以回過頭在產品詳細資料頁面中編輯產品資訊。

## <a name="connect-and-discover-interfaces"></a>連接及探索介面

若要執行認證測試，請將您的裝置連線至入口網站中可用的 [Azure IoT 認證服務](https://aka.ms/azure-iot-aics) (AIC)。

這些步驟是執行認證測試的一次性步驟，不需要變更您的產品裝置程式碼。 若要開始執行，請依照下列步驟連線至 AICS：

1. 使用您的合作夥伴中心帳戶登入入口網站。
1. 按一下 [連接 + 測試]  ，開始進行認證流程。
1. 選擇[驗證方法](../iot-dps/concepts-security.md#attestation-mechanism)，以使用 [Azure IoT 中樞裝置佈建服務](../iot-dps/about-iot-dps.md)將裝置佈建到 AIC。
   * 如果您使用的是 [X.509 憑證](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)，請上傳您產生的 X.509 憑證。 您可以檢閱範例程式碼以了解如何使用 X.509 憑證：[C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c)、[C#](../iot-hub/iot-hub-security-x509-get-started.md)。
   * 如果您使用的是[對稱金鑰](../iot-dps/concepts-symmetric-key-attestation.md)，請將對稱金鑰複製並貼到您的裝置程式碼中。
   * 目前不支援 TPM 驗證方法。
1. 將下列產生的識別碼複製並貼到您的裝置程式碼中。
   * [註冊識別碼](../iot-dps/use-hsm-with-sdk.md)
   * [DPS 識別碼](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS 端點](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. 當您的裝置程式碼已建置並部署到裝置後，請選取 [連線]  以探索 IoT 隨插即用介面。
1. 成功連線至 AIC 後，請選取 [下一步]  以檢閱探索到的 IoT 隨插即用介面。

## <a name="run-tests-and-publish-the-device"></a>執行測試和發佈裝置

在 [檢閱] 頁面上，您可以檢閱探索到的 IoT 隨插即用介面。 使用此頁面可檢查在介面中實作的基本項目是否正確顯示。 您也可以檢查介面的位置。

1. 確定已輸入必要欄位的承載輸入。 這些欄位包含指定介面之命令基本項目的承載資訊。
1. 輸入所有必要的資訊後，請選取 [下一步]  。
1. 若要執行已實作的 IoT 隨插即用介面的測試，請選取 [執行測試]  。
1. 所有測試都會自動執行。 如果有任何測試失敗，請選取 [檢視記錄]  以檢視來自 AIC 的錯誤訊息，以及傳送至 Azure IoT 中樞的原始遙測資料。
1. 若要完成認證測試，請選取 [完成]  。
1. 將已認證的 IoT 隨插即用裝置發佈至目錄。 若要將已認證的裝置新增至目錄，請選取工具列上的 [新增至目錄]  。 如果 [新增至目錄]  呈現為灰色，則表示產品資訊不完整或測試失敗。 
1. 選取 [已認證並在目錄中] 連結，以檢視您在裝置目錄中已發佈的裝置。

## <a name="next-steps"></a>後續步驟

您已了解如何認證 IoT 隨插即用裝置，建議執行的下一個步驟是深入了解如何管理功能模型：

> [!div class="nextstepaction"]
> [管理模型](./howto-manage-models.md)
