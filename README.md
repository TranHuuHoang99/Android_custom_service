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
   - <img width="714" height="1118" alt="image" src="https://github.com/user-attachments/assets/e5c16360-4a17-486e-8f6b-97575b0f54e4" />
   - filegroup use for define a name that point to *.aidl files that we created above and so on for *.cpp files
   - cc_library_static is the name we are going to use to generate Header file from AIDL (as static library in cpp)
4. CONFIGURE IHelloWorld.aidl
   - <img width="525" height="173" alt="image" src="https://github.com/user-attachments/assets/773f957b-7557-4659-8a52-aa3a0bc81bfe" />
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
   - After succeed generate IPC binder go to directory => out/soong/.intermediates/vendor
   - <img width="535" height="607" alt="image" src="https://github.com/user-attachments/assets/17d32b9d-f724-4544-bb45-80cc131599b1" />
   - You can see the BnHelloWorld.h generated in the folder level like the folder tree we created to contain IHelloWorld.aidl file
   - Open HelloWorld.h that we created above and edit like so:
   - <img width="1132" height="766" alt="image" src="https://github.com/user-attachments/assets/484e69bc-4677-4a1d-b75b-11fba31b88bd" />
   - SERVICE_NAME is defined for adding our service to Android service thread pool later
8. WRITE A DEFENITION FOR HEADER SERVICE
   - Open the HelloWorld.cpp that we created above and edit like so:
   - <img width="1270" height="1074" alt="image" src="https://github.com/user-attachments/assets/cf218364-15c2-4599-8997-a6c37d1ffb1b" />
   - __android_log_print is the function for logging message out on adb logcat first parameter ("HOANGPRODN") is the tag and the second string paramter is Message
   - in function isInstance we define method to add our service to android service manager as the SERVICE_NAME we define in the header that we going to use later      (service call vendor.helloworld.service.IHelloWorld 1 s16 str)
   - finally we going to run our service object in the main function,
   - Note that main function of each module/service will be call automatically whenever the android OS run up.
   - <img width="925" height="687" alt="image" src="https://github.com/user-attachments/assets/bf5b3e1a-38c2-4a94-8514-fc0e44d5092c" />
9. REWRITE Android.bp for building aosp image
   - Open Android.bp and add this to new line.
   - <img width="530" height="853" alt="image" src="https://github.com/user-attachments/assets/d49e7eaa-728f-4449-a545-c021de04895b" />
   - build aosp img again
   - cd android
   - source build/envsetup.sh
   - lunch sdk_car_x86_64-userdebug
   - m libhelloworld_aidl
   - mmm vendor/services/helloworld
   - <img width="845" height="586" alt="image" src="https://github.com/user-attachments/assets/61dbef44-3e43-4c2e-9062-e49d0a091dec" />
   - make -j16
10. SELINUX
   - After succeed to build image and run with emulator
   - we will call our custom service by calling (adb shell "service call vendor.helloworld.service.IHelloWorld 1 s16 hello")
   - the Selinux will stop it as below
   - <img width="2450" height="223" alt="image" src="https://github.com/user-attachments/assets/784764cd-2860-409a-88b6-a8536105762f" />
   - Save the log and move to ubuntu/linux PC
   - Download audit2allow to your linux PC by command 
   - sudo apt-get update
   - sudo apt install policycoreutils-python-utils
   - Then you have to filter the selinux logcat blocked your service like so
   - cat PussADB_20231219_103013_mainsys.log | grep IHelloWorld | audit2allow -p policy
   - <img width="1428" height="183" alt="image" src="https://github.com/user-attachments/assets/787fb029-1546-4ab5-832a-3c25f45f3dff" />
   - Create a folder tree and files for sepolicy as follow
   - <img width="300" height="265" alt="image" src="https://github.com/user-attachments/assets/44400560-e67b-4922-ae14-81fe73871c16" />
11. WRITE CUSTOM POLICY
   - go to private/service_contexts to define a service symbol for our package service
   - <img width="931" height="130" alt="image" src="https://github.com/user-attachments/assets/7b9f4b36-cc52-46c0-b6be-55be85b29327" />
   - go to public/helloworld.te to define type binary
   - <img width="635" height="95" alt="image" src="https://github.com/user-attachments/assets/ff5ec882-b9ee-4273-b29f-dfe326205804" />
   - go to public/service.te to define type service
   - <img width="657" height="117" alt="image" src="https://github.com/user-attachments/assets/5cc47c0b-9d32-4d3d-8d55-f47b30c6d71f" />
   - go to vendor/file_contexts
   - <img width="989" height="148" alt="image" src="https://github.com/user-attachments/assets/6f57b8c4-9976-49d4-92d0-e7d3f2c953f4" />
   - go to vendor/file.te
   - <img width="616" height="130" alt="image" src="https://github.com/user-attachments/assets/834fb5d1-bad9-4186-b24b-94e052bebbaa" />
   - go to vendor/helloworld.te
   - <img width="825" height="485" alt="image" src="https://github.com/user-attachments/assets/05afd4f7-d93c-4b9a-bb1c-954d5dee781f" />
   - Add the allow policy that we got from *.log file and audit2allow and change the default_service_name to our service name => default_service_name →                hello_world_service
   - Note that all the sepolicy created above have to have an empty space in the end of the file to avoid necessary problems while build aosp img.
   - Finally at these line to helloworld.mk to build our sepolicy
   - <img width="594" height="379" alt="image" src="https://github.com/user-attachments/assets/fd6b3aad-c83a-4686-82aa-456d74c72ee3" />
12. END => RUN COMMAND BUILD AGAIN AND YOU'RE READY TO GO




















