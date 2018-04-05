# stm32pio
Small Python app that can create and update [PlatformIO](https://platformio.org) project from [STM32CubeMX](http://www.st.com/en/development-tools/stm32cubemx.html) `.ioc` file.

## Features
  - Start new project in a single directory using only `.ioc` file
  - Update existing project to add/change hardware options from CubeMX
  - *[optional]* Automatically run your favorite editor in the end

## Requirements:
  - For this app:
    - Python 3.5+
  - For usage:
    - macOS or Linux OS
    - STM32CubeMX (all recent versions) with downloaded needed framework (F0, F1, ...). Try to generate code in the ordinary way (through the GUI) at least once before running stm32pio
    - Java CLI (probably already installed for STM32CubeMX)
    - PlatformIO CLI (if using from IDE (for example, Atom), run (macOS and Linux) `Menubar -> PlatformIO -> Install Shell Commands`). Therefore, currently stm32pio doesn't support Windows due to the lack of PlatformIO CLI.

## Usage
Check `settings.py` to make sure that all user-specific parameters are valid. Run
```sh
$ python3 stm32pio.py --help
```
to see help.

## Example
1. Run CubeMX, choose MCU/board, do all necessary stuff
2. Open `Project -> Settings` menu, set Project Name, choose Other Toolchains (GPDSC). In Code Generator tab check "Copy only the necessary library files" and "Generate periphery initialization as a pair of '.c/.h' files per peripheral"
3. Back in the first tab (Project) copy the "Toolchain Folder Location" string. Click OK
4. Use copied string as a `-d` argument for stm32pio
5. Run `pio boards` to list all supported devices. Pick one and use its ID as a `-b` argument (for example, `nucleo_f031k6`)
6. All done. You can now run
```sh
$ python3 stm32pio.py new -d /path/to/cubemx/project -b nucleo_f031k6 --start-editor=vscode
```
to complete generation.

## Testing
Since ver. 0.45 there are some unit-tests in file `test.py` (based on the unittest module). Run
```sh
$ python3 -m unittest -v tests.py
```
to test the app. You need to edit some parameters in this file to match your set-up: specify 100% correct `path` and `board`. **WARNING** Running tests erase all content of `path` except the `.ioc` file.
