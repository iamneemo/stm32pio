# stm32pio
Small cross-platform Python app that can create and update [PlatformIO](https://platformio.org) projects from [STM32CubeMX](http://www.st.com/en/development-tools/stm32cubemx.html) `.ioc` files.


## Features
  - Start the new project in a single directory using only `.ioc` file
  - Update existing project after adding/changing hardware options from CubeMX
  - Clean-up the project (WARNING: it deletes ALL content of 'path' except the `.ioc` file!)
  - *[optional]* Automatically run your favorite editor in the end
  - *[optional]* Make an initial build of the project


## Restrictions
  - The tool doesn't check for different parameters compatibility, e.g. CPU frequency, memory sizes and so on. It simply ease your workflow with these 2 programs (PlatformIO and STM32CubeMX) a little bit.
  - CubeMX middlewares doesn't support yet because it's hard to be prepared for every possible configuration. You need to manually adjust them to build appropriately. For example, FreeRTOS can be added via PlatformIO' `lib` feature or be directly compiled in its own directory using `lib_extra_dirs` option:
    ```ini
    lib_extra_dirs = Middlewares/Third_Party/FreeRTOS
    ```
    You also need to move all `.c`/`.h` files to the `src`/`include` folders respectively. See PlatformIO documentation for more information.


## Requirements:
  - For this app:
    - Python 3.6+
  - For usage:
    - macOS, Linux, Windows
    - STM32CubeMX (all recent versions) with downloaded necessary frameworks (F0, F1, etc.). Try to generate code in ordinary way (through the GUI) at least once before running stm32pio
    - Java CLI (JRE) (likely is already installed if STM32CubeMX works)
    - PlatformIO CLI.


## Usage
Basically, you need to follow such pattern:
  1. Create CubeMX project, set-up hardware configuration
  2. Run stm32pio that automatically invoke CubeMX to generate the code, create PlatformIO project, patch an '.ini' file and so on
  3. Work on the project in your editor, compile/upload/debug it
  4. Edit the configuration in CubeMX when necessary, then run stm32pio to regenerate the code.

Refer to Example section on more detailed steps.

stm32pio will create an accessory file 'cubemx-script' in your project directory that contains commands passed to CubeMX. You can safely delete it (it will be created again on the next run) or edit corresponding to your goals.

Check `settings.py` to make sure that all user-specific parameters are valid. Run
```bash
$ python3 stm32pio.py --help
```
to see help.


## Example
1. Run CubeMX, choose MCU/board, do all necessary stuff
2. Open `Project -> Settings` menu, specify Project Name, choose Other Toolchains (GPDSC). In Code Generator tab check "Copy only the necessary library files" and "Generate periphery initialization as a pair of '.c/.h' files per peripheral" options

![Code Generator tab](/screenshots/tab_CodeGenerator.png)

3. Back in the first tab (Project) copy the "Toolchain Folder Location" string. Click OK, close CubeMX

![Project tab](/screenshots/tab_Project.png)

4. Use copied string as a `-d` argument for stm32pio. So it is assumed that the name of the project folder matches the name of `.ioc` file. (`-d` argument can be omitted if your current working directory is already a project directory)
5. Run `platformio boards` (`pio boards`) or go to [boards](https://docs.platformio.org/en/latest/boards) to list all supported devices. Pick one and use its ID as a `-b` argument (for example, `nucleo_f031k6`)
6. All done. You can now run
```bash
$ python3 stm32pio.py new -d /path/to/cubemx/project -b nucleo_f031k6 --start-editor=vscode
```
to complete generation and start the Visual Studio Code editor with opened folder (as example, not required). Make sure you have all tools in PATH (`java` (or set in `settings.py`), editor, Python)

7. If you will be in need to update hardware configuration in the future, make all necessary stuff in CubeMX and run `generate` command in a similar way:
```bash
$ python3 stm32pio.py generate -d /path/to/cubemx/project
```
8. To clean-up the folder and keep only `.ioc` file run `clean` command


## Testing
Since ver. 0.45 there are some unit-tests in file `tests.py` (based on the unittest module). Run
```bash
$ python3 tests.py -v
```
to test the app. It uses STM32F0 framework to generate and build a code from the `./stm32pio-test/stm32pio-test.ioc` file.


## Notes
  - CI is hard to implement for all target OSes during the requirement to have all tools (PlatformIO, Java, CubeMX, etc.) installed during the test. For example, ST doesn't even provide a direct link to CubeMX for downloading
