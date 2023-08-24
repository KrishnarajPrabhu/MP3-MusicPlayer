# MP3-MusicPlayer
from tkinter import *
import pygame
from tkinter import filedialog
import time
from mutagen.mp3 import MP3
import tkinter.ttk as ttk
import os

root = Tk()
root.title('MP3 Player')
root.geometry("600x300")

# Initialze Pygame Mixer
pygame.mixer.init()

# Add songs to playlist
def add_songs():
	songs = filedialog.askopenfilenames(initialdir='audio/', title="Choose A Song", filetypes=(("mp3 Files", "*.mp3"), ))	
	# Loop thru song list and replace directory info and mp3
	for song in songs:
		song=os.path.basename(song)
		song=os.path.splitext(song)[0]
		song_box.insert(END, song)

# Play selected song
def play():
	# Set Stopped Variable To False So Song Can Play
	global stopped
	stopped = False
	song = song_box.get(ACTIVE)
	song = f'C:/Users/Krishnaraj/OneDrive/Desktop/PROJECT/MUSIC/{song}.mp3'
	pygame.mixer.music.load(song)
	pygame.mixer.music.play(loops=0)
	# Get current Volume
	current_volume = pygame.mixer.music.get_volume()
	# Times by 100 to make it easier to work with
	current_volume = current_volume * 100
	# Change Volume Meter Picture
	if int(current_volume) < 1:
		volume_meter.config(image=vol0)
	elif int(current_volume) > 0 and int(current_volume) <= 25:
		volume_meter.config(image=vol1)
	elif int(current_volume) >= 25 and int(current_volume) <= 50:
		volume_meter.config(image=vol2)
	elif int(current_volume) >= 50 and int(current_volume) <= 75:
		volume_meter.config(image=vol3)
	elif int(current_volume) >= 75 and int(current_volume) <= 100:
		volume_meter.config(image=vol4)

# Stop playing current song
global stopped
stopped = False

def stop():
	# Stop Song From Playing
	pygame.mixer.music.stop()
	song_box.selection_clear(ACTIVE)
	# Set Stop Variable To True
	global stopped
	stopped = True 
	# Get current Volume
	current_volume = pygame.mixer.music.get_volume()
	# Times by 100 to make it easier to work with
	current_volume = current_volume * 100
	# Change Volume Meter Picture
	if int(current_volume) < 1:
		volume_meter.config(image=vol0)
	elif int(current_volume) > 0 and int(current_volume) <= 25:
		volume_meter.config(image=vol1)
	elif int(current_volume) >= 25 and int(current_volume) <= 50:
		volume_meter.config(image=vol2)
	elif int(current_volume) >= 50 and int(current_volume) <= 75:
		volume_meter.config(image=vol3)
	elif int(current_volume) >= 75 and int(current_volume) <= 100:
		volume_meter.config(image=vol4)

# Play The Next Song in the playlist
def next_song():
	# Get the current song tuple number
	next_one = song_box.curselection() 
	# Add one to the current song number
	next_one = next_one[0]+1
	#Grab song title from playlist
	song = song_box.get(next_one)
	# add directory structure and mp3 to song title
	song = f'C:/Users/Krishnaraj/OneDrive/Desktop/PROJECT/MUSIC/{song}.mp3'
	# Load and play song
	pygame.mixer.music.load(song)
	pygame.mixer.music.play(loops=0)
	# Clear active bar in playlist listbox
	song_box.selection_clear(0, END)
	# Activate new song bar
	song_box.activate(next_one)
	# Set Active Bar to Next Song
	song_box.selection_set(next_one, last=None)

# Play Previous Song In Playlist
def previous_song():
	# Get the current song tuple number
	next_one = song_box.curselection() 
	# Add one to the current song number
	next_one = next_one[0]-1
	#Grab song title from playlist
	song = song_box.get(next_one)
	# add directory structure and mp3 to song title
	song = f'C:/Users/Krishnaraj/OneDrive/Desktop/PROJECT/MUSIC/{song}.mp3'
	# Load and play song
	pygame.mixer.music.load(song)
	pygame.mixer.music.play(loops=0)
	# Clear active bar in playlist listbox
	song_box.selection_clear(0, END)
	# Activate new song bar
	song_box.activate(next_one)
	# Set Active Bar to Next Song
	song_box.selection_set(next_one, last=None)

# Delete A Song
def delete_song():
	stop()
	# Delete Currently Selected Song
	song_box.delete(ANCHOR)
	# Stop Music if it's playing
	pygame.mixer.music.stop()

