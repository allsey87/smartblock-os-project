## Introduction
In this section of the project we want to simplify the code base into what is essentially a minimum working example (MWE). This will make the code easier to integrate into the smartblock platform as you will gain a good understanding of all the components and strip away any code that we don't need.

## Instructions
1. To start with, use the AVR OS code base that you have installed, start to remove all the ARM related code (e.g. by removing the relevant `#define`, `#ifndef`, `#endif` preprocessor directives). You will want to recompile and check if the OS is still working frequently. After making major changes that you have confirmed to work, make a backup.
2. Flatten the code-base so that everything is in a single folder and uses a single Makefile to compile
3. Schedule a meeting with me so we can discuss your results from steps 1 and 2
