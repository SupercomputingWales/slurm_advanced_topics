---
title: "Work with Graphical User Interfaces (GUIs)"
teaching: 0
exercises: 0
questions:
- "What is X11?"
- "How to connect to Hawk using a GUI?"
- "What is VNC?"
objectives:
- "Familiarize with common SSH clients and the options to enable X11 connections."
- "Learn how to connect to Hawk's VNC server."
keypoints:
- "It is possible to use Graphical User Interfaces when working on Hawk."
- "X11 is a system that enables the disply of graphical windows from a remote server"
- "Almost all popular SSH clients support X11"
- "Hawk provides VNC capabilities that enables the use of a remote Linux desktop."
---
This section describes common tools to work with graphical applications.
## X11
X is a Unix window system and it specifies methods to display, move and interact with windows using a keyboard and a mouse. X's most distributed version was version 11 (hence X11) created in 1987 and remaining mostly unchaged since then. X11's has been used on many Linux distributions to produce several different desktop environments thanks to X11's agnostic protocol.

Linux systems natively come with X11 support so that no extra software is required to display GUIs from a remote server. On Windows and MacOS systems, a X11 server is required that is able to understand the remote server instructions on how to interact with an application's GUI.

> ## Common X11 servers
>
> On Windows, **Xming and Putty** are classic applications used together to connect via SSH to a remote host and render graphics. **MobaXterm** is another enhanced terminal for Windows.
> On MacOS, **XQuartz** is a project that aims to provide Mac systems with X11 suport.
{: .callout}

## Connecting to Hawk using X11


### Windows: Putty/Xming

<img src="{{ page.root }}/fig/xming_settings.png" alt="Xming settings" width="50%" height="50%" />

<img src="{{ page.root }}/fig/putty_x11_arrows.png" alt="Putty X11 settings" width="50%" height="50%" />


> ## Cardiff Apps
>
> Putty and Xming are available as part of Cardiff Apps in University owned desktop computers. 
> This is useful if you don't have administrative rights to install new applications.
> <img src="{{ page.root }}/fig/Cardiff_apps_icon.png" alt="Cardiff Apps icon" />
{: .callout}

### Windows: MobaXterm
MobaXterm is a feature rich terminal for Windows that comes with an integrated SSH client and X11 server. When opening MobaXterm you should see something like the image below. To start a new session click on "Session".
<img src="{{ page.root }}/fig/MobaXterm-01-arrows.png" alt="MobaXterm home window" width="50%" height="50%" />

Choose SSH session and enter Hawk hostname (hawklogin.cf.ac.uk) and your username. Double check the port number (22). X11 is enable by default in MobaXterm but you can disable it by unticking X11-Forwarding in the "Advanced SSH settings" tab.
<img src="{{ page.root }}/fig/MobaXterm-02-arrows.png" alt="MobaXterm connection settings" width="50%" height="50%" />

Click "OK" and you should be able to connect to Hawk. You can download MobaXterm from its <a href="https://mobaxterm.mobatek.net" target="_blank">website</a>.

### MacOS: XQuartz
XQuartz is an open source project to develop an X window system that work on Mac MacOS. After installing XQuartz, enable X11 forwarding using SSH "-X" option:
<pre style="color: silver; background: black;">$ ssh -X username@hawklogin.cf.ac.uk</pre>

> ## Issues with XQuartz
>
> We have had some user reports of XQuartz throwing error messages with applications such as Gaussian View or Comsol:
>
> ~~~
> [xcb] Unknown sequence number while processing queue
>
> [xcb] Most likely this is a multi-threaded client and XInitThreads has not been called
>
> [xcb] Aborting, sorry about that.
> ~~~
>
> If you experience any such problem, please get in contact with us.
{: .callout}

## Testing a X11 connection
Linux comes with a couple of *toy* applications that can be used to easily test if your X11 connection is working as expected. In the terminal connected to Hawk try the following command:

<pre style="color: silver; background: black;">$ xeyes </pre>

If working correctly, you should see a new window open with a pair of eyes following your mouse movements.

<img src="{{ page.root }}/fig/xeyes.png" alt="Xeyes example" width="10%" height="10%" />

## VNC
VNC is a Virtual Network Computing desktop-sharing system that allows to remotely control another computer. The main differences with X11 are:
 - With VNC the graphical processing runs on the remote server transferring only a "screenshot" to the local machine. With X11, the application sends the instructions to the local machine and behaves as if the application were run locally. This can be problematic, for example, when trying to visualize simulation results with big data files.
 - With VNC your application survives disconnecting from the server. For example, if you need to close your laptop and change location, you can reconnect to the VNC server and continue working with the application. This is not possible with X11, since when the X11 server dies, the windows disappear.

We have recently setup a VNC server on Hawk to address these issues. To connect to the server you need to 1) create a session in the server and 2) connect via SSH tunneling with a VNC client. 

> ## Create a VNC session
>
> 1. Login to Hawk:
>    <pre style="color: silver; background: black;">$ ssh c.user@hawklogin.cf.ac.uk </pre>
>  2. On Hawk, login to VNC server (enter your Hawk password when prompted):
>     <pre style="color: silver; background: black;">$ ssh clvnc1 </pre>
>  3. Run vncserver. First time will ask to set a VNC password to access sessions (optionally you can set a view only password as well).
>     <pre style="color: silver; background: black;">$ vncserver </pre>
>  4. To run an X session – you need to find port number, for this add 5900 to DISPLAY number obtained from below command – e.g. 5901 in this example,
>     <pre style="color: silver; background: black;">$ vncserver -list
>     TigerVNC server sessions:
>     X DISPLAY #     PROCESS ID
>     :1              22063  </pre>
{: .checklist}

> ## Connect a VNC client.
>
> For this you need to connect to Hawk using a SSH tunnel, some applications such as MobaXterm has this feature integrated. In this example we will use another popular VNC client, TigerVNC.
>
> {:start="5"} 
> 5.  Download and install TigerVNC from their official <a href="https://tigervnc.org" target="_blank">website</a>.
> 6.  Open a terminal and use SSH port forwarding to use local port to access port on remote server, e.g. use local port 9000 to connect to *clvnc1* port from step 4 (e.g. 5901):
      <pre style="color: silver; background: black;">$ ssh -L 9000:clvnc1:(port number) c.user@hawklogin.cf.ac.uk </pre>
> 7.  Run TigerVNC and connect to localhost:9000
>     <img src="{{ page.root }}/fig/TigerVNC.png" alt="TigerVNC connection" width="40%" height="40%" />
> 8.  Type VNC password set in step 3.
> 9.  You should now have a VNC Linux desktop.
> 10. To open an GUI application (e.g. gview), run a terminal window within VNC’s desktop (Applications -> System Tools -> Terminal). Within the terminal load the required module (e.g. module load gaussian/09c01). Run the desired application (e.g. gview)
> 11. Once finished just close window.
> 12. If completely finished close down VNC server by logging back into clvnc1 and running (where screen number is the number obtained in step 4)
>     <pre style="color: silver; background: black;">
>     $ vncserver -list
>     $ vncserver -kill :<screen number>
>     </p>
> 13. Logout of VNC server to return to Hawk.
>     <pre style="color: silver; background: black;">
>     $ exit 
>     </p>
{: .checklist}


<img src="{{ page.root }}/fig/VNC-desktop.png" alt="VNC desktop" width="40%" height="40%" />


{% include links.md %}

