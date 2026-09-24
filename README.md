# SIEM Purple Team Lab - Docker

**De Helpdesk L1/L2 | ITIL a Junior SecOps | SIEM**

> Aún no trabajo en SOC, pero este lab demuestra cómo gestionaría una ofensa en producción.

Este repo simula el flujo LogRhythm Open Collector en Docker para practicar Blue Team vs Red Team = Learning.

## 🟦 BLUE TEAM vs 🟥 RED TEAM

### Arquitectura
```
[Kali Attacker] --nmap/hydra--> [Open Collector:514/5044] --> [Wazuh Manager (SIEM)]
[Win Victim Sim] --Sysmon 1,3,7,11 + 4624/4625--> [Open Collector]
```

### Stack
- Docker 20.10+
- Syslog-NG (simula LogRhythm OC + lrctl)
- Wazuh Manager (Platform Manager)
- Kali Linux (Red Team)
- MITRE ATT&CK Mapping

## 🚀 Despliegue

```bash
git clone https://github.com/TU_USUARIO/siem-purple-lab-docker.git
cd siem-purple-lab-docker
docker-compose up -d
docker logs -f lr-open-collector
```

## 🟪 Reglas Purple (3 reglas incluidas)

### 1. Brute Force -> Ofensa
**Logica:** 5x Failed Login (4625) + 1x Success (4624) en 5 min
**MITRE:** T1110.001
```sql
// Pseudo-regla para Wazuh/LogRhythm CRE
IF EventID=4625 COUNT 5 BY SourceIP in 5min
AND EventID=4624 BY Same SourceIP
THEN CREATE Offense: Brute Force Success
```

### 2. Sysmon - LSASS Dump (Mimikatz)
**Logica:** EventID 10 + TargetImage lsass.exe + GrantedAccess 0x1010/0x1410
**MITRE:** T1003.001

### 3. PowerShell Obfuscation
**Logica:** EventID 1 + Image powershell.exe + CommandLine contains -enc
**MITRE:** T1059.001

## 🔴 Simulacion Red Team

```bash
docker exec -it kali-attacker bash
# Dentro de Kali:
nmap -sV -p 22,80,443 wazuh-manager
hydra -l admin -P rockyou.txt ssh://open-collector
```

Valida en Wazuh si salta la ofensa.

## 🟦 Workflow SOC L1 (ITIL adaptado a SecOps)

Mi checklist real de Helpdesk adaptado:
1. **Validar:** ¿Es FP? ¿IP en BB:Approved Scanners?
2. **Enriquecer:** VirusTotal / AbuseIPDB para IP
3. **Clasificar:** MITRE + Severidad (Risk-Based Priority)
4. **Escalar:** A L2 con evidencias

## Autor

**Iván Ajenjo Morales**
Helpdesk L1/L2 | Gestion Incidencias (ITIL) | Hacker Ético | Especialista TI (IBM) & Docker | Aspirante a Junior SecOps | SIEM

En transición desde Helpdesk a SecOps. Este lab es mi portfolio Learning in Public.

#BlueTeam #RedTeam #PurpleTeam #SIEM #SOC #Wazuh #Docker #MITRE #HelpdeskToSecOps

## 🧠 Intelligent Response (LogRhythm)

![Intelligent Response](docs/intelligent-response.png)

Dashboard real de LogRhythm: Rules Triggers, Risk-Based Priority, Use Cases, Anomalies Users.
Este es el nivel que simulo con Wazuh + Open Collector. Mi objetivo: pasar de ver alertas a entender el contexto de riesgo.
