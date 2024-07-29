--
title: Reinstall MagicMirror
categories: [linux,magicmirror]
tags: [setup,github]
---

# Initial setup
Use Raspberry Pi Imager to flash a SD-card with the latest desktop version.<br>
Boot up and SSH into the Raspberry Pi.<br><br>

```bash
sudo apt update
sudo apt-get dist-upgrade
sudo reboot now
```
Wait till it is up again.
```bash
bash -c  "$(curl -sL https://raw.githubusercontent.com/sdetweil/MagicMirror_scripts/master/raspberry.sh)"
cd MagicMirror/modules/
git clone https://github.com/alexbergsland/MMM-BirthdayCountdown
git clone https://github.com/heskja/MMM-CalendarWeek
git clone https://github.com/timdows/MMM-JsonTable
git clone https://github.com/jclarke0000/MMM-OpenWeatherForecast
git clone https://github.com/Alvinger/MMM-ResRobot
git clone https://github.com/AgP42/MMM-SmartWebDisplay
git clone https://github.com/retroflex/MMM-Skolmaten
```