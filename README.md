# cpuscheduling
sajedul islam sifat
IUBAT
Computer scince and Engenering
import random
import numpy as np

def generate_burst_times(num_processes, mean_burst_time):
    return [max(1, int(np.random.poisson(mean_burst_time))) for _ in range(num_processes)]

def generate_arrival_times(num_processes, mean_arrival_time):
    return [int(random.gauss(mean_arrival_time, 1)) for _ in range(num_processes)]

def calculate_waiting_time(turaround_times, burst_times):
    return [turaround_times[i] - burst_times[i] for i in range(len(turaround_times))]
def calculate_turanaround_time(completion_times,arrival_times):
    return [completion_times[i]-arrival_times[i] for i in range(len(completion_times))]
def fcfs(arrival_times, burst_times):
    completion_times = [max(arrival_times[0], burst_times[0])]
    for i in range(1, len(arrival_times)):
        completion_times.append(max(completion_times[i - 1] + burst_times[i], arrival_times[i]))
 
    waiting_times = calculate_waiting_time(completion_times, burst_times)
    avg_waiting_time = sum(waiting_times) / len(waiting_times) 
    turaround_time=calculate_turanaround_time(completion_times,arrival_times)
    avg_turaround_time=sum(turaround_time)/len(turaround_time)
    return avg_waiting_time,avg_turaround_time

def sjf(arrival_times, burst_times):
    sorted_indices = sorted(range(len(burst_times)), key=lambda x: burst_times[x])
    completion_times = [max(arrival_times[sorted_indices[0]], burst_times[sorted_indices[0]])]
    for i in range(1, len(sorted_indices)):
        completion_times.append(max(completion_times[i - 1] + burst_times[sorted_indices[i]], arrival_times[sorted_indices[i]]))

    waiting_times = calculate_waiting_time(completion_times, burst_times)
    turaround_time=calculate_turanaround_time(completion_times,arrival_times)
    avg_waiting_time = sum(waiting_times) / len(waiting_times)
    avg_turaround_time=sum(turaround_time) / len(turaround_time)
   
    return avg_waiting_time,avg_turaround_time

def srtf(arrival_times, burst_times):
    n = len(arrival_times)
    remaining_time = burst_times.copy()
    waiting_time = [0] * n
    completion_times = [0] * n

    current_time = 0
    while any(remaining_time):
        shortest_index = None
        shortest_time = float('inf')

        for i in range(n):
            if remaining_time[i] > 0 and remaining_time[i] < shortest_time:
                shortest_time = remaining_time[i]
                shortest_index = i

        if shortest_index is not None:
           
            execute_time = min(shortest_time, remaining_time[shortest_index])
            remaining_time[shortest_index] -= execute_time
            current_time += execute_time

            
            for i in range(n):
                if i != shortest_index and remaining_time[i] > 0:
                    waiting_time[i] += execute_time

            
            if remaining_time[shortest_index] == 0:
                completion_times[shortest_index] = current_time
               
    turaround_time=calculate_turanaround_time(completion_times,arrival_times)
    waiting_time=calculate_waiting_time(completion_times, burst_times)
    avg_waiting_time = sum(waiting_time) / n
    avg_turaround_time=sum(turaround_time) / n

    return avg_waiting_time,avg_turaround_time

def round_robin(arrival_times, burst_times, time_quantum):
    
    n = len(arrival_times)
    remaining_time = burst_times.copy()
    waiting_time = [0] * n
    completion_times= [0] * n

    current_time = 0
    while any(remaining_time):
        for i in range(n):
            execute_time = min(time_quantum, remaining_time[i])
            remaining_time[i] -= execute_time
            current_time += execute_time

            for j in range(n):
                if i != j and remaining_time[j] > 0:
                    waiting_time[j] += execute_time

            
            if remaining_time[i] == 0:
                completion_times[i] = current_time
    

    turaround_time=calculate_turanaround_time(completion_times,arrival_times)
    waiting_time=calculate_waiting_time(completion_times, burst_times)
    avg_waiting_time = sum(waiting_time) / n
    avg_turaround_time=sum(turaround_time)/n

    return avg_waiting_time,avg_turaround_time
if __name__ == "__main__":
    num_processes = int(input("Enter the number of processes: "))
    mean_burst_time = float(input("Enter the mean burst time: "))
    mean_arrival_time = float(input("Enter the mean arrival time: "))
    time_quantum=float(input("Enter the time quantum:"))

    burst_times = generate_burst_times(num_processes, mean_burst_time)
    arrival_times = generate_arrival_times(num_processes, mean_arrival_time)
    from tabulate import tabulate
    table_data=[]
    print("\n Process informatiom")
    for i in range(num_processes):
        table_data.append([f"p{i+1}\t", burst_times[i],arrival_times[i],"ms"])
    print(tabulate(table_data,headers=["process","Burst Time","Arrival Time"]))
   
    avg_waiting_fcfs,avg_turaround_time_fcfs = fcfs(arrival_times, burst_times)
    avg_waiting_sjf,avg_turaround_time_sjf = sjf(arrival_times, burst_times)
    avg_waiting_srtf,avg_turaround_time_srtf=srtf(arrival_times, burst_times)
    avg_waiting_rr,avg_turaround_time_rr=round_robin(arrival_times, burst_times, time_quantum)
    print(f"FCFS: Avg Waiting Time = {avg_waiting_fcfs:.2f}")
    print(f"FCFS:Avg Turaround Time={avg_turaround_time_fcfs:.2f}")
    print(f"SJF: Avg Waiting Time = {avg_waiting_sjf:.2f}")
    print(f"SJF: Avg Turaround Time = {avg_turaround_time_sjf:.2f}")
    print(f"SRTF: Avg Waiting Time = {avg_waiting_srtf:.2f}")
    print(f"SRTF: Avg Turaround Time = {avg_turaround_time_srtf:.2f}")
    print(f"RR: Avg Waiting Time = {avg_waiting_rr:.2f}")
    print(f"RR: Avg Turaround Time = {avg_turaround_time_rr:.2f}")
     
    
    
