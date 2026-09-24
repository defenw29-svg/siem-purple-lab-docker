# 3 Reglas SOC L1 - Mapeadas a MITRE

## Regla 1: Brute Force Success (T1110.001)
```sql
IF EventID=4625 COUNT >=5 BY SourceIP WITHIN 5m
AND EventID=4624 BY Same SourceIP AFTER
THEN Offense: Brute Force Success - Risk 8
```
Building Block: BB:Approved Scanners = 10.10.10.0/24

## Regla 2: LSASS Dump - Mimikatz (T1003.001)
```sql
IF Sysmon EventID=10 AND TargetImage contains lsass.exe AND GrantedAccess IN (0x1010,0x1410,0x1438)
THEN Offense: Credential Dumping
```

## Regla 3: PowerShell Obfuscated (T1059.001)
```sql
IF Sysmon EventID=1 AND Image endswith powershell.exe AND CommandLine contains (-enc, -EncodedCommand, IEX)
THEN Offense: Obfuscated PowerShell
```

Todas probables con Red Team scripts.
