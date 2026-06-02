# Android_custom_service
1. CLONE ANDROID NATIVE SERVICE A-Z
   - Link to download : https://source.android.com/docs/setup/download/downloading
   - Choose a stable version for yourself:
   - mkdir android
   - cd android
   - repo init -u https://android.googlesource.com/platform/manifest -b android-14.0.0_r1
   - repo sync -c -j8
2. MAKE VENDOR FOLDER
   - create folders tree and files as the picture below
   <img width="552" height="302" alt="image" src="https://github.com/user-attachments/assets/e3275166-23e4-41a0-a9e0-071a53eb1d7d" />
3. CONFIGURE Android.bp
   <img width="714" height="1118" alt="image" src="https://github.com/user-attachments/assets/e5c16360-4a17-486e-8f6b-97575b0f54e4" />
   - filegroup use for define a name that point to *.aidl files that we created above and so on for *.cpp files
   - cc_library_static is the name we are going to use to generate Header file from AIDL (as static library in cpp)
4. CONFIGURE IHelloWorld.aidl
   <img width="525" height="173" alt="image" src="https://github.com/user-attachments/assets/773f957b-7557-4659-8a52-aa3a0bc81bfe" />
   - we pack our service to package name vendor.helloworld.service (should be named follow up to folder tree you created above)
   - <img width="226" height="33" alt="image" src="https://github.com/user-attachments/assets/1b124a10-c45f-4874-8bb8-c724476a65a9" />
   - syntax of aidl file is similar to JAVA
5. CONFIGURE helloworld.mk
   - add global parameter as follow <img width="388" height="41" alt="image" src="https://github.com/user-attachments/assets/ee973da2-b96a-4e7f-9836-9081b9598807" />
     to helloworld.mk
   - Then if you want this MakeFile be called add it to the variant of your project
   - From my side i added to device/generic/car/sdk_car_x86_64.mk
   - Add this command line  $(call inherit-product, vendor/services/helloworld/helloworld.mk) to sdk_car_x86_64.mk
   - <img width="1024" height="157" alt="image" src="https://github.com/user-attachments/assets/6f9baba2-529e-41a5-8165-f65e1a7c99c7" />
6. GENERATE HEADER File inherit from Android IPC binder
   - source build/envsetup.sh
   - lunch sdk_car_x86_64-userdebug
   - m libhelloworld_aidl
   - <img width="401" height="114" alt="image" src="https://github.com/user-attachments/assets/e5071b1f-116a-41ed-8e33-e45b9c81f4fb" />
   - <img width="856" height="613" alt="image" src="https://github.com/user-attachments/assets/fb484147-e1cb-4699-a437-054ee6b172a6" />
7. OVERWRITE GENERATED HEADER FILE





