# camera_util
> a tool for managing my personal camera files with zig

# TODO
- format date yyyy-mm-dd
- read argv
- mkdir -p
- copy files
- remove file
- remove dir
- create a timer
- fetch user input
- confirm prompt
- print pretty reports

## Goals
* import non imported files into a named directory in `_catalog`
* clean up camera only if files have allready been imported
* keep stats in a files so that better time estimations can be made over time
* print pretty status reports
* support multiple cameras

## Stretch Goals
* test usb connection
* make a progress bar

## About
Recently I have been filming the process of making some of my art projects and 
apartment modifications for fun. I quickly ran into to trouble with the storage
limits of my laptop and created a strat for managing on going projects on my
local machine while keeping a catalog of old footage on external hard drives.
Originally I wrote a shell function to help import files, but but when I started 
wanting to add more features I decided it would be a good opertunity to 
practice some zigggg :)

Here is the original shell fn for importing files:
``` bash
GOGOGO_SOURCE="/Volumes/gogogo/DCIM/Camera01"
GOGOGO_FLAG_COMPLETE_PATH="$GOGOGO_SOURCE/boing-ding.txt"
# NOTE: boing-ding.txt is just a flag that can be used to determine that an 
# import has allready occured

cam_gogogo_import(){
  GOGOGO_DATE=$(date +"%Y-%m-%d")
  GOGOGO_DEST="$HOME/_catalog/catalog_image/$GOGOGO_DATE-$1"

  if [[ -e $GOGOGO_FLAG_COMPLETE_PATH ]];then
    echo_red "ERROR: import is allready complete"
    echo "    if you really want to import again,"
    echo "    remove $GOGOGO_FLAG_COMPLETE_PATH"
    echo "    and then try again"
    return 1
  fi

  if [[ -z $1 ]]; then
    echo_red "ERROR: name required"
    echo "    $ cam_gogogo_import <folder-name>"
    return 1
  fi

  if [[ ! -e "$GOGOGO_SOURCE" ]];then
    echo_red "ERROR: gogogo camera data not found"
    return 1
  fi

  GOGOGO_FILEPATH_LIST=$(rg --files "$GOGOGO_SOURCE" | rg -v "Thumb")
  GOGOGO_FILE_COUNT=$(rg --files "$GOGOGO_SOURCE" | rg -v "Thumb" | wc -l | xargs)
  GOGOGO_COPY_COUNT=1

  echo "[CAM GOGOGO IMPORT] START"
  echo "[CAM GOGOGO IMPORT] FETCHING METDATA"
  echo "[CAM GOGOGO IMPORT] SIZE: $(du -d 0 -h  $GOGOGO_SOURCE | cut -d "/" -f 1)"
  echo "[CAM GOGOGO IMPORT] FILE COUNT: $GOGOGO_FILE_COUNT"

  mkdir -p $GOGOGO_DEST
  for SOURCE_FILE_PATH in $(rg --files "$GOGOGO_SOURCE" | rg -v "Thumb"); do
    FILE_NAME=$(basename $SOURCE_FILE_PATH)
    FILE_SIZE=$(du -h -d 0 $SOURCE_FILE_PATH | xargs)
    DEST_FILE_PATH="$GOGOGO_DEST/$FILE_NAME"

    echo "$GOGOGO_COPY_COUNT/$GOGOGO_FILE_COUNT $FILE_SIZE $FILE_NAME"
    cp "$SOURCE_FILE_PATH" "$DEST_FILE_PATH"
    GOGOGO_COPY_COUNT=$(( $GOGOGO_COPY_COUNT+1 ))
  done
  echo "$GOGOGO_DEST\nLUCKY_NUMBER: $RANDOM" > "$GOGOGO_FLAG_COMPLETE_PATH"

  echo_blue "[CAM GOGOGO IMPORT COMPLETE] $GOGOGO_DEST"
}
```
