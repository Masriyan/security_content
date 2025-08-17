# Ransomware Precursors — Response Plan 

---

## 1) Main control flow (with phases)
```mermaid
flowchart TD
  %% Phases
  subgraph P0[Init]
    S([Start]) --> U[Get Effective User]
    U --> DUser{Is user type automation?}
  end

  subgraph P1[Workbook Handling]
    WList[List Workbooks]
    DUser -- No --> WList
    DUser -- Yes --> WList
    WList --> DWB{Ransomware Investigation workbook exists?}
    DWB -- Yes --> WAdd[Add workbook Ransomware Investigation]
    WAdd --> WStart[Set task Containment Plan to In Progress]
    DWB -- No --> PList
  end

  subgraph P2[Playbook Discovery]
    WStart --> PList[List playbooks tagged ransomware_response_option]
    PList --> DPB{Any playbooks found?}
  end

  subgraph P3[Human Decision]
    DPB -- Yes --> Prompt[/Prompt: Select response plan/]
    Prompt --> Choice{Plan is Do Nothing?}
  end

  subgraph P4[Launch and Close]
    Choice -- No --> Launch[Launch selected playbook]
    Launch --> DWB2{Workbook is Ransomware Investigation?}
    DWB2 -- Yes --> Fmt[Add note: Launched selected plan]
    Fmt --> Close[Complete task Containment Plan]
    Close --> End([Finish])
    Choice -- Yes --> End
  end
```
---

## 2) No-playbook branches (errors or notes)
```mermaid
flowchart TD
  PList[List playbooks tagged ransomware_response_option] --> DPB{Any playbooks found?}

  DPB -- No (workbook exists) --> WBUpd[Workbook Task Update: note Auto-Generated Ransomware Plan Error]
  WBUpd --> End1([Finish])

  DPB -- No (workbook missing) --> ErrNote[Add note: No ransomware response playbooks found]
  ErrNote --> End2([Finish])
```
---

## 3) Edge summary (compact)
```mermaid
flowchart LR
  S --> U --> DUser
  DUser --> WList --> DWB
  DWB -->|Yes| WAdd --> WStart --> PList --> DPB
  DWB -->|No| PList
  DPB -->|Yes| Prompt --> Choice -->|No| Launch --> DWB2 -->|Yes| Fmt --> Close --> End
  Choice -->|Yes| End
  DPB -->|No and WB exists| WBUpd --> End
  DPB -->|No and WB missing| ErrNote --> End
```
---

### Notes
- Tag selectable child playbooks with **ransomware_response_option**.
- Suggested children: Aggressive Containment, Quarantine Only, Monitor and Hunt.
