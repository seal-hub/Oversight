# Oversight

This is the artifact repository of the paper

Forough Mehralian\*, Navid Salehnamadi\*, Syed Fatiul Huq, and Sam Malek, "**Too Much Accessibility is Harmful! Automated Detection and Analysis of Overly Accessible Elements in Mobile Apps**" in 2022 37th IEEE/ACM International Conference on Automated Software Engineering (ASE), 2022

## Abstract

Mobile apps, an essential technology in today's world, should provide equal access to all, including 15% of the world population with disabilities. Assistive Technologies (AT), with the help of Accessibility APIs, provide alternative ways of interaction with apps for disabled users who cannot see or touch the screen. Prior studies have shown that mobile apps are prone to the under-access problem, i.e., a condition in which functionalities in an app are not accessible to disabled users, even with the use of ATs. We study the dual of this problem, called the over-access problem, and defined as a condition in which an AT can be used to gain access to functionalities in an app that are inaccessible otherwise. Over-access has severe security and privacy implications, allowing one to bypass protected functionalities using ATs, e.g., using VoiceOver to read notes on a locked phone. Over-access also degrades the accessibility of apps by presenting to disabled users information that is actually not intended to be available on a screen, thereby confusing and hindering their ability to effectively navigate. In this work, we first empirically study overly accessible elements in Android apps and define a set of conditions that can result in over-access problem. We then present OverSight, an automated framework that leverages these conditions to detect overly accessible elements and verifies their accessibility dynamically using an AT. Our empirical evaluation of OverSight on real-world apps demonstrates OverSight's effectiveness in detecting previously unknown security threats, workflow violations, and accessibility issues.

## Setup
This repository is tested on Mac OSX; however, it should work on any Unix system For Windows systems, you need to either use bash or modify the scripts in `scripts` directory.

- (For OS X) Install coreutils "brew install coreutils"
- Use Java8, if there are multiple Java versions use [jenv](https://www.jenv.be/)
- Set `ANDROID_HOME` environment varilable (usually `export ANDROID_HOME=~/Library/Android/sdk`)
- Add emulator and platform tools to `PATH` (if it's not already added). `export PATH="$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:${PATH}"`
- For other UNIX system: it's not tested
- (Optional) create a virtual environment in `.env` (`python3 -m venv .env`)
- Run `source env`
- Install python packages `pip install -r requirements.txt`
- Initialize an Android Virtual Device (AVD) with SDK +28
- (Optional) To prevent any interrupt on the process of validating the apps, like random notifications during the analysis of an app, you can follow these steps.
	- Disable soft main keys and virtual keyboard by adding `hw.mainKeys=yes` and `hw.kayboard=yes`  to `~/.android/avd/testAVD_1.avd/config.ini`
		- If virtual device is not disabled, please follow this [link](https://support.honeywellaidc.com/s/article/CN51-Android-How-to-prevent-virtual-keyboard-from-popping-up)
	- Enable "Do not disturb" in the emulator to avoid notifications during testing (it can be found at the top menu)
- Install TalkBack, the version 12 can be found in `Setup/X86/TB_12_*.apk` (`adb install-multiple Setup/X86/TB_12_*.apk`)
- Build Oversight Service APK by running `./build_oversight.sh`, then install it (`adb install -r -g Setup/oversight.apk`) or install from Android Studio
	- To check if the installation is correct, first run the emulator and then execute `./scripts/enable-talkback.sh` (by clicking on a GUI element it should be highlighted).
	- Also, execute `./scripts/send-command.sh log` and check Android logs to see if Latte prints the AccessibilityNodeInfos of GUI element on the screen (`adb logcat | grep "LATTE_SERVICE"`)
-  Save the base snapshot by `./scripts/save_snapshot.sh BASE`


## Oversight CLI
You can interact with Oversight by sending commands to its Broadcast Receiver or receive generated information from Oversight by reading files from the local storage. First, you need to enable Oversight by running `./scritps/enable-service.sh`, then you can send command by running `./scripts/send-command.sh <COMMAND> <EXTRA>`. If you want to work with TalkBack, first you need to enable it by running `./scritps/enable-talkback.sh`. If any command has an output written in a file, you can use `./scripts/wait_for_file.sh <FILE_NAME>` which prints the content of the file and removes it. It's encouraged to watch the logs in a separate terminal `adb logcat | grep "LATTE_SERVICE"`. Here is the list of all commands:
- **General**
	- `log`: Prints the current layout's xpaths in Android logs.
	- `is_live`: Given a string as the extra, creates a file `is_live_<extra>.txt`. It can be used to determine Latte is alive.
	- `invisible_nodes`: Make Latte does (not) consider invisible nodes. Extra can be 'true' or 'false'
	- `capture_layout`: Dumps the XML file of the current layout. Output's file name: `a11y_layout.xml`
	- `report_a11y_issues`: Prints the accessibility issues (reported by Accessibility Testing Framework) in Android logs.
	- `sequence`: Execute a sequence of commands which is given in input as a JSON string. Example: [{'command': 'log', 'extra': 'NONE'}]

- **TalkBack Navigation**
	- `nav_next`: Navigates the focused element to the next element. Output's file name: `finish_nav_action.txt`
	- `nav_select`: Selects the focused element (equivalent to Tap). Output's file name: `finish_nav_action.txt`
	- `nav_interrupt`: Interrupt the current navigation action
	- `nav_clear_history`: In case the last navigation result is not removed.
- **TalkBack Information**
	- `nav_current_focus`: Report the current focused node in TalkBack. Output's file name: `finish_nav_action.txt`

- **UseCase Executor**
	- `enable`/`disable`: Enable/Disable the use-case executor component
	- `set_delay`: Sets the time for each interval (cycle).
	- `set_step_executor`: Sets the driver (step_executor). The extra can be `talkback`, `regular` (touch based), `sighted_tb` (touch based TalkBack).
	- `set_physical_touch`: If the extra is 'true', the regular executor emulates *touch*, otherwise it uses A11yNodeInfo events to perform actions.
	- `step_execute`: Performs a single step where the step is provided in extra.
	- `step_interrupt`: Interrupts the current step execution
	- `step_clear`: Stops the current step execution and remove the step result
	- `init`: Initializes a use case, the use case speicfication is provided in extra.
	- `start`: Starts the use case (`init` must be called beforehand)
	- `stop`: Stops the current use case execution

## Additional artifacts
- [Examples](https://drive.google.com/file/d/1EtH5Ou98iA027w4MuuPlQunms4snGmZn/view?usp=sharing)
- [Visualizer](https://drive.google.com/file/d/1-ACFgLnSEEhsPSITbITIPSOasm4eWtLx/view?usp=sharing)
- [Test set](https://docs.google.com/spreadsheets/d/1vUYE1D6dctMAVtshDctvS4rPpLTryOPgG66OgEF8rrw/edit#gid=0)
