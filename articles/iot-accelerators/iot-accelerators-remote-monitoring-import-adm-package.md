---
title: 遠端監視解決方案匯入套件-Azure |Microsoft Docs
description: 本文說明如何將自動裝置管理套件匯入至遠端監視解決方案加速器
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "61443272"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>將自動裝置管理套件匯入至遠端監視解決方案加速器

自動裝置管理設定會定義要部署至裝置群組的組態變更。 本文假設貴組織的開發人員已經建立自動裝置管理組態。 若要深入了解開發人員如何建立組態，請參閱下列其中一篇 IoT Hub 操作說明文章：

- [使用 Azure 入口網站大規模設定和監視 IoT 裝置](../iot-hub/iot-hub-auto-device-config.md)
- [使用 Azure CLI 大規模設定和監視 IoT 裝置](../iot-hub/iot-hub-auto-device-config-cli.md)

開發人員會在開發環境中建立及測試自動裝置管理組態。 當您準備就緒時，即可將組態匯入至遠端監視解決方案加速器。

## <a name="export-a-configuration"></a>匯出組態

使用 Azure 入口網站，從開發環境匯出自動裝置管理組態：

1. 在 Azure 入口網站中，瀏覽至您用來開發和測試 IoT 裝置的 IoT 中樞。 按一下 [IoT 裝置組態]****：

    [![IoT 裝置設定](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. 按一下您想要使用的組態。 [裝置組態詳細資料]**** 頁面隨即顯示：

    [![IoT 裝置設定詳細資料](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. 按一下 [下載組態檔案]****：

    [![下載設定檔](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. 將 JSON 檔案儲存為名為 **configuration.json** 的本機檔案。

現在您有一個包含自動裝置管理組態的檔案。 在下一節中，您會將此組態當作套件匯入遠端監視解決方案中。

## <a name="import-a-package"></a>匯入套件

請遵循下列步驟將自動裝置管理組態當作套件匯入您的解決方案中：

1. 在遠端監視 Web UI 中瀏覽至 [套件]**** 頁面：![套件頁面](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. 按一下 [+ 新增套件]****，選擇 [組態]**** 作為套件類型，然後按一下 [瀏覽]**** 以選取您在上一節中儲存的 **configuration.json** 檔案：

    ![選取組態](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. 按一下 [上傳]**** 將套件新增至遠端監視解決方案：

    ![上傳的套件](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

您現在已上傳自動裝置管理組態作為套件。 在 [部署]**** 頁面上，您可以將此套件部署到已連線的裝置。

## <a name="next-steps"></a>後續步驟

既然您已了解如何建立組態套件以及從遠端監視解決方案匯入它，下一個步驟是了解如何[管理已連線到遠端監視的大量裝置](iot-accelerators-remote-monitoring-bulk-configuration-update.md)。
