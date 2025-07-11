 🛡️ Linux File and Directory Permissions Project

**Author:** Mudapaka Sailaxman  
**Date:** 11 June 2025  
**Report:** [Linux_File_Permissions_Project.pdf](./Linux_File_Permissions_Project.pdf)

---

 📚 Project Overview

This project demonstrates how to manage file and directory permissions in a Linux environment using command-line tools. The aim was to enhance system security by applying the **principle of least privilege**, ensuring that only authorized users can access or modify critical files.

---

 🔧 Skills Demonstrated

- Linux File System Security
- Command-line Usage (`chmod`, `ls -la`)
- Understanding Permission Strings
- Applying Principle of Least Privilege

---

 🛠️ Key Tasks

 🔍 1. File and Directory Inspection
- Used `ls -la` to display hidden files and permission details.

 🔐 2. Analyze and Explain Permission Strings
- Explained 10-character permission formats (e.g., `-rw-r--r--`)
- Described user, group, and others’ access levels.

 ✏️ 3. Modify File Permissions
 ✅ `project_k.txt`
- Original: `rw-rw-rw-`
- Command: `chmod o-w project_k.txt`
- Result: Others lost write access.

 ✅ `.project_x.txt`
- Command: `chmod u-w,g-w+r .project_x.txt`
- Result: User and group write removed, group read added.

 ✅ `drafts/` directory
- Command: `chmod g-x drafts`
- Result: Restricted group access by removing execute permission.

---

 ✅ Outcome

This project highlights practical security techniques in Linux system administration. By carefully modifying file and directory permissions, I was able to:
- Minimize access risk
- Apply security best practices
- Protect sensitive information from unauthorized changes

> 📎 _View the full report: [Linux_File_Permissions_Project.pdf](./Linux_File_Permissions_Project.pdf)_
