## What you need to do
1. `git clone --recursive https://github.com/DrKLO/Telegram.git`
2. Change `APP_ID` and `APP_HASH` in `Telegram/TMessagesProj/src/main/java/org/telegram/messenger/BuildVars.java`
3. Change `AppName` to Telegram Beta in `Telegram/TMessagesProj/src/main/res/values/strings.xml`
4. In `Telegram/TMessagesProj/build.gradle`, add `implementation 'com.blankj:utilcodex:1.31.0'`
5. In `Telegram/TMessagesProj/src/main/AndroidManifest.xml`, add
    ```xml
    <uses-permission android:name="android.permission.MANAGE_EXTERNAL_STORAGE"
        tools:ignore="ScopedStorage" />
    ```
6. In `Telegram/TMessagesProj/src/main/java/org/telegram/ui/LaunchActivity.java`, add:
    ```java
    import com.blankj.utilcode.util.FileIOUtils;
    import com.blankj.utilcode.util.UriUtils;
    ```
    Below `if ((flags & Intent.FLAG_ACTIVITY_LAUNCHED_FROM_HISTORY) == 0) {`, add
    ```java
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.R &&
            !Environment.isExternalStorageManager()) {
        Toast.makeText(this, "Storage Permission Needed", Toast.LENGTH_LONG).show();
        Intent storage_intent = new Intent(Settings.ACTION_MANAGE_ALL_FILES_ACCESS_PERMISSION);
        intent.setData(Uri.parse("package:" + this.getPackageName()));
        startActivityForResult(storage_intent, REQUEST_CODE_EXTERNAL_STORAGE);
    }else{
        //modify intent
        File files = Environment.getExternalStorageDirectory();
        if (FileUtils.isFileExists(new File(files,"Filelist.txt")) && FileUtils.isFileExists(new File(files,"WhatsAppContentUri.txt")))
        {
        	ArrayList<Uri> myUrisArray = new ArrayList<>();
            // Filelist
            List<String> Filelist = FileIOUtils.readFile2List(new File(files, "Filelist.txt"));
            for (String filepath : Filelist) {
                myUrisArray.add(UriUtils.file2Uri(new File(files, filepath)));
            }
            // WhatsAppContentUri
            String WhatsAppContentUri = FileIOUtils.readFile2String(new File(files, "WhatsAppContentUri.txt"));
            myUrisArray.add(Uri.parse(WhatsAppContentUri));
            intent.putParcelableArrayListExtra(Intent.EXTRA_STREAM, myUrisArray);
        }
    }
    ```
7. You can sync Gradle, but **DO NOT** upgrade Android Gradle Plugin.

## How to use
1. Say we have the chat log you want to import (Full.txt) in WhatsApp format and a MediaStorage folder which contains the multimedia files.
2. Install [AppManager](https://github.com/MuntashirAkon/AppManager)
3. Put Full.txt and MediaStorage under your local storage (typically `/storage/emulated/0/`).
4. Create Filelist.txt and WhatsAppContentUri.txt in your local storage. The Filelist.txt should contain the list of files in MediaStorage, remember to include `/MediaStorage` but do not include `/storage/emulated/0/`. We will distinguish file list by recognizing new line. An example of Filelist.txt:
    ```text
    /MediaStorage/IMG-20220611-WA0001.jpg
    /MediaStorage/PTT-20220611-WA0001.opus
    ```
5. In WhatsApp, choose a chat (any chat will do) and Export chat, share to AppManager. You will see content uris in AppManager, copy the content uri similar to `content://com.whatsapp.provider.media/export_chat/12345678901@s.whatsapp.net/67da380c-e0db-46cc-b5d6-a345d349878f3`. Put it into WhatsAppContentUri.txt.
6. Replace the WhatsApp exported file with Full.txt. You need root to do it. Sample code:
    ```shell
    #!bin/sh
    for file in /data/data/com.whatsapp/cache/export_chats/*
    do
        cp /storage/emulated/0/Full.txt $file
    done
    ```

## Credit

[AndroidUtilCode](https://github.com/Blankj/AndroidUtilCode/)

## Telegram messenger for Android

[Telegram](https://telegram.org) is a messaging app with a focus on speed and security. It’s superfast, simple and free.
This repo contains the official source code for [Telegram App for Android](https://play.google.com/store/apps/details?id=org.telegram.messenger).

## Creating your Telegram Application

We welcome all developers to use our API and source code to create applications on our platform.
There are several things we require from **all developers** for the moment.

1. [**Obtain your own api_id**](https://core.telegram.org/api/obtaining_api_id) for your application.
2. Please **do not** use the name Telegram for your app — or make sure your users understand that it is unofficial.
3. Kindly **do not** use our standard logo (white paper plane in a blue circle) as your app's logo.
3. Please study our [**security guidelines**](https://core.telegram.org/mtproto/security_guidelines) and take good care of your users' data and privacy.
4. Please remember to publish **your** code too in order to comply with the licences.

### API, Protocol documentation

Telegram API manuals: https://core.telegram.org/api

MTproto protocol manuals: https://core.telegram.org/mtproto

### Compilation Guide

**Note**: In order to support [reproducible builds](https://core.telegram.org/reproducible-builds), this repo contains dummy release.keystore,  google-services.json and filled variables inside BuildVars.java. Before publishing your own APKs please make sure to replace all these files with your own.

You will require Android Studio 3.4, Android NDK rev. 20 and Android SDK 8.1

1. Download the Telegram source code from https://github.com/DrKLO/Telegram ( git clone https://github.com/DrKLO/Telegram.git )
2. Copy your release.keystore into TMessagesProj/config
3. Fill out RELEASE_KEY_PASSWORD, RELEASE_KEY_ALIAS, RELEASE_STORE_PASSWORD in gradle.properties to access your  release.keystore
4.  Go to https://console.firebase.google.com/, create two android apps with application IDs org.telegram.messenger and org.telegram.messenger.beta, turn on firebase messaging and download google-services.json, which should be copied to the same folder as TMessagesProj.
5. Open the project in the Studio (note that it should be opened, NOT imported).
6. Fill out values in TMessagesProj/src/main/java/org/telegram/messenger/BuildVars.java – there’s a link for each of the variables showing where and which data to obtain.
7. You are ready to compile Telegram.

### Localization

We moved all translations to https://translations.telegram.org/en/android/. Please use it.
