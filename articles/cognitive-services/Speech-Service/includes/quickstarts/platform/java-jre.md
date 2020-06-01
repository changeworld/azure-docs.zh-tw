---
title: 快速入門：適用於 JAVA (Windows、Linux、macOS) 的語音 SDK 平台設定 - 語音服務
titleSuffix: Azure Cognitive Services
description: 使用本指南來設定您的平台，以搭配使用 JAVA (Windows、Linux、macOS) 與語音服務 SDK。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 94946b4b6d0f0fc9c94cac7aca1e971b3f6262f9
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673215"
---
本指南說明如何安裝適用於 64 位元 JAVA 8 JRE 的[語音 SDK](~/articles/cognitive-services/speech-service/speech-sdk.md)。 如果您想直接開始使用套件名稱，就無法在 Maven 中央存放庫中使用 JAVA SDK。 無論您使用的是 Gradle 或 `pom.xml` 相依性檔案，都需要新增指向 `https://csspeechstorage.blob.core.windows.net/maven/` 的自訂存放庫 (請參閱下方的套件名稱)。

> [!NOTE]
> 針對語音裝置 SDK 和 Roobo 裝置，請參閱[語音裝置 SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md)。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>支援的作業系統

- JAVA 語音 SDK 套件適用於下列作業系統：
  - Windows：僅限 64 位元
  - Mac：macOS X 10.13 版或更新版本
  - Linux：64 位元僅在 Ubuntu 16.04、Ubuntu 18.04、Debian 9、RHEL 7/8 和 CentOS 7/8 提供

## <a name="prerequisites"></a>Prerequisites

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) \(英文\) 或 [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) \(英文\)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (需要已安裝 JAVA)
- 支援的 Linux 平台會要求安裝特定程式庫 (`libssl` 以取得安全通訊端層支援，`libasound2` 以取得音效支援)。 請參閱以下的發佈，以取得安裝這些程式庫正確版本所需的命令。

  - 在 Ubuntu 上，執行下列命令以安裝必要的套件：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - 在 Debian 9 上，執行下列命令以安裝必要的套件：

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - 在 RHEL/CentOS 上，執行下列命令以安裝必要的套件：

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> - 在 RHEL/CentOS 7 上，依照[如何設定適用於 Speech SDK 的 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的指示進行。
> - 在 RHEL/CentOS 8 上，依照[如何設定適用於 Linux 的 OpenSSL](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的指示進行。

- 在 Windows 上，您需根據平台來選擇[適用於 Visual Studio 2019 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 請注意，第一次安裝時，可能需要重新啟動 Windows，再繼續進行本指南。

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>建立 Eclipse 專案並安裝語音 SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>後續步驟

[!INCLUDE [windows](../quickstart-list.md)]