# Delete All Songs from Playlist
def delete_all_songs():
	stop()
	# Delete All Songs
	song_box.delete(0, END)
	# Stop Music if it's playing
	pygame.mixer.music.stop()

# Create Global Pause Variable
global paused
paused = False

# Pause and Unpause The Current Song
def pause(is_paused):
	global paused
	paused = is_paused
	if paused:
		# Unpause
		pygame.mixer.music.unpause()
		paused = False
	else:
		# Pause
		pygame.mixer.music.pause()
		paused = True
	

# Create Volume Function
def volume(x):
	pygame.mixer.music.set_volume(volume_slider.get())
	# Get current Volume
	current_volume = pygame.mixer.music.get_volume()
	# Times by 100 to make it easier to work with
	current_volume = current_volume * 100
	# Change Volume Meter Picture
	if int(current_volume) < 1:
		volume_meter.config(image=vol0)
	elif int(current_volume) > 0 and int(current_volume) <= 25:
		volume_meter.config(image=vol1)
	elif int(current_volume) >= 25 and int(current_volume) <= 50:
		volume_meter.config(image=vol2)
	elif int(current_volume) >= 50 and int(current_volume) <= 75:
		volume_meter.config(image=vol3)
	elif int(current_volume) >= 75 and int(current_volume) <= 100:
		volume_meter.config(image=vol4)	

def on_closing():
    # Stop Music if it's playing
    pygame.mixer.music.stop()
    root.destroy()
    
# Set the function to be called when the window is closed
root.protocol("WM_DELETE_WINDOW", on_closing)

# Create Master Frame
master_frame = Frame(root)
master_frame.pack(pady=20)

# Create Playlist Box
song_box = Listbox(master_frame, bg="black", fg="white", width=60, selectbackground="gray", selectforeground="black")
song_box.grid(row=0, column=0)

# Define Player Control Button Images
back_btn_img = PhotoImage(file='IMAGES/back_button.png')
forward_btn_img =  PhotoImage(file='IMAGES/forward_btn.png')
play_btn_img =  PhotoImage(file='IMAGES/play_button.png')
pause_btn_img =  PhotoImage(file='IMAGES/pause_button.png')
stop_btn_img =  PhotoImage(file='IMAGES/stop_btn.png')

# Define Volume Control Images
global vol0
global vol1
global vol2
global vol3
global vol4
vol0 = PhotoImage(file='image/Volume0.png')
vol1 = PhotoImage(file='image/Volume1.png')
vol2 = PhotoImage(file='image/Volume2.png')
vol3 = PhotoImage(file='image/Volume3.png')
vol4 = PhotoImage(file='image/Volume4.png')

# Create Player Control Frame
controls_frame = Frame(master_frame)
controls_frame.grid(row=1, column=0, pady=20)

# Create Volume Meter
volume_meter = Label(master_frame, image=vol0)
volume_meter.grid(row=1, column=1, padx=10)

# Create Volume Label Frame
volume_frame = LabelFrame(master_frame, text="Volume")
volume_frame.grid(row=0, column=1, padx=30)

# Create Player Control Buttons
back_button = Button(controls_frame, image=back_btn_img, borderwidth=0, command=previous_song)
play_button = Button(controls_frame, image=play_btn_img, borderwidth=0, command=play)
pause_button = Button(controls_frame, image=pause_btn_img, borderwidth=0, command=lambda: pause(paused))
stop_button =  Button(controls_frame, image=stop_btn_img, borderwidth=0, command=stop)
forward_button = Button(controls_frame, image=forward_btn_img, borderwidth=0, command=next_song)

back_button.grid(row=0, column=0, padx=10)
play_button.grid(row=0, column=1, padx=10)
pause_button.grid(row=0, column=2, padx=10)
stop_button.grid(row=0, column=3, padx=10)
forward_button.grid(row=0, column=4, padx=10)

# Create Menu
my_menu = Menu(root)
root.config(menu=my_menu)

# Create Add Song Menu 
add_song_menu = Menu(my_menu)
my_menu.add_cascade(label="Add Songs", menu=add_song_menu)
add_song_menu.add_command(label="Add Songs to the playlist ", command=add_songs)

# Create Delete Song Menu
remove_song_menu = Menu(my_menu)
my_menu.add_cascade(label="Remove Songs", menu=remove_song_menu)
remove_song_menu.add_command(label="Delete A Song From Playlist", command=delete_song)
remove_song_menu.add_command(label="Delete All Songs From Playlist", command=delete_all_songs)

# Create Volume Slider
volume_slider = ttk.Scale(volume_frame, from_=0, to=1, orient=VERTICAL, value=1, command=volume, length=125)
volume_slider.pack(pady=10)

root.mainloop()
