<a name="top"></a>

<p align="center"> 
  <a href="https://www.linkedin.com/in/clarence-fong" target="_blank">
    <img width="50" height="50" alt="LinkedIn" src="https://github.com/user-attachments/assets/7ab6e12b-ca8a-4aa6-8f10-79ba89d485b5" />
  </a>
  <a href="mailto:abc1230940@gmail.com">
    <img width="50" height="50" alt="Gmail" src="https://github.com/user-attachments/assets/4e0491ce-239c-413c-b433-74a5ff48f231" />
  </a>
  <a href="https://www.instagram.com/cyberbrexel?igsh=MXNxeWJid2VxZWxxaw%3D%3D&utm_source=qr" target="_blank">
    <img width="50" height="50" alt="Instagram Old" src="https://github.com/user-attachments/assets/62e4672b-d424-4489-a204-c301040905a3" />
  </a>
  <a href="https://discordapp.com/users/cyberbrexel" target="_blank">
    <img width="50" height="50" alt="Discord" src="https://github.com/user-attachments/assets/f76173ca-fad3-4390-bca1-5c2305bc748e" />
  </a>
  <a href="https://www.reddit.com/user/abc1230940/" target="_blank">
    <img width="50" height="50" alt="Reddit" src="https://github.com/user-attachments/assets/6e9bd985-dfa3-4349-b966-4cf49362bd61" />
  </a>
</p> <br>


<h2 align="center"> CyberDefenders Write-up - Brave </h2>
<h2 id="scenario"> Scenario </h2>
<p> A memory image was acquired from a suspected compromised Windows workstation. The system belonged to a user flagged for potentially malicious activities, including unauthorized access attempts and unusual browsing patterns. The security team observed network activity to external IPs associated with encrypted communication services. </p>
<p> Your task is to analyze the provided memory dump to uncover details about the processes involved, identify active connections at the time of the compromise, and trace the usage patterns of specific applications.</p>
<p align="right">(<a href="#top">Back to Top</a>)</p>


<h2 id="tools-used"> Tools Used </h2>
<ol>
  <li> Volatility 3 </li>
  <li> <a href="https://blog.onfvp.com/post/volatility-cheatsheet/"> Volatility 3 CheatSheet </a> </li>
  <li> <a href="https://whois.domaintools.com/"> Whois Lookup </a> </li>
  <li> xxd </li>
  <li> <a href="https://linuxcommandlibrary.com/man/xxd"> Linux Command Library for xxd </a> </li>
</ol>
<h3> Installation of Volatility 3 </h3>
<p> In this lab the memory dump was analyzed with Volatility 3 in my Ubuntu virtual machine. Please make sure the system is updated and python3 is installed to support volatility dependencies. </p>
<p> Step 1: Update the System and Install Python 3 </p>
<pre> <code lang="bash"> sudo apt update </code> </pre>
<pre> <code lang="bash"> sudo apt install -y python3 python3-pip python3-venv git </code> </pre>
<p> Step 2: Clone the Volatility 3 from the official GitHub repository and navigate into the folder </p> 
<pre> <code lang="bash"> git clone https://github.com/volatilityfoundation/volatility3.git </code> </pre>
<pre> <code lang="bash"> cd volatility3 </code> </pre>
<p> Step 3: Verify Installation </p>
<pre> <code lang="bash"> python3 vol.py -h </code> </pre>
<img width="1522" height="885" alt="Screenshot 2026-06-23 134140" src="https://github.com/user-attachments/assets/73bf615a-e5b4-4f25-bff6-31f28b7928cb" />
<p> The help menu will be shown if the installation is successful. Plugins can also be found in the help menu for memory analysis. </p>
<p align="right">(<a href="#top">Back to Top</a>)</p>


