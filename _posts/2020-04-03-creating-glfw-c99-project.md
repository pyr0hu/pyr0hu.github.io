---
layout: post
title: Creating a C99 project with GLFW on macOS
---

During this quarantine time, I got bored with all the web dev that I usually do and I was like let's do something fun. In the past I usually played a lot with OpenGL and with graphics in general, so it was time that I revisited that stuff. I also took up C99 instead of C++ or any higher level language, just because. I might regret it, but it seems fun.

When I create a C/C++ I usually have to fight a bit with CMake as I'm not really comfortable with it, usually used Make for smaller projects, PoCs. So I downloaded CLion from JetBrains and created a new project. Looks good so far.

## How do I modify CMake's default structure?

I usually go with the `bin/`, `include/`, `lib/` and `src/` folders with any C project. The one that CLion generated was using a `main.c` in the root, so I just moved it into a src directory and the IDE already updated the `CMakeLists.txt` file for me.
Next step was to use the `bin/` as the output folder. After 10 minutes of searching and browsing StackOverflow I found that setting the `CMAKE_RUNTIME_OUTPUT_DIRECTORY` seems to be the option for me. So I put this line into my `CMakeLists.txt`:

```
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
```

I also updated CMake to use my `include/` and `lib/` directory for headers and library files. To use globally installed packages (e.g. with brew) I added `/usr/local/include` and `/usr/local/lib` too.

```
include_directories(/usr/local/include ${PROJECT_SOURCE_DIR}/include)
link_directories(/usr/local/lib ${PROJECT_SOURCE_DIR}/lib)
```

## Install a simple logger

I wanted to use some logging to help debugging, but did not want to create any boilerplate code for logging so I grabbed a small package from GitHub. [This small package from rxi](https://github.com/rxi/log.c) is lightweight and easy to use.

Here came the first challange with CMake, how do I add a simple library that has no `CMakeLists.txt`, so I cannot use `find_package`. I was thinking, do I put these files to a `thirdparty/` folder and manually create a `CMakeLists.txt`? Nah, it was much much easier. Just drop the `.c` and `.h` files to `src/` and `include/`, add the `.c.` to `CMakeLists.txt` and we good to go.

## Using GLFW

Next step was to add GLFW so I can create windows and prepare OpenGL context. I just installed the lib with `brew install glfw`. That installed all the required headers and library files that I'll need when compiling. Because I already added the `lib` and `include` folder from `/usr/local`, I could just call `target_link_libraries(game glfw)` in `CMakeLists.txt` and GLFW was available for me.
