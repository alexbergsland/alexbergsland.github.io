---
title: Convert Images with Imagemagik
categories: [photo]
tags: [photo,photography,imagemagik,script,dragndrop]
---

# Shell script
Combined with a drag and drop desktop file you can drop files on the desktop file to convert to three different formats.
Full, 80% quality and a square pic for Instagram.

```shell
[Desktop Entry]
Encoding=UTF-8
Name=Drag and Drop Images
Comment=Convert images by dropping on this file
Exec=konsole -e "/home/alex/convert_images.sh '%u'"
Icon=utilities-terminal
Type=Application 
```

```shell
#!/bin/sh

thepath=$(dirname "$1")
thefile=$(basename "$1")
if [ ! -d "$thepath/border" ] 
then
    echo "Directory $thepath/border DOES NOT exists." >> /home/alex/convert_images.log
    mkdir "$thepath/border"
fi
myfiles="$1"
#echo "$1" >> /home/alex/convert_images.log
#echo $thefile >> /home/alex/convert_images.log
for each in "$myfiles"
    do
        thepath2=$(dirname "$each")
        thefile2=$(basename "$each")
        filename="${thefile2%.*}"
        extension="${thefile2##*.}"
        #thenewpath="${thepath2}/border/${filename}-border.${extension}"
        #echo "Working on "$each" ..." "$thepath2" "$thefile2" "$filename" "$extension" >> /home/alex/convert_images.log
        ## Normal ##
        echo "Converting "$thepath2"/border/full-"$filename"."$extension" " >> /home/alex/convert_images.log
        convert -resize 1144 "$each" "$thepath2/border/full-$filename.$extension" >> /dev/null
        convert "$thepath2/border/full-$filename.$extension" \
         -bordercolor white -border 3 \
         -bordercolor black -border 25 \
         -fill white -font YummyCupcakes -pointsize 25 -gravity southeast -annotate +25+0 "www.bergsland.se" \
         "$thepath2/border/full-$filename.$extension"
        ## Normal 80% ##
        echo "Converting "$thepath2"/border/web-"$filename"."$extension" " >> /home/alex/convert_images.log
        convert -resize 1144 "$each" "$thepath2/border/web-$filename.$extension" >> /dev/null
        convert "$thepath2/border/web-$filename.$extension" \
         -quality 80 \
         -bordercolor white -border 3 \
         -bordercolor black -border 25 \
         -fill white -font YummyCupcakes -pointsize 25 -gravity southeast -annotate +25+0 "www.bergsland.se" \
         "$thepath2/border/web-$filename.$extension"
        ## Instagram ##
        echo "Converting "$thepath2"/border/insta-"$filename"."$extension" " >> /home/alex/convert_images.log
        convert -resize 1144 "$each" "$thepath2/border/insta-$filename.$extension" >> /dev/null
        convert "$thepath2/border/insta-$filename.$extension" \
         -bordercolor white -border 3 \
         -bordercolor black -border 25x215 \
         -fill white -font YummyCupcakes -pointsize 25 -gravity southeast -annotate +25+190 "www.bergsland.se" \
         "$thepath2/border/insta-$filename.$extension"
        echo " .. Done!" >> /home/alex/convert_images.log
        echo " " >> /home/alex/convert_images.log
    done
#exit 0
```