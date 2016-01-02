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
	6. (So that the directory	structure can be supported by [CloudPebble](cloudpebble.net))
3. Add Pebble project files
	1. Drag `appinfo.json`, `resources`, `src` from Finder to Xcode project navigator. (The left files list corresponding to `⌘+1`)
	2. In the dialog
		1. Added folders: Choose **Create groups**
		2. Add to targets: Select **HiddenTarget**
4. Setup Xcode project's **Building Settings**
	1. Select the project (top blue icon in the files list)
	2. In the **Building Settings** tab
	3. Double click the value of **Header Search Paths** and add
		1. `~/Library/Application\ Support/Pebble\ SDK/SDKs/current/**`
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
	osascript -e 'tell app "Terminal"' -e 'if not (exists window 1) then' -e 'do script ""' -e 'end if' -e 'activate' -e 'end tell' -e 'tell app "System Events" to keystroke "c" using control down' -e 'tell app "Terminal"' -e 'set ctx to do script "" in window 1' -e "do script \"cd ${SRCROOT}\" in ctx" -e 'do script "pebble build" in ctx' -e 'do script "pebble install --emulator basalt" in ctx' -e 'do script "pebble logs --emulator basalt" in ctx' -e 'end tell'
	```
7. Setup target **Basalt**'s Scheme to avoid a warning dialog
	1. Menu: Product -> Scheme -> Basalt
	2. Menu: Product -> Scheme -> Edit Scheme... (`⌘+<`)
	3. Launch: Choose **Wait for executable to be launched**

8. Now you can press `⌘+B` to build & run your Pebble project, the emulator and the logs will be displayed automatically.

Notes:

1. You can add another target for **Aplite** emulator, just modify the script to

	```
	osascript -e 'tell app "Terminal"' -e 'if not (exists window 1) then' -e 'do script ""' -e 'end if' -e 'activate' -e 'end tell' -e 'tell app "System Events" to keystroke "c" using control down' -e 'tell app "Terminal"' -e 'set ctx to do script "" in window 1' -e "do script \"cd ${SRCROOT}\" in ctx" -e 'do script "pebble build" in ctx' -e 'do script "pebble install --emulator aplite" in ctx' -e 'do script "pebble logs --emulator aplite" in ctx' -e 'end tell'
	```
2. You can add another target for real device test, just modify the script to (remember to change the PHONE_IP of your iOS/Android devices), please make sure your phone and your computer are in the same Wifi network.


	```
	PHONE_IP=192.168.1.2
	osascript -e 'tell app "Terminal"' -e 'if not (exists window 1) then' -e 'do script ""' -e 'end if' -e 'activate' -e 'end tell' -e 'tell app "System Events" to keystroke "c" using control down' -e 'tell app "Terminal"' -e 'set ctx to do script "" in window 1' -e "do script \"cd ${SRCROOT}\" in ctx" -e 'do script "pebble build" in ctx' -e "do script \"pebble install -v --phone $PHONE_IP --logs\" in ctx" -e "do script \"pebble logs -v --phone ${PHONE_IP}\" in ctx" -e 'end tell'
	```
3. You may get the following errors during building, you need to update Python security package in Terminal by typing `pip install requests[security]`.
	```
	/usr/local/lib/python2.7/dist-packages/requests/packages/urllib3
	/util/ssl_.py:79: InsecurePlatformWarning: A true SSLContext object is not
	available. This prevents urllib3 from configuring SSL appropriately and 
	may cause certain SSL connections to fail. For more information, see 
	https://urllib3.readthedocs.org/en/latest/security.html#insecureplatformwarning.
	``` 
4. **HiddenTarget** is used for supporting Syntax Highlighting and Suggest Completion. It's better to delete its Scheme as we won't build & run it.
	1. Menu: Product -> Scheme -> Manage Schemes...
	2. Choose HiddenTarget, then delete.
5. Everytime you add some new source files, remember to only check target **HiddenTarget**, uncheck all the others.
6. You may fail to upgrade the Pebble SDK on Yosemite or El Capitan with compiling errors, edit `~/pebble-dev/PebbleSDK-3.4/requirements.txt`, and change `gevent==1.0.2` to `gevent==1.1.b1`. ([Discussions](https://github.com/pebble/homebrew-pebble-sdk/issues/18))
7. If your phone can't be connected during the installing, remember to close the Terminal window which is displaying the logs of your real Pebble watch.
8. Now we can install the apps through PebbleCloud! This is very helpful when your phone is on 3G/4G network! Remember to `pebble login` in Terminal before using this script!

	```
	osascript -e 'tell app "Terminal"' -e 'if not (exists window 1) then' -e 'do script ""' -e 'end if' -e 'activate' -e 'end tell' -e 'tell app "System Events" to keystroke "c" using control down' -e 'tell app "Terminal"' -e 'set ctx to do script "" in window 1' -e "do script \"cd ${SRCROOT}\" in ctx" -e 'do script "pebble build" in ctx' -e "do script \"pebble install -v --cloudpebble --logs\" in ctx" -e "do script \"pebble logs -v --cloudpebble\" in ctx" -e 'end tell'
	```
9. If you get compiler error `clang: error: unknown argument: '-meabi=5'` after installing Pebble SDK 4.0, you can fix it by replacing `/usr/local/Cellar/pebble-toolchain/2.0/arm-cs-tools` with an old version supplied with Pebble SDK 3.x.

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

