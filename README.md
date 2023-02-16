# TinyML Project

## Envionments
 * STM32CubeMX(https://www.st.com/en/development-tools/stm32cubemx.html)
 * MDK5-ARM(https://www2.keil.com/mdk5)
 * ST-Link Driver(https://www.st.com/content/my_st_com/en/products/development-tools/software-development-tools/stm32-software-development-tools/stm32-utilities/stsw-link009.license=1656325086116.product=STSW-LINK009.version=2.0.2.html)
 * STM32 Board (e.g. NUCLEO-L432KC)
 * Containing the structure and parameters of the learning model .onnx file (if trained by Pytorch)

## How to deploy training model?
 * **STM32CubeMX**  
 * Open STM32CubeMX and click ACCESS TO BOARD SELECTER
 * Select your board (Click Yes in the selection window "Initialize all peripherals with their default Mode ?")
 * Click Software Packs and Manage Software Pack
 * In Categoly STMicroelectronics, Click the check box of Artifacial Intelligence(v7.1.0 or later version) under X.CUBE.AI and Install
 * Go back to the first page, click again Software Pack - Select Components
 * Expand STMicroelectronics.X-CUBE-AI
 * Check Artificial Intelligence X-CUBE-AI - Core
 * Select Device Application - ApplicationTemp
 * In the first page, select TIM2 from Timers in the category on the left(The TIM must be enabled to perform accurate inference latency measurement)
 * Clock Source - Internal Clock and check Enabled in NVIC settings at the bottom
 * In Parameter settings, change prescaler, Counter Period, auto-reload preload (each 9, 5000, Enable)
 * Go to the software pack in the categories.
 * Click Yes in a window window that automatically adjusts the clock and other parameters when clicked
 * Select new network from configuration
 * Change Keras -> ONNX, and import our models in .onnx
 * After choosing model, you can click Analyze to view the resources needed to run the model.
 * Go to Project Manager, Change Application - Basic and write Project Name\
 * Choose Toolchain / IDE - MDK-ARM
 * **The stack and heap sizes of the original Keil project were set as just 0x800 (2,048 bytes each), which caused unknown bugging behaviours in running.**
  * **After setting stack and heap sizes as 0x3000 and 0x5000, our code was safely run.**  
 * In order to operate the header file later, don't forget to check option Generate peripheral initialization as a pair of '.c/.h' files per peripheral in the Code Generator when generating code in Project Manager-Code Generator.
 * TIM in LL library should be selected in Project Manager-Advance Settings.

 * **MDK-ARM (Keil)**
 * After generating C source code based project, you should replace certain files in order to enable evaluation. You could directly copy and paste the files to the generated project in Keil.(four file - usart.c, app_x-cube-ai.c, app_x-cube-ai.h, main.c)
 * When build the project, make sure that you check Use MicroLIB in Setting<img src="img/Option_icon.png">->Target->Code Generation.
 <img src="img/option_target.png">
 * Click Build <img src="img/build_icon.png">
 * **If an error like <L6050U: The code size of this image exceeds the maximum allowed for this version...> occur, your model size should be reduced. The Lite version can only be compiled up to 32 Kb.**
 <img src="img/build_error.png">
 * **One way is to reduce the size by changing Optimization from C/C++ (Tap) to -Oz image size or -Os balanced in the Option. (But it's not going to be much.)**
 * Build is complete, you can now upload the model on the your board. 
 * Click Download button or F8

 * **Validation in python**
 * You need to set up before running on Python.
 * First, go to Device Manager in Windows.
 * On the Ports tab, check the number of COM ports the device is connected (e.g. (STMicroelectronics STLINK Virtual COM Port(COM4))).
 * And click STMicroelectronics STLINK Virtual COM Port to go to the Port Settings tab and change Bit/S(B) to 115200.
 * Then, you go down to the bottom of the validation.py file, you will see argparser codes like above. Here, '--com', '--path_data' must be modified.
 * com is the name of the port to which the board is connected, and path_data is the location of the data set that was used when learning.
 * **(The verification is conducted in the Windows environment, so you must download dataset to Windows and enter the location in default='path' path.)**
 * Finally, you must install the required Python library before running.(serial, numpy, scikit-learn, tqdm, pyserial)
 * "pip install serial numpy scikit-learn tqdm pyserial"
 * **If an error such as "ERROR: Error [WinError 225]" occurred during installation, shut down the Windows Defender and all vaccines.**
 * When you finish installing the library, you can run it through "python validation.py" 
 * Finally, press the Reset button located at the top of the board to start verification.
