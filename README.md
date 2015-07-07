# Pebcode
How to set up Xcode for [Pebble](https://getpebble.com) development.

## Prerequisites
Before you continue, please make sure you have...

* The latest version of the Pebble SDK, installed as per [these instructions](http://developer.getpebble.com/sdk/install/mac)
* Xcode from [Mac App Store](https://itunes.apple.com/gb/app/xcode/id497799835?mt=12)

## Xcode, Meet Pebble

Steps to build and run a Pebble project in Xcode:

1. **Create a new Xcode project** in Xcode
	1. OS X -> Application -> **Command Line Tool**
	2. Product Name: **MyProject**
	3. Language: **C**
	3. Next -> Create
	4. Rename target **MyProject** to **HiddenTarget**
	5. Delete main.c and its parent folder *MyProject*
2. Create a new Pebble project in Terminal
	1. `cd PATH_TO_MyProject`
	2. `pebble new-project MyProject`
	3. Move all files from subfolder **MyProject** to its parent folder
	4. Delete the subfolder **MyProject**
	5. (So that `MyProject.xcodeproj` is together with all Pebble project files)
	6. (So that the directory	structure could be supported by [CloudPebble](cloudpebble.net))
3. Add Pebble project files
	1. Drag `appinfo.json`, `resources`, `src` from Finder to Xcode project navigator. (The left files list corresponding to `⌘+1`)
	2. In the dialog
		1. Added folders: Choose **Create groups**
		2. Add to targets: Select **HiddenTarget**
4. Setup Xcode project's **Building Settings**
	1. Select the project (top blue icon in the files list)
	2. In the **Building Settings** tab
	2. 	Menu: Editor -> Add Build Setting -> Add User-Defined Setting
	3. Key = **`PEBBLE_SDK_PATH`**
	4. Value = **`$(HOME)/pebble-dev/PebbleSDK-3.1`**
	5. Double click the value of **Header Search Paths** and add
		1. `$(PEBBLE_SDK_PATH)/Pebble/aplite/include/**`
		2. `$(PEBBLE_SDK_PATH)/Pebble/basalt/include/**`
5. Add a new target for **Build & Run**.
	1. Select the project (top blue icon in the files list)
	2. Menu: Editor -> Add Target...
	3. OS X -> Application -> Command Line Tool
	4. Product Name: **Basalt**
	5. Delete main.c and it's parent folder *Basalt*
6. Setup target **Basalt**'s **Build Phases**
	1. Delete all phases that can be deleted
		1. Compile Sources
		2. Link Binary With Libraries
		3. Copy Files
	2. Menu: Editor -> Add New Build Phase -> Add Run Script Build Phase
	3. Paste the following scripts

	```
	PATH=${PATH}:${PEBBLE_SDK_PATH}/bin/
	cd ${SRCROOT}
	pebble build
	pebble install --emulator basalt
	```
7. Setup target **Basalt**'s Scheme to avoid a warning dialog
	1. Menu: Product -> Scheme -> Basalt
	2. Menu: Product -> Scheme -> Edit Scheme... (`⌘+<`)
	3. Launch: Choose **Wait for executable to be launched**

8. Now you can press `⌘+R` to build your Pebble project and run it in Basalt emulator.

Notes:

1. You can add another target for **Aplite** emulator, just modify the script to

	```
	PATH=${PATH}:${PEBBLE_SDK_PATH}/bin/
	cd ${SRCROOT}
	pebble build
	pebble install --emulator aplite
	```
2. You can add another target for real device test, just modify the script to (remember to change the IP address of your iOS/Android device)

	```
	PATH=${PATH}:${PEBBLE_SDK_PATH}/bin/
	cd ${SRCROOT}
	pebble build
	pebble install --phone 192.168.1.2
	```
3. You can check all logs in Report Navigator (`⌘+8`)

4. You may get the following error during the building, you need to update Python security package in Terminal by typing `pip install requests[security]`.

	```
	/usr/local/lib/python2.7/dist-packages/requests/packages/urllib3
	/util/ssl_.py:79: InsecurePlatformWarning: A true SSLContext object is not
	available. This prevents urllib3 from configuring SSL appropriately and 
	may cause certain SSL connections to fail. For more information, see 
	https://urllib3.readthedocs.org/en/latest  
	/security.html#insecureplatformwarning.
	``` 
5. **HiddenTarget** is only used for supporting Syntax Highlighting and Suggest Completion. It's better to delete its Scheme as we don't want to build&run it.
	1. Menu: Product -> Scheme -> Manage Schemes...
	2. Choose HiddenTarget, then delete.
6. Everytime you add new source files, remember to only check target **HiddenTarget**, uncheck all the others.

## But Why?

You might be thinking there’s a lot of hoops to jump through here, so what’s value in doing so? Well, once setup you get the following:

### Syntax Highlighting

![](Images/17.png)

### Code Completion

![](Images/18.png)

### Build Support

![](Images/19.png)

You can use ⌘+b to build your Pebble app, and you can even see the output from the build in the Report navigator.

### Quick Help

![](Images/20.png)

I :heart: Pebble’s SDK developers for making sure the headers are so well documented.

### Syntax Checking

This comes in really useful if you’ve recently moved to [Swift](https://developer.apple.com/swift/) and have therefore completely forgot about the need for semi-colons :smirk:

