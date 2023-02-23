# Static Analysis

### Windows Defender

You can use [DefenderCheck](https://github.com/matterpreter/DefenderCheck) to split in a binaries into pieces, identifying the parts that trigger a detection. 

Another tool would be [Find-AVSignature](http://obscuresecurity.blogspot.com/2012/12/finding-simple-av-signatures-with.html) for this \([gist](https://gist.github.com/xct/891a98fd671073cf4340b08ae460094d)\):

```text
. .\Find-AVSignature.ps1;Find-AVSignature -StartByte 0 -EndByte max -Interval 10000 -Path C:\Users\xct\payload.exe -OutPath C:\Users\xct\out -Verbose -Force
```



