# Mole Mania

## Group information
### Section number: 02
### Group number: 07
### Team members
Abdulaziz Alsuhaimi - B00091749
Mohammed Ibrahim - B00095826

## Project Description
Describe your project. What is it? how does it work? who is it for?
Mole Mania is a reaction based "Whack a Mole" game built on an ESP32 microcontroller using FreeRTOS. The game lights up one of six LEDs at random, and the player must press the matching button before the time window expires. It is designed for anyone looking for a fun, hands-on embedded systems game that challenges reaction speed.
The player first selects a difficulty level using a dedicated button, a short press cycles through Easy, Medium, and Hard, while a long press starts the game. Over 10 rounds, the system randomly activates one of six "mole" LEDs. A correct button press scores a hit; missing or pressing the wrong button counts as a miss. A buzzer provides audio feedback for hits, misses, and game over. The LCD screen shows the current difficulty, score, round number, and game status in real time.

## System Diagram
Show your CrikitDesigner/Wokwi diagram
<img width="427" height="318" alt="image" src="https://github.com/user-attachments/assets/579af2d8-b563-4bf3-b74b-05d748972e83" />


## How it works
Show a flowchart and/or state diagram
<img width="940" height="1033" alt="image" src="https://github.com/user-attachments/assets/930bcd1d-c259-4e5d-9844-9dcc96c4e7dc" />


## Project Imapact
What impact does your project have, whether it is a real-life application or a game?
Mole Mania is a fun and interactive reaction-speed game designed for anyone who enjoys a quick challenge. The game tests how fast a player can respond under pressure, with three difficulty levels to keep things interesting for both beginners and experienced players. It can be set up as a standalone arcade-style game for casual entertainment or used in a classroom setting to make learning about embedded systems more engaging.

## FreeRTOS Implementation
### Tasks

| Task Name | Priority | Stack Size | What It Does | Time Constraints |
|---|---|---|---|---|
| `btn_in` | 5 | 2048 bytes | Polls all 6 mole buttons every 30 ms with debounce and sends the pressed button index to `btn_queue` | Must poll every 30 ms to avoid missing a button press |
| `game` | 4 | 4096 bytes | Waits for game start, runs 10 rounds, picks a random mole, lights its LED, waits for a button press within the difficulty time window, then updates the score and status | Round window is 500 ms, 1000 ms, or 2000 ms depending on difficulty |
| `diff` | 3 | 2048 bytes | Polls the difficulty button; a short press cycles the difficulty and a long press triggers a countdown then starts the game; waits for game over before returning to selection | Long press threshold is 700 ms |
| `lcd` | 2 | 4096 bytes | Waits on a binary semaphore, then updates both LCD lines with the current difficulty, score, round, and status | Only runs when triggered, no strict deadline |
| `buzz` | 2 | 2048 bytes | Waits on `tone_queue` and plays the matching buzzer tone for HIT, MISS, or GAME OVER | Tones last between 120 ms and 400 ms, no strict deadline |

### Tasks
Derscribe the tasks inclding task name, priority, what it does, time constraints in a table format.

### Task Communication and Synchronization
Desribe any queues, events, sempahore, mutexes, etc
Queues

-btn_queue — Carries the index (0–5) of a pressed mole button from the btn_in task to the game task. Has a depth of 8.
-tone_queue — Carries a tone type (HIT, MISS, GAMEOVER) from the game task to the buzz task after each round result. Has a depth of 4.

Semaphores

-lcd_mutex — A mutex that protects the LCD from being written by more than one task at a time.
-lcd_sem — A binary semaphore used as a signal. Whenever the game state changes, trigger_lcd_update() gives this semaphore so the lcd task wakes up and refreshes the display.

Event Groups

-game_events — Coordinates the start and end of each game session. The diff task sets BIT_GAME_START when the player long-presses the difficulty button, and the game task sets BIT_GAME_OVER when all 10 rounds are done. Each task waits on the other's bit so neither busy-waits.

## Screenshots
Show screenshots of the system in various states. Required for simulation projects only.
<img width="427" height="318" alt="image" src="https://github.com/user-attachments/assets/600ed496-8482-4f76-8ad7-e83e7ebb52cd" />


## Video
Provide a video of the project in action. The video should show the project in action, showing all features, and should be accompanied by a brief explanation. There is no need to explain the code in details.
You can upload a video (if the video size allows) or paste a youtube link.
