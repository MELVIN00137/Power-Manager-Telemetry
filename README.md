# Power-Manager-Telemetry
Intel Unnati Project

## Project Description: Power Management Telemetry Analysis

### Project Overview

This project aims to monitor, limit, and analyze CPU usage of applications running on a macOS system. By simulating CPU limitations (80%, 50%, and 30%) and collecting telemetry data, we compare the system performance under these conditions. The data collected includes CPU and memory usage for each application, and the battery status. The comparison and analysis are visualized using various Python libraries.

### Objective

- **Monitor**: Collect telemetry data including CPU and memory usage for each running application and battery status.
- **Limit**: Simulate CPU usage limits by adjusting process priorities to 80%, 50%, and 30%.
- **Analyze**: Compare the CPU usage before and after applying the limitations.
- **Visualize**: Present the results using visualizations for better understanding and insights.

### Tools and Packages Used

1. **psutil**:
   - **Description**: psutil (process and system utilities) is a cross-platform library for retrieving information on running processes and system utilization (CPU, memory, disks, network, sensors) in Python.
   - **Installation**: 
     ```bash
     pip install psutil
     ```
   - **Usage**: Used for collecting system and process-level telemetry data such as CPU and memory usage.

2. **csv**:
   - **Description**: The csv module in Python provides functionality to read from and write to CSV files, allowing data to be easily stored and transferred.
   - **Usage**: Used for saving the collected telemetry data to CSV files for further analysis.

3. **datetime**:
   - **Description**: The datetime module supplies classes for manipulating dates and times in both simple and complex ways.
   - **Usage**: Used for timestamping the collected telemetry data.

4. **subprocess**:
   - **Description**: The subprocess module allows you to spawn new processes, connect to their input/output/error pipes, and obtain their return codes.
   - **Usage**: Used for simulating CPU usage limitations by adjusting process priorities using system commands.

5. **pandas**:
   - **Description**: pandas is a powerful, fast, flexible, and easy-to-use open-source data analysis and data manipulation library built on top of the Python programming language.
   - **Installation**: 
     ```bash
     pip install pandas
     ```
   - **Usage**: Used for data manipulation and analysis, including reading CSV files, grouping data, and calculating averages.

6. **matplotlib**:
   - **Description**: matplotlib is a comprehensive library for creating static, animated, and interactive visualizations in Python.
   - **Installation**: 
     ```bash
     pip install matplotlib
     ```
   - **Usage**: Used for creating visualizations to present the comparison results of CPU usage.

7. **seaborn**:
   - **Description**: seaborn is a Python visualization library based on matplotlib that provides a high-level interface for drawing attractive statistical graphics.
   - **Installation**: 
     ```bash
     pip install seaborn
     ```
   - **Usage**: Used for creating more refined and aesthetically pleasing visualizations.

### Data Collection

The data collection process involves monitoring the system for a specified duration (e.g., 5 minutes) and recording the CPU and memory usage for each application, as well as the battery status. Here is the script for collecting initial data and then limiting the CPU usage to 80%, 50%, and 30%.

#### Initial Data Collection

```python
import psutil
import time
import csv
from datetime import datetime
import subprocess

def get_battery_status():
    battery = psutil.sensors_battery()
    if battery is not None:
        return battery.percent
    return None

def get_cpu_usage_per_app():
    app_cpu_usage = []
    for proc in psutil.process_iter(['pid', 'name', 'cpu_percent']):
        try:
            app_cpu_usage.append({
                'pid': proc.info['pid'],
                'name': proc.info['name'],
                'cpu_percent': proc.info['cpu_percent'],
                'memory_percent': proc.memory_percent(),
                'cpu_time': proc.cpu_times(),
                'num_threads': proc.num_threads()
            })
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            pass
    return app_cpu_usage

def collect_data(file_name, duration):
    with open(file_name, 'w', newline='') as csvfile:
        fieldnames = ['timestamp', 'battery_percent', 'pid', 'app_name', 'cpu_percent', 'memory_percent', 'cpu_time', 'num_threads']
        writer = csv.DictWriter(csvfile, fieldnames=fieldnames)
        writer.writeheader()
        
        end_time = time.time() + duration  # Collect data for the specified duration
        while time.time() < end_time:
            timestamp = datetime.now().isoformat()
            battery_percent = get_battery_status()
            app_cpu_usage = get_cpu_usage_per_app()

            for app in app_cpu_usage:
                writer.writerow({
                    'timestamp': timestamp,
                    'battery_percent': battery_percent if battery_percent is not None else 'N/A',
                    'pid': app['pid'],
                    'app_name': app['name'],
                    'cpu_percent': app['cpu_percent'],
                    'memory_percent': app['memory_percent'],
                    'cpu_time': app['cpu_time'],
                    'num_threads': app['num_threads']
                })
            time.sleep(60)  # Collect data every 60 seconds

def main():
    duration = 300  # Duration to collect data in seconds (5 minutes)
    file_name = 'app_power_telemetry_initial.csv'
    collect_data(file_name, duration)

if __name__ == '__main__':
    main()
```

#### Limiting CPU Usage and Collecting Data

The following scripts limit the CPU usage to 80%, 50%, and 30%, respectively, and collect the corresponding telemetry data.

