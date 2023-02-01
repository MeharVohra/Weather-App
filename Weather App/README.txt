This code imports the Tkinter, customTkinter, and PIL libraries. 

import tkinter as tk
import customtkinter
from PIL import Image, ImageTk
from datetime import date, datetime
import datetime
import requests
import pandas as pd

It then sets up a GUI window using customTkinter with the title "Select Your Location". 

def main():
    customtkinter.set_appearance_mode("dark")
    customtkinter.set_default_color_theme("dark-blue")
    window = customtkinter.CTk()
    window.geometry("650x550")
    window.title("Select Your Location")


The user can refresh the window by clicking on the "Refresh" button. 

def refresh():
        window.destroy()
        main()

    refresh_button = customtkinter.CTkButton(master=window, width=80, text="Refresh", command=refresh)
    refresh_button.place(x=430, y=10)

The user can choose a location from a dropdown menu. When the location is selected, the program uses the OpenWeatherMap API to retrieve weather data for that location. 

    df = pd.read_excel("cities_list.xlsx")
    print(df)
    values_a = [str(item) for item in df["name"]]
    # print(values_a)
    values_b = [str(item) for item in df["country"]]
    # print(values_b)
    result = [f"{cities}, {country}" for cities, country in zip(values_a, values_b)]
    # print(result)
    combobox = customtkinter.CTkOptionMenu(master=window,
                                        values=result, command=optionmenu_callback)

                    
    combobox.pack(padx=20, pady=10)
    combobox.set("Delhi, India")


The weather data is then displayed on a separate window with an image background and text labels showing the location, temperature, weather condition, and date. 

def optionmenu_callback(choice):
        user = choice
  
        api_key = " "


        #The URL is constructed using string formatting to include these variables in the correct format.

        url = f"http://api.openweathermap.org/data/2.5/forecast?q={user}&appid={api_key}"
     

        response = requests.get(url)

        if response.status_code == 200:
            print("Success!")
            # print(response.json())
        else:
            print(f"Error: {response.status_code}")

        data = response.json()
        # print(data.keys())

     

        location = data["city"]["name"]
        # weather = data["weather"][0]["main"]
        # temp = data["main"]["temp"]
        # celcius_temp = float(temp) - 273.15

    


The canvas object from the Tkinter library is used to display the image background and text labels.

   # create the main window
        customtkinter.set_appearance_mode("dark")
        window = customtkinter.CTk()
        window.geometry("530x500")
        window.title("Weather News")
        refresh_button = customtkinter.CTkButton(master=window, width=140, text="Refresh")
        refresh_button.place(x=500, y=20)


        # putting canvas on top of window
        canvas = tk.Canvas(height=1000, width=1500)
        canvas.pack()

        # # open the image
        weather_icon = Image.open("weather-bg.png")
        resize_weather_icon = weather_icon.resize((1000, 800))
        img = ImageTk.PhotoImage(resize_weather_icon)

        weather_label = tk.Label(image=img)
        weather_label.image = img
        # # where to place the image
        # # The value 'nw' stands for "northwest" and it is used to align the top-left corner of the image
        # # or text to the coordinates specified in x and y.
        canvas.create_image(0,0,image=img, anchor='nw')


        # adding text on top of image
        # location label
        canvas.create_text(10, 10, text=f"{location}", anchor='nw', fill="black",
                        font=("Freestyle Script", 100, "bold"))

        positions_x_temp = (450, 50, 300, 550, 750)
        positions_y_temp = (200, 400, 400, 400, 400)
        positions_x_weather = (465, 50, 300, 550, 750)
        positions_y_weather = (280, 480, 480, 480, 480)
        positions_x_date = (790, 50, 300, 550, 750)
        positions_y_date = (10, 550, 550, 550, 550)
        jt = 0
        jw = 0
        jd = 0
        for i in range(0, 35, 7):

            temp_kelvin = data["list"][i]["main"]["temp"]
            temp_celsius = temp_kelvin - 273.15
            print(int(temp_celsius))
            
            # temperature Label
            canvas.create_text(positions_x_temp[jt], positions_y_temp[jt], text=f"{int(temp_celsius)}°C", anchor='nw', fill="black",
                        font=("calibri", 50, "bold"))
            jt += 1
            if jt == len(positions_x_temp) and jt == len(positions_y_temp):
                jt = 0
            
            # weather label
            weather = data["list"][i]["weather"][0]["main"]
            print(weather)
            canvas.create_text(positions_x_weather[jw], positions_y_weather[jw], text=f"{weather}", anchor='nw', fill="black",
                        font=("Script MT Bold", 28, "bold"))
            jw += 1
            if jw == len(positions_x_weather) and jw == len(positions_y_weather):
                jw = 0

            # date label   
            date_time = data["list"][i]["dt_txt"]
            dt_object = datetime.datetime.strptime(date_time, "%Y-%m-%d %H:%M:%S")
            date = dt_object.strftime("%Y-%m-%d")
            print(date)
            canvas.create_text(positions_x_date[jd], positions_y_date[jd], text=f"{date}", anchor="nw", fill="black",
                        font=("calibri", 28, "bold"))
            jd += 1
            if jd == len(positions_x_date) and jd == len(positions_y_date):
                jd = 0

