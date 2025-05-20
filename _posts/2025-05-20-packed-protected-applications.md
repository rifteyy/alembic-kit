---
title: Packed, protected applications
categories:
- Malware
---

Packed or protected applications are nothing new. Some legitimate software such as Steam games use it ([Antivirus detected "Win64/Packed.Themida.L" after update 29th april](https://steamcommunity.com/app/553850/discussions/2/4358997997661686219/) or [# False Positive in Helldivers 2](https://forum.eset.com/topic/40894-false-positive-in-helldivers-2/), [Themida - You can't play this game any more](https://steamcommunity.com/app/383120/discussions/0/1608274347724504289/)). Why do they use it, though and what is the problem of it?

### Purpose of packers
Software packers are used to protect applications from advanced reverse engineering, debugging and cracking (ideal for paid video games) but in regards of malware, they work as an **detection evasion method** to make it difficult for an antivirus to scan the executable.

For example, a  `.ZIP` or `.RAR` archives are also packed. They are compressed using an algorithm and they are often used in phishing campaigns or to also deliver malware. 

However, something like UPX or Themida is a completely different level. Themida is not only a packer, but also a protector. Some popular capabilities of Themida are:
- Anti-debugger
- Various encryption algorithms
- Anti-disassembly
- Anti-memory patching

It is important to understand that Themida and other packers are a legitimate tool, but often abused by malware to evade detection.

### Popular packers
We know many packers by now, for example:
- [Themida](https://www.oreans.com/Themida.php)
- [VMProtect](https://vmpsoft.com/)
- [Enigma Protector](https://enigmaprotector.com/)
- [FlyStudio](https://www.malwarebytes.com/blog/detections/trojan-malpack-flystudio) - compiled software written in FlyStudio scripting language
- [Nuitka](https://nuitka.net/) - Python to executable compiler
- [UPX](https://upx.github.io/)

... and many more.

[Lumma stealer](https://malpedia.caad.fkie.fraunhofer.de/details/win.lumma) has used both Themida and VMProtect and VMProtect also has been used by for example [Arkei stealer](https://malpedia.caad.fkie.fraunhofer.de/details/win.arkei_stealer) ([Vidar stealer](https://www.gdatasoftware.com/blog/2025/04/38169-vidar-stealer) is a newer modern version based on Arkei).

### Why are they detected?

You may ask, why these packers are detected by anti-malware software. For example ESET detects Themida and other packers as a **suspicious application** or a **potentially unsafe application** if they do not have a valid digital signature. 

Let's take a look at this Helldivers 2 `game.dll` file ([VirusTotal](https://www.virustotal.com/gui/file/ab920976c7aebc1d3c50a9ef23b3a2eda36551002f37f466b1664aecd4f684e4/details)). If we open it's details, we can see that [DetectItEasy](https://github.com/horsicq/Detect-It-Easy) - a software used in malware analysis to identify packers, obfuscators and file types claims that it is packed by Themida.

![VirusTotal scan #1](https://raw.githubusercontent.com/rifteyy/alembic-kit/refs/heads/master/img/protectors/virustotal.png)

However, if we look at the website, the `game.dll` file has 0 detections, because it has a valid digital signature and the game owners likely reached out to the vendors to remove their signature detection for Themida.

![VirusTotal Scan #2](https://github.com/rifteyy/alembic-kit/blob/master/img/protectors/virustotal2.png?raw=true)

## Packers and detection problem on VirusTotal
Packed and protected software are detected statically on VirusTotal by many engines, however when it comes to behavioral monitoring from SIGMA and IDS rules and the VM monitoring, it usually fails.

Most of the protected software also checks if it is being ran inside a virtual machine and that is why when someone posts a VirusTotal link we can not decide if the file is safe or not.

For example, here is a malicious sample protected by Themida ([VirusTotal](https://www.virustotal.com/gui/file/92366ca75c5e466e4ab34c2df2fca51f59c4b32cfbb24db255cda14d3c86ef59/detection)).

![virustotal3.png](https://github.com/rifteyy/alembic-kit/blob/master/img/protectors/virustotal3.png?raw=true)

We can see that ESET recognizes it as `A Variant Of Win32/Packed.Themida.HZB`. 

If we now open it's behavior, we can see that it is quite empty. There is only a bunch of Google updater processes starting and nothing really extra ordinary or unusual. 

That is due to the fact the scanned sample did not fully execute, because it detected that it was being ran in a sandbox environment. You might be surprised, but detecting a VirusTotal sandbox is not that complicated.

If we look at `Malware Behavior Catalog Tree` section in behavior, we can see that there are more than enough checks for debugging and sandbox environment.

![VirusTotal scan #4](https://github.com/rifteyy/alembic-kit/blob/master/img/protectors/virustotal4.png?raw=true)



### How do we confirm a false positive if the software is protected?
Nobody really wants to dig inside of it unless it is a valuable sample and there is a possibility to discover new malware. The process of identifying it is unfortunately not that simple here and running it through VirusTotal, AnyRun or Triage is not enough.

It would require someone experienced to dig manually through the file and determine if it's safe or not. However, if the file is packed by Themida and does not have a digital signature, then it is in high percentage either a malware or pirated/cracked software, but when it comes to pirated and cracked software, we actually cannot determine a false positive due to their nature and source.

If your legitimate software that you created and protected using Themida or by other protectors was detected as a generic malware, you may be able to haggle about the detection and get it removed from the AV vendors detection engine, however when it comes to for example ESET, they do not remove Themida detections as they are indeed unsafe and deserve to be flagged with the option to run them.

