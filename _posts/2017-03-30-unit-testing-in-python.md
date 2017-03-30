---
title: 'Unit Testing in Python'
layout: post
tags:
  - Python
  - Unit-Test
category: Programming
mathjax: false
---

If you want to be able to change or rewrite your code and know you didn't break anything, proper unit testing is imperative.

The unittest test framework is python’s xUnit style framework.
It is a standard module that you already have if you’ve got python version 2.1 or greater.   The `unittest` module used to be called PyUnit, due to it’s legacy as a xUnit style framework.

<!--more-->

`unittest` module includes 4 main parts:

- [測試案例（Test case）](#test-case) - 測試的最小單元。
- 測試設備（Test fixture） - 執行一或多個測試前必要的預備資源，以及相關的清除資源動作。
- [測試套件（Test suite）](#test-suite) - 一組測試案例、測試套件或者是兩者的組合。
- [測試執行器（Test runner）](#text-test-runner) - 負責執行測試並提供測試結果的元件。

## Test Case

### Standard Workflow

1. You define your own **class** derived from `unittest.TestCase`.
2. Then you fill it with **functions** that start with `def test_XXX`.
3. You run the tests by placing `unittest.main()` in your file, usually at the bottom.

```python
import unittest

class SimplisticTest(unittest.TestCase):

    def setUp(self):
        pass
        
    def test(self):
        self.failUnless(True)
        
    def tearDown(self):
        pass

if __name__ == '__main__':
    unittest.main()
```

### Test Outcomes

Note that for more detailed test results, include the `-v` option as it indicates higher verbosity.

```shell
$ python unittest_simple.py -v

test (__main__.SimplisticTest) ... ok

----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

Tests have 3 possible outcomes:

- OK - The test passes.
- FAIL - The test does not pass, and raises an AssertionError exception.
- ERROR - The test raises an exception other than AssertionError.

### Example

```python
import unittest
from markdown_adapter import run_markdown
 
class TestMarkdownPy(unittest.TestCase):
 
    def setUp(self):
        pass
 
    def test_non_marked_lines(self):
        '''
        Non-marked lines should only get 'p' tags around all input
        '''
        self.assertEqual(
                run_markdown('this line has no special handling'),
                'this line has no special handling</p>')
 
    def test_em(self):
        '''
        Lines surrounded by asterisks should be wrapped in 'em' tags
        '''
        self.assertEqual(
                run_markdown('*this should be wrapped in em tags*'),
                '<p><em>this should be wrapped in em tags</em></p>')
 
    def test_strong(self):
        '''
        Lines surrounded by double asterisks should be wrapped in 'strong' tags
        '''
        self.assertEqual(
                run_markdown('**this should be wrapped in strong tags**'),
                '<p><strong>this should be wrapped in strong tags</strong></p>')
 
if __name__ == '__main__':
    unittest.main()
```

```shell
$ python test_markdown_unittest.py
FFF
======================================================================
FAIL: test_em (__main__.TestMarkdownPy)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_markdown_unittest.py", line 29, in test_em
    '<em>this should be wrapped in em tags</em></p>')
AssertionError: '*this should be wrapped in em tags*' != '<p><em>this should be wrapped in em tags</em></p>'
 
======================================================================
FAIL: test_non_marked_lines (__main__.TestMarkdownPy)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_markdown_unittest.py", line 21, in test_non_marked_lines
    '<p>this line has no special handling</p>')
AssertionError: 'this line has no special handling' != '<p>this line has no special handling</p>'
 
======================================================================
FAIL: test_strong (__main__.TestMarkdownPy)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "test_markdown_unittest.py", line 37, in test_strong
    '<p><strong>this should be wrapped in strong tags</strong></p>')
AssertionError: '**this should be wrapped in strong tags**' != '<p><strong>this should be wrapped in strong tags</strong></p>'
 
----------------------------------------------------------------------
Ran 3 tests in 0.142s
 
FAILED (failures=3)
```

## Test Suite

`TestSuite` allows programmers to select certain tests in a TestCase or combine tests from different TestCase.

1. Add tests from a TestCase respectively
```python
suite = unittest.TestSuite()
suite.addTest(CalculatorTestCase('test_plus'))
suite.addTest(CalculatorTestCase('test_minus'))
```
2. Add tests from a TestCase with a list
```python
suite = unittest.TestSuite()
tests = ['test_plus', 'test_minus']
suite = unittest.TestSuite(map(CalculatorTestCase, tests))
```
3. Add all tests from a TestCase
```python
suite = unittest.TestLoader().loadTestsFromTestCase(CalculatorTestCase)
```
4. Combine different TestCases
```python
suite = unittest.TestLoader().loadTestsFromTestCase(CalculatorTestCase)
suite2 = unittest.TestSuite()
suite2.addTest(suite)
suite2.addTest(OtherTestCase('test_orz'))
```

## Test Fixtures

Fixtures are resources needed by a test. For example, if you are writing several tests for the same class, those tests all need an instance of that class to use for testing. Other test fixtures include database connections and temporary files. 

To configure the fixtures, override `setUp()`.

```python
import unittest

class FixturesTest(unittest.TestCase):
    ...
    def setUp(self):
        self.fixture = range(1, 10)
    ...
```

To use the fixture, call through `self.fixture`

```python
import unittest

class FixturesTest(unittest.TestCase):
    ...
    def test(self):
        self.failUnlessEqual(self.fixture, range(1, 10))
    ...
```

To clean up, override `tearDown()`.

```python
import unittest

class FixturesTest(unittest.TestCase):
    ...
    def tearDown(self):
        del self.fixture
    ...
```

## Text Test Runner
To execute testing programmingly, except for calling `unittest.main(verbosity=2)`, you can use `TextTestRunner` to handle the execution of testing and its outcomes as well.

```python
suite = unittest.TestLoader().loadTestsFromTestCase(CalculatorTestCase)
unittest.TextTestRunner(verbosity=2).run(suite)
```


## References
- [unittest — Unit testing framework](https://docs.python.org/3/library/unittest.html)
- [unittest — 單元測試框架](https://docs.python.org.tw/3/library/unittest.html)
- [unittest – Automated testing framework](https://pymotw.com/2/unittest/)
- [unittest introduction](http://pythontesting.net/framework/unittest/unittest-introduction/)
- [Python Tutorial 第六堂（1）使用 unittest 單元測試](http://www.codedata.com.tw/python/python-tutorial-the-6th-class-1-unittest/)
