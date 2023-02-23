# Analyze Desktop App

1. Run PrivEsc scripts to find low hanging fruits
2. [https://github.com/PowerShellMafia/PowerSploit/blob/dev/Privesc/PowerUp.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/dev/Privesc/PowerUp.ps1)
3. [https://github.com/itm4n/PrivescCheck](https://github.com/itm4n/PrivescCheck)
4. Check Behaviour
5. ProcessMonitor
   * C:\ProgramData
   * C:\Windows\Temp
   * C:\Users\xct\AppData
6. ProcessExplorer
7. [https://ioninja.com/plugins/pipe-monitor.html](https://ioninja.com/plugins/pipe-monitor.html)
8. Monitor \ProgramData\
9. Analyze Code in DnSPY/IDA

### Checklist

* Installed services \(both the service permissions and the service executable/path permission\)
* Named pipes \(and their ACLs\)
* Log file permissions in folders like C:\ProgramData
* Network sockets
* DCOM servers and hosted interfaces

