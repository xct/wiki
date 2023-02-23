# Antivirus

### Meterpreter

We can set "EnableStageEncoding" to true and StageEncoder to "x64/zutto\_dekiru" on a Meterpreter payload, this way the 2nd stage will be encrypted and encoded.

### Detect Function Emulation

#### Sleep Timing

Some AV fast forward through sleep timers to speed up analysis \(via function emulation\). We can abuse this behavior by measuring the time between start and end of a Sleep, in case it is a lot faster than expected we can just exit.

#### VirtualAllocExNuma

If the API is emulated, it will not  return a valid address:

```csharp
[DllImport("kernel32.dll")]
static extern IntPtr GetCurrentProcess();

[DllImport("kernel32.dll", SetLastError = true, ExactSpelling = true)]
static extern IntPtr VirtualAllocExNuma(IntPtr hProcess, IntPtr lpAddress, uint dwSize, UInt32 flAllocationType, UInt32 flProtect, UInt32 nndPreferred);

IntPtr mem = VirtualAllocExNuma(GetCurrentProcess(), IntPtr.Zero, 0x1000, 0x3000, 0x4,0);
if(mem == null){
    return;
}
```

Some other methods where this technique works: FlsAlloc.

