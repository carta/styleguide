# Writing unit tests

Unit tests are a great way for developers to get immediate feedback when introducing new features, behavior, fixing bugs, and to prevent future regressions.

There are many resources describing the benefit and usage of _what_ you can do with unit tests. But, for python, there are few resources devoted to _best practices_ when creating a great pytest test suite.

Based on developer experience, we standardized test suite "best practices" for Carta that you can implement just as easily.

## Folder structure

Folder structure for tests follow a defined pattern. The rule to follow is that for any given python file the corresponding test file should be easy to find.

Good test folder structure mimics the root directory. For instance, If the python file you are adding behavior to is located at `/apps/vehicles/models.py` the corresponding tests for the classes in that file are located at `/tests/unit/apps/vehicles/models/`.

[Discuss this guideline](/../../issues/1)

### One file per class

Inside of the file `/apps/vehicles/models.py` there is a `Car` class. The tests for this class are easily found in the file:  `/apps/vehicles/models/test_car.py`

[Discuss this guideline](/../../issues/2)

### Files without Classes

Inside of the file `/app/vehicles/helpers.py` there are only top level function definitions and no classes. The tests for these functions could be found in `/app/vehicles/test_helpers.py`.

## Classes for methods under test

Inside of `test_car.py` there will be classes which mimic the public interfaces of the object. For instance, if the `Car` class has the method `start()` there will be a class in the test file called, `class TestStart`.

The functions that belong to the TestStart class correspond to the code under test.

```py
# bad
class TestThatTheCarStarts:

# good
# describing Car.start()
# located in car.py
class TestStart:
```

[Discuss this guideline](/../../issues/3)

## How to describe your test methods

Be clear about what behavior you are describing. When describing a context,
start its description with "when" or "with".

```py
# bad
def test_car_does_not_start_if_no_gas_in_the_tank(self):
    car = Car(fuel=None)

    with pytest.raises(RuntimeError):
        car.start()

# good
def test_when_tank_is_empty_does_not_start(self):
    car = Car(fuel=None)

    with pytest.raises(RuntimeError):
        car.start()
```

[Discuss this guideline](/../../issues/4)

## One assertion per test

The "one expectation" tip is more broadly expressed as "each test should make only one assertion". This helps you on finding possible errors, going directly to the failing test, and to make your code readable.

In isolated unit specs, you want each example to specify one (and only one) behavior. Multiple expectations in the same example is a code smell that you may be specifying multiple behaviors.

```py
# bad
class TestGetCarInfo():
    def test_default_instance_has_factory_settings(self):
        car = Car()
        info = car.get_car_info()

        assert info['color'] == 'gray'
        assert info['has_gps'] is False
        assert info['interior_material'] is not 'leather'

# good
class TestGetCarInfo():
    def test_default_instance_color_is_gray(self):
        car = Car()

        assert car.get_car_info()['color'] == 'gray'

    def test_default_instance_does_not_have_gps(self):
        car = Car()

        assert car.get_car_info()['has_gps'] is False

    def test_default_instance_does_not_have_leather_interior(self):
        car = Car()

        assert car.get_car_info()['interior_material'] is not 'leather'
```

[Discuss this guideline](/../../issues/5)


## Mocking functions

Decorators become very nasty to read once you have more than one. The [pytest-mock](https://pypi.python.org/pypi/pytest-mock) library description sums up the problem nicely.

* test functions must receive the mock objects as parameter, even if you don’t plan to access them directly.
* The argument order depends on the order of the decorated patch functions.
* receiving the mocks as parameters doesn’t mix nicely with pytest’s approach of naming fixtures as parameters, or pytest.mark.parametrize;
* you can’t easily undo the mocking during the test execution;

```py
# bad
@patch.object(FuelTank, 'get_fuel_left', lambda self: 0)
def test_when_no_fuel_exists_returns_empty_1(self):
    mocked_tank = FuelTank()
    car = Car(fuel_tank=mocked_tank)

    assert car.get_remaining_fuel() == 'empty'

# good
def test_when_no_fuel_exists_returns_empty(self):
    mocked_tank = FuelTank()
    mocked_tank.get_fuel_left = MagicMock(return_value=0)
    car = Car(fuel_tank=mocked_tank)

    assert car.get_remaining_fuel() == 'empty'
```

[Discuss this guideline](/../../issues/6)

## Mocking properties

When needing to mock a property, use the [pytest-mock](https://pypi.python.org/pypi/pytest-mock) library. Decorators create too much code and become hard to read when needing to mock multiple properties. The `mocker` fixture provided by `pytest-mock` makes things clean and easy to read.

```py
# bad
@patch.object(Car, 'condition', None)
@patch.object(Car, 'year', None)
@patch.object(Car, 'miles', None)
@patch.object(Car, 'base_price', None)
def test_returns_a_value(self):
    car = Car()
    car.condition = 'very good'
    car.year = 2005
    car.miles = 65000
    car.base_price = Decimal(25000)

    assert car.get_value() == Decimal('7812.5')

# good
def test_better_returns_a_value(self, mocker):
    car = Car()
    mocker.patch.object(Car, 'condition', 'very good')
    mocker.patch.object(Car, 'year', 2005)
    mocker.patch.object(Car, 'miles', 65000)
    mocker.patch.object(Car, 'base_price', Decimal(25000))

    assert car.get_value() == Decimal('7812.5')
```

[Discuss this guideline](/../../issues/7)


### Acknowledgments

* [Better Specs](http://www.betterspecs.org/)
