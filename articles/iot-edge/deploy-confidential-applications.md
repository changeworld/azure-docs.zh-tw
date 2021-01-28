---
title: 以 Azure IoT Edge 模組形式的機密應用程式
description: 使用 Open 記憶體保護區 SDK 和 API 撰寫機密應用程式，並將其部署為機密運算的 IoT Edge 模組
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kgremban
ms.openlocfilehash: e63397176cae87178cfc5cc343aeed6f76099be2
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956464"
---
# <a name="confidential-computing-at-the-edge"></a>邊緣的機密計算

Azure IoT Edge 支援在裝置的安全記憶體保護區內執行的機密應用程式。 加密可在傳輸或待用時提供資料的安全性，但記憶體保護區會在使用中提供資料和工作負載的安全性。 IoT Edge 支援 Open 記憶體保護區作為開發機密應用程式的標準。

安全性一直是物聯網 (IoT) 的重要焦點，因為 IoT 裝置通常會在世界各地，而不是在私用設備內受到保護。 這種風險會讓裝置面臨篡改和偽造的風險，因為不正確的執行者實際上可以存取這些裝置。 IoT Edge 裝置有更多的信任和完整性需求，因為它們允許在邊緣執行機密工作負載。 不同于常見的感應器和傳動器，這些智慧型邊緣裝置可能會公開先前只在受保護的雲端或內部部署環境中執行的機密工作負載。

[IoT Edge 的安全性管理員](iot-edge-security-manager.md)解決了一段機密運算挑戰。 安全性管理員會使用硬體安全模組 (HSM) 來保護 IoT Edge 裝置的身分識別工作負載和持續的進程。

機密運算的另一個層面是保護在邊緣使用的資料。 *受信任的執行環境* (t) 是處理器上安全的隔離環境，有時也稱為 *記憶體保護區*。 *機密應用* 程式是在記憶體保護區中執行的應用程式。 由於記憶體保護區的本質，機密應用程式會受到保護，以防止在主要處理器或在中執行的其他應用程式。

## <a name="confidential-applications-on-iot-edge"></a>IoT Edge 上的機密應用程式

機密應用程式會在傳輸和待用時加密，而且只會解密以在受信任的執行環境內執行。 此標準適用于部署為 IoT Edge 模組的機密應用程式。

開發人員會建立機密應用程式，並將其封裝為 IoT Edge 模組。 應用程式會先經過加密，再推送至容器登錄。 在 IoT Edge 裝置上啟動模組之前，應用程式會在 IoT Edge 部署程式中保持加密狀態。 當機密應用程式在裝置的範圍內時，就會解密並開始執行。

![圖表-機密應用程式會在 IoT Edge 模組內加密，直到部署到安全記憶體保護區為止](./media/deploy-confidential-applications/confidential-applications-encrypted.png)

IoT Edge 的機密應用程式是 [Azure 機密計算](../confidential-computing/overview.md)的邏輯擴充。 在雲端的安全記憶體保護區內執行的工作負載，也可以部署在邊緣的安全記憶體保護區內執行。

## <a name="open-enclave"></a>Open Enclave

[Open 記憶體保護區 SDK](https://openenclave.io/sdk/)是開放原始碼專案，可協助開發人員建立多個平臺和環境的機密應用程式。 Open 記憶體保護區 SDK 會在裝置的受信任執行環境中運作，而 Open 記憶體保護區 API 則是做為 t-sql 和非 t 處理環境之間的介面。

Open 記憶體保護區支援多個硬體平臺。 記憶體保護區的 IoT Edge 支援目前需要開放的可攜性的 t-sql 作業系統 (OP-t o) 。 若要深入瞭解，請參閱 [Open 記憶體保護區 SDK FOR OP-t a OS](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/OP-TEE/Introduction.md)。

Open 記憶體保護區存放庫也包含可協助開發人員開始使用的範例。 如需詳細資訊，請選擇其中一篇簡介文章：

* [在 Linux 上建立開放式記憶體保護區 SDK 範例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md)
* [在 Windows 上建立開放式記憶體保護區 SDK 範例](https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesWindows.md)

## <a name="hardware"></a>硬體

目前， [TrustBox By Scalys](https://scalys.com/trustbox-industrial/) 是唯一支援製造商服務合約的裝置，可將機密應用程式部署為 IoT Edge 模組。 TrustBox 是以 TrustBox Edge 為基礎，而 TrustBox EdgeXL 裝置則是使用 Open 記憶體保護區 SDK 預先載入並 Azure IoT Edge。

如需詳細資訊，請參閱 [Scalys TrustBox 的 Open 記憶體保護區](https://aka.ms/scalys-trustbox-edge-get-started)使用者入門。

## <a name="develop-and-deploy"></a>開發與部署

當您準備好開發和部署機密應用程式時，適用于 Visual Studio Code 的 [Microsoft Open 記憶體保護區](https://marketplace.visualstudio.com/items?itemName=ms-iot.msiot-vscode-openenclave) 延伸模組可提供協助。 您可以使用 Linux 或 Windows 作為開發電腦，以開發適用于 TrustBox 的模組。

## <a name="next-steps"></a>下一步

瞭解如何使用[適用于 Visual Studio Code 的 Open 記憶體保護區延伸](https://github.com/openenclave/openenclave/tree/master/devex/vscode-extension)模組 IoT Edge 模組開始開發機密應用程式
