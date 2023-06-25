import platform
import socket
import json
import csv
import os


def get_computer_name():
    return socket.gethostname()


def get_os_info():
    os_name = platform.system()
    os_version = platform.release()
    os_architecture = platform.machine()

    return {
        'Name': os_name,
        'Version': os_version,
        'Architecture': os_architecture
    }


def get_processor_info():
    return platform.processor()


def get_ram_info():
    if platform.system() == 'Windows':
        import wmi
        c = wmi.WMI()
        total_memory = int(c.Win32_ComputerSystem()[0].TotalPhysicalMemory) / (1024 ** 3)
        return f'{total_memory:.2f} GB'
    elif platform.system() == 'Linux':
        with open('/proc/meminfo', 'r') as f:
            total_memory = int(f.readlines()[0].split()[1]) / 1024
            return f'{total_memory:.2f} GB'
    elif platform.system() == 'Darwin':
        import subprocess
        command = 'sysctl -n hw.memsize'
        total_memory = int(subprocess.check_output(command, shell=True)) / (1024 ** 3)
        return f'{total_memory:.2f} GB'
    else:
        return 'N/A'


def get_gpu_info():
    if platform.system() == 'Windows':
        import wmi
        c = wmi.WMI()
        gpu_info = []
        for gpu in c.Win32_VideoController():
            gpu_info.append(f'{gpu.Name} ({gpu.AdapterRAM // (1024 ** 2)} MB)')
        return gpu_info
    elif platform.system() == 'Linux':
        gpu_info = []
        try:
            with open('/var/log/Xorg.0.log', 'r') as f:
                lines = f.readlines()
                for line in lines:
                    if 'NVIDIA' in line:
                        gpu_info.append(line.split(':')[-1].strip())
        except FileNotFoundError:
            pass
        return gpu_info
    elif platform.system() == 'Darwin':
        import subprocess
        command = 'system_profiler SPDisplaysDataType | grep Chipset'
        output = subprocess.check_output(command, shell=True).decode()
        gpu_info = [line.split(':')[-1].strip() for line in output.split('\n') if line.strip()]
        return gpu_info
    else:
        return []


def get_device_storage_info():
    if platform.system() == 'Windows':
        import wmi
        c = wmi.WMI()
        storage_info = []
        for disk in c.Win32_DiskDrive():
            model = disk.Model.strip()
            vendor = disk.Manufacturer.strip()
            capacity = int(disk.Size) / (1024 ** 3)
            storage_info.append({
                'Vendor': vendor,
                'Model': model,
                'Capacity': f'{capacity:.2f} GB'
            })
        return storage_info
    elif platform.system() == 'Linux':
        import subprocess
        command = "lsblk -d -n -o NAME,SIZE,ROTA,VENDOR,MODEL"
        output = subprocess.check_output(command, shell=True).decode()
        lines = output.strip().split('\n')
        storage_info = []
        for line in lines:
            name, size, rota, vendor, model = line.split()
            if rota == '0':
                capacity = int(size) / (1024 ** 3)
                storage_info.append({
                    'Vendor': vendor,
                    'Model': model,
                    'Capacity': f'{capacity:.2f} GB'
                })
        return storage_info
    elif platform.system() == 'Darwin':
        import subprocess
        command = "system_profiler SPSerialATADataType | grep 'Medium Type'"
        output = subprocess.check_output(command, shell=True).decode()
        lines = output.strip().split('\n')
        storage_info = []
        for line in lines:
            vendor, model = line.split(':')[-1].strip().split(' ', 1)
            storage_info.append({
                'Vendor': vendor,
                'Model': model,
                'Capacity': 'N/A'
            })
        return storage_info
    else:
        return []


def export_to_text(info):
    with open('device_info.txt', 'w') as f:
        f.write('Device Information\n')
        f.write('-' * 20 + '\n\n')
        f.write(f'Computer Name: {info["computer_name"]}\n')
        f.write(f'Operating System: {info["os_info"]["Name"]} {info["os_info"]["Version"]} '
                f'({info["os_info"]["Architecture"]})\n')
        f.write(f'Processor: {info["processor_info"]}\n')
        f.write(f'RAM: {info["ram_info"]}\n')
        f.write('GPU:\n')
        for gpu in info['gpu_info']:
            f.write(f'- {gpu}\n')
        f.write('\nDevice Storage:\n')
        for storage in info['storage_info']:
            f.write(f'- Vendor: {storage["Vendor"]}, Model: {storage["Model"]}, Capacity: {storage["Capacity"]}\n')
        f.write('\nExported to text file.')


