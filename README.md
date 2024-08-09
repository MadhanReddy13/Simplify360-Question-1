# Simply360-Question-1
from collections import defaultdict, deque

class Task:
    def __init__(self, name, duration, dependencies):
        self.name = name
        self.duration = duration
        self.dependencies = dependencies
        self.earliest_start_time = 0
        self.latest_finish_time = float('inf')
        self.earliest_finish_time = 0
        self.latest_start_time = float('inf')

def calculate_times(tasks):
    graph = defaultdict(list)
    in_degree = defaultdict(int)

    for task in tasks.values():
        for dep in task.dependencies:
            graph[dep].append(task.name)
            in_degree[task.name] += 1
    queue = deque(task for task in tasks if in_degree[task] == 0)
    while queue:
        task_name = queue.popleft()
        task = tasks[task_name]
        task.earliest_finish_time = task.earliest_start_time + task.duration
        for neighbor in graph[task_name]:
            tasks[neighbor].earliest_start_time = max(tasks[neighbor].earliest_start_time, task.earliest_finish_time)
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
                
    queue = deque(task for task in tasks if not graph[task])
    for task in tasks.values():
        task.latest_finish_time = task.earliest_finish_time

    while queue:
        task_name = queue.popleft()
        task = tasks[task_name]
        for neighbor in graph:
            if task_name in graph[neighbor]:
                tasks[neighbor].latest_start_time = min(tasks[neighbor].latest_start_time, task.latest_finish_time - tasks[neighbor].duration)
                queue.append(neighbor)
    
    earliest_completion_time = max(task.earliest_finish_time for task in tasks.values())
    latest_completion_time = min(task.latest_finish_time for task in tasks.values())
    
    return earliest_completion_time, latest_completion_time

def main():
    tasks = {
        'A': Task('A', 3, []),
        'B': Task('B', 2, ['A']),
        'C': Task('C', 4, ['A']),
        'D': Task('D', 1, ['B','C']),
    }
    
    earliest, latest = calculate_times(tasks)
    print(f"Earliest completion time: {earliest}")
    print(f"Latest completion time: {latest}")
    
