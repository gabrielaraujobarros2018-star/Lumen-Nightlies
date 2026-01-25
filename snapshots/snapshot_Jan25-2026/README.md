# PalisadeOS 1.0.6 Snapshot Jan25 2026

We implemented the HTML Parser (only the directory and files yet, no code). This build brings a Big structural change by Abbreviating the Name of many directories, Deleting unecessary folders, and Nesting directories. This change brings:

## Changes

[Structure]
- Nested directories
- Some folders moved
- Unecessary folders deleted
- Folder names abbreviated

[Scheduler Foundations]  
- Basic priority classes (foreground, background, system)  
- Idle thread specialization (mobile vs desktop)  
- Cooperative + preemptive hybrid flag  
  
[Debug Facilities]  
- Unified logging daemon  
- Log levels with runtime toggling  
- Ring buffer logs for early boot  
  
[Inspection Tools]  
- /sys/palisade/ inspection tree  
- Live system state viewer app  
- Build fingerprint exposure  
  
[Web]  
- Create a WebView  
- Implement content filters
