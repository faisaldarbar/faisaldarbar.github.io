---
title: "CS50 Libraries in VS Code"
date: 2023-12-21T02:30:00Z
description: "This guide focuses on seamlessly incorporating the CS50 libraries into VS Code to enhance your C programming experience"
categories: ["Dev Docs"]
tags: [cs50, library, vscode, cs50.h]

cover:
  image: "/images/libcs50.webp"
  alt: "CS50 Libraries in VS Code"
  caption: "Photo by Rubaitul Azad on Unsplash"

---

Setting up a C development environment on Linux with essential tools like VS Code, GCC, C/C++ extension, and Code Runner extension is crucial for effective programming. This guide focuses on seamlessly incorporating the CS50 libraries into VS Code to enhance your C programming experience. (Works on Debian 12)

## Step-by-Step Guide:

### Prerequisites:
- Ensure you have VS Code, GCC, the C/C++ extension, and Code Runner extension installed on your Linux system.
- Download the `cs50.h` and `cs50.c` libraries from the official CS50 repository.

### Open Your C Project in VS Code:
- Create a new folder or open an existing C project folder in VS Code.

### Include `cs50.h` and `cs50.c` Libraries in Your Project:
- Copy both the downloaded `cs50.h` and `cs50.c` files into your project folder.

### Edit Configuration UI for C/C++:
- Press `Ctrl+Shift+P` to open the Command Palette.
- Type "C/C++ edit configuration UI" and select it.
- Scroll down to "Include Path" and add the path to your `cs50.h` library.

### Modify Code Runner Executor Map:
- Click on the gear icon in the lower-left corner of VS Code to access settings.
- Search for "code runner executor map" and select "Edit in settings.json".
- Add or modify the `"c"` command in the `settings.json` file to include the path to your `cs50.h` and `cs50.c` files:
  ```json
  "c": "gcc -I/path/to/your/project -L/path/to/your/project /path/to/your/project/cs50.c $fileName -o $fileNameWithoutExt && $dir$fileNameWithoutExt",
  ```
  Replace `/path/to/your/project` with the actual path to your project folder.

### Write and Run Your C Code:
- Write your C code, including the `#include "cs50.h"` statement.
- Use Code Runner to execute the code (click the play button in the top right corner).

**Note:** Ensure both `cs50.h` and `cs50.c` libraries are placed in the same location within your project folder for successful compilation and execution.

## Conclusion
By following these detailed steps, you can seamlessly integrate the CS50 libraries into your C project in VS Code on Linux (Debian). This setup enhances your development environment, enabling smooth and efficient C programming experiences.
