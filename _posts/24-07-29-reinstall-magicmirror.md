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

# Install MagicMirror

```bash
bash -c  "$(curl -sL https://raw.githubusercontent.com/sdetweil/MagicMirror_scripts/master/raspberry.sh)"
```

# Install modules

```bash
cd MagicMirror/modules/
#
git clone https://github.com/alexbergsland/MMM-BirthdayCountdown
cd MMM-BirthdayCountdown
npm install
cd ..
#
git clone https://github.com/heskja/MMM-CalendarWeek
cd MMM-CalendarWeek
npm install
cd ..
#
git clone https://github.com/timdows/MMM-JsonTable
cd MMM-JsonTable
npm install
cd ..
#
git clone https://github.com/jclarke0000/MMM-OpenWeatherForecast
cd MMM-OpenWeatherForecast
npm install
cd ..
#
git clone https://github.com/Alvinger/MMM-ResRobot
cd MMM-ResRobot
npm install
cd ..
#
git clone https://github.com/AgP42/MMM-SmartWebDisplay
cd MMM-SmartWebDisplay
npm install
cd ..
#
git clone https://github.com/retroflex/MMM-Skolmaten
cd MMM-Skolmaten
npm install
cd ..
```