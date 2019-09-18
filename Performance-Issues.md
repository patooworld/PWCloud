# Azure Data Studio starts up slowly

### Read the Startup Timers
When Azure Data Studio feels slow to start, you can check the startup timers. Open the Command Palette (**F1**) and select **Developer: Startup Performance** This will open developer tools and print some startup stats onto the "Console."

![startup-profile](https://user-images.githubusercontent.com/172399/32089769-3df19924-baec-11e7-9654-e199e1ab8c92.png)

Please share these numbers with us as they often allows us to understand what is slow.

### Optionally, Create a Startup CPU Profile
If Azure Data Studio is slow to start then please create a startup CPU profile. Do the following

* Make sure to only have one window open
* Quit Azure Data Studio (Cmd+Q for Mac, closing the last window on Linux/Windows)
* Start Azure Data Studio from the command line like so <code>azuredatastudio --prof-startup</code>
* Azure Data Studio will start and create two profile-files in your home-directory. Please attach these files to your issue or create a new issue with these two files