```python
# The code is the same as the above but with `limit_cpu_usage(proc.info['pid'], 80)`
def limit_cpu_usage(pid, cpu_percent):
    # Simulate CPU usage limitation by adjusting process priority
    subprocess.run(['renice', '-n', str(cpu_percent), '-p', str(pid)], stdout=subprocess.PIPE)

def main():
    duration = 300  # Duration to collect data in seconds (5 minutes)
    file_prefix = 'app_power_telemetry'
    
    # Adjust CPU usage limits (simulate by adjusting process priorities)
    processes = psutil.process_iter(['pid', 'name'])
    for proc in processes:
        try:
            if proc.info['name']:  # Only adjust for processes with a name
                limit_cpu_usage(proc.info['pid'], 80)
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            pass
    
    # Collect data after limiting CPU usage
    collect_data(f'{file_prefix}_limited_80.csv', duration)

if __name__ == '__main__':
    main()
```

Repeat the above script for 50% and 30% CPU limits by changing the `cpu_percent` parameter in the `limit_cpu_usage` function call.

### Data Analysis and Comparison

After collecting the data, we analyze and compare the CPU usage for each limitation.

```python
import pandas as pd

# Load the data for different CPU limitations
initial_data = pd.read_csv('app_power_telemetry_initial.csv')
limited_data_80 = pd.read_csv('app_power_telemetry_limited_80.csv')
limited_data_50 = pd.read_csv('app_power_telemetry_limited_50.csv')
limited_data_30 = pd.read_csv('app_power_telemetry_limited_30.csv')

# Function to group by application name and calculate the average CPU usage
def calculate_avg_cpu(data):
    return data.groupby('app_name')['cpu_percent'].mean()

# Calculate average CPU usage for initial and limited data
initial_avg_cpu = calculate_avg_cpu(initial_data)
limited_avg_cpu_80 = calculate_avg_cpu(limited_data_80)
limited_avg_cpu_50 = calculate_avg_cpu(limited_data_50)
limited_avg_cpu_30 = calculate_avg_cpu(limited_data_30)

# Compare the average CPU usage and calculate the change for each limitation
comparison_80 = pd.DataFrame({
    'initial_avg_cpu': initial_avg_cpu,
    'limited_avg_cpu_80': limited_avg_cpu_80
}).reset_index()
comparison_80['cpu_usage_change_80'] = comparison_80['limited_avg_cpu_80'] - comparison_80['initial_avg_cpu']

comparison_50 = pd.DataFrame({
    'initial_avg_cpu': initial_avg_cpu,
    'limited_avg_cpu_50': limited_avg_cpu_50
}).reset_index()
comparison_50['cpu_usage_change_50'] = comparison_50['limited_avg_cpu_50'] - comparison_50['initial_avg_cpu']

comparison_30 = pd.DataFrame({
    'initial_avg_cpu': initial_avg_cpu,
    'limited_avg_cpu_30': limited_avg_cpu_30
}).reset_index()
comparison_30['cpu_usage_change_30'] = comparison_30['limited_avg_cpu_30'] - comparison_30['initial_avg_cpu']

# Combine all comparisons into a single DataFrame
comparison_combined = pd.merge(comparison_80, comparison_50, on='app_name', suffixes=('_80', '_50'))
comparison_combined = pd.merge(comparison_combined, comparison_30, on='app_name')

# Print the comparison results
print(comparison_combined)

# Save the comparison results to a CSV file
comparison_combined.to_csv('cpu_usage_comparison.csv', index=False)
```

### Visualization

To visualize the comparison of CPU usage before and after applying the CPU limitations, we

 can use the matplotlib and seaborn libraries.

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the comparison data
comparison_data = pd.read_csv('cpu_usage_comparison.csv')

# Filter the top 50 applications by initial CPU usage
top_50_apps = comparison_data.nlargest(50, 'initial_avg_cpu')

# Set up the matplotlib figure
plt.figure(figsize=(12, 8))

# Create a bar plot
sns.barplot(x='app_name', y='cpu_usage_change_80', data=top_50_apps, color='b', label='80% Limit')
sns.barplot(x='app_name', y='cpu_usage_change_50', data=top_50_apps, color='r', label='50% Limit')
sns.barplot(x='app_name', y='cpu_usage_change_30', data=top_50_apps, color='g', label='30% Limit')

# Add labels and title
plt.xlabel('Application Name')
plt.ylabel('Change in CPU Usage (%)')
plt.title('Comparison of CPU Usage Change Under Different Limitations')
plt.legend()

# Rotate the x labels for better readability
plt.xticks(rotation=90)

# Display the plot
plt.tight_layout()
plt.show()

# Save the plot to a file
plt.savefig('cpu_usage_comparison_visualization.png')
```

**Insert Visualization Here**

### Conclusion

This project successfully demonstrates the process of monitoring, limiting, and analyzing CPU usage on a macOS system. By using Python and various libraries, we were able to collect, analyze, and visualize the telemetry data, providing valuable insights into system performance under different CPU limitations. This approach can be extended to other performance metrics and different operating systems for more comprehensive system analysis.

### Future Work

- Implement similar scripts for other operating systems like Windows and Linux.
- Expand the analysis to include other system metrics such as disk I/O and network usage.
- Develop a real-time dashboard to monitor and visualize system performance continuously.
