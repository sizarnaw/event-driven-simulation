from random import choices
import sys
from numpy import random


def parseInput():
    input_simu = sys.argv[1:]
    time = int(input_simu[0])
    num_servers = int(input_simu[1])
    prob_list = [float(prob) for prob in input_simu[2:num_servers + 2]]
    lam = float(input_simu[num_servers + 2])
    capacity_list = [int(capacity) for capacity in input_simu[num_servers + 3:3 + 2 * num_servers]]
    mu_list = [float(x) for x in input_simu[2 * num_servers + 3:3 * num_servers + 3]]
    return time, num_servers, prob_list, lam, capacity_list, mu_list


class Event:
    def __init__(self, arrivalTime, requiredTime, StartTime):
        self.arrivalTime = arrivalTime
        self.requiredTime = requiredTime
        self.StartTime = StartTime


class Simulator:
    def __init__(self, time, num_servers, prob_list, lam, capacity_list, mu_list):
        self.MaxService = time
        self.num_servers = num_servers
        self.prob_list = prob_list
        self.lam = lam
        self.capacity_list = capacity_list
        self.mu_list = mu_list
        self.tasks = 0
        self.thrown = 0
        self.waiting_time = 0.0
        self.service_time = 0.0
        self.end_last_event_time = 0.0
        self.queues = [[]] * num_servers
        self.current_time = 0.0
        self.num_of_messages_serviced = 0
        self.avg_service_time = 0.0

    def simulator(self):
        avg_waiting_time = 0
        num_of_messages_not_thrown_out = 0
        self.current_time += random.exponential(1.0 / self.lam)
        while self.current_time < self.MaxService:
            # print(len(self.queues[0]), len(self.queues[1]), sep=" ")
            # print(self.current_time, len(self.queues), len(self.queues[0]), len(self.queues[1]), sep=" ")
            queue_index = choices(range(self.num_servers), self.prob_list)[0]
            # print(queue_index)
            self.refresh_all_queues()
            event = self.insert_event(queue_index)
            if event is not None:
                avg_waiting_time += event.StartTime - event.arrivalTime
                num_of_messages_not_thrown_out += 1
            self.current_time += random.exponential(1.0 / self.lam)

        # calculating the messages that remained in the queue when current_time=MaxService
        self.current_time = self.MaxService
        self.refresh_all_queues()
        # print(self.current_time)

        avg_waiting_time = avg_waiting_time / num_of_messages_not_thrown_out
        self.avg_service_time = self.service_time / self.tasks  # self.avg_service_time / self.num_of_messages_serviced

        return self.tasks, self.thrown, self.end_last_event_time, avg_waiting_time, self.avg_service_time

    def finished_event(self, event):
        # print(self.current_time, event.StartTime,event.requiredTime,sep=" ")
        return self.current_time >= (event.StartTime + event.requiredTime)

    def refresh_all_queues(self):
        for index in range(self.num_servers):
            self.queues[index] = [event for event in self.queues[index] if
                                  self.current_time < event.StartTime + event.requiredTime]

    def insert_event(self, queue_index):
        # check if its empty is considered 1 or 0 . if its 1 we should plus 1 to the latter
        # print(len(self.queues[queue_index]) , self.capacity_list[queue_index], sep= " ")
        if len(self.queues[queue_index]) == self.capacity_list[queue_index]+1:
            # print("=")
            self.thrown += 1
            return None
        else:
            self.tasks += 1
            required_working_time = random.exponential(1.0 / self.mu_list[queue_index])
            # print(required_working_time)
            if len(self.queues[queue_index]) == 0:
                start_time = self.current_time
            else:
                start_time = max(self.queues[queue_index][-1].StartTime + self.queues[queue_index][-1].requiredTime,
                                 self.current_time)
            # start_time = self.current_time if not self.queues[queue_index] else start_time
            event = Event(self.current_time, required_working_time, start_time)
            self.queues[queue_index].append(Event(self.current_time, required_working_time, start_time))
            # print(len(self.queues[0]), len(self.queues[1]))
            # check if we need to get all the service time that are running or only what it should run ( required_working_time)
            # that we should put self.service_time += required_working_time+(start_time - self.current_time)
            self.end_last_event_time = max(self.end_last_event_time, (event.StartTime + event.requiredTime))
            self.service_time += required_working_time + start_time - self.current_time
            self.waiting_time += start_time - self.current_time
            return event


if __name__ == "__main__":
    time, num_servers, prob_list, lam, capacity_list, mu_list = parseInput()
    s = Simulator(time, num_servers, prob_list, lam, capacity_list, mu_list)
    A, B, T_end, T_w_, T_s_ = s.simulator()
    print(A, B, T_end, T_w_, T_s_, sep=" ")
