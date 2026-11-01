## Step 1. Creating the Virtual Machine

1. Open **VirtualBox** and click the **New** button at the top
2. In the setup window:
* **Name:** Type `test-dev-vm`
* **ISO Image:** Choose your `ubuntu-...-live-server-amd64.iso` file
* **Skip Unattended Installation:** Make sure this box is **unchecked** 



> *Why:* We don't use auto-install so we can pick our own settings and user login manually instead of letting VirtualBox do it in the background

3. Click on the **Hardware** section:
* **RAM:** Slide it to `2048 MB` (2 GB)
* **Processors:** Set it to `2` cores



> *Why:* 2 GB of RAM leaves enough memory for your main computer to run without lagging, and 2 CPU cores help Ubuntu install faster without overloading your PC

4. Click on the **Hard Disk** section:
* Choose **Create a virtual hard disk now**
* Set the size to `25 GB` and select **Dynamically allocated**



> *Why:* 25 GB gives you plenty of room for updates and logs so it won't run out of space. Dynamic allocation means it only takes up real space on your PC as you actually use it, instead of grabbing 25 GB right away

5. Click the **Finish** button at the bottom
* *Check:* The new `test-dev-vm` will show up in the list on the left



---

## Step 2. Setting Up Network

1. Click on `test-dev-vm` and hit the **Settings** button at the top
2. Go to **Network** -> **Adapter 1**
3. Make sure **Enable Network Adapter** is checked and the connection type is set to **NAT**
4. Click **OK**

> *Why:* NAT lets the virtual machine use your computer's internet connection safely without exposing it to risks on the local network

---

## Step 3. Starting and Installing Ubuntu

1. Click on your `test-dev-vm` machine in the list on the left
2. Click the arrow next to the **Start** button at the top and pick **Normal Start** (or **Start with GUI**)
3. Follow the steps on the screen to install Ubuntu (pick your language, type your username and password)
4. When the installation is done, you will see a message saying: `Please remove the installation medium, then press ENTER`
* *What to do:* VirtualBox usually takes the disk out by itself. Just press **Enter** on your keyboard
* *If it gets stuck:* Go to the top menu of the VM window, click **Devices** -> **Optical Drives** -> **Remove disk from virtual drive**, and then press **Enter**



> *Why:* Taking out the install disk stops the VM from getting stuck in a loop and starting the installation all over again when it reboots

---

## Step 4. Verifying via Terminal 

1. After it reboots and you log in, open the terminal by pressing `Ctrl` + `Alt` + `T`
2. Check the system version with this command:
```bash
uname -a

```


* *Check:* You will see text pop up showing the Linux kernel details


3. Test if the internet works by typing:
```bash
ping -c 3 8.8.8.8

```


* *Why:* This command sends exactly 3 packets (-c 3) to the public IP address 8.8.8.8 to quickly check if the virtual machine has a real connection to the internet through NAT. If you see `0% packet loss`, your internet is working fine



---

## Step 5. Shutting Down the Virtual Machine

To turn off the virtual machine safely using the command line:

1. Type this command in the terminal and press **Enter**:
```bash
sudo poweroff

```


2. Type your password (don't worry if dots or letters don't show up while typing, that's normal for Linux) and press **Enter**
* *Check:* The VM window will close and its status in VirtualBox will change to *Powered Off*



> *Why:* Shutting it down properly through the terminal stops files from getting corrupted, which can happen if you just close the window suddenly.

---

## Step 6. Creating a Snapshot

A snapshot saves the exact state of your VM so you can go back to it if something breaks later

1. Make sure your virtual machine is completely turned off
2. Click on `test-dev-vm` in the left list
3. In the top right panel, click on the **Snapshots** tab (or press `Ctrl` + `Shift` + `S`)
4. Click the **Take** button 
5. Type a name for your snapshot (like `clean-base`) and click **OK**

> *Why:* Making a save-point right after finishing the setup means you won't have to reinstall everything from scratch if you break the system later

* *Check:* Your new snapshot will appear right under *Current State*
