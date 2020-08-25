---
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.date: 06/22/2020
ms.topic: include
ms.author: rgarcia
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4d20b7dee533dae17cfb1c6138e336b91c7e2b0c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "85296180"
---
## <a name="putting-everything-together"></a>合併所有元素

以下顯示完整的 `MainActivity` 類別檔案在所有不同的元素組合在一起之後所應呈現的樣貌。 您可加以參考以利比較您自己的檔案，並確認您是否有任何疏漏之處。

```java
package com.example.myfirstapp;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;

import com.google.ar.core.HitResult;
import com.google.ar.core.Plane;
import com.google.ar.sceneform.AnchorNode;
import com.google.ar.sceneform.math.Vector3;
import com.google.ar.sceneform.rendering.Color;
import com.google.ar.sceneform.rendering.MaterialFactory;
import com.google.ar.sceneform.rendering.Renderable;
import com.google.ar.sceneform.rendering.ShapeFactory;
import com.google.ar.sceneform.ux.ArFragment;

import android.view.MotionEvent;
import android.util.Log;

import com.google.ar.sceneform.ArSceneView;
import com.google.ar.sceneform.Scene;
import com.microsoft.azure.spatialanchors.CloudSpatialAnchor;
import com.microsoft.azure.spatialanchors.CloudSpatialAnchorSession;
import com.microsoft.azure.spatialanchors.SessionLogLevel;

import java.util.concurrent.CompletableFuture;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

import com.microsoft.azure.spatialanchors.AnchorLocateCriteria;
import com.microsoft.azure.spatialanchors.LocateAnchorStatus;

public class MainActivity extends AppCompatActivity {

    private boolean tapExecuted = false;
    private final Object syncTaps = new Object();
    private ArFragment arFragment;
    private AnchorNode anchorNode;
    private Renderable nodeRenderable = null;
    private float recommendedSessionProgress = 0f;

    private ArSceneView sceneView;
    private CloudSpatialAnchorSession cloudSession;

    private String anchorId = null;
    private boolean scanningForUpload = false;
    private final Object syncSessionProgress = new Object();
    private ExecutorService executorService = Executors.newSingleThreadExecutor();

    // <onCreate>
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        this.arFragment = (ArFragment) getSupportFragmentManager().findFragmentById(R.id.ux_fragment);
        this.arFragment.setOnTapArPlaneListener(this::handleTap);

        this.sceneView = arFragment.getArSceneView();
        Scene scene = sceneView.getScene();
        scene.addOnUpdateListener(frameTime -> {
            if (this.cloudSession != null) {
                this.cloudSession.processFrame(sceneView.getArFrame());
            }
        });

        initializeSession();
    }
    // </onCreate>

    // <initializeSession>
    private void initializeSession() {
        if (this.cloudSession != null){
            this.cloudSession.close();
        }
        this.cloudSession = new CloudSpatialAnchorSession();
        this.cloudSession.setSession(sceneView.getSession());
        this.cloudSession.setLogLevel(SessionLogLevel.Information);
        this.cloudSession.addOnLogDebugListener(args -> Log.d("ASAInfo", args.getMessage()));
        this.cloudSession.addErrorListener(args -> Log.e("ASAError", String.format("%s: %s", args.getErrorCode().name(), args.getErrorMessage())));

        this.cloudSession.addSessionUpdatedListener(args -> {
            synchronized (this.syncSessionProgress) {
                this.recommendedSessionProgress = args.getStatus().getRecommendedForCreateProgress();
                Log.i("ASAInfo", String.format("Session progress: %f", this.recommendedSessionProgress));
                if (!this.scanningForUpload)
                {
                    return;
                }
            }

            runOnUiThread(() -> {
                synchronized (this.syncSessionProgress) {
                    MaterialFactory.makeOpaqueWithColor(this, new Color(
                            this.recommendedSessionProgress,
                            this.recommendedSessionProgress,
                            this.recommendedSessionProgress))
                        .thenAccept(material -> {
                            this.nodeRenderable.setMaterial(material);
                        });
                }
            });
        });

        this.cloudSession.addAnchorLocatedListener(args -> {
            if (args.getStatus() == LocateAnchorStatus.Located)
            {
                runOnUiThread(()->{
                    this.anchorNode = new AnchorNode();
                    this.anchorNode.setAnchor(args.getAnchor().getLocalAnchor());
                    MaterialFactory.makeOpaqueWithColor(this, new Color(android.graphics.Color.GREEN))
                        .thenAccept(greenMaterial -> {
                            this.nodeRenderable = ShapeFactory.makeSphere(0.1f, new Vector3(0.0f, 0.15f, 0.0f), greenMaterial);
                            this.anchorNode.setRenderable(nodeRenderable);
                            this.anchorNode.setParent(arFragment.getArSceneView().getScene());

                            this.anchorId = null;
                            synchronized (this.syncTaps) {
                                this.tapExecuted = false;
                            }
                        });
                });
            }
        });

        this.cloudSession.getConfiguration().setAccountId(/* Copy your account Identifier in here */);
        this.cloudSession.getConfiguration().setAccountKey(/* Copy your account Key in here */);
        this.cloudSession.getConfiguration().setAccountDomain(/* Copy your account Domain in here */);
        this.cloudSession.start();
    }
    // </initializeSession>

    // <handleTap>
    protected void handleTap(HitResult hitResult, Plane plane, MotionEvent motionEvent) {
        synchronized (this.syncTaps) {
            if (this.tapExecuted) {
                return;
            }

            this.tapExecuted = true;
        }

        if (this.anchorId != null) {
            this.anchorNode.getAnchor().detach();
            this.anchorNode.setParent(null);
            this.anchorNode = null;
            initializeSession();
            AnchorLocateCriteria criteria = new AnchorLocateCriteria();
            criteria.setIdentifiers(new String[]{this.anchorId});
            cloudSession.createWatcher(criteria);
            return;
        }

        this.anchorNode = new AnchorNode();
        this.anchorNode.setAnchor(hitResult.createAnchor());
        CloudSpatialAnchor cloudAnchor = new CloudSpatialAnchor();
        cloudAnchor.setLocalAnchor(this.anchorNode.getAnchor());

        MaterialFactory.makeOpaqueWithColor(this, new Color(
                this.recommendedSessionProgress,
                this.recommendedSessionProgress,
                this.recommendedSessionProgress))
            .thenAccept(material -> {
                this.nodeRenderable = ShapeFactory.makeSphere(0.1f, new Vector3(0.0f, 0.15f, 0.0f), material);
                this.anchorNode.setRenderable(nodeRenderable);
                this.anchorNode.setParent(arFragment.getArSceneView().getScene());

                uploadCloudAnchorAsync(cloudAnchor)
                    .thenAccept(id -> {
                        this.anchorId = id;
                        Log.i("ASAInfo", String.format("Cloud Anchor created: %s", this.anchorId));
                        runOnUiThread(() -> {
                            MaterialFactory.makeOpaqueWithColor(this, new Color(android.graphics.Color.BLUE))
                                .thenAccept(blueMaterial -> {
                                    this.nodeRenderable.setMaterial(blueMaterial);
                                    synchronized (this.syncTaps) {
                                        this.tapExecuted = false;
                                    }
                                });
                        });
                    });
            });
    }
    // </handleTap>

    // <uploadCloudAnchorAsync>
    private CompletableFuture<String> uploadCloudAnchorAsync(CloudSpatialAnchor anchor) {
        synchronized (this.syncSessionProgress) {
            this.scanningForUpload = true;
        }

        return CompletableFuture.runAsync(() -> {
            try {
                float currentSessionProgress;
                do {
                    synchronized (this.syncSessionProgress) {
                        currentSessionProgress = this.recommendedSessionProgress;
                    }
                    if (currentSessionProgress < 1.0) {
                        Thread.sleep(500);
                    }
                }
                while (currentSessionProgress < 1.0);

                synchronized (this.syncSessionProgress) {
                    this.scanningForUpload = false;
                }
                runOnUiThread(() -> {
                    MaterialFactory.makeOpaqueWithColor(this, new Color(android.graphics.Color.YELLOW))
                        .thenAccept(yellowMaterial -> {
                            this.nodeRenderable.setMaterial(yellowMaterial);
                        });
                });

                this.cloudSession.createAnchorAsync(anchor).get();
            } catch (InterruptedException | ExecutionException e) {
                Log.e("ASAError", e.toString());
                throw new RuntimeException(e);
            }
        }, executorService).thenApply(ignore -> anchor.getIdentifier());
    }
    // </uploadCloudAnchorAsync>
}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何建立可整合 ARCore 功能與 Azure Spatial Anchors 的新 Android 應用程式。 若要深入了解 Azure Spatial Anchors 程式庫，請繼續參考關於如何建立和尋找錨點的指南。

> [!div class="nextstepaction"]
> [使用 Azure Spatial Anchors 建立和尋找錨點](/azure/spatial-anchors/create-locate-anchors-overview)
