# Vulnversity Wlakthrough
### Link
- https://tryhackme.com/room/vulnversity
------------------------
#### Task1 - Reconnaissance
Scan the box, how many ports are open?
        sudo nmap -sV -sC -oN vulnversity_nmap_result [IP]
        picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
        Answer : 6

What version of the squid proxy is running on the machine?
        picture: Vulnversity_Reco_1.png
        Answer: 3.5.12

How many ports will nmap scan if the flag -p-400 was used?
        sudo nmap -p-400 [IP]
        picture:[Vulnversity_Reco_2.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_2.png)
        Answer: 400

Using the nmap flag -n what will it not resolve?
        man nmap
        picture: [Vulnversity_Reco_3.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_3.png)

What is the most likely operating system this machine is running?
        picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
        Answer: Ubuntu

What port is the web server running on?
        picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_1.png)
        Answer: 3333

Its important to ensure you are always doing your reconnaissance thoroughly before progressing. Knowing all open services (which can all be points of exploitation) is very important, don't forget that ports on a higher range might be open so always scan ports after 1000 (even if you leave scanning in the background)

        sudo nmap -p- -sV -sC -oN ./vulnversity_nmap_all_port_result [IP]
        picture: [Vulnversity_Reco_1.png](https://github.com/LNB283/THM/blob/main/Vulnversity/Pictures/Vulnversity_Reco_4.png)
