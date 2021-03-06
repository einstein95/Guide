---
title: "DSiWare Downgrade"
permalink: /dsiware-downgrade.html
---

**If you are on 11.0.0 or 11.1.0, do NOT update to 11.2.0. A new update will be coming soon that allows for DSiWare NFIRM Downgrading _without_ a second 3DS or hardmod for versions under 11.2.0.**
{: .notice--primary}

If you are between versions 11.0.0 and 11.2.0, you must follow this guide to downgrade your NATIVE_FIRM using DSiWare and a second 3DS which has already has a Custom Firmware installed on it in order to dump and restore your NAND.
{: .notice}   

If you are below 11.2.0 on either device, then you should do the ctr-httpwn steps (when prompted) on each device under 11.2.0 to allow you to System Transfer with them.
{: .notice--info}

This takes advantage of an oversight which allows DSiWare titles to read and write anywhere in NAND.
{: .notice--info}

This is a currently working implementation of the "FIRM partitions known-plaintext" exploit detailed [here](https://www.3dbrew.org/wiki/3DS_System_Flaws).
{: .notice--info}

This guide will assume the CFW 3DS is running arm9loaderhax and was setup with this guide, but will work (with slight modifications such as doing all SysNAND steps on EmuNAND) on systems running an EmuNAND. Note that the terms EmuNAND and RedNAND refer to slightly different implementations of [the same concept](http://3dbrew.org/wiki/NAND_Redirection).
{: .notice--info}

You can skip everything related to **Steel Diver: Sub Wars** and steelhax if you already have a working primary entrypoint (e.g: OOT3dHax, FreakyHax) for **the target 3DS**, and use that instead.
{: .notice--info}

{% capture notice-4 %}
This exploit requires you to [System Transfer](http://en-americas-support.nintendo.com/app/answers/detail/a_id/13996/) from a CFW 3DS to a stock 3DS as part of the steps. System Transfers will work in the following directions *only*:    
  + New 3DS -> New 3DS    
  + Old 3DS or 2DS -> Old 3DS or 2DS    
  + Old 3DS or 2DS -> New 3DS    
{% endcapture %}

<div class="notice--warning">{{ notice-4 | markdownify }}</div>

Both systems MUST be from the same region.
{: .notice--warning}

The source 3DS's NNID will be stuck on the target 3DS unless you either system transfer back or call Nintendo! (details in the instructions)
{: .notice--danger}

System Transfers can only be performed once a week.
{: .notice--danger}

#### What you need

* A computer to mount the `public.sav` file (for now)
* Two 3DS systems
  + **The source 3DS**: the 3DS running some kind of custom firmware (arm9loaderhax or some form of EmuNAND/EmuNAND) *on the latest version*
  + **The target 3DS**: the 3DS on stock firmware *between 11.0.0 and 11.2.0*
* Purchase a DSiWare game from the eShop on **the source 3DS**
  + A pirated copy of the game will **not** work
  + The game's `.app` must greater than or equal to `3,457,024 bytes`
  + The game's `public.sav` must greater than or equal to `81,920 bytes`
  + The system transfer will only accept the hacked app and save if they do not increase the overall size of each file
  + For a community list of compatible games, see the [DSiWare List](dsiware-list) page
* [`DSiWare_sudokuhax_v0_injection.zip`](torrents/DSiWare_sudokuhax_v0_injection.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:43e54206ccff490bb66743f8c414ec00837e605f&dn=DSiWare_sudokuhax_v0_injection.zip&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>
* The latest release of [3DSident](https://github.com/joel16/3DSident/releases/latest)
* The latest release of [FBI](https://github.com/Steveice10/FBI/latest)
{::comment}* The latest release of [GodMode9](https://github.com/d0k3/GodMode9/latest){:/comment}
* The latest release of [dgTool](https://github.com/Plailect/dgTool/releases/latest)
* The Homebrew [Starter Kit](http://smealum.github.io/ninjhax2/starter.zip)
* The NFIRM zip corresponding to the device and version of **the target 3DS**:
  + [New 3DS 11.0.0 to 10.4.0](torrents/11.0.0_to_10.4.0_n3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:2d13a5ea1570f911bd5c6423e0c30e51d548837a&dn=11.0.0%5Fto%5F10.4.0%5Fn3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>
  + [Old 3DS 11.0.0 to 10.4.0](torrents/11.0.0_to_10.4.0_o3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:72393bbd99bc285db84a9cabf39d9b3200058d6a&dn=11.0.0%5Fto%5F10.4.0%5Fo3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>     
  ~    
  + [New 3DS 11.1.0 to 10.4.0](torrents/11.1.0_to_10.4.0_n3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:d7d60c27c18f53bd8508a194656a465f6448bedf&dn=11.1.0%5Fto%5F10.4.0%5Fn3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>     
  + [Old 3DS 11.1.0 to 10.4.0](torrents/11.1.0_to_10.4.0_o3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:0caf9a948a2d8bf23606d641f6628e2baeb983bb&dn=11.1.0%5Fto%5F10.4.0%5Fo3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>     
  ~        
  + [New 3DS 11.2.0 to 10.4.0](torrents/11.2.0_to_10.4.0_n3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:881388a552a1ce9a963d391bf1a023642270991c&dn=11.2.0%5Fto%5F10.4.0%5Fn3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>     
  + [Old 3DS 11.2.0 to 10.4.0](torrents/11.2.0_to_10.4.0_o3ds.torrent) - <code class="highlighterrouge"><a href="magnet:?xt=urn:btih:a479e4ee55efbc18c181d426cd77a34815388151&dn=11.2.0%5Fto%5F10.4.0%5Fo3ds.zip&tr=udp%3A%2F%2Ftracker.coppersurfer.tk%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=http%3A%2F%2Ftracker.opentrackr.org%3A1337%2Fannounce&tr=udp%3A%2F%2Fzer0day.ch%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.leechers-paradise.org%3A6969%2Fannounce&tr=http%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2Fexplodie.org%3A6969%2Fannounce&tr=udp%3A%2F%2F9.rarbg.com%3A2710%2Fannounce&tr=udp%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=http%3A%2F%2Fp4p.arenabg.com%3A1337%2Fannounce&tr=udp%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker.aletorrenty.pl%3A2710%2Fannounce&tr=http%3A%2F%2Ftracker1.wasabii.com.tw%3A6969%2Fannounce&tr=http%3A%2F%2Ftracker.baravik.org%3A6970%2Fannounce&tr=http%3A%2F%2Ftracker.tfile.me%2Fannounce&tr=udp%3A%2F%2Ftorrent.gresille.org%3A80%2Fannounce&tr=http%3A%2F%2Ftorrent.gresille.org%2Fannounce&tr=udp%3A%2F%2Ftracker.yoshi210.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.tiny-vps.com%3A6969%2Fannounce&tr=udp%3A%2F%2Ftracker.filetracker.pl%3A8089%2Fannounce"><i class="fa fa-magnet" aria-hidden="true"></i></a></code>    
* Download a legitimate copy of **Steel Diver: Sub Wars** (the game is free, but any copy of the game not from the eShop will **not** work) on **the source 3DS**
* The previous release of [steelhax](https://vegaroxas.github.io/files/steelhax-installer.zip)
* If **the target 3DS** is below 11.2.0, you will _also_ need the following
  + The latest release of [ctr-httpwn](https://github.com/yellows8/ctr-httpwn/releases/latest)

#### Instructions

##### Section I - Prep Work

Use a [save manager](https://github.com/J-D-K/JKSM/releases/latest) to backup any saves you care about on the target 3DS (it will be formatted!)
{: .notice--warning}

1. Create a folder named `files9` on the root of **the source 3DS**'s SD card if it does not already exist
{::comment}2. Copy `GodMode9.bin` from the GodMode9 zip to the `/luma/payloads` folder on **the source 3DS**'s' SD card and rename `GodMode9.bin` in `/luma/payloads` to `up_GodMode9.bin`{:/comment}
4. Reinsert **the source 3DS**'s SD card
5. On **the source 3DS**, hold Start on boot to launch Hourglass9
6. Go to SysNAND Options, then SysNAND Backup/Restore, then backup **(min size)** SysNAND to `NANDmin.bin`
7. Press (Select) on the main menu to eject **the source 3DS**'s SD card, then put it in your computer
8. Copy `NANDmin.bin` and `NANDmin.bin.sha` from the `/files9/` folder on your SD card to a safe location; make backups in multiple locations; this backup will save you from a brick if anything goes wrong in the future **(Your backup should match one of the sizes on [this](nand-size) page; if it does not, you should delete it and make a new one!)**
9. Put **the target 3DS**'s SD card into your computer
10. **Backup every file on both 3DS's SD cards to two separate folders on your computer (keep track of which is which)!**
11. Reinsert each SD card back into their corresponding 3DS
12. Press (Start) to reboot
13. Purchase any DSiWare game from the eShop (a pirated copy of the game will **not** work) on **the source 3DS**
2. Go to System Settings, then "Data Management", then "DSiWare", then "Nintendo DS Profile" on **the source 3DS**
3. Copy any DSiWare games that are already on the SD Card back to the System Memory
4. Copy the DSiWare game you intend to use to the SD Card
5. Power off **the source 3DS**, then put its SD card into your computer
6. Navigate to `/Nintendo 3DS/(32 Character ID)/(32 Character ID)/Nintendo DSiWare/`
7. Make a note of the 8 Character ID in the file name of the `.bin` file in this folder
  + For example, if you see `4B4C4545.bin`, remember the ID `4B4C4545`
8. Reinsert your SD card into **the source 3DS** and boot it back up
9. Go to System Settings, then "Data Management", then "DSiWare", then "Nintendo DS Profile" on **the source 3DS**
10. Delete the DSiWare game from the SD Card *(do not delete it from the System Memory)*

##### Section II - Injecting the game and save

1. Launch FBI on **the source 3DS**
2. Navigate to `TWL NAND` -> `title` -> `00030004` -> `(8 Character ID)`
  + The 8 Character ID will be the one you got from the `.bin` file earlier
3. Navigate to the `content` folder
4. Make a note of the `.app` file name in this folder
  + For example, if you see `00000000.app`, remember the file name `00000000.app`
5. Power off **the source 3DS**, then put its SD card into your computer
7. Copy `sudoku.app` to the root of **the source 3DS**'s SD card from `DSiWare_sudokuhax_v0_injection.zip`
8. On **the source 3DS**'s SD card, rename `sudoku.app` to the name of the `.app` file you noted earlier
10. Reinsert your SD card into **the source 3DS** and boot it back up
11. Launch FBI on **the source 3DS**
3. Navigate to `SD`
4. Press (A) on the renamed sudoku `.app` and copy it
5. Press (B) to get back to the main menu
6. Navigate to `TWL NAND` -> `title` -> `00030004` -> `(8 Character ID)`
  + The 8 Character ID will be the one you got from the `.bin` file earlier
9. Navigate to the `content` folder
9. Press (A) on the current directory and paste the renamed sudoku`.app`
  + This will overwrite the existing one
10. Press (B) to get back to `TWL NAND` -> `title` -> `00030004` -> `(8 Character ID)`
11. Navigate to the `data` folder
12. Press (A) on `public.sav` and copy it
13. Press (B) to get back to the main menu
14. Navigate to `SD`
15. Press (A) on the current directory and paste `public.sav`
11. Press (Start) to exit
12. Power off **the source 3DS**, then put its SD card into your computer
13. Copy `public.sav` to your computer
14. Mount `public.sav` so it can be edited
  + In the future this will be done on device, but for now a computer is needed
  + Windows users can use something like [OSFMount](http://www.osforensics.com/tools/mount-disk-images.html)
  + Ensure the mounted image is *not* Read Only
15. Copy `savedata.bin` from `DSiWare_sudokuhax_v0_injection.zip` to the mounted `public.sav`, replacing the existing one
16. Dismount `public.sav`
17. Copy the modified `public.sav` to the **the source 3DS**'s SD card
18. Reinsert your SD card into **the source 3DS** and boot it back up
19. Launch FBI on **the source 3DS**
3. Navigate to `SD`
4. Press (A) on `public.sav` and copy it
5. Press (B) to get back to the main menu
6. Navigate to `TWL NAND` -> `title` -> `00030004`
9. Navigate to the `data` folder
9. Press (A) on the current directory and paste `public.sav`
  + This will overwrite the existing one
10. Press (B) to get back to the main menu
3. Launch your DSiWare game on **the source 3DS**
4. Tap the screen or press any button to start the game and test if the save is functional
  + If your game has an error about `boot.nds`, **then the exploit has been successful**
  + If your game behaves normally and does not give you this error, then you should stop and figure out what went wrong
  + If you get a black screen, [follow this troubleshooting guide](troubleshooting#twl_broken)

##### Section III - steelhax

**This will allow you to enter the homebrew launcher after the System Transfer.**

1. Copy the `steelhax-installer` folder from the steelhax zip to the `/3ds/` folder on **the source 3DS**'s SD card
2. Reinsert your SD card into your 3DS
3. Ensure that **Steel Diver: Sub Wars** does not have any updates installed using System Settings:
  + Go to "Data Management", then "Nintendo 3DS", then "Downloadable Content"
  + Select **Steel Diver: Sub Wars**, then select "delete"
  + Exit the System Settings
2. Launch **Steel Diver: Sub Wars**
  + Do not update the game
3. Press (A) to continue, then create / select a Mii
4. Exit the game
2. Launch the homebrew launcher on **the source 3DS**
  + If it is an arm9loaderhax installed device, you can do that with [hblauncher_loader](https://github.com/yellows8/hblauncher_loader/releases)
3. Launch steelhax installer
4. Press (A) to continue
5. Press (A) to confirm **Steel Diver: Sub Wars**'s version
6. Press (A) to confirm **the source 3DS**'s system version
7. Press (Start) to exit the installer
8. Press (Start) to open the homebrew launcher exit menu
7. Press (X) to Return to Home Menu (no reboot)
  + You may get an "Error has occurred" message with the option to continue. This is fine, just hit (A)
9. Launch **Steel Diver: Sub Wars** to test the exploit
  + Do not update the game
  + The save game may be corrupted
    + Do not press "ok" to delete the corrupted save data, just exit with the home button
      + If you do press "ok" by mistake, you will have to recreate the Mii
    + Redo the installation starting with the homebrew launcher
    + This can take many tries
  + If it is successful, the device will boot into the homebrew launcher
10. Once you are in the homebrew launcher successfully, launch steelhax installer
11. Press (A) to continue
12. Press (A) to confirm **Steel Diver: Sub Wars**'s version
16. This time, change the version to match **the target 3DS**'s system version
  + Even though you will be downgrading its NFIRM, you should still select the system version it is on now
12. Copy _the contents of_ the `starter.zip` to the root of **the target 3DS**'s SD card, then put the SD card back into **the target 3DS**

##### Section IV - ctr-httpwn

**This section is only required if _the target 3DS_ is under 11.2.0.**

**This will allow you to system transfer on versions other than the latest.**

1. Copy and merge the `3ds` folder from the ctr-httpwn zip to **the target 3DS**'s SD card
2. Reinsert your SD card into **the target 3DS**
2. Launch the homebrew launcher on the device using [Homebrew Launcher (No Browser)](homebrew-launcher-(no-browser))
  + **New 3DSs on versions 10.7.0 and 11.0.0 can use [Homebrew Launcher (Browser)](homebrew-launcher-(browser)) instead**
  + **Ensure menuhax is not installed, or you won't be able to return to Home Menu from the homebrew launcher**
3. Launch ctr-httpwn on **the target 3DS**
4. Press (A) to continue
5. Press (Start) to exit ctr-httpwn
6. Press (Start) to open the homebrew launcher exit menu
7. Press (X) to Return to Home Menu (no reboot)
  + You may get an "Error has occurred" message with the option to continue. This is fine, just hit (A)
8. Continue to the next section **without rebooting**
  + **the target 3DS** has been temporarily patched to allow network functions (such as System Transfer) without running the latest system version
  + Keep in mind that exiting the System Settings will reboot the system
  + If the system is rebooted, you'll have to re-run ctr-httpwn before System Transfer will work

##### Section V - System Transfer

1. **Backup every file on both 3DS's SD cards to two separate folders on your computer (keep track of which is which)!**
2. Reinsert each SD card back into their corresponding 3DS
4. If **the target 3DS** has a Nintendo Network ID on it, you must format the device using System Settings:
  + Go to the last page of "Other Settings" and select "Format System Memory", then follow all instructions
5. Read the following:
  + Your CFW 3DS = the source 3DS = "Source System"
  + Your Stock 3DS = the target 3DS = "Target System"
  + **Move DSiWare titles if prompted!**
  + Do **NOT** delete the source system's SD card contents if prompted
  + Make sure neither device's battery dies during the transfer
  + 2DS/Old 3DS (source) to New 3DS (target) only - if asked which method you wish to use to transfer the SD card data:
    + **Do NOT** choose the "Low-Capacity microSD Card Transfer" or minimal option (option 2), it will only transfer tickets and likely will not transfer the DSiWare save.
    + Fast Method: If you have the ability to move the data from the SD card (source) to the microSD card (target), when prompted use the "PC-Based Transfer" option (option 3).
    + Slowest Method: If you don't have the ability to move the data on a PC use the **full** "Wireless Transfer" option (option 1).
6. Go to [this link](http://en-americas-support.nintendo.com/app/answers/detail/a_id/227/) and follow Nintendo's official instructions for System Transferring from one system to another while keeping in mind what you just read

##### Section VI - Restoring the source 3DS

1. On **the source 3DS**, complete initial setup
2. Do one of the following *(or neither if you don't mind __the source 3DS__'s NNID being nonfunctional)*
    + Do the rest of the sections and then the full guide on **the target 3DS**, then wait one week, then System Transfer from **the target 3DS** back to **the source 3DS** *(remember you cannot transfer back from a New 3DS to an Old 3DS)*
    + Call Nintendo and tell them you no longer have access to the device that your NNID is linked to (which is **the target 3DS** in this case), and would like it linked to a different device (which is **the source 3DS** in this case)
3. Reboot **the source 3DS** while holding Start to launch Hourglass9
4. Go to SysNAND Backup/Restore and restore SysNAND from `NANDmin.bin`

##### Section VII - Backing up the target 3DS's NFIRM

1. Copy `boot.nds` to the root of **the target 3DS**'s SD card
1. Create a folder named `dgTool` on the root of **the target 3DS**'s SD card if it does not already exist
3. Copy the contents of the NFIRM zip to the `dgTool` folder on the root of **the target 3DS**'s SD card
3. Launch your DSiWare game on **the target 3DS**
4. Launch dgTool by starting your DSiWare game
  + If the game does not launch dgTool, [follow this troubleshooting guide](troubleshooting#ts_dsiware)
5. Select "Dump f0f1" to backup **the target 3DS**'s NFIRM
6. Make note of the NFIRM backup's location
7. Exit dgTool
  + You may have to force power off by holding the power button
8. Put your SD card in your computer, then copy `F0F1_N3DS.bin` or `F0F1_O3DS.bin` (depending on your device) to a safe location; make backups in multiple locations; this backup will save you from a brick if anything goes wrong

##### Section VIII - Flashing the target 3DS's NFIRM

**Do NOT downgrade with dgTool on a device that already has arm9loaderhax installed or you will BRICK!**

1. Launch your DSiWare game on **the target 3DS**
4. Launch dgTool by starting your DSiWare game
3. Select "Downgrade FIRM to 10.4" and confirm to flash the 10.4.0 NFIRM bin to **the target 3DS**
4. Exit dgTool
  + You may have to force power off by holding the power button
5. Reboot

##### Section IX - Exploit verification

1. Copy and merge the `3ds` folder from the 3DSident zip to **the target 3DS**'s SD card
2. Reinsert your SD card into **the target 3DS**
3. Launch the homebrew launcher on **the target 3DS** using [Homebrew Launcher (No Browser)](homebrew-launcher-(no-browser))
4. Launch 3DSident
5. Verify that the following:
  + **Kernel version**: 2.50-11
  + **FIRM version**: 2.50-11
  + If either of these do not display the versions above, something has gone wrong and you should try again from the beginning

Continue to [Homebrew Launcher (No Browser)](homebrew-launcher-(no-browser)), using steelhax for your entrypoint instead of one of the ones listed.
{: .notice--primary}

You can use another entrypoint if you want to, I just recommend steelhax because it is free.
{: .notice--info}

**the target 3DS**'s version number will *not* have changed in the settings.
{: .notice--info}

If, once transfered, steelhax only crashes to a black screen on **the target 3DS**, [follow this troubleshooting guide](troubleshooting#ts_steelhax).
{: .notice--warning}
