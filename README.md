Steam Flash Drive Launcher

Plug in a USB flash drive, pick a game and a Steam account, and play. A floppy-disk-style game launcher for Steam, written in PowerShell.

Each flash drive carries a small game.txt with one or more Steam game IDs. When the drive is connected, a menu lets you choose the game and the Steam account, Steam is switched to that account if needed, and the game starts.

Features
Multiple games per drive: list as many Steam game IDs as you like in game.txt.
Game menu: choose which game to launch. With a single game on the drive the menu is skipped.
Account prompt every time: the accounts are read from Steam itself (config\loginusers.vdf), so there is nothing to configure. Press Enter to keep the current account.
Automatic account switching: Steam is closed, the selected account is set for auto-login and the game is started through the steam://rungameid/<id> protocol.
Reset: after launching, press R to return to the menu, or simply remove the drive.
Background watcher: a hidden script starts with Windows, waits for a drive with game.txt and opens the launcher automatically. It is event-driven (no constant polling) and writes a log.
Files
File	Purpose
steam-launcher.ps1	Main script: game menu, account menu, account switching, game launch
watcher.ps1	Background watcher: waits for the flash drive, starts the main script, writes watcher.log
watcher-hidden.vbs	Starts watcher.ps1 without any console window
game.txt	Example game list. This file belongs on the flash drive, not on the PC
Requirements
Windows with Windows PowerShell 5.1 (powershell.exe)
Steam installed
Every account you want to switch to must be signed in on this PC at least once with the "Remember me" option, so Steam keeps a saved login
In Steam, the "Who's playing?" screen toggle that requires account selection on every client launch must be off. When it is on, Steam always shows the account chooser and ignores the account set by the script
game.txt format

One game per line, in the form Name;id=AppID. The name is optional. Without it the menu shows Game <AppID>.

Portal 2;id=620
Mini Motorways;id=1127500
Wolfenstein: The New Order;id=201810

The Steam AppID is the number in the game's store URL (store.steampowered.com/app/<AppID>/...) and is also listed on SteamDB.

Setup
Copy the scripts to a folder, for example C:\Scripts.
Put a game.txt in the root of your flash drive.
Edit the paths:
$MainScript at the top of watcher.ps1
the script path inside watcher-hidden.vbs
Press Win+R, type shell:startup and put watcher-hidden.vbs (or a shortcut to it) into that folder.
Log out and back in, or double-click watcher-hidden.vbs to start the watcher right away.

Plug in the flash drive and the launcher window opens.

You can also run the main script by hand. The drive letter defaults to E: and can be passed as a parameter:

powershell
.\steam-launcher.ps1 -Drive F:
Example session
Choose a game:
  1) Portal 2
  2) Mini Motorways
Number: 1

Choose account:
  1) account_one (Display Name)  <- current
  2) account_two
Number (Enter = keep current account): 2

Closing Steam to switch to account 'account_two'...
Launching Portal 2 (ID: 620)...
Press R to reset (or remove the drive)...
How it works
watcher.ps1 listens for Windows volume-arrival events (Win32_VolumeChangeEvent) and also checks drives that are already connected at startup.
When a drive with game.txt appears, it starts steam-launcher.ps1 with the drive letter and waits until that window is closed.
The launcher reads game.txt and shows the menus.
To switch accounts it shuts down Steam (steam.exe -shutdown), sets AutoLoginUser and RememberPassword under HKCU\Software\Valve\Steam, and marks the account as MostRecent in config\loginusers.vdf (a backup loginusers.vdf.bak is created first).
The game is started with steam://rungameid/<AppID>.
Troubleshooting
Steam shows the "Who's playing?" account chooser instead of logging in: turn off the toggle that requires account selection on every client launch.
Steam asks for a password: sign in to that account once with "Remember me" enabled.
Nothing opens when the drive is plugged in: check watcher.log next to watcher.ps1. It records every volume event, whether game.txt was found and any errors.
Stopping the watcher: end the powershell.exe process in Task Manager.
Notes
Switching accounts closes Steam, and with it any game that is currently running.
The scripts modify Steam's per-user registry keys and loginusers.vdf. Steam updates may change how auto-login works, so this can stop working in the future.
This project is not affiliated with or endorsed by Valve.
