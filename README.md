# Display-Clock
# Author: Rosie Pham and Tia Merheb
# File: addressbook.py
# Date: September 26, 2020
Create a clock that displays the current time and allows the user to stop/ start

import math 
import datetime
import tkinter as tk
from enum import Enum
from tkinter import StringVar

class Display_Clock:
    def __init__(self):
        ''' Instance variables for clock formatting '''
        self.state = State.PAUSED
        self.size = 200
        self.clock_face_scale = .8
        self.hour_hand_scale = .5
        self.minute_hand_scale = .65
        self.second_hand_scale = .8
        self.update_time_millis = 1000
        self.calc_length_coord() # Calculates the length of the hand and coordinates for the clock
        self.window = tk.Tk() # Create a window
        self.window.title("Current Time") # Set a title

        '''Create frames'''
        # Clock frame
        self.clock = tk.Canvas(self.window, width = self.size, height = self.size)
        self.clock.grid(row = 1, column = 1)
            # Clock outline
        self.clock.create_oval(self.upper_coord, self.upper_coord, self.lower_coord, self.lower_coord)
            # Hour labels
        self.clock.create_text(self.size//2, self.upper_coord + 10, text = "12")
        self.clock.create_text(self.lower_coord - 10, self.size//2, text = "3")
        self.clock.create_text(self.size//2, self.lower_coord - 10, text = "6")
        self.clock.create_text(self.upper_coord + 10, self.size//2, text = "9")
        
        # Time frame
        self.time_frame = tk.Frame(self.window)
        self.time_frame.grid(row = 2, column = 1)
        self.time = StringVar()
        # Time label
        self.time_label = tk.Label(self.time_frame, textvariable = self.time)
        self.time_label.grid(row = 1, column = 1)
        self.get_time()
        
        # Buttons frame
        self.buttons_frame = tk.Frame(self.window)
        self.buttons_frame.grid(row = 3, column = 1)
            # Stop/start button
        self.start_stop_button = tk.Button(self.buttons_frame, text = "Stop", command = self.start_stop)
        self.start_stop_button.grid(row = 1, column = 1)
            # Quit button
        self.quit_button = tk.Button(self.buttons_frame, text = "Quit", command = self.quit)
        self.quit_button.grid(row = 1, column = 2)
        
        self.start_stop() # Starts counting the time
        tk.mainloop()

    def calc_length_coord(self):
        # Calculates length of clock hands
        self.hour_hand_length = (self.hour_hand_scale * self.clock_face_scale * self.size) / 2
        self.minute_hand_length = (self.minute_hand_scale * self.clock_face_scale * self.size) / 2
        self.second_hand_length = (self.second_hand_scale * self.clock_face_scale *self.size) / 2
        # Creates coordinates for clock placement
        self.upper_coord = .5 * self.size * (1 - self.clock_face_scale)
        self.lower_coord = .5 * self.size * (1 + self.clock_face_scale)

    # Starts and stops the time
    def start_stop(self):
         # Gets current time and updates it 
         # As the PAUSED state ends, the clock starts updating again
        if self.state == State.PAUSED:
            self.timer = self.clock.after(
                self.update_time_millis, self.step_handler)
            self.state = State.RUNNING
            self.start_stop_button["text"] = "Stop"
        # As the RUNNING state ends, the clock stops updating
        elif self.state == State.RUNNING:
            self.clock.after_cancel(self.timer)
            self.state = State.PAUSED
            self.start_stop_button["text"] = "Start"

    def step_handler(self):
        self.get_time()

        # Calculates the placement of the second hand 
        seconds_angle = math.pi/30*self.second
        end_y_sec = self.size//2 - math.cos(seconds_angle)*self.second_hand_length
        end_x_sec = self.size//2 + math.sin(seconds_angle)*self.second_hand_length

        # Calculates the placement of the minute hand
        minutes_angle = math.pi/30*self.minute
        end_y_min = self.size//2 - math.cos(minutes_angle)*self.minute_hand_length
        end_x_min = self.size//2 + math.sin(minutes_angle)*self.minute_hand_length

        # Calculate the placement of the hour hand
        hours_angle = math.pi/6*self.hour
        end_y_hour = self.size//2 - math.cos(hours_angle)*self.hour_hand_length
        end_x_hour = self.size//2 + math.sin(hours_angle)*self.hour_hand_length

        # Deletes the line
        self.clock.delete("hand")

        # Hour, minute, second hand
        self.clock.create_line(self.size//2, self.size//2, end_x_sec, end_y_sec, fill = "red", tag = "hand")
        self.clock.create_line(self.size//2, self.size//2, end_x_min, end_y_min, fill = "blue", tag = "hand")
        self.clock.create_line(self.size//2, self.size//2, end_x_hour, end_y_hour, fill = "green", tag = "hand")


        # Updates the time
        self.timer = self.clock.after(self.update_time_millis, self.step_handler)

    # Closes window
    def quit(self):
        self.window.destroy()

    # Gets the current time
    def get_time(self):
        current_time = datetime.datetime.now()
        self.hour = current_time.hour
        self.minute = current_time.minute
        self.second = current_time.second
        self.time.set(f"{self.hour}:{self.minute}:{self.second}")

class State(Enum):
    PAUSED = 0
    RUNNING = 1


if __name__ == "__main__":
    Display_Clock()
