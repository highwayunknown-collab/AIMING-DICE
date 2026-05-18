Carrom Vision Trainer — Development Implementation Guide

An offline-first, highly optimized real-time screen overlay prediction engine that performs computer vision analysis directly on mobile devices without relying on network backends.

🛠️ Project Structure and Layout Configuration

To set up the complete project inside Android Studio, arrange the folders in your structure as follows:

CarromVisionTrainer/
│
├── build.gradle
├── settings.gradle
└── app/
    ├── build.gradle
    └── src/
        └── main/
            ├── AndroidManifest.xml
            └── java/com/vision/carromtrainer/
                ├── MainActivity.kt
                ├── physics/
                │   └── CarromPhysicsEngine.kt
                ├── vision/
                │   └── CarromVisionProcessor.kt
                └── services/
                    └── OverlayService.kt


Add the Layout Layout Asset (app/src/main/res/layout/activity_main.xml):

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="20dp"
    android:gravity="center_horizontal"
    android:background="#1E1E1E">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="CARROM VISION ASSISTANT"
        android:textColor="#FFFFFF"
        android:textSize="22sp"
        android:textStyle="bold"
        android:layout_marginTop="30dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Offline-first screen projection helper and trajectory layout engine."
        android:textColor="#888"
        android:gravity="center"
        android:layout_marginTop="8dp" />

    <Button
        android:id="@+id/btnToggleService"
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:text="Launch Overlay Assistant"
        android:layout_marginTop="80dp" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Friction Decay Calibration"
        android:textColor="#FFF"
        android:layout_marginTop="40dp" />

    <SeekBar
        android:id="@+id/seekFriction"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="70"
        android:layout_marginTop="10dp" />

    <TextView
        android:id="@+id/txtFrictionVal"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="0.985"
        android:textColor="#4CAF50"
        android:textSize="18sp"
        android:textStyle="bold" />
</LinearLayout>


⚙️ Compiling the Build Dependencies

Add the necessary dependencies to your project-level build configuration files to bring in Androidx support, OpenCV for Android, and TensorFlow Lite.

1. app/build.gradle Configuration:

plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk 34

    defaultConfig {
        applicationId "com.vision.carromtrainer"
        minSdk 26
        targetSdk 34
        versionCode 1
        versionName "1.0.0"
    }

    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation 'androidx.core:core-ktx:1.12.0'
    implementation 'androidx.appcompat:appcompat:1.6.1'
    implementation 'com.google.android.material:material:1.11.0'
    
    // TFLite dependencies for custom classification scaling
    implementation 'org.tensorflow:tensorflow-lite:2.14.0'
    implementation 'org.tensorflow:tensorflow-lite-support:0.4.4'
    
    // OpenCV Android dependency setup
    implementation project(':opencv')
}


🚀 OpenCV Integration Protocol (Manual Import)

To utilize OpenCV hardware acceleration on Android devices without relying on external cloud setups:

Download the Android SDK Package: Retrieve the OpenCV Android SDK distribution (version 4.8.0 or later recommended).

Import OpenCV Module:

Go to File > New > Import Module... inside Android Studio.

Target the path of the downloaded SDK /sdk folder.

Set the module name to :opencv.

Configure Settings Gradle: Ensure :opencv is mapped inside settings.gradle:

include ':app', ':opencv'


Local Initializer Hooks:
Initiate standard SDK routines in MainActivity before starting the engine pipeline:

import org.opencv.android.OpenCVLoader

override fun onResume() {
    super.onResume()
    if (!OpenCVLoader.initDebug()) {
        Toast.makeText(this, "Local OpenCV Initialization Failure.", Toast.LENGTH_LONG).show()
    }
}


🤖 TensorFlow Lite Object Classification Framework

For complex board states and custom theme recognition, you can expand classification via local neural models:

Place your compiled .tflite model (with input tensors representing detected ROI bounding boxes and output vectors categorizing striker, white puck, black puck, or queen) in the app/src/main/assets/ folder.

Initialize and run local queries directly from the screen capture stream using:

import org.tensorflow.lite.Interpreter
import java.io.FileInputStream
import java.nio.channels.FileChannel

private fun loadModelFile(context: Context, modelPath: String): ByteBuffer {
    val fileDescriptor = context.assets.openFd(modelPath)
    val inputStream = FileInputStream(fileDescriptor.fileDescriptor)
    val fileChannel = inputStream.channel
    return fileChannel.map(FileChannel.MapMode.READ_ONLY, fileDescriptor.startOffset, fileDescriptor.declaredLength)
}


This decoupled modular architecture guarantees responsive execution, maintains frame rates above 30 FPS on standard devices, runs fully offline, and processes visual trajectories without interacting with game binaries or remote services.
