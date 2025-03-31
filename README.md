Copy the `apollo-sync.prf` file into the `~/.unison` folder.

Make sure to update the folder locations if needed.

---

## ChatGPT's instructions

Certainly! `Unison` is a powerful file synchronization tool that can be used to synchronize two directories on your Mac. Here's how you can set it up and use it to achieve bidirectional synchronization:

### Step 1: Install Unison

First, you'll need to install `Unison` using Homebrew. Open Terminal and run:

```bash
brew install unison
```

### Step 2: Create a Unison Profile

Unison uses profiles to define synchronization tasks. You can create a profile file to specify the directories you want to sync and any other options.

1. **Create a Profile Directory:**

   By default, Unison looks for profiles in the `~/.unison` directory. Create this directory if it doesn't exist:

   ```bash
   mkdir -p ~/.unison
   ```

2. **Create a Profile File:**

   Create a new profile file in the `~/.unison` directory. You can name it something like `sync.prf`:

   ```bash
   nano ~/.unison/sync.prf
   ```

3. **Edit the Profile File:**

   Add the following content to the profile file, replacing `/path/to/source` and `/path/to/destination` with the actual paths you want to synchronize:

   ```plaintext
   root = /path/to/source
   root = /path/to/destination

   # Uncomment the following line to automatically resolve conflicts by preferring newer files
   # prefer = newer

   # Uncomment the following line to run in batch mode without user interaction
   # batch = true

   # Uncomment the following line to enable logging
   # log = true
   ```

   Save the file and exit (`CTRL + X`, then `Y`, and `Enter` in `nano`).

### Step 3: Run Unison

To perform the synchronization, you can run Unison with the profile you created:

```bash
unison sync
```

This command will start the synchronization process using the `sync.prf` profile. Unison will compare the two directories and synchronize the changes between them. By default, Unison will prompt you to confirm each change, allowing you to review the actions it will take.

### Step 4: Automate the Synchronization (Optional)

If you want to automate the synchronization process, you can create a script that runs Unison in batch mode and schedule it using `launchd` or another scheduling tool.

1. **Create a Synchronization Script:**

   Create a new script file:

   ```bash
   nano ~/run_unison_sync.sh
   ```

   Add the following content to the script:

   ```bash
   #!/bin/bash
   unison sync -batch
   ```

   Make the script executable:

   ```bash
   chmod +x ~/run_unison_sync.sh
   ```

2. **Schedule the Script with launchd:**

   You can create a `launchd` job to run this script at regular intervals or when changes occur. This step is similar to the previous `launchd` setup but will use the Unison script instead.

Unison provides a robust way to synchronize directories with a variety of options for conflict resolution and automation. You can customize the profile file further to suit your specific needs.

Running `Unison` as a brew service is not directly supported because `Unison` is typically run interactively or in batch mode for manual synchronization tasks. However, you can create a custom service using `launchd` to automate the synchronization process, similar to how you would set up a service manually.

Here's how you can set up a custom `launchd` service to run `Unison` as a background task:

### Step 1: Create a Unison Profile

First, ensure you have a Unison profile set up as described earlier. Let's assume this profile is named `sync.prf` and is located in `~/.unison`.

### Step 2: Create a Synchronization Script

Create a script that runs Unison in batch mode:

```bash
nano ~/run_unison_sync.sh
```

Add the following content to the script:

```bash
#!/bin/bash
unison sync -batch
```

Make the script executable:

```bash
chmod +x ~/run_unison_sync.sh
```

### Step 3: Create a launchd Service

Create a `launchd` plist file to run the script as a service:

1. **Create the LaunchAgents Directory:**

   Make sure the `LaunchAgents` directory exists:

   ```bash
   mkdir -p ~/Library/LaunchAgents
   ```

2. **Create the Plist File:**

   Create a new plist file for your service:

   ```bash
   nano ~/Library/LaunchAgents/com.example.unisonsync.plist
   ```

3. **Edit the Plist File:**

   Add the following content to the plist file:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
       <key>Label</key>
       <string>com.example.unisonsync</string>
       <key>ProgramArguments</key>
       <array>
           <string>/Users/yourusername/run_unison_sync.sh</string>
       </array>
       <key>StartInterval</key>
       <integer>300</integer> <!-- Runs every 5 minutes -->
       <key>RunAtLoad</key>
       <true/>
   </dict>
   </plist>
   ```

   - Replace `/Users/yourusername/run_unison_sync.sh` with the path to your script.
   - Adjust the `StartInterval` value to your desired frequency in seconds (e.g., 300 seconds for every 5 minutes).

### Step 4: Load the Service

Load the `launchd` service to start it:

```bash
launchctl load ~/Library/LaunchAgents/com.example.unisonsync.plist
```

### Step 5: Verify the Service

Ensure the service is running by checking the list of loaded services:

```bash
launchctl list | grep com.example.unisonsync
```

This setup will run the Unison synchronization task every specified interval (e.g., every 5 minutes) in batch mode, effectively acting as a background service. Adjust the script and plist file paths as needed for your environment.


