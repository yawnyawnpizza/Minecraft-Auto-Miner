# Minecraft-Auto-Miner


Auto Miner is a simple C# program designed to automate mining actions in Minecraft using global hotkeys. It simulates mouse clicks for mining and adjusts mining speed based on the type of pickaxe selected by the user.

# Features
- Prompts the user to select a pickaxe type (wooden, stone, iron, diamond) and adjusts mining speed accordingly.
- Starts and stops mining using global hotkeys:
- Start Mining: Press \
- Stop Mining: Press ]
- Simulates left mouse clicks for mining actions.
# How It Works
- The user is prompted to choose a pickaxe type when the program starts.
- After a short countdown, the program waits for hotkey input to start or stop mining.
- Mining actions are simulated by sending mouse input events to the system.
# Requirements
- Windows OS (due to user32.dll dependencies).
- .NET Framework (version 4.5 or later).
# Hotkeys
- \ (Backslash): Start mining.
- ] (Right Bracket): Stop mining.

# Disclaimer
This program is intended for educational purposes only. Automating in-game actions may violate the terms of service of certain games. Use responsibly.
