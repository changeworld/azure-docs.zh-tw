---
title: 教學課程 - 在 Azure IoT Central 中建立店內分析應用程式
description: 本教學課程說明如何在 IoT Central 中建立店內分析零售應用程式。 您將會建立此應用程式、加以自訂，並新增感應器裝置。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: a15ccd6d67b4819ba5dd6233a2a001015037c404
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165667"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>教學課程：在 Azure IoT Central 中建立店內分析應用程式



本教學課程會說明解決方案建置者該如何建立 Azure IoT Central 店內分析應用程式。 此應用程式範例適用於零售商店。 這個解決方案可滿足常見的商務需求，讓企業得以監視和適應佔用量與環境狀況。

您所建置的應用程式範例會包含三個實際裝置：一個 Rigado Cascade 500 閘道和兩個 RuuviTag 感應器。 本教學課程也會說明如何使用應用程式範本中所包含的模擬佔用量感應器來進行測試。 Rigado C500 閘道會作為應用程式中的通訊中樞。 其會與您商店內的感應器通訊，並管理其與雲端的連線。 RuuviTag 則是環境感應器，會提供包括溫度、濕度和壓力在內的遙測資料。 模擬佔用量感應器可讓企業追蹤商店結帳區內的結帳速度和人數多寡。 

本教學課程內有如何將 Rigado 和 RuuviTag 裝置連線到應用程式的指示。 如果您擁有的是其他閘道和感應器，一樣可以遵循這些步驟來建置應用程式。 本教學課程也會說明如何建立模擬的 RuuviTag 感應器。 如果您沒有實際裝置，模擬感應器可讓您建置應用程式。 

您可以透過三個部分來開發結帳和狀況的監視解決方案：

