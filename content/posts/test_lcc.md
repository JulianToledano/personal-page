+++
date = "2020-07-27"
title = "Test With Python and Lemoncheesecake"
description = "Test With Python and Lemoncheesecake"
katex = false
series = ["Python", "testing"]
+++
# Test With Python and Lemoncheesecake

We all know that testing is a crucial part of software development. But lets be honest, most of us procrastinate them until the base code is so big that is hard to know where to start, so you just don’t do it.

I’m currently learning [Go](https://golang.org/), making small and easy projects and I had an idea “*this is the perfect moment to learn software testing”*. For someone who never made a test the problem begins with the question “*What do I have to test?*”. There are so many tools to pick and so many [kind of tests](https://www.softwaretestinghelp.com/types-of-software-testing/) with different purpose that makes hard to know which one to choose. One day I got tired of reading theoretical testing posts and decided to get my hands dirty, *“better start and make things the wrong way while I learn than overthink and do nothing”*. With this idea in mind, I decided on my starting point: functional testing for my Go ApiRest.

What is [functional testing?](https://en.wikipedia.org/wiki/Functional_testing) There are a lot of definitions on the internet but for now lets assume that functional testing is a type of black-box test. This means that given an entry *X* the output *Z* will always be the same.
> # Functional testing is a black-box kind of test.

Ok. Now I know what kind of test I’ll do but, how do I make them?. There are a lot of functional testing tools to pick but since I’ve been working as a Python developer for the last two years, I wanted to choose a python framework. The most famous frameworks are for unit testing: [unittes](https://docs.python.org/3/library/unittest.html), [nose](https://nose.readthedocs.io/en/latest/) and [pytest](https://docs.pytest.org/en/latest/). Of course those are great testing frameworks but didn’t meet my requirements. I was completely lost until somehow I end up in a [presentation ](https://docs.google.com/presentation/d/1GUPp1bwBpJUfnbAfuVa0np61xk4S30MpQwxucc7Xuss/edit#slide=id.p)about Rest Api functional testing with python from the pycon2018. That’s how I discovered lemoncheesecake.

![**Photho by [Gimena Leguizamon](https://www.pinterest.co.uk/gimenaleguizamon/)**](https://cdn-images-1.medium.com/max/2000/1*3oKJIVYq56AV34lWYCgl3w.jpeg)***Photho by [Gimena Leguizamon](https://www.pinterest.co.uk/gimenaleguizamon/)***

[Lemoncheesecake](http://docs.lemoncheesecake.io/en/latest/index.html) as defined in its web is a functional test framework for Python that brings trust around test results. Among its features I’d highlight the advanced test hierarchies using suites, tests, nested suites and the beautiful html reports. The purpose of this story is to give you a small intro in testing an API rest with lcc. To do so we’ll make request against a backend and check if the results are correct. Our lucky backend will be [fruityvice](https://www.fruityvice.com/) but for now let’s get our environment ready.

## Set up the project

### Installation

Create a virtual environment and install lcc through pip:

* virtualenv .venv

* source .venv/bin/activate

* pip install lemoncheesecake

### Create project’s structure

One of the most powerful lemoncheesecake feature is it’s command line interface called lcc. We’ll get in touch later with the cli but for now type:

lcc bootstrap fruityvice_tests

This command will create a directory called fruityvice_tests with two directories and a python file.
```
    fruityvice_tests
    ├── fixtures
    ├── project.py
    └── suites
```
* project.py is the project file and allows the customization of several behaviors of lcc.

* fixtures directory is where our fixtures are. I’ll explain what a fixture is later.

* suites directory is where our test suites are.

## Testing

Once we have set up our project it’s time to start testing. As I mentioned before, we’ll use fruityvice as our backend to test. You can check all its endpoints [here](https://www.fruityvice.com/doc/index.html#api-GET-GETsByFamily).

### First test

Let’s begin with an easy one. Fruityvice exposes and endpoint that let you retrieve all the fruits at the same time so let’s call it and check the response code is correct.

First we have to create our suite in the suites directory, to do so create a file with desired name. In my case it’ll be suites/get_all.py

<iframe src="https://medium.com/media/7def3c814308be4d2e9f7c048459b4fa" frameborder=0></iframe>

First we have created a suite with the decorator @lcc.suite(), a suite is a way to group tests in a class. The tests are all the class methods with decorator @lcc.test() as in line 11. This suite is pretty simple, we have just one test that checks the response code of a GET request is 200. Note that the function check_that is part of the lemoncheesecake.matching package that was imported with *. lcc documentation recommend import the matching package with a wildcard for readability purpose.

Before runing the test let’s check our project structure. lcc cli has a command for that, in the base dir of the project run lcc show and you’ll see hierarchically the structure of your suites and all the tests in them.
```
    $ lcc show
    * get_all
        * get_all.GetFruits
            - get_all.GetFruits.key_already_exists
```
Now let’s run the test, just type lcc run and it’ll prompt the results of all tests.
```
    $ lcc run
    ======================== get_all.GetFruits ========================
     OK  1 # get_all.GetFruits.key_already_exists

    Statistics :
     * Duration: 0.642s
     * Tests: 1
     * Successes: 1 (100%)
     * Failures: 0
    #
```
You can check our test was a success but let’s be honest this is not useful in a production environment. We have the need of reports that we can check over the time. Don’t worry, lcc can create reports in various formats including: html, json, junit and more. By default every time you run lcc run a beautiful html report is created. You can find them in the newly created directories report/ and reports/. The last report will always be in report dir while old ones will be moved automatically to reports/. You can open report/report.html file with your browser to see the beautiful report lcc did for you.

![Full test report](https://cdn-images-1.medium.com/max/3840/1*Jl3lG-JDM59k0ZuoYL2SMQ.png)*Full test report*

You can click on every test to see all the details.

![Get all fruits test result](https://cdn-images-1.medium.com/max/3302/1*_HSghlLqjy-H34VE3hNCnA.png)*Get all fruits test result*

### Test with logs

You can add more information to the html report with [lcc loggin](http://docs.lemoncheesecake.io/en/latest/logging.html). Let’s add to the previous test a log of the request.

<iframe src="https://medium.com/media/74315d6fd351d7c930ee388941848407" frameborder=0></iframe>

![First test with log](https://cdn-images-1.medium.com/max/3302/1*W10azAv0rOv-wKbOxG4QTw.png)*First test with log*

As you can see the log in line 15 was added to the html report. There are all the common logs levels but take in consideration that if you write a log with error level, the whole test will be consider as failed.

### Auto generate test

Fruityvice provides an endpoint to retrieve one fruit’s family subset. Let’s write a simple suite to retrieve two existing families.

<iframe src="https://medium.com/media/89de0b69958d4ff3ad864f80d13e9a8a" frameborder=0></iframe>

```
    $ lcc run
    ======================== get_all.GetFruits ========================
     OK  1 # get_all.GetFruits.get_all_fruits

    ==================== get_by_familys.GetFruits =====================
     OK  1 # get_by_families.GetFruits.get_rutaceae
     OK  2 # get_by_families.GetFruits.get_bromeliaceae

    Statistics :
     * Duration: 2s
     * Tests: 3
     * Successes: 3 (100%)
     * Failures: 0

    #
```

You can see the tests are exactly the same except for lines 14 and 22 where the family is specified. Luckily there’s an option to avoid duplicate test cases, you can implement the test function as a [callable object](https://rszalski.github.io/magicmethods/#callable) implementing the [magic method](https://rszalski.github.io/magicmethods/) __call__. Let’s write a callable test to get all the families.

<iframe src="https://medium.com/media/ca841c37c3514d137628f192c709434f" frameborder=0></iframe>

You can observe in line 22 Test class takes three arguments the first one is the test name, second one is its description and third one the callable object.
> *class *lemoncheesecake.api.Test(*name*, *description*, *callback*)
```
    $ lcc run
    ======================== get_by_familys.get ========================
     OK  1 # get_by_families.get.Musaceae
     OK  2 # get_by_families.get.Anacardiaceae
     OK  3 # get_by_families.get.Rutaceae
     OK  4 # get_by_families.get.Cucurbitaceae
     OK  5 # get_by_families.get.Solanaceae
     OK  6 # get_by_families.get.Bromeliaceae
     OK  7 # get_by_families.get.Rosaceae
```
As you can see the name of the family is also the description of the test.

### Fixtures

Fixtures are a powerful and modular way to inject dependencies into your tests. In the above tests the urls are hardcoded. What happens if one day instead of [http://www.fruityvice.com/api/fruit/](http://www.fruityvice.com/api/fruit/) the url change to [http://www.fruityvice.com/api/v2/fruit/](http://www.fruityvice.com/api/fruit/). You’ll have to change every single test. This is one of the purpose of fixtures.

<iframe src="https://medium.com/media/02dd044dc8f196881bf0d86785b87c07" frameborder=0></iframe>

Now the url can be injected in all the tests through the url fixture.

### Customize report

Lemoncheesecake reports are one of its strength. You can customize them in the Project class. Let’s change the report’s tittle and specify app and version.

<iframe src="https://medium.com/media/8ef07df1cbfd9d8b0d0ebfda4566d2df" frameborder=0></iframe>

![Final report](https://cdn-images-1.medium.com/max/3806/1*yjVREQCQT046qIJCHt1HnQ.png)*Final report*

As you can see now the report tittle completely change and version and app are specify.

Lemoncheesecake is a powerful functional testing tool. Here I give a couple of easy examples but this is just the tip of the iceberg. You’ll find more examples on its [official documentation](http://docs.lemoncheesecake.io/en/latest/index.html).
