# Testing Infrastructure

In this course, all labs will use Google Tests. This README gives you a small
primer and use this infrastructure to include your own tests locally. Note that
these tests will not be run when you push to GitHub, as we use a standardized
set of visible (those in your tests/ directory) and hidden tests to evaluate and
score your lab assignment.

## Defining a test

In the lab assignment directory, we begin by creating a new file under one of
the test directories `tests/tests-ex01`
`tests`, called `tests/tests-custom`.

```bash
$ touch tests/tests-ex01/test-ex01-custom.cpp
```

## Writing a test

Using your favorite editor, open this file and write a sample test case.

```c++
#include <gtest/gtest.h>

extern "C" {
#include "../../inc/strcmp.h"
}

TEST(custom, my_custom_test) {
    EXPECT_EQ(ex01_strcmp("Hello", "Hello"), 0);
}

int main(int argc, char **argv) {
    ::testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}

```

The first line includes the gtest header. The second include inside the `extern
"C"` block includes your C header file. Since we are operating on Lab 0 to
define a custom test, we include the `strcmp.h` header.

Next, we define our own test `custom` with a test case `my_custom_test`.  Here,
we assert that the expected value of our call to the function `ex01_strcmp`
returns 0, as we expect it to return 0 when matching equal strings.

You can find more information on other available assertions [here](https://google.github.io/googletest/reference/assertions.html).

## Building the test

Simply run make, and this now be running your custom test case as part of the
grading process.

In our case, we will see:

```bash
============================
Note: Google Test filter = *
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from custom
[ RUN      ] custom.my_custom_test
tests/tests-ex01/test-ex01-custom.cpp:8: Failure
Expected equality of these values:
  ex01_strcmp("Hello", "Hello")
    Which is: 42
  0
[  FAILED  ] custom.my_custom_test (0 ms)
[----------] 1 test from custom (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 0 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] custom.my_custom_test
...
```

THis is because we haven't really implemented anything in our Lab 0 assignment
yet, so it fails.

You can also run ex01 tests in isolation using the grading.sh script in the
repository, like so:

```bash
$ grading.sh 1
...
============================
Note: Google Test filter = *
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from custom
[ RUN      ] custom.my_custom_test
tests/tests-ex01/test-ex01-custom.cpp:8: Failure
Expected equality of these values:
  ex01_strcmp("Hello", "Hello")
    Which is: 42
  0
[  FAILED  ] custom.my_custom_test (0 ms)
[----------] 1 test from custom (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 0 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] custom.my_custom_test

 1 FAILED TEST
============================
```

Once we implement `ex01_strcmp`, our test case passes:

```bash
$ grading.sh 1
...
============================
Note: Google Test filter = *
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from custom
[ RUN      ] custom.my_custom_test
[       OK ] custom.my_custom_test (0 ms)
[----------] 1 test from custom (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
============================
```

**NOTE: We will not be running your custom test cases on CI during evaluation.
These are only meant for your own local testing process, to allow you to easily
extend the test suite and test logic of your code.**