* 建立應用程式，然後與裝置連線以監視狀況
* 自訂儀表板，讓操作員能夠監視和管理裝置
* 設定資料匯出功能，讓店長能夠執行分析並以視覺方式呈現所產生的深入解析

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 使用 Azure IoT Central **店內分析 - 結帳** 範本來建立零售商店應用程式
> * 自訂應用程式設定
> * 建立和自訂 IoT 裝置範本
> * 將裝置連線到應用程式
> * 新增規則和動作來監視狀況

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程系列，您需要：
* 建議要有 Azure 訂用帳戶。 您可以選擇使用免費的 7 天試用版。 如果您沒有 Azure 訂用帳戶，則可以在 [Azure 註冊頁面](https://aka.ms/createazuresubscription)上建立一個。
* 一個閘道裝置和兩個環境感應器的存取權 (您可以選擇使用模擬裝置，如本教學課程所述)
* 適用於所用裝置的裝置範本 (本教學課程使用的所有裝置皆有提供範本)

## <a name="create-an-application"></a>建立應用程式
在本節中，您會從範本建立新的 Azure IoT Central 應用程式。 您會在整個教學課程系列中，使用此應用程式來建置完整的解決方案。

若要建立新的 Azure IoT Central 應用程式：

1. 瀏覽至 [Azure IoT Central 的應用程式管理員](https://aka.ms/iotcentral)網站。

1. 如果您有 Azure 訂用帳戶，請使用您存取時所用的認證來登入，否則請使用 Microsoft 帳戶來登入：

    ![輸入您的組織帳戶](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. 若要開始建立新的 Azure IoT Central 應用程式，請選取 [新增應用程式]  。

1. 選取 [零售]  。  [零售] 頁面會顯示數個零售應用程式範本。

若要建立新的店內分析結帳應用程式：  

1. 選取 [店內分析 - 結帳]  應用程式範本。 針對本教學課程中所用的所有裝置 (RuuviTag 感應器除外)，其裝置範本皆包含在此範本中。 此範本也會提供操作員儀表板，以便用來監視結帳和環境狀況與裝置狀態。 

1. (選擇性) 選擇易記的 [應用程式名稱]  。 此應用程式的建置基礎是名為 Contoso 的虛構零售商店。 本教學課程會使用「Contoso 結帳」  作為 [應用程式名稱]  。 應用程式範本的製作基礎則是虛構公司 Northwind。 在本教學課程中，您會使用 Contoso 來了解如何自訂應用程式。

    > [!NOTE]
    > 如果您使用易記的 **應用程式名稱** ，您仍然必須針對應用程式的 **URL** 使用唯一值。

1. 如果您有 Azure 訂用帳戶，請輸入您的 [目錄]、[Azure 訂用帳戶] 和 [區域]  。 如果您沒有訂用帳戶，則可以啟用 [7 天免費試用]  並填妥必要的連絡人資訊。  

    如需關於目錄和訂用帳戶的詳細資訊，請參閱[建立應用程式快速入門](../core/quick-deploy-iot-central.md)。

1. 選取 [建立]  。

    ![Azure IoT Central 的建立應用程式頁面](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central 的建立應用程式計費資訊](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>自訂應用程式設定

身為建置者，您可以變更數個設定，以自訂應用程式的使用者體驗。 在本節中，您將選取預先定義的應用程式主題。 (選擇性) 您會了解如何建立自訂主題，並更新應用程式影像。 自訂主題可讓您設定應用程式瀏覽器色彩、瀏覽器圖示，以及在刊頭中出現的應用程式標誌。

若要選取預先定義的應用程式主題：

1. 選取刊頭上的 [設定]  。

    ![Azure IoT Central 應用程式設定](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. 選取新的 [主題]  。

3. 選取 [儲存]  。

您可以建立自訂主題，而不是使用預先定義的主題。 如果您想要使用一組影像範例來自訂應用程式並完成本教學課程，請下載 [Contoso 影像範例](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)。

若要建立自訂主題：

1. 展開左窗格 (如果尚未展開)。

    ![Azure IoT Central 左窗格](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. 選取 [系統管理] > [自訂應用程式]  。

1. 使用 [變更]  按鈕來選擇要上傳作為 **應用程式標誌** 的影像。 (選擇性) 指定 [標誌替代文字]  的值。 

1. 使用 [變更]  按鈕，以選擇將會出現在瀏覽器索引標籤上的 **瀏覽器圖示** 影像。

1. (選擇性) 藉由新增 HTML 十六進位色彩代碼來取代預設的 [瀏覽器色彩]  。 針對 [標頭]  ，新增 [#008575]  。  針對 [輔色]  ，新增 [#A1F3EA]  。 

1. 選取 [儲存]  。 

    ![Azure IoT Central 自訂標誌](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    儲存之後，應用程式便會更新瀏覽器色彩、刊頭中的標誌和瀏覽器圖示。 

    ![Azure IoT Central 已更新應用程式設定](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

若要更新應用程式影像：

1. 選取 [系統管理] > [應用程式設定]  。

1. 使用 [選取影像]  按鈕來選擇要上傳作為應用程式影像的影像。 此影像會出現在 IoT Central 應用程式管理員 [我的應用程式]  頁面中的應用程式圖格上。

1. 選取 [儲存]  。

1. (選擇性) 在 [Azure IoT Central 應用程式管理員](https://aka.ms/iotcentral)網站上瀏覽至 [我的應用程式]  檢視。 應用程式圖格便會顯示已更新的應用程式影像。

    ![Azure IoT Central 自訂應用程式影像](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>建立裝置範本
身為建置者，您可以建立裝置範本，以便讓您自己和應用程式操作員能夠設定和管理裝置。 藉由建置自訂範本、匯入現有範本檔案或從 Azure IoT 裝置目錄匯入範本，即可建立範本。 在建立並自訂裝置範本後，請使用該範本將實際裝置連線到應用程式。 (選擇性) 使用裝置範本來產生用於測試的模擬裝置。

**店內分析 - 結帳** 應用程式範本有適用於數個裝置的裝置範本。  在您於應用程式中所使用的三個裝置之中，其中兩個有適用的裝置範本。 RuuviTag 裝置範本則未包含在 **店內分析 - 結帳** 應用程式範本中。 在本節中，您要將 RuuviTag 感應器的裝置範本新增至應用程式。

若要將 RuuviTag 裝置範本新增至應用程式：

1. 在左窗格中選取 [裝置範本]  。

1. 選取 [+ 新增]  以建立新的裝置範本。

1. 在 Azure IoT 裝置目錄中尋找並選取 [RuuviTag]  感應器裝置範本。 

1. 完成時，選取 [下一步:  自訂]。

    ![醒目提示 [下一步: 自訂] 按鈕的螢幕擷取畫面。](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. 選取 [建立]  。 應用程式便會新增 RuuviTag 裝置範本。

1. 在左窗格上選取 [裝置範本]  。 此頁面會顯示應用程式範本所包含的所有裝置範本，以及您剛才新增的 RuuviTag 裝置範本。

    ![Azure IoT Central RuuviTag 感應器裝置範本](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>自訂裝置範本
您可以透過三種方式在應用程式中自訂裝置範本。 首先，您可以藉由變更裝置功能，來自訂裝置中的原生內建介面。 例如，針對溫度感應器，您可以變更各項詳細資料，例如溫度介面的顯示名稱、資料類型、測量單位，以及最小和最大運作範圍。 

其次，您可以藉由新增雲端屬性來自訂裝置範本。 雲端屬性不是內建裝置功能的一部分。 雲端屬性是 Azure IoT Central 應用程式針對裝置所建立、儲存和關聯的自訂資料。 舉例來說，雲端屬性可以是計算得到的值，或是您想要與一組裝置產生關聯的中繼資料 (例如位置)。 

第三，您可以藉由建置自訂檢視來自訂裝置範本。 檢視讓操作員有辦法以視覺方式呈現裝置的遙測資料和中繼資料，例如裝置的計量和健康情況。

在這裡，您會使用前兩種方式來自訂 RuuviTag 感應器的裝置範本。 如需如何為感應器建立檢視的相關資訊，請參閱[將模擬裝置新增至 IoT Central 應用程式](../core/quick-create-simulated-device.md)快速入門。

若要自訂 RuuviTag 裝置範本的內建介面：

1. 在左窗格中選取 [裝置範本]  。 

1. 選取 RuuviTag 感應器的範本。 

1. 隱藏左窗格。 範本的 [摘要] 檢視會顯示裝置功能。

    ![Azure IoT Central RuuviTag 裝置範本摘要檢視](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. 選取 RuuviTag 裝置範本功能表中的 [自訂]  。 

1. 在功能清單中捲動，並尋找 `humidity` 遙測類型。 這是 [顯示名稱]  值為 [濕度]  且可供編輯的資料列項目。

在下列步驟中，您會自訂 RuuviTag 感應器的 `humidity` 遙測類型。 (選擇性) 自訂其他某些遙測類型。

針對 `humidity` 遙測類型，進行下列變更：

1. 選取 [展開]  控制項，以展開資料列的結構描述詳細資料。

1. 將 [顯示名稱]  值從 [濕度]  更新為自訂值，例如 [相對濕度]  。

1. 將 [語義類型]  選項從 [無]  變更為 [濕度]  。  (選擇性) 在展開的結構描述檢視中設定濕度遙測類型的結構描述值。 結構描述設定可讓您針對感應器所追蹤的資料，建立詳細的驗證需求。例如，您可以設定指定介面的最小和最大運作範圍值。

1. 選取 [儲存]  來儲存變更。

    ![顯示 [自訂] 畫面並醒目提示 [儲存] 按鈕的螢幕擷取畫面。](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

若要將雲端屬性新增至應用程式中的裝置範本：

1. 在 RuuviTag 裝置範本功能表中，選取 [雲端屬性]  。

1. 選取 [新增雲端屬性]  。 

指定下列值來建立自訂屬性，以儲存每個裝置的位置：

1. 輸入「位置」  作為 [顯示名稱]  的值。 這個值會自動複製到 [名稱]  欄位，這是容易記得的屬性名稱。 您可以使用複製的值或加以變更。

1. 在 [結構描述]  下拉式清單中，選取 [字串]  。 字串類型可讓您根據範本，將位置名稱字串與任何裝置產生關聯。 例如，您可以將商店內的某個區域與每個裝置產生關聯。 (選擇性) 您可以將屬性的 [語義類型]  設定為 [位置]  ，以自動將 [結構描述]  設定為 [地理位置點]  。 這可讓您將 GPS 座標與裝置產生關聯。 

1. 將 [最小長度]  設定為 [2]  。 

1. 將 [修剪空白]  設定為 [開啟]  。

1. 選取 [儲存]  以儲存自訂雲端屬性。

    ![Azure IoT Central RuuviTag 裝置範本自訂](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. 選取 [發佈]  。 

    發佈裝置範本可讓應用程式操作員看到此範本。 在發佈範本後，請使用該範本來產生用於測試的模擬裝置，或將實際裝置連線到應用程式。 如果您已經將裝置連線到應用程式，則發佈自訂範本會對裝置推送變更。

## <a name="add-devices"></a>新增裝置
在建立並自訂好裝置範本後，就可以新增裝置了。 

在本教學課程中，您會使用下面這組實際裝置和模擬裝置來建置應用程式：
- 一個實際的 Rigado C500 閘道
- 兩個實際的 RuuviTag 感應器
- 一個模擬的 **佔用量** 感應器。 模擬感應器會包含在應用程式範本中，因此不需要建立。 

> [!NOTE]
> 如果您沒有實際裝置，仍可藉由建立模擬的 RuuviTag 感應器來完成本教學課程。 下列指示包含用來建立模擬 RuuviTag 的步驟。 您不需要建立模擬閘道。

完成下列兩篇文章中的步驟，與實際的 Rigado 閘道和 RuuviTag 感應器連線。 完成之後，請返回本教學課程。 因為您已經在本教學課程中建立好裝置範本，所以不需要在下列這組指示中再次建立。

- 若要與 Rigado 閘道連線，請參閱[將 Rigado Cascade 500 連線到 Azure IoT Central 應用程式](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)。
- 若要與 RuuviTag 感應器連線，請參閱[將 RuuviTag 感應器連線到 Azure IoT Central 應用程式](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json)。 如有需要，您也可以使用這兩個指示來建立兩個模擬感應器。

## <a name="add-rules-and-actions"></a>新增規則和動作
在使用 Azure IoT Central 應用程式中的感應器來監視狀況的過程中，您可以建立規則以在符合特定狀況時執行動作。 規則會與裝置範本以及一或多個裝置相關聯，並包含根據裝置的遙測資料或事件所必須符合的狀況。 規則也會有一或多個相關聯的動作。 這些動作可能包括傳送電子郵件通知，或觸發 Webhook 動作以將資料傳送到其他服務。 **店內分析 - 結帳** 應用程式範本包含應用程式裝置的一些預先定義規則。

在本節中，您會建立新的規則，以根據 RuuviTag 感應器的遙測資料來檢查最大相對濕度程度。 您會在規則中新增動作，以便在濕度超過最大值時，讓應用程式傳送電子郵件。 

若要建立規則： 

1. 展開左窗格。

1. 選取 [規則]  。

1. 選取 [+ 新增]  。

1. 輸入「濕度程度」  作為規則的名稱。 

1. 在 [範圍]  中選擇 RuuviTag 裝置範本。 您定義的規則會根據該範本套用到所有感應器。 (選擇性) 您可以建立只將規則套用至所定義感應器子集的篩選條件。 

1. 選擇 `Relative humidity` 作為 [遙測資料]  。 這是您在上一個步驟中自訂的裝置功能。

1. 選擇 `Is greater than` 作為 [運算子]  。 

1. 為您的環境輸入一般的室內濕度程度範圍上限來作為 [值]  。 例如，輸入「65」  。 您已為規則設定狀況，讓其在任何 RuuviTag 實際或模擬感應器中的相對濕度超過此值時發生。 視您環境中的一般濕度範圍而定，您可能需要上調或下調此值。  

   ![Azure IoT Central 新增規則狀況](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

若要將動作新增至規則：

1. 選取 [+ 電子郵件]  。 

1. 輸入「高濕度通知」  作為此動作的易記 [顯示名稱]  。 

1. 在 [收件人]  中，輸入與您帳戶相關聯的電子郵件地址。 如果您使用其他電子郵件，則使用的地址必須適用於已新增至應用程式的使用者。 該使用者也必須登入再登出至少一次。

1. (選擇性) 輸入附註來納入電子郵件的文字。

1. 選取 [完成]  來完成此動作。

   ![Azure IoT Central 將動作新增至規則](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. 選取 [儲存]  以儲存並啟用新的規則。 

    在幾分鐘內，指定的電子郵件帳戶應該就會開始收到電子郵件。 每當感應器指出濕度程度超過狀況中的值時，應用程式就會傳送電子郵件。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已了解如何：

* 使用 Azure IoT Central **店內分析 - 結帳** 範本來建立零售商店應用程式
* 自訂應用程式設定
* 建立和自訂 IoT 裝置範本
* 將裝置連線到應用程式
* 新增規則和動作來監視狀況

由於您已建立好 Azure IoT Central 狀況監視應用程式，接下來建議進行的步驟如下：

> [!div class="nextstepaction"]
> [自訂操作員儀表板](./tutorial-in-store-analytics-customize-dashboard.md)
