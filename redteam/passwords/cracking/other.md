# Other

### Crack Zip Password

```bash
#!/bin/zsh
if [ $# -ne 2 ]
then
  echo "Usage $0 <file.7z> <wordlist>";
  exit;
fi
7z l "${1}"

echo "Generating wordlist..."
/home/xct/tools/JohnTheRipper/run/john --wordlist="${2}" --rules --stdout | while read i
do
  echo -ne "\rTrying \"$i\" " 
  7z x -p"${i}" "${1}" -aoa >/dev/null
  STATUS=$?
  if [ $STATUS -eq 0 ]; then
    echo -e "\rArchive password is: \"${i}\"" 
    break
  fi
done
```

