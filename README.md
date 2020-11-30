# Quick tool: A minimalistic Telnet library

Send commands to your servers from your programs using the Telnet protocol

[Download source files](https://github.com/rafinhaa/MinimalisticTelnet/releases "Download source files")

![Image 1](https://www.codeproject.com/KB/IP/MinimalisticTelnet/telnetconsole.jpg "Image 1")

## Introduction
This article provides a minimalistic Telnet interface. Possible applications of this class include :

- A Telnet console
- A Windows GUI around some UNIX commands that need to be executed on a server
- A program that executes scripts (scripted telnet)

This example offers both a minimal console, and a possibility to use a script, using file piping.

## Background
A little while ago one of my clients had the problem that a person who was managing a system using UNIX scripts (HP-UX) was leaving the company. This person was not to be replaced by a new employee, so his functions would have to be done by several other persons.

When estimating the cost of teaching the other people how to use UNIX, we made some calculations, and found a much cheaper solution: we built a simple Windows program that contained a GUI for every script used.

The user simply has to press some buttons, select some values and press the "Start" button. When the user presses the start button the UNIX commands are built using the parameters from the GUI, and sent to the server using Telnet.

Since I remember spending quite a few hours figuring out the minimal implementation of the Telnet protocol, I decided to do a simple rewrite for the users of codeproject.

## Using the code
The class is actually quite simple to use. Please take a look at the example code :

```csharp
//create a new telnet connection to hostname "gobelijn" on port "23"
TelnetConnection tc = new TelnetConnection("gobelijn", 23);

//login with user "root",password "rootpassword", using a timeout of 100ms, 
//and show server output
string s = tc.Login("root", "rootpassword",100);
Console.Write(s);

// server output should end with "$" or ">", otherwise the connection failed
string prompt = s.TrimEnd();
prompt = s.Substring(prompt.Length -1,1);
if (prompt != "$" && prompt != ">" )
    throw new Exception("Connection failed");

prompt = "";

// while connected
while (tc.IsConnected && prompt.Trim() != "exit" )
{
    // display server output
    Console.Write(tc.Read());

    // send client input to server
    prompt = Console.ReadLine();
    tc.WriteLine(prompt);

    // display server output
    Console.Write(tc.Read());
}


Console.WriteLine("***DISCONNECTED");
Console.ReadLine();
```

I suppose this is self-explanatory, isn't it ? Create the Telnet connection, login, send the login output to the screen, and while connected send serveroutput to the screen, read a command from the commandline, and again, send the serveroutput to the screen.
If the command is **"exit"** then the loop needs to finish.

Instead of reading the input from the console, the input can also be piped from a script: if the script was named telnetstuff.txt then execute the script as follows :

    MinimalisticTelnet < telnetstuff.txt > output.txt
	
The result will be inside the file *output.txt*. Currently the servername, port, username and password is hardcoded, but it should be a piece of cake to change this into command line parameters.

## How does it work
The **TelnetConnection** parses every byte received from the TcpClient, and if a byte series is a Telnet option request (**DO**, **DONT**, **WILL**, **WONT**), the client simply responds that the option is not available by sending (**DONT**, **WONT**) in return.

The only exception to these responses is the command **SGA: suppress go ahead**, since this command allows async traffic.

## Points of Interest
The **TelnetConnection.Read** function assumes that if there is no data available for more then **TimeOutMs** milliseconds, the output is complete.

The login works by parsing the server output after the initial connection. It will look for a colon **":"** in the screen output, and will send username and password after the colon.

To check if the connection succeeded, I look for whether the serveroutput ends with a **"$"** or a **">"**. If your Telnet server has another prompt, please replace this.

## Votes & Comments
The votes & comments on my previous article gave me the motivation to publish a new article, so please keep your votes & comments coming !!

## History
2007-06-06: First version

## License
This article, along with any associated source code and files, is licensed under [The Code Project Open License (CPOL)](http://www.codeproject.com/info/cpol10.aspx "he Code Project Open License (CPOL)")

## About the Author
Tom Janssens
Founder Virtual Sales Lab
Belgium Belgium
Tom Janssens, founder of Virtual Sales Lab, a company that builds online 3D- and VR product configurators.

Blog: http://tojans.me
Github: http://github.com/ToJans
Twitter: http://twitter.com/ToJans
LinkedIn: http://www.linkedin.com/in/tomjanssens
