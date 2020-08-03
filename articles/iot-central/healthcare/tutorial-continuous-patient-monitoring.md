---
title: 使用 Azure IoT Central 建立持續性患者監視應用程式 |Microsoft Docs
description: 了解如何使用 Azure IoT Central 應用程式範本建立持續性患者監視應用程式。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 704c56745ad89e9ed2f79e8a863f1d0bc9845bf9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001820"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>教學課程：部署和逐步解說持續性患者監視應用程式範本



本教學課程會以解決方案產生器的形式，示範如何開始部署 IoT Central 持續性患者監視應用程式範本。 您將了解如何部署範本、現成可用的項目，以及您接下來可以執行的動作。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立應用程式範本
> * 逐步執行應用程式範本

## <a name="create-an-application-template"></a>建立應用程式範本

瀏覽至 [Azure IoT Central 應用程式管理員網站](https://apps.azureiotcentral.com/)。 從左側導覽列選取 [建置]  ，然後按一下 [醫療保健]  索引標籤。 

>[!div class="mx-imgBorder"] 
>![應用程式管理員醫療保健](media/app-manager-health.png)

按一下 [建立應用程式]  按鈕，即可開始建立您的應用程式，然後請使用 Microsoft 個人、公司或學校帳戶登入。 系統會將您引導至 [新增應用程式]  頁面。

![建立醫療保健應用程式](media/app-manager-health-create.png)

![建立醫療保健應用程式計費資訊](media/app-manager-health-create-billinginfo.png)

如何建立您的應用程式：

1. Azure IoT Central 會根據您所選取的範本，自動建議應用程式名稱。 您可以接受此名稱，或輸入自訂的應用程式名稱，例如**持續性患者監視**。 Azure IoT Central 也會根據應用程式名稱，為您產生唯一的 URL 前置詞。 您可以依個人需求，將其變更為更好記的 URL 前置詞。

2. 您可以選取要使用「免費」  定價方案還是其中一個「標準」  定價方案來建立應用程式。 您使用免費方案建立的應用程式可以免費試用七天，而且最多可以有五台免費裝置。 您可以隨時在到期前，將應用程式從免費方案移至標準定價方案。 如果您選擇免費方案，則必須輸入連絡資訊，並選擇是否要收到來自 Microsoft 的資訊和提示。 您使用標準方案建立的應用程式最多可支援兩台免費裝置，而且您必須輸入用來計費的 Azure 訂用帳戶資訊。

3. 選取頁面底部的 [建立]  ，即可部署您的應用程式。

## <a name="walk-through-the-application-template"></a>逐步執行應用程式範本

### <a name="dashboards"></a>儀表板

部署應用程式範本之後，會先進入 **Lamna 住院患者監視儀表板**。 Lamna 醫療保健是虛構的醫院系統，包含兩家醫院：Woodgrove 醫院和 Burkville 醫院。 在 Woodgrove 醫院的這個操作員儀表板上，可看到此範本的裝置相關資訊和遙測資料，還有一組可供您採行的命令、作業和動作。 您可以在儀表板上執行以下操作：

* 查看裝置的遙測資料和屬性，例如裝置的**電池電量**或其**連線**狀態。

* 查看**平面圖**和 Smart Vitals Patch 裝置的位置。

* **重新佈建**新患者的 Smart Vitals Patch。

* 查看醫護小組追蹤患者狀態時，可能會看到的**提供者儀表板**範例。

* 變更裝置的 [患者狀態]  ，以註明裝置是否用於住院患者或遠端情境。

>[!div class="mx-imgBorder"] 
>![Lamna 住院患者](media/lamna-in-patient.png)

您也可以按一下 [Go to remote patient dashboard]\(前往遠端患者儀表板\)  ，查看用於 Burkville 醫院的第二個操作員儀表板。 此儀表板含有一組類似的動作、遙測和資訊。 此外可看到多台裝置正在使用中，且能**更新每台裝置的韌體**。

>[!div class="mx-imgBorder"] 
>![Lamna 遠端](media/lamna-remote.png)

在這兩個儀表板上，都能夠透過連結返回此文件。

### <a name="device-templates"></a>裝置範本

如果按一下 [裝置範本]  索引標籤，您會看到範本中有兩種不同的裝置類型：

* **Smart Vitals Patch**：此裝置代表可測量不同生命體徵類型的貼片。 其可用來監控醫院內外的病人。 如果按一下範本，就會發現除了傳送裝置資料 (例如電池電量和裝置溫度) 之外，修補程式也會傳送患者健康資料，例如呼吸速率和血壓。

* **Smart Knee Brace**：此裝置代表患者在接受膝關節更換手術後恢復時，可能會使用到的護膝。 如果按一下此範本，除了裝置資料以外，也會看到各種功能，例如動作範圍和加速。

>[!div class="mx-imgBorder"] 
>![Smart Vitals Patch 裝置範本](media/smart-vitals-device-template.png)

### <a name="device-groups"></a>裝置群組 
裝置群組可讓您以邏輯方式將一組裝置分組，然後執行大量查詢或作業。 

如果您按一下 [裝置群組] 索引標籤，就會看到我們已為應用程式中的每個裝置範本建立一些預設的裝置群組。 您會注意到我們也建立了兩個額外的範例裝置群組，稱為「佈建裝置」和「韌體過期裝置」。 我們將使用這些範例裝置群組作為輸入，以執行某些 [作業](#jobs)。

### <a name="rules"></a>規則

您跳到 [規則] 索引標籤時，會看到應用程式範本有三條規則：

* **護膝高溫**：若智慧護膝的裝置溫度高於 95&deg;F 超過 5 分鐘，就會觸發此規則。 您可以利用此規則向患者和護理小組發出警示，並從遠端將裝置冷卻。

* **跌倒偵測**：如果偵測到患者跌倒，就會觸發此規則。 您可以使用此規則來設定動作，部署作業小組協助跌倒的患者。

* **修補程式電量低**：裝置的電池電量低於 10% 時，就會觸發此規則。 您可以使用此規則來觸發通知，請患者幫裝置充電。

>[!div class="mx-imgBorder"] 
>![護膝高溫規則](media/brace-temp-rule.png)

### <a name="jobs"></a>工作

作業可讓您在一組裝置上執行大量作業，使用[裝置群組](#device-groups)作為輸入。 我們已植入應用程式範本，其中有兩個解決方案操作員可能需要在裝置生命週期的某個時間點執行的範例作業：
* **更新護膝韌體**：此工作會在裝置群組「過期韌體裝置」中尋找裝置，並執行命令以將這些裝置更新為護膝的最新韌體版本。 此範例作業假設裝置具有可接收「更新」命令，並直接從雲端提取韌體檔案的功能。  

* **重新佈建裝置**：如果您有一組最近交還給醫院的裝置，而且需要針對下一組患者重新佈建，可以執行此作業來大量更新您的佈建裝置。 在此情況下，我們會從名為「佈建裝置」的裝置群組中取得所有裝置，並會執行命令以進行佈建。 

### <a name="devices"></a>裝置

按一下 [裝置] 索引標籤，然後選取 [智慧型護膝] 的執行個體。 您會看到三個檢視畫面，其可深入研究所選特定裝置的相關資訊。 在建置裝置的裝置範本時會建立和發佈這些檢視，這表示其會在您連線或模擬的所有裝置上保持一致。

[儀表板] 可概覽來自裝置的遙測資料和屬性，主要供操作員使用。

[屬性] 索引標籤可讓您編輯雲端屬性和讀取/寫入裝置屬性。

[命令] 索引標籤可讓您執行已模型化為裝置範本一部分的命令。

>[!div class="mx-imgBorder"] 
>![護膝檢視](media/knee-brace-dashboard.png)

### <a name="data-export"></a>資料匯出

資料匯出可讓您將 IoT Central 的裝置資料持續匯出至其他 Azure 服務，包括 [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請前往 [系統管理] > [應用程式設定]，然後按一下 [刪除] 以刪除應用程式。

>[!div class="mx-imgBorder"] 
>![刪除應用程式](media/admin-delete.png)

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，瞭解如何建立可連接到 IoT Central 應用程式的提供者儀表板。

> [!div class="nextstepaction"]
> [建置提供者儀表板](howto-health-data-triage.md)