def export_to_html(info):
    with open('device_info.html', 'w') as f:
        f.write('<html>\n')
        f.write('<head>\n')
        f.write('<title>Device Information</title>\n')
        f.write('</head>\n')
        f.write('<body>\n')
        f.write('<h1>Device Information</h1>\n')
        f.write('<hr>\n\n')
        f.write(f'<h2>Computer Name: {info["computer_name"]}</h2>\n')
        f.write(f'<h2>Operating System: {info["os_info"]["Name"]} {info["os_info"]["Version"]} '
                f'({info["os_info"]["Architecture"]})</h2>\n')
        f.write(f'<h2>Processor: {info["processor_info"]}</h2>\n')
        f.write(f'<h2>RAM: {info["ram_info"]}</h2>\n')
        f.write('<h2>GPU:</h2>\n')
        f.write('<ul>\n')
        for gpu in info['gpu_info']:
            f.write(f'<li>{gpu}</li>\n')
        f.write('</ul>\n\n')
        f.write('<h2>Device Storage:</h2>\n')
        f.write('<ul>\n')
        for storage in info['storage_info']:
            f.write(f'<li>Vendor: {storage["Vendor"]}, Model: {storage["Model"]}, Capacity: {storage["Capacity"]}</li>\n')
        f.write('</ul>\n\n')
        f.write('</body>\n')
        f.write('</html>\n')
        f.write('\nExported to HTML file.')


def export_to_csv(info):
    with open('device_info.csv', 'w', newline='') as f:
        fieldnames = ['Computer Name', 'Operating System', 'Processor', 'RAM', 'GPU Vendor', 'GPU Model',
                      'Device Storage Vendor', 'Device Storage Model', 'Device Storage Capacity']
        writer = csv.DictWriter(f, fieldnames=fieldnames)
        writer.writeheader()
        for storage in info['storage_info']:
            writer.writerow({
                'Computer Name': info['computer_name'],
                'Operating System': f'{info["os_info"]["Name"]} {info["os_info"]["Version"]} '
                                    f'({info["os_info"]["Architecture"]})',
                'Processor': info['processor_info'],
                'RAM': info['ram_info'],
                'GPU Vendor': '',
                'GPU Model': '',
                'Device Storage Vendor': storage['Vendor'],
                'Device Storage Model': storage['Model'],
                'Device Storage Capacity': storage['Capacity']
            })
        for gpu in info['gpu_info']:
            writer.writerow({
                'Computer Name': info['computer_name'],
                'Operating System': f'{info["os_info"]["Name"]} {info["os_info"]["Version"]} '
                                    f'({info["os_info"]["Architecture"]})',
                'Processor': info['processor_info'],
                'RAM': info['ram_info'],
                'GPU Vendor': gpu.split('(')[0].strip(),
                'GPU Model': gpu.split('(')[1].split(')')[0].strip(),
                'Device Storage Vendor': '',
                'Device Storage Model': '',
                'Device Storage Capacity': ''
            })
        f.write('\nExported to CSV file.')


def export_to_json(info):
    with open('device_info.json', 'w') as f:
        json.dump(info, f, indent=4)
        f.write('\nExported to JSON file.')


# Main program
device_info = {
    'computer_name': get_computer_name(),
    'os_info': get_os_info(),
    'processor_info': get_processor_info(),
    'ram_info': get_ram_info(),
    'gpu_info': get_gpu_info(),
    'storage_info': get_device_storage_info()
}

print('Device Information:')
print('-' * 20)
print(f'Computer Name: {device_info["computer_name"]}')
print(f'Operating System: {device_info["os_info"]["Name"]} {device_info["os_info"]["Version"]} '
      f'({device_info["os_info"]["Architecture"]})')
print(f'Processor: {device_info["processor_info"]}')
print(f'RAM: {device_info["ram_info"]}')
print('GPU:')
for gpu in device_info['gpu_info']:
    print(f'- {gpu}')
print('Device Storage:')
for storage in device_info['storage_info']:
    print(f'- Vendor: {storage["Vendor"]}, Model: {storage["Model"]}, Capacity: {storage["Capacity"]}')

print('\nExport Options:')
print('1. Export to text file')
print('2. Export to HTML')
print('3. Export to CSV')
print('4. Export to JSON')

export_option = input('Select an export option (1-4): ')
if export_option == '1':
    export_to_text(device_info)
elif export_option == '2':
    export_to_html(device_info)
elif export_option == '3':
    export_to_csv(device_info)
elif export_option == '4':
    export_to_json(device_info)
else:
    print('Invalid option. Export aborted.')
