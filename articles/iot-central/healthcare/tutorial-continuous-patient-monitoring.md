---
title: 教學課程 - 使用 Azure IoT Central 建立持續性患者監視應用程式 | Microsoft Docs
description: 在本教學課程中，您會了解如何使用 Azure IoT Central 應用程式範本建立持續性患者監視應用程式。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 1967a2fb5adebe01ef4bff8d58f7832bffe95762
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531265"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>教學課程：部署和逐步解說持續性患者監視應用程式範本

本教學課程會以解決方案產生器的形式，示範如何開始部署 IoT Central 持續性患者監視應用程式範本。 您會了解如何部署和使用範本。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立應用程式範本
> * 逐步執行應用程式範本

## <a name="create-an-application-template"></a>建立應用程式範本

瀏覽至 [Azure IoT Central 應用程式管理員網站](https://apps.azureiotcentral.com/)。 從左側導覽列選取 [建置]，然後選取 [醫療保健] 索引標籤。

:::image type="content" source="media/app-manager-health.png" alt-text="醫療保健應用程式範本":::

選取 [建立應用程式] 按鈕，即可開始建立您的應用程式，然後請使用 Microsoft 個人、公司或學校帳戶登入。 系統會將您引導至 [新增應用程式]  頁面。

![建立醫療保健應用程式](media/app-manager-health-create.png)

![建立醫療保健應用程式計費資訊](media/app-manager-health-create-billinginfo.png)

如何建立您的應用程式：

1. Azure IoT Central 會根據您所選取的範本，自動建議應用程式名稱。 您可以接受此名稱，或輸入自訂的應用程式名稱，例如**持續性患者監視**。 Azure IoT Central 也會根據應用程式名稱，為您產生唯一的 URL 前置詞。 您可以依個人需求，將其變更為更好記的 URL 前置詞。

2. 您可以選取要使用「免費」  定價方案還是其中一個「標準」  定價方案來建立應用程式。 您使用免費方案建立的應用程式可以免費試用七天，而且最多可以有五台免費裝置。 您可以隨時在到期前，將應用程式從免費方案移至標準定價方案。 如果您選擇免費方案，則必須輸入連絡資訊，並選擇是否要收到來自 Microsoft 的資訊和提示。 您使用標準方案建立的應用程式最多可支援兩台免費裝置，而且您必須輸入用來計費的 Azure 訂用帳戶資訊。

3. 選取頁面底部的 [建立]  ，即可部署您的應用程式。

## <a name="walk-through-the-application-template"></a>逐步執行應用程式範本

### <a name="dashboards"></a>儀表板

部署應用程式範本之後，會先進入 **Lamna 住院患者監視儀表板**。 Lamna 醫療保健是虛構的醫院系統，包含兩家醫院：Woodgrove 醫院和 Burkville 醫院。 在 Woodgrove 醫院操作員儀表板上，您可以：

* 查看裝置的遙測資料和屬性，例如裝置的**電池電量**或其**連線**狀態。

* 查看**平面圖**和 Smart Vitals Patch 裝置的位置。

* **重新佈建**新患者的 Smart Vitals Patch。

* 查看醫護小組追蹤患者狀態時，可能會看到的**提供者儀表板**範例。

* 變更裝置的 [患者狀態]  ，以註明裝置是否用於住院患者或遠端情境。

:::image type="content" source="media/lamna-in-patient.png" alt-text="醫療保健應用程式範本":::

您也可以選取 [前往遠端患者儀表板]，查看 Burkville 醫院操作員儀表板。 此儀表板含有一組類似的動作、遙測和資訊。 您也可以查看多個使用中的裝置，並選擇對每個裝置**更新韌體**。

:::image type="content" source="media/lamna-remote.png" alt-text="醫療保健應用程式範本":::

### <a name="device-templates"></a>裝置範本

如果您選取**裝置範本**，您會在範本中看到兩個裝置類型：

* **Smart Vitals Patch**：此裝置代表可測量各種生命體徵的貼片。 其可用來監控醫院內外的患者。 如果選取範本，就會發現貼片會傳送裝置資料 (例如電池電量和裝置溫度)，以及患者健康資料 (例如呼吸速率和血壓)。

* **Smart Knee Brace**：此裝置代表患者在接受膝關節更換手術後恢復時，會使用到的護膝。 如果選取此範本，您會看到裝置資料、動作範圍和加速等功能。

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="醫療保健應用程式範本":::

### <a name="device-groups"></a>裝置群組

使用裝置群組以邏輯方式將一組裝置分組，然後執行大量查詢或作業。

如果您選取 [裝置群組] 索引標籤，您會在應用程式中看到每個裝置範本的預設裝置群組。 同時建立了兩個額外的範例裝置群組，稱為**佈建裝置**和**韌體過期裝置**。 您可以使用這些範例裝置群組作為輸入，在應用程式中執行某些[作業](#jobs)。

### <a name="rules"></a>規則

如果您選取 [規則]，則會在範本中看到三個規則：

* **護膝高溫**：若智慧護膝的裝置溫度高於 95&deg;F 超過 5 分鐘，就會觸發此規則。 使用此規則向患者和護理小組發出警示，並從遠端將裝置冷卻。

* **跌倒偵測**：如果偵測到患者跌倒，就會觸發此規則。 使用此規則來設定動作，部署作業小組協助跌倒的患者。

* **修補程式電量低**：裝置的電池電量低於 10% 時，就會觸發此規則。 使用此規則來觸發通知，請患者幫裝置充電。

:::image type="content" source="media/brace-temp-rule.png" alt-text="醫療保健應用程式範本":::

### <a name="jobs"></a>工作

作業可讓您在一組裝置上執行大量作業，使用[裝置群組](#device-groups)作為輸入。 應用程式範本有兩個操作員可以執行的範例作業：

* **更新護膝韌體**：此工作會在裝置群組**過期韌體裝置**中尋找裝置，並執行命令以將這些裝置更新為最新韌體版本。 此範例作業假設裝置可處理**更新**命令，然後從雲端擷取韌體檔案。  

* **重新佈建裝置**：您有一組最近退回醫院的裝置。 這項作業會尋找**佈建裝置**裝置群組中的裝置，並執行命令以針對下一組患者重新佈建裝置。

### <a name="devices"></a>裝置

選取 [裝置] 索引標籤，然後選取 [智慧型護膝] 的執行個體。 有三個檢視畫面，其可深入研究所選特定裝置的相關資訊。 建置裝置的裝置範本時，即會建立並發佈這類檢視畫面。 因此，這些檢視畫面會在您連接或模擬的所有裝置之間保持一致。

[儀表板] 檢視可概覽來自裝置、供操作員使用的遙測資料和屬性。

[屬性] 索引標籤可讓您編輯雲端屬性和讀取/寫入裝置屬性。

[命令] 索引標籤可讓您在裝置上執行命令。

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="醫療保健應用程式範本":::

### <a name="data-export"></a>資料匯出

資料匯出可讓您將裝置資料持續匯出至其他 Azure 服務，包括 [Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式，請前往 [系統管理] > [應用程式設定]，然後按一下 [刪除] 以刪除應用程式。

:::image type="content" source="media/admin-delete.png" alt-text="醫療保健應用程式範本":::

## <a name="next-steps"></a>後續步驟

請前往下一篇文章，瞭解如何建立可連接到 IoT Central 應用程式的提供者儀表板。

> [!div class="nextstepaction"]
> [建置提供者儀表板](howto-health-data-triage.md)