---
title: Azure 媒體索引器的工作預設
description: 本主題提供 Azure 媒體服務媒體索引子的工作預設總覽。
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: f8daa25239b935a9e0092c6bf2e388c7cc3c6789
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89264754"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure 媒體索引器的工作預設

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure 媒體索引器是您執行下列工作所使用的媒體處理器：使媒體檔案和內容可供搜尋、產生隱藏式輔助字幕追蹤和關鍵字、為屬於您資產的資產檔案編製索引。

本主題說明您需要傳遞給索引建立工作的工作預設。 如需完整範例，請參閱[使用 Azure 媒體索引器為媒體檔案編製索引](media-services-index-content.md)。

## <a name="azure-media-indexer-configuration-xml"></a>Azure 媒體索引器設定 XML

下表說明設定 XML 的項目和屬性。

|名稱|要求|描述|
|---|---|---|
|輸入|true|您想要編製索引的資產檔案。<br/>Azure 媒體索引器支援下列媒體檔案格式︰MP4、MOV、WMV、MP3、M4A、WMA、AAC、WAV。 <br/><br/>您可以在 **input** 項目的 **name** 或 **list** 屬性中指定檔案名稱 (如下所示)。 如不指定要編製索引的資產檔案，就會挑選主要檔案。 如果未設定主要資產檔案，則會對輸入資產中的第一個檔案編製索引。<br/><br/>若要明確指定資產檔案名稱，請執行︰<br/>```<input name="TestFile.wmv" />```<br/><br/>您也可以一次對多個資產檔案編制索引 (最多 10 個檔案)。 若要這樣做：<br/>- 建立文字檔 (資訊清單檔) 並指定 .lst 副檔名。<br/>- 將輸入資產中所有資產檔案名稱的清單新增至此資訊清單檔案。<br/>- 將資訊檔案新增 (上傳) 到資產。<br/>- 在輸入的 list 屬性中指定資訊清單檔的名稱。<br/>```<input list="input.lst">```<br/><br/>**注意：** 如果您在資訊清單檔中新增超過10個檔案，索引作業將會失敗，並出現2006錯誤碼。|
|中繼資料|false|所指定資產檔案的中繼資料。<br/>```<metadata key="..." value="..." />```<br/><br/>您可以提供預先定義的索引鍵值。 <br/><br/>目前支援下列索引鍵：<br/><br/>**標題**和**描述** - 用來更新語言模型，以改善語音辨識準確度。<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**使用者名稱**和**密碼** - 透過 http 或 https 下載網際網路檔案時用於驗證。<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>使用者名稱和密碼值會套用至輸入資訊清單中的所有媒體 URL。|
|特性<br/><br/> 在 1.2 版中新增。 目前唯一支援的功能是語音辨識 ("ASR")。|false|語音辨識功能具有下列設定索引鍵︰<br/><br/>語言：<br/>- 要在多媒體檔案中辨識的自然語言。<br/>- 英文、西班牙文<br/><br/>CaptionFormats：<br/>- 所需輸出字幕格式的分號分隔清單 (如果有的話)<br/>-ttml; webvtt<br/><br/><br/>GenerateKeywords：<br/>- 布林值旗標，用於指定是否需要關鍵字 XML 檔案。<br/>- True 或 False。|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure 媒體索引器設定 XML 範例

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>後續步驟

請參閱[使用 Azure 媒體索引器為媒體檔案編製索引](media-services-index-content.md)。

