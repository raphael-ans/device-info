## Device Information Python Script

This Python script retrieves device information such as computer name, operating system details, processor, RAM, GPU, and device storage. It provides users with options to export this information in various formats, including text file, HTML, CSV, or JSON.

### Functionality

1. The script imports necessary modules and libraries based on the operating system, including `socket`, `platform`, `json`, `csv`, and `os`.

2. Several functions are defined to retrieve specific device information:

   - `get_computer_name()`: Retrieves the computer name using the `socket` library.

   - `get_os_info()`: Retrieves the operating system's name, version, and architecture using the `platform` library.

   - `get_processor_info()`: Retrieves the processor information using the `platform` library.

   - `get_ram_info()`: Retrieves RAM information based on the operating system. Different approaches are used for Windows, Linux, and macOS.

   - `get_gpu_info()`: Collects GPU information based on the operating system. Different approaches are used for Windows, Linux, and macOS.

   - `get_device_storage_info()`: Gathers device storage information based on the operating system. Different approaches are used for Windows, Linux, and macOS.

3. The script defines four export functions for text, HTML, CSV, and JSON formats. Each function takes the device information as input and exports it accordingly.

4. The main program performs the following steps:

   - It creates a dictionary called `device_info` and populates it with the retrieved device information using the previously defined functions.

   - The device information is displayed on the console.

   - The user is prompted to select an export option (1-4) to export the information in their desired format.

   - Based on the selected export option, the corresponding export function is called with the `device_info` as input.

   - The exported file is saved in the current directory with the corresponding format and appropriate file extension.

### Usage

1. Install necessary dependencies for each operating system (e.g., `wmi` for Windows, `subprocess` for macOS, etc.).

2. Run the script in a Python environment compatible with the required dependencies.

3. The script retrieves and displays the device information on the console.

4. Select an export option (1-4) when prompted to export the information in the desired format.

5. The exported file will be saved in the current directory with the appropriate format and file extension.
