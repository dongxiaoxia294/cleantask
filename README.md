# Clean Task - Minimalist To-Do App

A minimalist to-do application built with UniApp + Vue 2, designed for a clean user experience.

## 1. Prerequisites
1. Download and install [HBuilderX](https://www.dcloud.io/hbuilderx.html).
2. (Recommended) Install an Android emulator or prepare an Android device.

## 2. Import Project
1. Open HBuilderX.
2. Go to **File -> Import -> From Local Directory**.
3. Select the `CleanTask` folder.

## 3. Running and Debugging
1. **Run in Browser**:
   - Go to **Run -> Run to Browser -> Chrome** (or your preferred browser).
2. **Run on Device**:
   - Connect your Android device via USB.
   - Enable "Developer Options" and "USB Debugging" on your device.
   - Go to **Run -> Run to Mobile Phone or Emulator -> [Your Device]**.

## 4. Packing for Android (APK / AAB for Google Play)
1. Open `manifest.json` in HBuilderX.
2. **Basic Settings**:
   - Click "Reacquire" to get a unique `AppID`.
3. **App Icon Settings**:
   - Browse and select a 1024x1024 icon, then click "Auto-generate all icons".
4. **App Distribution**:
   - Go to **Publish -> App-Native Deployment (Cloud Packaging)**.
   - **Package Name**: Enter something like `com.yourname.cleantask` (must be unique).
   - **Certificate**:
     - For Google Play, use **Custom Certificate** (you need to generate a .keystore or .jks file).
     - For testing, you can use "Public Test Certificate".
   - **Package Type**:
     - **AAB (Android App Bundle)**: Recommended for Google Play.
     - **APK**: For direct installation on devices.
5. **Start Packaging**:
   - Click **Package** and wait for the cloud process to finish.
   - A download link will be provided in the console once complete.

## 5. Core Features
- **Add Task**: Type in the bottom input and tap the blue "+" button.
- **Mark Done**: Tap a task card. Completed tasks become translucent and strike-through.
- **Filter**: Use the "All/To-Do/Done" buttons at the top.
- **Delete**: **Swipe left** on a task item to reveal the red "Delete" button.
- **Dark Mode**: Automatically follows system settings.
- **Persistence**: Data is saved locally using `uni.setStorageSync`.

