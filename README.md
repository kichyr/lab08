## Laboratory work V [![Build Status](https://travis-ci.com/puchkovki/lab05.svg?branch=master)](https://travis-ci.com/puchkovki/lab05)

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```ShellSession
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=puchkovki
$ alias gsed=sed # for *-nix system
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab05
Cloning into 'projects/lab05'...
Unpacking objects: 100% (32/32), done.
$ cd projects/lab05
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05
```

```ShellSession
$ mkdir third-party
$ git submodule add https://github.com/google/googletest third-party/gtest # add a test framework as a submodule of the repository that we’re working on
Cloning into '/mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest'...
Resolving deltas: 100% (14730/14730), done.
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../.. # stay on stable version 1.8.1
Checking out files: 100% (299/299), done.
Note: checking out 'release-1.8.1'.
HEAD is now at 2fe3bd99 Merge pull request #1433 from dsacre/fix-clang-warnings
$ git add third-party/gtest
$ git commit -m "added gtest framework"
[master fa23b07] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

```ShellSession
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\ # insert after the line "option(BUILD_EXAMPLES "Build examples" OFF)" line "option(BUILD_TESTS "Build tests" OFF)" into the configuration file CMakeLists
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF # add test target

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

```ShellSession
$ mkdir tests
$ cat > tests/test1.cpp <<EOF # add simple test
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON # build the project
-- The C compiler identification is GNU 7.4.0
-- The CXX compiler identification is GNU 7.4.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found PythonInterp: /usr/bin/python (found version "2.7.17")
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Check if compiler accepts -pthread
-- Check if compiler accepts -pthread - yes
-- Found Threads: TRUE
-- Configuring done
-- Generating done
-- Build files have been written to: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/_build
$ cmake --build _build # build subdirectory
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 33%] Linking CXX static library libprint.a
[ 33%] Built target print
Scanning dependencies of target gtest_main
[ 41%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library libgtest_main.a
[ 50%] Built target gtest_main
Scanning dependencies of target check
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
Running tests...
Test project /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.11 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.24 sec
```

```ShellSession
$ _build/check # run all tests
Running main() from /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (38 ms)
[----------] 1 test from Print (39 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (45 ms total)
[  PASSED  ] 1 test.
$ cmake --build _build --target test -- ARGS=--verbose # build test in detail
Running tests...
Test project /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/_build
test 1
    Start 1: check

1: Test command: /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/_build/check
1: Test timeout computed to be: 9.99988e+06
1: Running main() from /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 1 test from 1 test case.
1: [----------] Global test environment set-up.
1: [----------] 1 test from Print
1: [ RUN      ] Print.InFileStream
1: [       OK ] Print.InFileStream (5 ms)
1: [----------] 1 test from Print (5 ms total)
1:
1: [----------] Global test environment tear-down
1: [==========] 1 test from 1 test case ran. (5 ms total)
1: [  PASSED  ] 1 test.
1/1 Test #1: check ............................   Passed    0.04 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.09 sec
```

```ShellSession
$ gsed -i 's/lab05/lab05/g' README.md # substitute all 'lab05' occurence by 'lab05' in README file
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml # add code for the test building
$ gsed -i '/cmake --build _build --target install/a\ #add code for the test launching
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```ShellSession
$ travis lint
Hooray, .travis.yml looks valid :)
```

```ShellSession
$ git add .travis.yml
$ git add tests
$ git add -p # check which files have been modified
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 96a361e..aa7a323 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -4,6 +4,7 @@ set(CMAKE_CXX_STANDARD 11)
 set(CMAKE_CXX_STANDARD_REQUIRED ON)

 option(BUILD_EXAMPLES "Build examples" OFF)
+option(BUILD_TESTS "Build tests" OFF)

 project(print)

Stage this hunk [y,n,q,a,d,j,J,g,/,e,?]? y
@@ -34,3 +35,12 @@ install(TARGETS print

 install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
 install(EXPORT print-config DESTINATION cmake)
+
+if(BUILD_TESTS)
+  enable_testing()
+  add_subdirectory(third-party/gtest)
+  file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
+  add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
+  target_link_libraries(check ${PROJECT_NAME} gtest_main)
+  add_test(NAME check COMMAND check)
+endif()
Stage this hunk [y,n,q,a,d,K,g,/,e,?]? y

diff --git a/README.md b/README.md
index 26b5480..eaa9621 100644
--- a/README.md
+++ b/README.md
@@ -1,3 +1,3 @@
-[![Build Status](https://travis-ci.com/puchkovki/lab05.svg?branch=master)](https://travis-ci.com/puchkovki/lab05)
+[![Build Status](https://travis-ci.com/puchkovki/lab05.svg?branch=master)](https://travis-ci.com/puchkovki/lab05)
 In this lab we use the most common git utilites: fork, pull, merge, commit, etc.
 We also rewrite file '.gitignore'
Stage this hunk [y,n,q,a,d,e,?]? y
$ git commit -m"added tests"
[master 06cf4f8] added tests
 4 files changed, 32 insertions(+), 2 deletions(-)
 create mode 100644 tests/test1.cpp
$ git push origin master
Username for 'https://github.com': puchkovki
Password for 'https://puchkovki@github.com':
Counting objects: 43, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (35/35), done.
Writing objects: 100% (43/43), 5.99 KiB | 100.00 KiB/s, done.
Total 43 (delta 11), reused 0 (delta 0)
remote: Resolving deltas: 100% (11/11), done.
To https://github.com/puchkovki/lab05
 * [new branch]      master -> master
```

```ShellSession
$ travis login --auto --pro
Successfully logged in as puchkovki!
$ travis enable --pro
puchkovki/lab05: enabled :)
```

```ShellSession
$ mkdir artifacts
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png # make a screenshot of the correct test work
```

## Report

```ShellSession
$ popd
/mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace/projects/lab05/third-party/gtest /mnt/c/Users/dns/Documents/GitHub/puchkovki/workspace ~
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
Cloning into 'tasks/lab05'...
Resolving deltas: 100% (52/52), done.
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist-paste REPORT.md
```

## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2019 The ISC Authors
```
