---
title: 快速入門：產生縮圖 - REST、Python
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將搭配使用電腦視覺 API 與 Python 來產生影像的縮圖。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 38fb7f3b0f2d85762161c384f4e5564078c9a63c
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745186"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>快速入門：使用電腦視覺 REST API 和 Python 產生縮圖

在此快速入門中，您會使用電腦視覺 REST API 從影像產生縮圖。 使用[取得縮圖](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c)方法時，您可以指定所需的高度和寬度，然後電腦視覺就會使用智慧型裁剪，以智慧方式識別關注區域，並根據該區域產生裁剪座標。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/try/cognitive-services/)。

## <a name="prerequisites"></a>Prerequisites

- 您必須有電腦視覺的訂用帳戶金鑰。 您可以從[試用認知服務](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision)取得免費試用的金鑰。 或者，依照[建立認知服務帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)中的指示訂閱電腦視覺並取得金鑰。 然後，分別為名為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的金鑰和服務端點字串[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。
- 程式碼編輯器，例如 [Visual Studio Code](https://code.visualstudio.com/download)。

## <a name="create-and-run-the-sample"></a>建立並執行範例

若要建立及執行範例，請將下列程式碼複製到程式碼編輯器中。 

```python
import os
import sys
import requests
# If you are using a Jupyter notebook, uncomment the following lines.
# %matplotlib inline
# import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Add your Computer Vision subscription key and endpoint to your environment variables.
subscription_key = os.environ['COMPUTER_VISION_SUBSCRIPTION_KEY']
endpoint = os.environ['COMPUTER_VISION_ENDPOINT']

thumbnail_url = endpoint + "vision/v3.0/generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

# Construct URL
headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
# Call API
response = requests.post(thumbnail_url, headers=headers, params=params, json=data)
response.raise_for_status()

# Open the image from bytes
thumbnail = Image.open(BytesIO(response.content))

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))

# Save thumbnail to file
thumbnail.save('thumbnail.png')

# Display image
thumbnail.show()

# Optional. Display the thumbnail from Jupyter.
# plt.imshow(thumbnail)
# plt.axis("off")
```

接下來，執行下列作業：

1. (選用) 將 `image_url` 的值取代為您影像的 URL。
1. 將程式碼儲存為副檔名為 `.py` 的檔案。 例如： `get-thumbnail.py` 。
1. 開啟 [命令提示字元] 視窗。
1. 出現提示時，使用 `python` 命令執行範例。 例如： `python get-thumbnail.py` 。

## <a name="examine-the-response"></a>檢查回應

成功的回應會以二進位資料的形式傳回，代表縮圖的影像資料。 此範例應該會顯示此影像。 如果要求失敗，則回應會顯示於命令提示字元視窗中，且應該會包含錯誤碼。

## <a name="run-in-jupyter-optional"></a>在 Jupyter 中執行 (選擇性)

您可以選擇性地使用 [MyBinder](https://mybinder.org) (英文) 上的 Jupyter Notebook，以逐步方式執行本快速入門。 若要啟動 Binder，請選取下列按鈕：

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>後續步驟

接下來，探索使用「電腦視覺」在影像中執行光學字元辨識 (OCR)、建立智慧型裁剪縮圖以及偵測、分類、標記和描述視覺特徵的 Python 應用程式。

> [!div class="nextstepaction"]
> [電腦視覺 API Python 教學課程](../Tutorials/PythonTutorial.md)

* 若要快速地試驗電腦視覺 API，請嘗試 [Open API 測試主控台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c)。