<h2 id="questions"> Questions </h2>
<p> <strong> 1. What time was the RAM image acquired according to the suspect system? </strong> </p>
<p> In order to determine the timestamp when the RAM image was acquired, we can use the info plugin in volatility 3, the plugin also verified the operating system of the system. </p> 
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.info </code> </pre>
<img width="1387" height="552" alt="Screenshot 2026-06-23 141240" src="https://github.com/user-attachments/assets/eeafc4d5-17b6-4004-8f39-e81871cae6f7" />
<p> The metadata about the captured image was found and the timestamp was <strong>2021-04-30 17:52</strong>. </p>
<br>
<p> <strong> 2. What is the SHA256 hash value of the RAM image? </strong> </p>
<p> We can calculate the SHA256 hash value using sha256sum to verify the integrity of the captured image. </p>
<pre> <code lang="bash"> sha256sum "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" </code> </pre>
<img width="1331" height="45" alt="Screenshot 2026-06-23 142023" src="https://github.com/user-attachments/assets/8fe95eda-7332-44e9-93a7-ca4afdd87ba9" /> <br>
<br>
<p> <strong> 3. What is the process ID of brave.exe? </strong> </p>
<p> In order to identify the process ID of "brave.exe", we can use the pslist plugin targeting the executable. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.pslist | grep -i "brave.exe" </code> </pre>
<img width="1627" height="42" alt="Screenshot 2026-06-23 142713" src="https://github.com/user-attachments/assets/63dd1548-e22e-4e15-a32a-b40e03cb9415" />
<p> The Process ID of brave.exe was <strong>4856</strong>. </p>
<br>
<p> <strong> 4. How many established network connections were there at the time of acquisition? </strong></p>
<p> In order to identify the established network connections upon acquisition, we can use the netscan plugin targeting "ESTABLISHED" connections. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.netscan | grep -i "ESTABLISHED" </code> </pre>
<img width="1656" height="241" alt="Screenshot 2026-06-23 143210" src="https://github.com/user-attachments/assets/73c09448-d6b0-4607-a25b-89e76bd4073d" />
<p> The total established connections was <strong>10</strong>. </p>
<br>
<p> <strong> 5. Which domain name does Chrome have an established network connection with? </strong> </p>
<p> According to the last screenshot, we can identify that chrome.exe had the established connection with an IP Address 185.70.41.130 </p>
<img width="1652" height="238" alt="Screenshot 2026-06-23 143703" src="https://github.com/user-attachments/assets/d23b60f0-0d34-4b94-bdf8-7767c0c83f7b" />
<p> Therefore, we can determine the domain name for this IP address with the open-source intelligence WHOIS. </p> 
<img width="970" height="610" alt="Screenshot 2026-06-23 144736" src="https://github.com/user-attachments/assets/333773f0-e706-44f9-8088-19cf77022172" />
<p> The WHOIS lookup confirms that the IP address originates from Switzerland and belongs to <strong>protonmail[.]ch</strong>. </p> 
<br>
<p> <strong> 6. What is the MD5 hash value of the process executable for PID 6988? </strong> </p>
<p> First, we extracted the process executable for PID 6988 using the plugin pslist with the flag --dump. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.pslist --dump --pid 6988 </code> </pre>
<img width="1585" height="131" alt="Screenshot 2026-06-23 145853" src="https://github.com/user-attachments/assets/9dfd2235-88db-4278-889c-f604286e5d12" />
<p> Then we can calculate the MD5 hash value of the extracted process using md5sum. </p>
<pre> <code lang="bash"> md5sum "path/to/6988.OneDrive.exe.0x1c0000.dmp" </code> </pre>
<img width="762" height="42" alt="Screenshot 2026-06-23 150334" src="https://github.com/user-attachments/assets/e7b83412-a5ca-4250-b782-6ee64eec5c6b" />
<p> The MD5 hash value was calculated and I did a little bit research for the executable on VirusTotal. </p> 
<img width="1823" height="465" alt="Screenshot 2026-06-23 150810" src="https://github.com/user-attachments/assets/6a4b1d30-d531-4c84-a215-a33a97ef4496" />
<img width="745" height="362" alt="Screenshot 2026-06-23 151443" src="https://github.com/user-attachments/assets/12125476-b272-4669-8c02-6d3122598caa" />
<p> The MD5 belongs to OneDrive.exe but it was not officially signed by Microsoft. It was believed that the process was suspicious. </p>
<br>
<p> <strong> 7. Can you identify the word that begins at offset 0x45BE876 and is 6 bytes long? </strong> </p>
<p> Actually I did not know how to find the anwser until seeing the hints. We can find the string at the memory offset using xxd command with the aid of documentation from <a href="https://linuxcommandlibrary.com/man/xxd"> Linux Command Library</a>. </p>
<img width="522" height="847" alt="Screenshot 2026-06-23 152641" src="https://github.com/user-attachments/assets/6a06e720-ec7b-48a0-a969-0bb7c651472a" />
<pre> <code lang="bash"> xxd -s 0x45BE876 -l 6 "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" </code> </pre>
<ul>
  <li> -s: specific offset </li>
  <li> -l: the number of bytes </li>
