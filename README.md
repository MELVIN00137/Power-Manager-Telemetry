# Power Management Telemetry Analysis

## Project Overview
The Power Management Telemetry Analysis project aims to monitor, limit, and analyze CPU usage of applications running on a macOS system. By simulating CPU limitations (80%, 50%, and 30%) and collecting telemetry data, we compare the system performance under these conditions. The data collected includes CPU and memory usage for each application, as well as the battery status. The comparison and analysis are visualized using various Python libraries.

## Objective
- **Monitor**: Collect telemetry data, including CPU and memory usage for each running application and battery status.
- **Limit**: Simulate CPU usage limits by adjusting process priorities to 80%, 50%, and 30%.
- **Analyze**: Compare the CPU usage before and after applying the limitations.
- **Visualize**: Present the results using visualizations for better understanding and insights.

## Tools and Packages Used

### Tools
- **JupyterLab and Google Colab**: Used for coding, data collection, and analysis.
- **Activity Monitor**: Used to monitor the performance of the macOS system.

### Packages
- **psutil**: A cross-platform library for retrieving information on running processes and system utilization (CPU, memory, disks, network, sensors) in Python.
    - **Installation**: `pip install psutil`
    - **Usage in This Project**:
        - Collecting system and process-level telemetry data:
            - CPU Usage: `psutil.cpu_percent(interval=1)` and `psutil.Process(pid).cpu_percent(interval=1)`
            - Memory Usage: `psutil.virtual_memory()` and `psutil.Process(pid).memory_percent()`
            - Battery Status: `psutil.sensors_battery()`
        - Monitoring and logging data using `psutil.process_iter()`

- **csv**: A module in Python that provides functionality to read from and write to CSV files.
    - **Usage**: Saving the collected telemetry data to CSV files for further analysis.

- **datetime**: Supplies classes for manipulating dates and times.
    - **Usage**: Timestamping the collected telemetry data.

- **subprocess**: Allows spawning new processes, connecting to their input/output/error pipes, and obtaining their return codes.
    - **Usage**: Simulating CPU usage limitations by adjusting process priorities using system commands.

- **pandas**: A powerful data analysis and manipulation library built on top of the Python programming language.
    - **Installation**: `pip install pandas`
    - **Usage**: Data manipulation and analysis, including reading CSV files, grouping data, and calculating averages.

- **matplotlib**: A comprehensive library for creating static, animated, and interactive visualizations in Python.
    - **Installation**: `pip install matplotlib`
    - **Usage**: Creating visualizations to present the comparison results of CPU usage.

- **seaborn**: A Python visualization library based on matplotlib that provides a high-level interface for drawing attractive statistical graphics.
    - **Installation**: `pip install seaborn`
    - **Usage**: Creating refined and aesthetically pleasing visualizations.

## Architecture Diagram
![Architecture Diagram](https://github.com/user-attachments/assets/39dd778c-5ae7-4f92-8d82-f31d3e376ea1)



## Data Collection
The data collection process involves monitoring the system for a specified duration (e.g., 5 minutes) and recording the CPU and memory usage for each application, as well as the battery status.

### Steps:
1. **Initial Data Collection**:
    - Monitor CPU and memory usage for each running application.
    - Record battery status.
    - Log this data at regular intervals (e.g., every minute) for a specified duration.

      <img width="1295" alt="image" src="https://github.com/user-attachments/assets/403a1187-a52c-4083-b465-404757f5f43d">


2. **Limiting CPU Usage and Collecting Data**:
    - Simulate CPU usage limitations by adjusting process priorities to 80%, 50%, and 30%.
    - Collect and log the same telemetry data under these conditions.
      
**80% LIMITATION:**

<img width="1293" alt="image" src="https://github.com/user-attachments/assets/60495136-e21b-4836-97ee-3449cd09a389">

<img width="675" alt="image" src="https://github.com/user-attachments/assets/68e2e697-25cf-499d-8751-87c24c71d0a6">

<img width="672" alt="image" src="https://github.com/user-attachments/assets/12ec63f4-886f-4beb-84fd-ac33702c17b3">


**50% LIMITATION:**

<img width="1293" alt="image" src="https://github.com/user-attachments/assets/3bb23c1c-3def-40c7-b0b2-81b8dc0eacb4">

<img width="659" alt="image" src="https://github.com/user-attachments/assets/7d689cd7-ddd1-451c-a618-0ec53c59bd2f">

<img width="659" alt="image" src="https://github.com/user-attachments/assets/eb770b15-6f2a-477b-8c16-36b7b59a247b">




**30% LIMITATION:**

<img width="1293" alt="image" src="https://github.com/user-attachments/assets/4b717192-fe72-4b92-842e-547d2e1a6698">





<img width="677" alt="image" src="https://github.com/user-attachments/assets/15ae29be-ca45-4c44-949a-bcaa17ed2823">





<img width="659" alt="image" src="https://github.com/user-attachments/assets/2cdf37b1-a1f8-4436-ad58-ba38fffbb030">



## Data Analysis and Comparison
After collecting the data, we analyze and compare the CPU usage for each limitation.

### Steps:
1. **Load the Data**: Load the CSV files containing telemetry data for different CPU limitations.
2. **Calculate Average CPU Usage**: Group the data by application name and calculate the average CPU usage.
3. **Compare the CPU Usage**: Calculate the change in CPU usage before and after applying the limitations.
4. **Combine the Comparison Results**: Merge the data into a single DataFrame for comprehensive comparison.

<img width="599" alt="Screenshot 2024-07-12 at 10 18 35 PM" src="https://github.com/user-attachments/assets/f3e8b85b-4176-4e45-ad3f-68079ba90691">


## Visualization
Visualizations are created to present the comparison of CPU usage before and after applying the CPU limitations.

### Steps:
1. **Set Up the Visualization**: Use matplotlib and seaborn to create visualizations.
2. **Bar Plot**: Create bar plots to show the change in CPU usage for the top applications.
3. **Labels and Title**: Add labels and titles for clarity.
4. **Save and Display**: Save the visualizations to files and display them.

<img width="905" alt="image" src="https://github.com/user-attachments/assets/b25f052e-3cee-4f01-9ce8-14aed7ad047a">

<img width="905" alt="image" src="https://github.com/user-attachments/assets/fb08031a-0e07-48b2-a619-9de2d51cf79a">



## Conclusion
This project offers a user-friendly solution compatible with Windows, macOS, and Linux, aimed at maximizing system performance and battery life. By intelligently managing CPU and power usage, it minimizes lag during heavy software usage, promotes energy efficiency, and enhances overall user productivity and satisfaction.


## Future Work
- Implement similar scripts for other operating systems like Windows and Linux.
- Expand the analysis to include other system metrics such as disk I/O and network usage.
- Develop a real-time dashboard to monitor and visualize system performance continuously.
