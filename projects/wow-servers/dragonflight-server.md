# Dragonflight Server

This page covers everything you'll need to connect to my private World of Warcraft: Dragonflight server on a Windows 10/11 machine.

## Acquire Dragonflight Retail

You will need the latest retail copy of Dragonflight. The game currently requires around 80-90GB of disk space (I would shoot for 100GB just to be safe).

If you already have a subscription with Dragonflight purchased, simply install it normally using the publicly available [Battle.net Client](https://www.blizzard.com/download/confirmation?product=bnetdesk).

If you don't have a subscription with Dragonflight, contact me for an archive :)

## Acquire the Arctium Launcher

Prior to 6.x World of Warcraft Clients, you could simply add a `SET realmlist <server ip>` directive in your `Config.wtf` / `realmlist.wtf` files.

This is no longer the case, as 6.x+ game clients will automatically override those configuration directives when launching.

To get around this inconvenience, the Arctium group has created a nifty little [Wow Launcher](https://github.com/Arctium/WoW-Launcher) that will patch certain memory locations when `WoW.exe` gets executed, and make the client read the configuration directives again.

* Get the most up-to-date Arctium WoW Launcher by [visiting their Releases section on GitHub](https://github.com/Arctium/WoW-Launcher/releases) and downloading the [latest `Windows.x86_64.zip` file](https://github.com/Arctium/WoW-Launcher/releases/download/latest/Windows.x86_64.zip) listed.
* Unzip the `Windows.x86_64.zip` that you downloaded and copy the `Arctium WoW Launcher.exe` to your World of Warcraft directory (e.g. `C:\BattleNet\World of Warcraft`).

## Create an Arctium Launcher shortcut

* Using **Windows Explorer**, navigate to your **World of Warcraft directory** (e.g. `C:\BattleNet\World of Warcraft`)
* **Right-click** on the `Arctium WoW Launcher.exe` in Windows Explorer, and then click on **Create Shortcut** _(on Windows 11, you may have to select **Show more options** first)_
* Rename the newly created `Arctium WoW Launcher.exe - Shortcut` to be `Private Dragonflight Server`
* **Right-click** on the `Private Dragonflight Server` shortcut and click on **Properties**
* Append ` -Config Private-Dragonflight.wtf` to the end of the **Target** field value
  * e.g.<br />
    `"C:\BattleNet\World of Warcraft\Arctium WoW Launcher.exe"` changes to...<br />
    `"C:\BattleNet\World of Warcraft\Arctium WoW Launcher.exe" -Config Private-Dragonflight.wtf`
* Cut-and-Paste the shortcut to your Desktop, or somewhere easily accessible

## First time run

* Double-click on your new `Private Dragonflight Server` shortcut to launch Dragonflight
* A World of Warcraft dialog will appear, and you need to select a Region. Select **English (US)** and click the **OK** button.
* A message stating `You are setting your region to Americas.` will appear, click the **OK** button.
* The game will now officially launch
* After any cinematics, you'll arrive at the login screen. Simply click **Quit** to exit the game at this point
* **Wait 5-10 seconds** after exiting the game, and a new `_retail_\WTF\Private-Dragonflight.wtf` file should have been created in your World of Warcraft directory (e.g. `C:\BattleNet\World of Warcraft\_retail_\WTF\Private-Dragonflight.wtf`).
* Using Notepad, open the newly created `_retail_\WTF\Private-Dragonflight.wtf` file and add the following lines to it (anywhere in the file):
  ```config
  SET patchlist "jgarfield.synology.me"
  SET realmlist "jgarfield.synology.me"
  ```
* Now search for the line `SET portal "US"` and change it to be `SET portal "jgarfield.synology.me"`
* Save the edited file and close it

## Logging In

* Double-click on your new `Private Dragonflight Server` shortcut to launch Dragonflight
* Login using the credentials I provided, and select the **Dragonflight (Remote)** realm
* Enjoy! :)