</ul>
<p> Therefore, xxd looks directly at the first 6 bytes of the string located at memory offset 0x45BE876. </p>
<img width="1296" height="41" alt="Screenshot 2026-06-23 153740" src="https://github.com/user-attachments/assets/842f0ef0-7d39-4d6a-85a0-dc939bd7e59b" />
<p> The first 6 bytes of the string was <strong>hacker</strong>. </p>
<br>
<p> <strong> 8. What is the creation date and time of the parent process of powershell.exe? </strong> </p>
<p> First, we needed to identify the parent process ID of powershell.exe using the plugin pslist. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.pslist | grep -i "powershell.exe" </code> </pre>
<img width="1677" height="43" alt="Screenshot 2026-06-23 154215" src="https://github.com/user-attachments/assets/884afef7-8dfe-4599-93de-7a02c92632ec" />
<p> The PPID was 4352. Then we can find the creation date and time using the same plugin targeting the parent prcoess ID. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.pslist --pid 4352 </code> </pre>
<img width="1513" height="132" alt="Screenshot 2026-06-23 154438" src="https://github.com/user-attachments/assets/41702e92-bb4e-4695-96a1-86a3e8584d80" />
<p> The parent process was explorer.exe created at <strong>2021-04-30 17:39</strong>. </p>
<br>
<p> <strong> 9. What is the full path and name of the last file opened in notepad? </strong> </p>
<p> We can identify a full file path opened by an executable by using the cmdline plugin. </p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.cmdline | grep -i "notepad.exe" </code> </pre>
<img width="1655" height="47" alt="Screenshot 2026-06-23 155728" src="https://github.com/user-attachments/assets/9753869f-8261-4aed-a2c8-ccefed52c08f" />
<p> The attacker directly clicked on the file containing account numbers in the path <strong>C:\Users\JOHNDO~1\AppData\Local\Temp\7zO4FB31F24\accountNum</strong>. </p>
<br>
<p> <strong> 10. How long did the suspect use Brave browser? (In Hours) </strong> </p>
<p> This question also took me long time to think until I looked at the hints. We can use the registry.userassist plugin to extract the process running time in the user's NTUSER.DAT registry hive, reminding me the EZ Tools...</p>
<pre> <code lang="bash"> python3 vol.py -f "path/to/20210430-Win10Home-20H2-64bit-memdump.mem" windows.registry.userassist | grep -i "Brave" </code> </pre>
<img width="1805" height="238" alt="Screenshot 2026-06-23 161244" src="https://github.com/user-attachments/assets/7c418edf-cefc-4a8b-a381-8a73b82a0187" />
<p> Based on the result, the Brave browser was run for about <strong>4</strong> hours. </p>
<p align="right">(<a href="#top">Back to Top</a>)</p>


<h2 id="reference"> Reference </h2>
<p> <a href="https://cyberdefenders.org/blueteam-ctf-challenges/achievements/abc1230940/brave/"> CyberDefenders - Brave Lab </p>
<p> <a href="https://whois.domaintools.com/185.70.41.130"> Whois Lookup for 185[.]70[.]41[.]130 </a> </p>
<p> <a href="https://www.virustotal.com/gui/file/91a9ab62ee196e53539e0cf33455238b8e418e8c3a8344cbc449bcccd0c775ab/detection"> Static Analysis Report on VirusTotal for the suspicious OneDrive.exe </a> </p>
<p align="right">(<a href="#top">Back to Top</a>)</p>
